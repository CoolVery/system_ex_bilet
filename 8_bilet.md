## Программа для преобразования числа в строку и склейки двух строк. Разработать без использования системных функций (отдельно для Windows и Linux)
Этот код не будет работать, если числа 3000.32 - он выведет 3.32, поэтому смотрим код ниже или рискуйте, что Мухин не попадет
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

Этот код проверяет отрицательное, если мы вписали просто 0, или 0.3 - крч много че
```C
char* concatStr(char* a, char* b) {
	int lenA = 0;
	int lenB = 0;


	for (size_t i = 0; a[i] != '\0'; i++)
	{
		lenA = i + 1;
	}
	for (size_t i = 0; b[i] != '\0'; i++)
	{
		lenB = i + 1;
	}
	char* result = calloc(lenA + lenB - 1, 1);

	for (size_t i = 0; i < lenA; i++)
	{
		result[i] = a[i];
	}

	for (size_t i = 0; i < lenB; i++)
	{
		result[lenA + i] = b[i];
	}
	result[lenA + lenB] = '\0';
	return result;
}
char* doubleToStr(double a) {
	int flagNegativ = 0;
	int flagPoint = 0;
	char* numstr = calloc(2, 1);
	char* buf = calloc(1, 1);
	if (a == 0)
	{
		numstr[0] = '0';
		numstr[1] = '\0';
		return numstr;
	}
	if (a < 0) {
		flagNegativ++;
		a *= -1;
		
	}

	int intA = (int)a;
	double drob = a - intA;
	numstr[0] = '\0';
	buf[1] = '\0';
	if (intA == 0) {
		numstr[0] = '0';
		numstr[1] = '\0';
	}
	while (intA != 0) {
		int left = intA % 10;
		intA /= 10;
		buf[0] = (char)left + '0';
		numstr = concatStr(buf, numstr);
	}
	
	
	int count = 0;
	while (drob != 0 && count < 5) {
		if (!flagPoint)
		{
			flagPoint++;
			buf[0] = ',';
			numstr = concatStr(numstr, buf);
		}
		drob = drob * 10;
		int left = (int)drob;
		buf[0] = (char)left + '0';
		numstr = concatStr(numstr, buf);
		count++;
		drob = drob - left;
	}

	

	if (flagNegativ)
	{
		int lenStr = 0;
		for (size_t i = 0; numstr[i] != '\0'; i++)
		{
			lenStr = i + 1;
		}
		char* resultNeg = calloc(lenStr + 2, 1);
		for (size_t i = 0; i < lenStr; i++)
		{
			resultNeg[i + 1] = numstr[i];
		}
		resultNeg[0] = '-';
		resultNeg[lenStr + 1] = '\0';
		return resultNeg;
	}
	return numstr;
}
```
