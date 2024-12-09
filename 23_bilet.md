## Программа с импортом функции из файла DLL. Функция принимает  структуру, содержащую координаты двух точек, а возвращает длину отрезка, который образуется этими точками 


-----


1. Создаем общий проект "Пустой проект c++" - UserExploingDLL 
2. В решении добавляем такой же пустой проект - DLL
3. Настраиваем UserExploingDLL (В нем уже есть файл main.cpp) <br /> <br />
3.1 C/C++ - Все параметры - Компилировать как - код С++ <br /> <br />
3.2. С/C++ - Дополнительно - Комплиировать как - Код C++ <br /> <br />
3.3. Компоновщик - Все параметры - Подсистема - Консоль <br /> <br />
4. Настраиваем DLL (В нем уже есть файл main.c) <br /> <br />
4.1 C/C++ - Все параметры - Компилировать как - код С++ <br /> <br />
4.2. С/C++ - Дополнительно - Комплиировать как - Код C++ <br /> <br />
4.3. Компоновщик - Все параметры - Подсистема - Windows <br /> <br />
4.4. Общие - Тип конфигурации - .dll <br /> <br />
Коды:
В UserExploingDLL
```C
#define _CRT_SECURE_NO_WARNINGS
#include <windows.h>
#include <stdio.h>
//Путь находится в Debug
#define PATH L"DLL_system_ex.dll"

typedef struct {
	float firstX;
	float firstY;
	float secondX;
	float secondY;

} TwoPoint;

//Тут храним наши функции
typedef float(_cdecl* MyFunction)(const TwoPoint*);

int main() {
	//Получаем библиотеку, а потом функции
	HINSTANCE dll = LoadLibrary(PATH);
	MyFunction Fun = (MyFunction)GetProcAddress(dll, "CountWidth");
	TwoPoint points = {1, 2 ,2, 2};
	printf("%f", Fun(&points));
}
```

В DLL

```C
#include <stdio.h>
#include <malloc.h>
#include <string.h>
#include <math.h>

BOOL WINAPI DllMain(HINSTANCE hlnstDll, DWORD dwReason, LPVOID IpReserved)
{
	return TRUE;
}
typedef struct {
	float firstX;
	float firstY;
	float secondX;
	float secondY;

} TwoPoint;

extern "C" __declspec(dllimport) float CountWidth(const TwoPoint* points);
float CountWidth(const TwoPoint* points) {
	float result = sqrt(pow(points->secondX - points->firstX, 2) + pow(points->secondY - points->firstY, 2));
	return result;
}
```
