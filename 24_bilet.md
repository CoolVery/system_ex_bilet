## Отправка и получение данных из именованного канала. Данные содержатся в строковой форме. Перед отправкой следующей строки клиенту необходимо дождаться ответа от сервера (отдельно для Windows и Linux).

### Windows

Нам надо создать два вообще отдельных проекта - сервер и клиент
В Сервере пишем код
```C
#define SIZE_BUFFER 140
#include <stdio.h>
#include <Windows.h>

int main() {
	system("chcp 1251");
	//имя канала
	HANDLE hNamePide;
	//имя канала для создания
	LPSTR lpszPipeName = L"\\\\.\\pipe\\MyPipe";
	//размер буффера
	DWORD size_buffer = SIZE_BUFFER;
	//буффер для хранения данных
	LPWSTR buffer = (CHAR*)calloc(size_buffer, sizeof(CHAR));
	//сообщение в ответ
	char message[SIZE_BUFFER];
	//флаг того, что подключение успешное
	BOOL connected;
	//сколько прочитали
	DWORD count_readen;
	//флаг того, успешно ли прочитали
	BOOL success_read;
	while (TRUE) {
		//Создаем наш канал
		hNamePide = CreateNamedPipe(
			lpszPipeName,
			PIPE_ACCESS_DUPLEX,
			PIPE_TYPE_MESSAGE | PIPE_READMODE_MESSAGE | PIPE_WAIT,
			PIPE_UNLIMITED_INSTANCES,
			SIZE_BUFFER,
			SIZE_BUFFER,
			INFINITE,
			NULL
		);
		//проверяем подключение
		connected = ConnectNamedPipe(hNamePide, NULL);
		if (connected)
		{
			//читаем из канала
			success_read = ReadFile(hNamePide, buffer, size_buffer, &count_readen, NULL);
			if (success_read)
			{
				//Отвечаем ему
				printf("\nКлиент пишет: %s\n", buffer);

				printf("\nВведите ответ для клиента:\n");
				gets(message);
				buffer = &message;
				WriteFile(hNamePide, buffer, size_buffer, &count_readen, NULL);
			}
		}
		else {
			printf("\nКлиент покинул нас\n");
		}
		CloseHandle(hNamePide);
	}
}
```

В клиенте пишем
```C
#define _CTR_SECURE_NO_WARNINGS
#include <stdio.h>
#include <Windows.h>
#define SIZE_BUFFER 140

int main() {
	system("chcp 1251");
	//имя канала
	LPSTR lpNamePipe = L"\\\\.\\pipe\\MyPipe";
	//флаг того, есть ответ от сервера или нет
	BOOL flag_otvet = TRUE;
	//буффер для ответа
	char message[SIZE_BUFFER];
	//размер буффера
	DWORD size_buffer = SIZE_BUFFER;
	//буфер для хранения данных
	LPWSTR buffer = (CHAR*)calloc(size_buffer, sizeof(CHAR));
	//сколько прочитали или записали
	DWORD count_read;
	//прочитали ли успешно
	BOOL success_read;
	while (TRUE) {
		//получаем канал
		HANDLE hNamePipe = CreateFile(
			lpNamePipe,
			GENERIC_READ | GENERIC_WRITE,
			0,
			NULL,
			OPEN_EXISTING,
			0,
			NULL
		);
		//задаем мод на чтение
		DWORD dwMode = PIPE_READMODE_MESSAGE;
		//Проверяем подключение к каналу
		BOOL isSuccess = SetNamedPipeHandleState(hNamePipe, &dwMode, NULL, NULL);
		if (!isSuccess) {
			printf("Сервер не отвечает\n");
			flag_otvet = TRUE;
		}
		else {
			//Если можно ответить, то клиент пишет сервуре
			if (flag_otvet) {
				printf("\nНапишите свое сообщение серверу\n");
				gets(message);
				buffer = &message;
				WriteFile(hNamePipe, buffer, SIZE_BUFFER, &count_read, NULL);
				flag_otvet = FALSE;
			}
			//читаем из канала данные
			success_read = ReadFile(hNamePipe, buffer, SIZE_BUFFER, &count_read, NULL);
			if (success_read) {
				printf("\nСервер пишет: %s\n", buffer);
				flag_otvet = TRUE;
			}
			Sleep(1000);
			CloseHandle(hNamePipe);
		}
	}
}
```

---
## Линукс

Клиент
```C
#include <sys/stat.h>//mkfifo, S_I...
#include <fcntl.h>//O_...
#include <unistd.h>//read, write, open
#include <stdio.h>
#include <malloc.h>
#include <string.h>
#include <stdlib.h>
#define PATHSERVER "/tmp/FIFOserver"//общедоступное имя сервера
#define PATHCLIENT "/tmp/FIFOclient"//имя конкретного клиента

struct DATA//структура для обмена данными с сервером
{
char clientFIFOPath[20];
char text[50];
};

typedef struct DATA DATA;

int main()
{
	char* buffer = malloc(100 * sizeof(char));
	for(;;) {
		mkfifo(PATHCLIENT,0664);//rw user, rw group, r other
		int fifoWrite=open(PATHSERVER,O_WRONLY);
		printf("\nнапишите свое сообщение серверу\n");
		gets(buffer);
		DATA data = {PATHCLIENT, ""};
		strncpy(data.text, buffer, sizeof(data.text) - 1);
		write(fifoWrite,&data,sizeof(DATA));
		close(fifoWrite);
		int fifoRead=open(PATHCLIENT,O_RDONLY);
		char answer[100];
		read(fifoRead,&answer,sizeof(char) * 100);
		printf("Ответ сервера: %s\n",answer);
	}
}
```

Сервер
```C
#include <sys/stat.h>//mkfifo, S_I...
#include <fcntl.h>//O_...
#include <unistd.h>//read, write, open
#include <stdio.h>
#include <malloc.h>
#define PATH "/tmp/FIFOserver"
  
struct DATA //структура, в которой содержится передаваемая от клиента информация
{
char clientFIFOPath[20];
char text[50];
};

typedef struct DATA DATA;

int main()
{
	char* buffer = malloc(100 * sizeof(char));
	mkfifo(PATH,0664);//создаем очередь со следующими правами: rw user, rw group, r other
	write(STDOUT_FILENO, "сервер запустился\n",35); //сообщение при старте сервера
	DATA data;
	for(;;)
	{
		int fifoRead = open(PATH,O_RDONLY);//O_NONBLOCK - чтобы не блокировалась система. O_RDONLY заблокирует систему до тех пор, пока в нее кто-либо не напишет
		read(fifoRead,&data,sizeof(DATA));//считываем данные от клиента
		close(fifoRead);//закрываем дескриптор для чтения
		printf("Подключился клиент со следующими данными: имя канала: %s, передаваемые данные %s\n", data.clientFIFOPath, data.text);
		printf("\nнапишите свое сообщение клиенту\n");
		gets(buffer);
		char answer[100];
		strncpy(answer, buffer, sizeof(answer));
		printf("отвте %s", answer);
		int fifowrite = open(data.clientFIFOPath,O_WRONLY);//открываем очередь на запись для клиента
		write(fifowrite,&answer,sizeof(char) * 100);//записываем в канал клиента ответ
		close(fifowrite);//закрываем дескриптор для записи
	}
}
```