## Создание динамической библиотеки в Linux. Для демонстрации работы написать две элементарные функции в библиотеке и вызвать их из основной программы.

1. Создаем файл библиотеки static.c
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
2. Правильно компилируем
```C
gcc -fPIC static.c -shared -o libdynamic.so
```
3. Создаем файл main.c, где будем вызывать нашу библиотеку
```C
#include <dlfcn.h>
#include <stdio.h>

typedef void (*fundll)(char*);
typedef void (*fundllSecond)(int, int);

int main()
{
	void * libhandle = dlopen("./libdynamic.so",RTLD_LAZY);//подключаем библиотеку
	fundll myfundll = dlsym(libhandle,"PrintHellow");//импортируем функцию
	fundllSecond myPlus = dlsym(libhandle, "Plus");
	myfundll("Petr");
	myPlus(3, 2);
}
```
4. Правильно компилируем и вызываем
```linux
gcc -o main main.c -ldl
./main
```
