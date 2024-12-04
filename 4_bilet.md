## Программа для считывания и записи строкового значения из текстового файла с последующей записью его в другой текстовый файл. Для работы с файлами используются функции WinAPI.

```C
#include <stdio.h>
#include <stdlib.h>
#include <malloc.h>
#include <Windows.h>

char* readFile(HANDLE fileHandle) {
	DWORD count = 0; //счет прочитанных символов
	char* buffer = calloc(100, sizeof(char)); //хранение прочитанного
	int read = ReadFile(
		fileHandle, //хендл
		buffer, //куда кладем
		100, //максимум
		&count, //скок в итоге прочитали
		NULL //просто нул
	);
	if (!read) {
		return NULL; //если косяк в чтении
	}
	else {
		buffer = realloc(buffer, sizeof(char) * count); //делаем реалок на количество прочитанного
		buffer[count] = '\0'; 
		return buffer;
	}
}

int writeInFile(char* buffer) {
	DWORD count = 0;
	HANDLE FileHandle;
	FileHandle = CreateFileA(
		"answer.txt",
		GENERIC_WRITE, //для записи
		FILE_SHARE_WRITE | FILE_SHARE_READ, //и читать и записать можем
		NULL, //безопасность
		OPEN_ALWAYS, //открывает, если есть, иначе создает
		FILE_ATTRIBUTE_NORMAL, //Дефолт атрибуты
		0 //шаблон файла - нет крч
	);
	if (FileHandle == INVALID_HANDLE_VALUE) { //Проверка на плохой хендл
		return 1;
	}
	int isWrite = WriteFile(
		FileHandle, //Хендл
		buffer, //Че записать
		strlen(buffer), //Скок писать
		&count, //Скок по итогу записали
		NULL //Просто нул
	);
	if (!isWrite) {
		return 1;
	}
	else {
		return 0;
	}
}

int main() {
	system("chcp 1251>nul");
	HANDLE FileHandle;
	FileHandle = CreateFileA(
		"file1.txt",
		GENERIC_READ, //Для чтения
		FILE_SHARE_READ, //Другие процессы могут читьать
		NULL, //безопасность
		OPEN_ALWAYS, //открывает, если есть, иначе создает
		FILE_ATTRIBUTE_NORMAL, //Дефолт атрибуты
		0 //шаблон файла - нет крч
	);
	if (FileHandle == INVALID_HANDLE_VALUE) { //Проверка на адекватный хендл
		printf("%lu", GetLastError());
		return 0;
	}
	char* buffer = readFile(FileHandle);
	printf("%s", buffer);
	writeInFile(buffer);
}
```
