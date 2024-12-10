![image](https://github.com/user-attachments/assets/125d6737-978c-4c66-8bf7-a007e880c9e0)## Создание консольной утилиты, которая печатает список переданных ей аргументов из командной строки. При демонстрации работы программы рассмотреть не менее трех способов передачи аргументов в запускаемый процесс


-----


Windows
код
```C
#define _CRT_SECURE_NO_WARNINGS
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int main(int args, char* argv[]) {
	printf("%s", argv[0]);
	printf("%s", argv[1]);
	printf("%s", argv[2]);
	int f = 0;
	scanf("%d", &f);
}
```
Способы передачи
1. Ярлык - в папке проекта/x64/Debug находим .exe файла и создаем от него ярлык
![image](https://github.com/user-attachments/assets/aa68b6e4-2691-41dd-9c73-d8d9a4f9bb27)
В Свойствах ярылка после пути проги пишем любые буквы
![image](https://github.com/user-attachments/assets/5d83783a-a8ed-4def-a43d-3bf0c5a51655)
Запускаем Ярлык

2. Cmd
Через cmd по команде cd <Путь к папке, где лежит .exe> заходим в нее и через команду start <имя.exe> <Всякие буквы через пробел> запускаем
```
C:\Users>cd C:\Users\source\repos\21_bilet\x64\Debug

C:\Users\source\repos\21_bilet\x64\Debug>start secondProc.exe

C:\Users\source\repos\21_bilet\x64\Debug>start secondProc.exe ff gg

```
3. Visual Studio
![image](https://github.com/user-attachments/assets/2030e5c9-b135-47f3-8865-f7fa334fad72)
![image](https://github.com/user-attachments/assets/27611fad-ef14-4a25-b03a-237d218ece67)
Запускаем
4. Через создание CreateProc, где один из параметров - это строка аргумента параметров, надо помнить код и создать другой проект

Linux 

