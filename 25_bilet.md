## Создание линейного односвязного списка из n элементов. Значение элемента генерируется случайным образом. Поместить в отдельные указатели адрес минимального и максимального элемента списка. Вывести список и значения минимального и максимального элемента через указатель (отдельно для Windows и Linux).


-----


Windows
```C
#include <stdio.h>
#include <malloc.h>
#include <time.h>

typedef struct {
	int value_in_list;
	struct Onelist_list* new_elem;
} Onelist_list;

Onelist_list* CreateList(int count_elem_in_list) {
	int min = 10;
	int max = 100;
	srand(time(NULL));
	Onelist_list* first_elem = (Onelist_list*)malloc(sizeof(Onelist_list));
	first_elem->value_in_list = min + rand() % (max - min);
	Onelist_list* post_elem, * new_elem;
	post_elem = first_elem;
	for (size_t i = 0; i < count_elem_in_list - 1; i++)
	{
		new_elem = (Onelist_list*)malloc(sizeof(Onelist_list));
		new_elem->value_in_list = min + rand() % (max - min);
		post_elem->new_elem = new_elem;
		post_elem = new_elem;
	}
	post_elem->new_elem = NULL;
	return first_elem;
}
Onelist_list* SearcMaxElemInList(Onelist_list* first_elem) {
	Onelist_list* max_list = (Onelist_list*)malloc(sizeof(Onelist_list));
	Onelist_list* temp_list = first_elem;
	int max_elem = 0;
	while (temp_list) {
		if (temp_list->value_in_list > max_elem)
		{
			max_elem = temp_list->value_in_list;
			max_list = temp_list;
		}
		temp_list = temp_list->new_elem;
	}
	return max_list;
}

Onelist_list* SearcMinElemInList(Onelist_list* first_elem) {
	Onelist_list* min_list = (Onelist_list*)malloc(sizeof(Onelist_list));
	Onelist_list* temp_list = first_elem;
	int min_elem = 100;
	while (temp_list) {
		if (temp_list->value_in_list < min_elem)
		{
			min_elem = temp_list->value_in_list;
			min_list = temp_list;
		}
		temp_list = temp_list->new_elem;
	}
	return min_list;
}

void PrintAllList(Onelist_list* list) {
	int index_in_list = 1;
	while (list) {
		printf("Элемент в листе %d - Значение %d\n", index_in_list, list->value_in_list);
		index_in_list++;
		list = list->new_elem;
	}
}
int main() {
	system("chcp 1251");
	Onelist_list* list = CreateList(15);
	Onelist_list* max_list = SearcMaxElemInList(list);
	Onelist_list* min_list = SearcMinElemInList(list);
	PrintAllList(list);
	printf("%d \n", max_list->value_in_list);
	printf("%d", min_list->value_in_list);
}
```
