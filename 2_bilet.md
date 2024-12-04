## Создание статической библиотеки в Linux. Для демонстрации работы написать две элементарные функции в библиотеке и скомпилировать библиотеку вместе с основной программой.

1. Создаем файл static.c 
2. Впишем в данный файл две функции
```C
#include <stdio.h>

extern void PrintHellow(char* name) {
	printf("Hellow, %s", name);
}

extern void Plus(int first, int second) {
	int result = first + second;
	printf("%d", result);
}
```
3. Теперь надо правильно скомпилировать файл библиотеки
```linux
Компилируем в объектный модуль
gcc -c static.c -o static.o
Создаем архив с этими модулями
ar rc libMY_STATIC.a static.o
Индексируем модули в библиотеке
ranlib libMY_STATIC.a
```
4. Создаем файл основной программы main.c
```C
#include <stdio.h>

int main() {
	PrintHellow("Petr");
	Plus(4, 5);
}
```
5. Правильно компилируем и запуска файл
```linux
gcc main.c libMY_STATIC.a -o main
Запускаем без gcc
./main
```
