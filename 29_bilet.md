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
#include <unistd.h>
#include <sys/wait.h>
#include <stdlib.h>
#include <string.h>

#define BUFSIZ 512

int main()
{
    int pipAnim[2];
    int numRead, numWrite;
    if (pipe(pipAnim) == -1) {
        printf("ошибка создания дескриптор\n");
        exit(1);
    }
    switch(fork()) {
        case -1:
            printf("Форк не сработал\n");
            exit(1);
            break;
        case 0:
            if (close(pipAnim[1]) == -1) {
                printf("ошибка закрытия записи\n");
                exit(1);
            }
            do
            {
                char* buf = calloc(BUFSIZ,sizeof(char));
                numRead = read(pipAnim[0], buf, BUFSIZ * sizeof(char));
                if (numRead == -1)
                {
                    printf("ошибка чтения\n");
                    exit(1);
                }
                printf("мать пишет - %s\n", buf);
                free(buf);                
            } while (numRead != 0);
            if (close(pipAnim[0]) == -1) {
                printf("ошибка закрытия чтение\n");
                exit(1);
            }
            exit(0);
            break;
        default:
            if (close(pipAnim[0]) == -1) {
                printf("ошибка закрытия чтения\n");
                exit(1);
            }
            char* buf = calloc(BUFSIZ, sizeof(char));
            while (buf[0] != ' ')
            {
               gets(buf);
                int numWrite = write(pipAnim[1], buf, BUFSIZ * sizeof(char));
                if (numWrite == -1)
                {
                    printf("ошибка записи\n");
                    exit(1);
                }
            }
            if (close(pipAnim[1]) == -1)
            {
                printf("ошибка закрытия записи\n");
                exit(1);
            }
            wait(NULL);
            free(buf);
            break;
    }
}
```