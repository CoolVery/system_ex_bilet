## Использование анонимного канала для передачи строковых значений между процессами (отдельно для Windows и Linux)

---
Для Windows надо создать две программы
В 1 программе
```C
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <Windows.h>
#define PATH "C:\\Users\\zepli\\source\\repos\\Project1\\x64\\Debug\\ProjectSecond.exe"//имя запускаемого дочернего процесса
#define BUF_SIZE 256

HANDLE hRead, hWrite;//дескрипторы чтения и записи в анонимный канал
SECURITY_ATTRIBUTES sa = { sizeof(sa),NULL,TRUE };//структура безопасности, в которой разрешено наследование дескрипторов

int main()
{
	system("chcp 1251>nul");
	CreatePipe(//создаем анонимный канал
		&hRead,//ссылка на дескриптор для чтения
		&hWrite,//ссылка на дескриптор для записи
		&sa, //указатель на структуру безопасности (должно быть разрешено наследование дескрипторов)
		0);	//размер буфера канала (0 - по умолчанию)
	//блок создания дочернего процесса

	LPSTR str = calloc(100, 1);//для аргументов командной строки
	sprintf(str, "%d %d", hWrite, hRead);//переводим дескриптор для записи в строку
	STARTUPINFO* sti = calloc(1, sizeof(STARTUPINFO));				// структура
	sti->cb = sizeof(STARTUPINFO);
	PROCESS_INFORMATION* pi = calloc(1, sizeof(PROCESS_INFORMATION));
	if (!CreateProcessA(PATH,//путь к файлу
		str,//агрументы командной строки
		NULL,//структура безопасности процесса
		NULL,//структура безопасности потока
		TRUE,//наследовать дескриптор нового процесса от старого?
		0,//флаги приоритета процессов CREATE_NEW_CONSOLE
		NULL,//среда окружения процесса ANSI или UNICODE
		NULL,//консольный путь к каталогу, из которого вызывается процесс
		sti,//указатель на структуру STARTUPINFO
		pi))//указатель на структуру PROCESS_INFORMATION
	{
		printf("Не удалось создать процесс. Код ошибки: %d.\n", GetLastError());
	}
	WriteFile(hWrite, "ОПААААААА", 66, NULL, NULL);	//пишем в канал

	return 0;

}
```
Во 2 программе
```C
#include <stdio.h>
#include <Windows.h>
#define BUF_SIZE 256

HANDLE hRead, hWrite;//дескрипторы чтения и записи в анонимный канал

int main(int argc, char* argv[])
{
	//получаем дескрипторы для чтения и записи из аргументов командной строки
	hWrite = (HANDLE)atoi(argv[0]);
	hRead = (HANDLE)atoi(argv[1]);
	system("chcp 1251>nul");
	system("pause");
	CloseHandle(hWrite);//закрываем дескриптор для записи у потомка
	char buffer[BUF_SIZE];
	ReadFile(hRead, buffer, BUF_SIZE, NULL, NULL);//читаем данные из канала
	printf("Ребенок прочитал: %s и завершил работу \n", buffer);//выводим на экран и завершаем работу
	return(0);
}
```

В линукс
```C
#include <stdio.h>
#include <unistd.h> //fork, close, read, write, pipe, _exit
#include <sys/wait.h> //wait
#include <string.h>//strlen
#include <malloc.h>
#define BUF_SIZE 1
#define MESSAGE "Hello world"

int main() {
	int pfd[2]; // Файловые дескрипторы канала
	ssize_t numRead;
	if (pipe(pfd) == -1) // Создаем канал
		_exit(1);//в случае ошибки завершаем работу программы
	switch (fork()) {
	case -1:
		_exit(1);
	case 0: //потомок только читает данные из канала
		if (close(pfd[1]) == -1) // Записывающий конец не используется
			_exit(1);
			do { // Считываем данные из канала до тех пор, пока он не опустеет
				char * buf = calloc(1,BUF_SIZE);//создаем буфер для чтения
				numRead = read(pfd[0], buf, BUF_SIZE);//операция блокируется до тех пор, пока в канал не поступят данные
				if (numRead == -1)
					_exit(1);
				printf("Родитель написал: %s\n",buf);//выводим на экран то, что прочитали
				free(buf);//очищаем буфер
			}
			while (numRead != 0);
				if (close(pfd[0]) == -1)
					_exit(1);
				_exit(0);
	default: // Родитель записывает в канал
		if (close(pfd[0]) == -1) // Считывающий конец не используется
			_exit(1);
		if (write(pfd[1], MESSAGE, strlen(MESSAGE)) != strlen(MESSAGE))
			_exit(1);
		if (close(pfd[1]) == -1) // Потомок увидит символ завершения файла
			_exit(1);
		wait(NULL); // Ждем завершения потомка
		_exit(0);
	}
}
```