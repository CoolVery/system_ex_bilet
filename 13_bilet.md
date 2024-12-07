## Программа для считывания нескольких числовых значений из строки и из записи в числовой массив (разделитель - пробел). Количество значений в строке заранее неизвестно. (отдельно для Windows и Linux)

Код работает как на винде так и на линукс
```C
#include <stdio.h>
#include <stdlib.h>
#include <malloc.h>
#include <ctype.h>

int main() {
    //Для линукса надо убрать
    system("chcp 1251");
    //Вводимая строка
    char inputString[200];
    printf("Введите все числа\n");
    gets(&inputString);
    //Строка для обработки
    char buffer[200];
    //Просто надо для функции преобразования строки во флоат
    char* endptr;
    //Итоговый массив чисел
    float* result_array = NULL;
    //Индекс во вводимой строке
    int index_str = 0;
    //Выход из цикла обработки всей строки
    int exit = 1;
    //Индекс для строки обработки
    int index_buf = 0;
    //Размер итогового массива
    int size_result_array = 0;
    //Проверка, что вся строка содержит значения ТОЛЬКО цифры, -, .
    while (inputString[index_str] != '\0') {
        if (isdigit(inputString[index_str]))
        {
            index_str++;
            continue;
        }
        else if (inputString[index_str] == ' ' || inputString[index_str] == '-') {
            index_str++;
            continue;
        }
        else {
            return 100;
        }
        
    }
    index_str = 0;
    //Обработка всей строки
    while (1) {
        //Если не пробел и не конец, то просто кладем символ в буфер
        if (inputString[index_str] != ' ' && inputString[index_str] != '\0') {
            buffer[index_buf] = inputString[index_str];
            index_buf++;
            index_str++;
        }
        else {
            //Иначе мы увеличиваем размер массива, через реалок делаем это, кладем туда float от функции
            //Также проверяем, что если конец строки, значит цикл надо убить
            size_result_array++;
            result_array = (float*)realloc(result_array, sizeof(float) * size_result_array);
            result_array[size_result_array - 1] = strtod(buffer, &endptr);
            if (inputString[index_str] == '\0') {
               break;
            }
            //Очищаем временную строку и индексы
            memset(buffer, 0, 200);
            index_buf = 0;
            index_str++;
        }
    }
    // Печатаем массив
    for (size_t i = 0; i < size_result_array; i++)
    {
        printf("\n%f\n", result_array[i]);
    }
}
```
