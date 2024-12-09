## Создание и настройка проекта WinAPI. Вывод сообщения в messagebox. Текст сообщения считывается из текстового файла в кодировке Юникод

Уточнение - файлик текстовый создаcтся в кодировке UTF-8, нам нужно поменять на UTF-16 (через Сохранить Как)

```C
#include <Windows.h>
#include <stdlib.h>
#include <stdio.h>

int main() {
	wchar_t buffer[200];
	
	DWORD count_read = 0;
	HANDLE fileH;
	fileH = CreateFileW(
		L"20_bilet.txt",
		GENERIC_READ,
		FILE_SHARE_READ,
		NULL,
		OPEN_ALWAYS,
		FILE_ATTRIBUTE_NORMAL,
		0);
	if (fileH != INVALID_HANDLE_VALUE) {
		int readFile = ReadFile(
			fileH,
			buffer,
			200,
			&count_read,
			NULL);
		int numChars = count_read / sizeof(wchar_t);

		buffer[numChars] = L'\0';
		if (readFile) {
			MessageBox(NULL, buffer, L"Содержимое файла", NULL);
		}
	}
}
```
