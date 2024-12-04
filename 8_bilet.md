## Программа для преобразования числа в строку и склейки двух строк. Разработать без использования системных функций (отдельно для Windows и Linux)
```C
#include<malloc.h>
#include<stdio.h>
#include<stdlib.h>

char* concat(char* first, char* second) {
	char* result = (char*)calloc(100, 1);
	int len_a = 0;
	int index_a = 0;
	int len_b = 0;
	int index_b = 0;
	int index_rex = 0;
	while (first[index_a] != '\0') {
		len_a++;
		index_a++;

	}
	while (second[index_b] != '\0') {
		len_b++;
		index_b++;
	}
	for (size_t i = 0; i < len_a; i++)
	{
		result[index_rex] = first[i];
		index_rex++;
	}
	for (size_t i = 0; i < len_b; i++)
	{
		result[index_rex] = second[i];
		index_rex++;
	}
	result[index_rex] = '\0';
	return result;
}

char* doubleToStr(double a) {
	char* result = (char*)calloc(100, 1);
	//Длина, место точки, отрицательное число
	int len = 1, comma_placed = 0, negative = 0;
	//если отрицательное
	if (a < 0) {
		//умножаем число, чтобы убрать минус
		a *= -1;
		//ставим флаг
		negative = 1;
		//первый символ ставим -
		result[0] = '-';
	}
	//Здесь цикл, который запуститься тогда, когда число больше 10, он будет увеличивать на десятки и делить число на 10
	for (double b = a; b >= 10; b /= 10) {
		len *= 10;
	}
	//ставим число
	double b = a;
	for (int i = 0; 1; i++) {
		//В позицию строки прибавляем i и все флаги = Символу сумме интовому деление b и double длниа и '0'
		result[i + comma_placed + negative] = (char)((int)(b / (double)len) + '0');
		//Далее проверка, что если b минус интовое b == 0 и просто интовое б == 0, значит все
		if (b - (int)b == 0 && (int)b == 0) break;
		//Из б вычитает интовое произведение деления б и доубле длине и длины
		b -= (int)(b / (double)len) * len;
		//Если длина больше 1, то делим на 10 ее
		if (len > 1) len /= 10;
		//Если интовое б равно 0
		if ((int)b == 0) {
			//Умножаем на 10, чтобы число после точки перешло вперед
			b *= 10;
			//проверяем на флаг точки
			if (!comma_placed) {
				//ставим точку фалг
				comma_placed = 1;
				//ставим в строку точку
				result[i + 1 + negative] = '.';
			}
		}
	}

	return result;
}


int main() {
	printf("%s", doubleToStr(55));
	printf("%s", concat("RE32dsds", "ew"));
}
```
