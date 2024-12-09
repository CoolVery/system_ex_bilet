## Программа для считывания числового значения из файла (с сохранением его в числовом формате). Исходный файл является бинарным (отдельно для Windows и Linux)

---
Работает там и там
```C
#include <stdio.h>
#include <malloc.h>
#define MAX_BUFFER 50
#define PATH "textBin.txt"
int main(void)
{
	FILE* myfile = fopen(PATH, "wb");
	int numberToWrite = 1555;
	fwrite(&numberToWrite, sizeof(int), 1, myfile);
	fclose(myfile);
	
	myfile = fopen(PATH, "rb");
	int numberToRead = 0;
	fread(&numberToRead, sizeof(int), 12, myfile);
	fclose(myfile);
	printf("%d", numberToRead);
	return 0;
}
```
