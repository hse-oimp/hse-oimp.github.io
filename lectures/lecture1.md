# Лекция №1 Типы данных

## Система оценки

[см. gitlab](https://gitlab.com/levanovd/cpp-pilot-hse/-/blob/main/SCORE.md)

## Компиляция программы

```shell
g++ program.cpp -o program -Werror -Wall
```

* `g++` - компилятор, можно использовать `clang`
* `program.cpp` - путь до файла с исходным кодом
* `-o program` - название исполняемого файла
* `-Werror` - все `warnings` становятся ошибками
* `-Wall` - включить все `warnings`

Запускать программу надо так:
* Относительный путь: `./program`
* Абсолютный путь: `/home/user/path/to/file/program`
* Положить программу куда-либо в `PATH`, например `/usr/bin`, тогда запускать можно так: `program`

## Как делать нельзя

```c++
//#include <bits/stdc++.h> нельзя!
#include <iostream>
//using namespace std; нельзя!

int main () {
    // \n нельзя!
    std::cout << "Hello world!" << std::endl;
    //Забивать на return 0;  нельзя!
    return 0;
}
```

## Типы данных

Стандарт гарантирует что

```c++
sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof (long) <= sizeof(long long)
```

А также

```c++
sizeof(unsigned char) <= sizeof(unsigned short) <= sizeof(unsigned int) <= sizeof (unsigned long) <= sizeof(unsigned long long)
```

Если мы хотим использовать типы фиксированного размера, надо использовать

```c++
int8_t i8;
int16_t i16;
int32_t i32;
int64_t i64;

uint8_t ui8;
uint16_t ui16;
uint32_t ui32;
uint64_t ui64;
```
