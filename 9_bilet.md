## Программа для преобразования строки в число. Строка хранится в виде указателя LPWSTR. Реализовать без использования системных функций

```C
#include<malloc.h>
#include<stdio.h>
#include<stdlib.h>
#include<Windows.h>

double convert_str_to_double(LPWSTR str) {
	//Итоговое число
	double result = 0.0;
	//Флаг того, что число отрицательное
	int flag_negative = 0;
	//Длина строки
	int len_str = 0;
	//Индекс для циклов
	int index_while = 0;
	//Проверка на отрицательное число
	if (str[0] == L'-') {
		flag_negative = 1;
	}
	//Получение длины строки
	while (str[index_while] != L'\0') {
		len_str++;
		index_while++;
	}
	index_while = 0;
	//счетчик минусов
	int count_min = 0;
	while (str[index_while] != L'\0') {
		if (str[index_while] == L'-')
		{
			count_min++;
		}
		index_while++;
	}
	//Если в строке больше двух, то конец
	if (count_min > 1) return -22;
	index_while = 0;
	//Счетчик точек
	int count_point = 0;
	//Если в строке больше 1 точки, то конец
	while (str[index_while] != L'\0') {
		if (str[index_while] == L'.')
		{
			count_point++;
		}
		index_while++;
	}
	if (count_point > 1) return -23;
	index_while = 0;
	//Проверка, что в строке только цифры кроме точки и минуса
	while (str[index_while] != L'\0') {
		if (str[index_while] >= L'0' && str[index_while] <= L'9') {
			index_while++;
		}

		else if (str[index_while] == L'.' || str[index_while] == L'-') {
			index_while++;
		}
		else return -24;
	}
	//Запоминаем индекс точки
	int index_point = 0;
	for (size_t i = 0 + flag_negative; i < len_str; i++)
	{
		//Если не точка
		if (str[i] != L'.') {
			//Умножаем на 10 и прибавляем число
			result *= 10;
			result += (double)(str[i] - L'0');
		}
		else {
			//Запомнили точку
			index_point = i;
		}
	}
	int delitel = 1;
	//Если индекс точки больше 0, т.е. . не первый символ и нет у нас -.3232
	if (index_point > 1) {
		//Узнаем сколько знаков после точки и надо вычесть 1
		for (size_t i = 0; i < (len_str - index_point) - 1; i++)
		{
			delitel *= 10;
		}
	}
	result = result / delitel;
	if (flag_negative) return result * -1;
	else return result;
}


int main() {
	printf("%f", convert_str_to_double(L"-3.2777"));
}
```
