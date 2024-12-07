## Программа для записи и считывание строки с использованием системного буфера.

```C
#include <Windows.h>
#include <stdio.h>
#include <stdlib.h>

int AddTextInClipboard(LPWSTR buffer) {
	DWORD len; //длина текста
	HANDLE hMen; //хранит область глобальной памяти
	len = wcslen(buffer) + 1; //узнаем длину широкой строки
	hMen = GlobalAlloc(GMEM_MOVEABLE, len * sizeof(LPWSTR)); //делаем глобальный аллок
	memcpy(GlobalLock(hMen), buffer, len * sizeof(LPWSTR)); //копируем память в хМен
	GlobalUnlock(hMen); //Открываем память для остального
	OpenClipboard(0); //Открываем буфер
	EmptyClipboard(); //Чистим буфер
	SetClipboardData(CF_UNICODETEXT, hMen); //Кладем в буфер
	CloseClipboard(); // Закрываем буфер
	return 0;
}
int GetTextInClipboard() {
	OpenClipboard(NULL); //Открываем буфер
	LPWSTR mess = (LPWSTR)GetClipboardData(CF_UNICODETEXT); //Получаем из буфера
	CloseClipboard(); //Закрываем
	MessageBox(NULL, mess, L"Буфер", MB_OK);
}
int main() {
	AddTextInClipboard(L"Тессссссст");
	GetTextInClipboard();
}
```
