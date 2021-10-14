# Шаблоны

## Статический полиморфизм

```c++
//Версия для int
int Sum(int a, int b){
    return a + b;
}

//Версия для double
double Sum(double a, double b) {
    return a + b;
}
```

Компилятор сам выберет наиболее подходящую функцию. Такой механизм называется перегрузкой функций.

## Шаблоны

### typename

Шаблонная версия функции. Может принимать значения любого типа.

```c++
template <typename T>
T sum(T a, T b) {
    return a + b;
}
```

Когда компилятор встречает вызов функции с конкретным типом, например `double`, он генерирует реализацию функции
для `double`. Этот процесс называется инстанциированием шаблонов. Мы можем явно указать какие типы использовать для
инстанциирования:

```c++
sum(10, 2.0); //Не работает, т.к. не понятно как вывести тип
sum<double>(10, 2.0); //Будет инстанциирована версия для double
```

Можно использовать несколько типов в шаблонах:

```c++
template <typename T1, typename T2>
auto Sum(T1 a, T2 b) {
    return a + b;
}
```

Для нескольких шаблонов тоже можно явно указывать типы:

```c++
Sum<double, double>Sum(10, 2.0);//Можно указать все типы
Sum<double>Sum(10,2.0); //Можно указать только несколько первых, если остальные можно вывести.
```

Шаблонные функции и классы нужно описывать в той единице трансляции, где они созданы, т.е. нельзя указать сигнатуру
функции в h-файле, а определить в cpp-файле.

### decltype

`decltype` - оператор, который возвращает тип выражения указанного в скобках:

```c++
int a = 0, b = 0;
decltype(a + b) // == int
decltype(a + b) c; // == int c;
using Type = decltype(a + b); //Теперь Type == int
```

Выражение в скобках не будет вычисляться, и не будут вызываться функции, методы или операторы.

```c++
int f() {
    assert(false);
    return 1;
}

decltype(f()) c; //assert не сработает
```

### Специализация шаблонов

Можно явно указать реализацию для некоторых типов:

 ```c++
template<typename T1, typename T2>
auto Sum(T1 a, T2 b) {
    return a + b;
}

template<>
auto Sum<std::unordered_set<int>, std::unordered_set<int>>(std::unordered_set<int> a, std::unordered_set<int> b){
    return a.merge(b);
}
```

### Шаблонный класс

Шаблонный класс `!=` шаблонный метод:

```c++
//Шаблонный класс
template <typename T>
class C {
public:
    T field;
};


class C {
public:
//Шаблонный метод
template <typename T>
    T method();
};
```

Шаблонный метод не должен быть виртуальным.

Необходимо указывать типы для шаблонных классов в момент создания.

```c++
std::pair(1, 2);//Нельзя, хотя типы можно вывести из конструктора
```

Чтобы решить эту проблемы используют шаблонные функции вида `make_*`:

```c++
template <typename T1, typename T2>
std::pair<T1, T2> make_pair(T1 a, T2 b) {
    return std::pair<T1, T2>(a, b);
}
```

Можно указывать частичные специализации для классов:
```c++
template<typename T1>
class pair<T1, int> {
    //...
};

//Или даже так:

template<typename T1, typename T2>
class pair<T1*, T2*> {
    //...
};
```

### Variadic template
Можно передавать произвольное число параметров в шаблон.
```c++
template<typename... TArgs>
class Tuple;
```

Чтобы определить подобный класс, можно развернуть его как односвязанный список.

```c++
template <typename THead, typename... TTail>
class Tuple<THead, TTail...> : public Tuple<TTail...> {
    using TBase = tuple<TTail...>;
public:
    Tuple(THead head, TTail... tail) : TBase(tail...), value_(head) {
        
    }
    void Print() {
        std::cout << value_ << " ";
        TBase::Print();
    }
private:
    THead value_;
};

template <>
class Tuple<> {
protected:
    Print() {
        
    }
};
```

## Лямбды

Лямбда - это анонимная функция;

```c++
auto lambda = [](int x) { return x > 0; };
```

Лямбда - это объект, уникального анонимного типа.


```c++
auto lambda1 = [](int x) { return x > 0; };
auto lambda2 = [](int x) { return x > 0; };
```

`lambda1` и `lambda2` имеют разные типы.

Лямбды могут захватывать переменные из локального контекста.

```c++
int limit = 1;
int limit2 = 1;
int limit3 = 1;
auto lambda1 = [limit](int x) { return x > 0; }; //Захватить limit по значению
auto lambda2 = [&limit](int x) { return x > 0; }; // Захватить limit по ссылке.
auto lambda3 = [&](int x) { return x > 0; }; // Захватить весь локальный контекст по ссылке.
auto lambda4 = [=](int x) { return x > 0; }; // Захватить весь локальный контекст по значению.
auto lambda4 = [limit2, &limit3](int x) { return x > 0; }; // Захватить limit2 по значению и limit3 по ссылке
```

Напишем свою лямбду.

```c++
class Lambda {
public:
    Lambda(int& limit): limit(limit) {}
    
    bool operator()(int x) {
        return x > limit;
    }
private:
    int& limit;
};
```

Теперь мы можем использовать класс `Lambda` вместо обычных лямбд:

```c++
auto lambda1 = [&limit](int x) { return x > limit; };
auto lambda2 = Lambda(limit);
```

`operator()` у лямбд по умолчанию константный.
Можно создать неконстантную версию:

```c++
auto lambda [&limit](auto x) mutable { return x > limit; }; 
```

## P.S.
Вывод типов для шаблонов и вывод типов для `auto` - это один и тот же механизм. 
