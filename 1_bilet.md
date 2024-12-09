## Создание и отладка библиотеки DLL. Для демонстрации работы написать две элементарные функции в библиотеке и вызвать их из основной программы.

1. Создаем общий проект "Пустой проект c++" - UserExploingDLL 
2. В решении добавляем такой же пустой проект - DLL
3. Настраиваем UserExploingDLL (В нем уже есть файл main.cpp) <\ br>
3.1 C/C++ - Все параметры - Компилировать как - код С++
3.2. С/C++ - Дополнительно - Комплиировать как - Код C++
3.3. Компоновщик - Все параметры - Подсистема - Консоль
4. Настраиваем DLL (В нем уже есть файл main.c)
4.1 C/C++ - Все параметры - Компилировать как - код С++
4.2. С/C++ - Дополнительно - Комплиировать как - Код C++
4.3. Компоновщик - Все параметры - Подсистема - Windows
4.4. Общие - Тип конфигурации - .dll
Коды:
В UserExploingDLL
```C
#define _CRT_SECURE_NO_WARNINGS
#include <windows.h>
#include <stdio.h>
//Путь находится в Debug
#define PATH L"DLL.dll"

//Тут храним наши функции
typedef int(_cdecl* MyFunction)(const int, const int);
typedef wchar_t*(_cdecl* MyFunctionPrint)(const char*);

int main() {
	//Получаем библиотеку, а потом функции
	HINSTANCE dll = LoadLibrary(PATH);
	MyFunction Fun = (MyFunction)GetProcAddress(dll, "PowMy");
	MyFunctionPrint pri = (MyFunctionPrint)GetProcAddress(dll, "StrPrint");
	printf("%d", Fun(2, 3));
	printf("%s", pri("ig"));
}
```
В DLL
```C
#define _CRT_SECURE_NO_WARNINGS
#include <windows.h>
#include <stdio.h>
#include <malloc.h>
#include <string.h>

BOOL WINAPI DllMain(HINSTANCE hlnstDll, DWORD dwReason, LPVOID IpReserved)
{
	switch (dwReason)
	{
	case DLL_PROCESS_ATTACH:
	case DLL_THREAD_ATTACH:
	case DLL_THREAD_DETACH:
	case DLL_PROCESS_DETACH:
		break;
	}
		return TRUE;
}
	extern "C" __declspec(dllimport) int PowMy(const int num, const int num_pow);
	int PowMy(const int num, const int num_pow) {
		int result = num;
		for (int i = 1; i < num_pow; i++)
		{
			result *= num;
		}
		return result;
	}
	extern "C" __declspec(dllimport) char* StrPrint(const char* name);
	char* StrPrint(const char* name) {
		char* result = (char*)malloc(100);
		snprintf(result, 100, "Hellow, %s", name);
		return result;
	}
```
