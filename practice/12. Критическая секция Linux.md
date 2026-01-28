## Создание программы с критической секцией в ОС Linux. Программа должна содержать минимум два потока. Использование критической секции в функции потока должно быть обосновано.

Шаг 1. В файле main.c на ОС Linux написать следующий код.

``` C

int counter = 0;
pthread_mutex_t mutex;

void* pthreadFun(){
	for(int i = 0; i < 10000000; i++){
		pthread_mutex_lock(&mutex);
		counter++;
		pthread_mutex_unlock(&mutex);
	}
	pthread_exit(0);
}

int main(){
	pthread_mutex_init(&mutex, 0);
	
	pthread_t t1, t2, t3;
	pthread_create(&t1, 0, pthreadFun, 0);
	pthread_create(&t2, 0, pthreadFun, 0);
	pthread_create(&t3, 0, pthreadFun, 0);
	
	pthread_join(t1, 0);
	pthread_join(t2, 0);
	pthread_join(t3, 0);
	
	printf("%d\n", counter);
	
	pthread_mutex_destroy(&mutex);
	return 0;
}
```

Шаг 2. Ввести следующие команды в терминале проекта.

``` 
gcc main.c
./a.out
```