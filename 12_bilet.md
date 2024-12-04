## Создание программы с критической секцией в ОС Linux. Программа должна содержать минимум два потока. Использование критической секции в функции потока должно быть обосновано.

```C
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <unistd.h>

// Общий ресурс (счетчик)
int counter = 0;

// Мьютекс для защиты критической секции
pthread_mutex_t mutex;

// Функция потока
void *increment_counter(void *arg) {
	int iterations = *(int *)arg; //Получаем число итераций из аргумента
	for (int i = 0; i < iterations; i++) {
		// Блокируем мьютекс перед доступом к общему ресурсу	
		pthread_mutex_lock(&mutex);
		// Критическая секция: доступ к общему ресурсу
		counter++;
		// Разблокируем мьютекс после завершения доступа к общему ресурсу
		pthread_mutex_unlock(&mutex);
	}
	pthread_exit(NULL);
}

int main() {
// Инициализируем мьютекс
pthread_mutex_init(&mutex, NULL);

// Создаем два потока
pthread_t thread1, thread2;
int iterations1 = 10000;
int iterations2 = 15000;
// Запускаем потоки
pthread_create(&thread1, NULL, increment_counter, &iterations1);
pthread_create(&thread2, NULL, increment_counter, &iterations2);

// Ждем завершения потоков
pthread_join(thread1, NULL);
pthread_join(thread2, NULL);

// Выводим окончательное значение счетчика
printf("Окончательное значение счетчика: %d\n", counter);

// Уничтожаем мьютекс
pthread_mutex_destroy(&mutex);
return 0;
}
```