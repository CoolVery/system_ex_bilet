## Программа для считывания и записи строкового значения из текстового файла с последующей записью его в другой текстовый файл. Для работы с файлами используются функции Linux.

```C
#include <stdio.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>

int main() {
	int file = open("coef.txt", O_RDWR | O_CREAT, S_IRWXU);
	char* buffer = (char*)calloc(100, sizeof(char));
	int readIs = read(file, buffer, 100);
	printf("%d", readIs);
	printf("Код ошибки: %d\n", errno);
	perror("read");
	if (readIs != -1) {
		buffer[strlen(buffer)] = '\0';
		int fileW = open("coefAns.txt", O_WRONLY | O_CREAT | O_TRUNC, S_IRWXU);
		int writeIs = write(fileW, buffer, strlen(buffer));
		if (writeIs != -1) {
			printf("%s", buffer);
		}
		else {
			printf("ошибка");
		}
	}
}
```