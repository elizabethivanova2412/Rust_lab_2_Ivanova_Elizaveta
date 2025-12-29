# Лабораторная работа №2 – Ссылки, Ownership. Impl. Traits. Generics

## Тема лабораторной работы
Изучение ссылок, владения (ownership), реализации методов (impl), трейтов (traits) и обобщенных типов (generics) в языке Rust.

## Задача 1. Обобщенная структура Pair<T>

**Постановка задачи:**
Создайте структуру Pair<T>, которая хранит два значения одного типа T. Реализуйте методы для получения и изменения значений пары. Убедитесь, что использование ссылок и копирование данных работает корректно.

**Математическая модель:**
Pair<T> = (T, T) - упорядоченная пара элементов одного типа

**Список идентификаторов:**
| Имя переменной | Тип данных | Смысловое обозначение |
|----------------|------------|-----------------------|
| pair           | Pair<T>    | Пара значений         |

**Код программы:**
```rust
#[derive(Debug, Clone)]
struct Pair<T> {
    a: T,
    b: T,
}

impl<T> Pair<T> {
    fn create(a: T, b: T) -> Self {
        Pair { a, b }
    }
    
    fn get_a(&self) -> &T {
        &self.a
    }
    
    fn get_b(&self) -> &T {
        &self.b
    }
    
    fn update_a(&mut self, val: T) {
        self.a = val;
    }
    
    fn update_b(&mut self, val: T) {
        self.b = val;
    }
    
    fn get_values(&self) -> (&T, &T) {
        (&self.a, &self.b)
    }
}

fn print_pair<T: std::fmt::Debug>(pair: &Pair<T>) {
    println!("Пара (через ссылку): {:?}", pair);
}

fn main() {
    let mut num_pair = Pair::create(5, 8);
    println!("Исходная пара: {:?}", num_pair);
    
    let (ref_a, ref_b) = num_pair.get_values();
    println!("Значения через ссылки: a={:?}, b={:?}", ref_a, ref_b);
    
    num_pair.update_a(12);
    num_pair.update_b(15);
    println!("После изменения: {:?}", num_pair);
    
    let pair_copy = num_pair.clone();
    println!("Копия пары: {:?}", pair_copy);
    
    print_pair(&num_pair);
    
    let word_pair = Pair::create("да", "нет");
    println!("\nПара слов: {:?}", word_pair);
    
    println!("Первое слово: {}", word_pair.get_a());
    println!("Второе слово: {}", word_pair.get_b());
    
    let word_ref = &word_pair;
    println!("Через ссылку: {:?}", word_ref);
    
    let float_pair = Pair::create(3.14, 2.71);
    println!("Пара чисел с плавающей точкой: {:?}", float_pair);
}
```

**Результат выполнения**

![Решение задачи 1](https://raw.githubusercontent.com/elizabethivanova2412/Rust_lab_2_Ivanova_Elizaveta/main/задача1.png)
```

```

## Задача 2. Трейт PrintInfo для структуры Person

**Постановка задачи:**
Определите трейт PrintInfo, который содержит метод print_info(&self). Реализуйте этот трейт для структуры Person, чтобы можно было вызвать метод print_info на объекте Person.

**Математическая модель:**
Не требуется.

**Список идентификаторов:**
| Имя переменной | Тип данных | Смысловое обозначение |
|----------------|------------|-----------------------|
| human          | Person     | Человек               |

**Код программы:**
```rust
trait PrintInfo {
    fn show(&self);
}

struct Person {
    name: String,
    years: u8,
}

impl Person {
    fn make(name: &str, years: u8) -> Self {
        Person {
            name: name.to_string(),
            years,
        }
    }
}

impl PrintInfo for Person {
    fn show(&self) {
        println!("{} - {} лет", self.name, self.years);
    }
}

fn main() {
    let person1 = Person::make("Олег", 28);
    let person2 = Person::make("Мария", 24);
    
    person1.show();
    person2.show();
}
```

**Результаты выполненной работы:**
```
Олег - 28 лет
Мария - 24 лет
```

## Задача 3. Обработка списка задач (Todo List)

**Постановка задачи:**
Разработайте программу для обработки списка задач (Todo List). Создайте структуру Task с полями id, title и completed, а также методы для добавления, удаления и изменения статуса задачи. Реализуйте вывод информации о всех задачах.

**Математическая модель:**
TodoList = {Task₁, Task₂, ..., Taskₙ}, где Taskᵢ = (idᵢ, titleᵢ, completedᵢ)

**Список идентификаторов:**
| Имя переменной | Тип данных | Смысловое обозначение |
|----------------|------------|-----------------------|
| job            | Task       | Задача                |
| list           | TaskList   | Список задач          |

**Код программы:**
```rust
struct Task {
    id: u32,
    name: String,
    done: bool,
}

impl Task {
    fn build(id: u32, name: &str) -> Self {
        Task {
            id,
            name: name.to_string(),
            done: false,
        }
    }
    
    fn mark_done(&mut self) {
        self.done = true;
    }
    
    fn print(&self) {
        let sign = if self.done { "+" } else { "-" };
        println!("{} Задача {}: {}", sign, self.id, self.name);
    }
}

struct TaskList {
    jobs: Vec<Task>,
}

impl TaskList {
    fn new() -> Self {
        TaskList { jobs: Vec::new() }
    }
    
    fn add(&mut self, name: &str) {
        let id = (self.jobs.len() + 1) as u32;
        self.jobs.push(Task::build(id, name));
    }
    
    fn finish(&mut self, id: u32) {
        if let Some(job) = self.jobs.iter_mut().find(|t| t.id == id) {
            job.mark_done();
        }
    }
    
    fn display(&self) {
        for job in &self.jobs {
            job.print();
        }
    }
}

fn main() {
    let mut list = TaskList::new();
    
    list.add("Купить хлеб");
    list.add("Позвонить");
    list.add("Отправить письмо");
    
    println!("Список задач:");
    list.display();
    
    list.finish(2);
    
    println!("\nПосле выполнения:");
    list.display();
}
```

**Результаты выполненной работы:**
```
Список задач:
- Задача 1: Купить хлеб
- Задача 2: Позвонить
- Задача 3: Отправить письмо

После выполнения:
- Задача 1: Купить хлеб
+ Задача 2: Позвонить
- Задача 3: Отправить письмо
```

## Задача 4. Поиск уникальных элементов

**Постановка задачи:**
Напишите функцию find_unique_elements, которая принимает вектор элементов типа T и возвращает вектор уникальных элементов. Определите трейт Unique с методом is_unique, который проверяет, является ли элемент уникальным.

**Математическая модель:**
unique(S) = {x ∈ S | count(x, S) = 1}, где S - исходное множество

**Список идентификаторов:**
| Имя переменной | Тип данных | Смысловое обозначение |
|----------------|------------|-----------------------|
| items          | Vec<T>     | Элементы              |

**Код программы:**
```rust
trait Unique {
    fn check(&self, list: &[Self]) -> bool where Self: Sized + PartialEq;
}

impl<T: PartialEq> Unique for T {
    fn check(&self, list: &[Self]) -> bool {
        list.iter().filter(|&x| x == self).count() == 1
    }
}

fn find_unique<T: PartialEq + Clone>(items: Vec<T>) -> Vec<T> {
    let mut result = Vec::new();
    
    for i in 0..items.len() {
        let mut found = false;
        
        for j in 0..items.len() {
            if i != j && items[i] == items[j] {
                found = true;
                break;
            }
        }
        
        if !found {
            result.push(items[i].clone());
        }
    }
    
    result
}

fn main() {
    let data = vec![3, 7, 3, 8, 7, 9];
    println!("Данные: {:?}", data);
    
    let unique = find_unique(data.clone());
    println!("Уникальные: {:?}", unique);
    
    for val in &data {
        println!("{} уникален? {}", val, val.check(&data));
    }
}
```

**Результаты выполненной работы:**
```
Данные: [3, 7, 3, 8, 7, 9]
Уникальные: [8, 9]
3 уникален? false
7 уникален? false
3 уникален? false
8 уникален? true
7 уникален? false
9 уникален? true
```

## Задача 5. Суммирование коллекции элементов

**Постановка задачи:**
Напишите функцию sum_collection, которая принимает коллекцию элементов типа T и возвращает их сумму. Ограничьте тип T требованием наличия метода addition() в некотором трейте Addable для поддержки суммирования. Просуммируйте затем любую коллекцию структур вида:

struct Number {
    value: u32,
}

**Математическая модель:**
sum(S) = Σᵢ sᵢ, где S = {s₁, s₂, ..., sₙ}

**Список идентификаторов:**
| Имя переменной | Тип данных | Смысловое обозначение |
|----------------|------------|-----------------------|
| nums           | Vec<Value> | Числа                |

**Код программы:**
```rust
trait Addable {
    fn add(&self, other: &Self) -> Self;
}

#[derive(Debug)]
struct Value {
    num: u32,
}

impl Value {
    fn new(num: u32) -> Self {
        Value { num }
    }
}

impl Addable for Value {
    fn add(&self, other: &Self) -> Self {
        Value {
            num: self.num + other.num,
        }
    }
}

fn total<T: Addable + Clone + Default>(items: &[T]) -> T {
    let mut sum = T::default();
    for item in items {
        sum = sum.add(item);
    }
    sum
}

impl Default for Value {
    fn default() -> Self {
        Value { num: 0 }
    }
}

fn main() {
    let values = vec![
        Value::new(5),
        Value::new(10),
        Value::new(15),
    ];
    
    let result = total(&values);
    println!("Сумма: {:?}", result);
}
```

**Результаты выполненной работы:**
```
Сумма: Value { num: 30 }
```

## Задача 6. Обобщенная структура Collection<T>

**Постановка задачи:**
Создайте обобщенную структуру Collection<T>, которая содержит вектор элементов типа T. Реализуйте методы add_element(), get_element() и remove_element(), ограничив тип T traitом Clone.

**Математическая модель:**
Collection<T> = {t₁, t₂, ..., tₙ}, где tᵢ ∈ T

**Список идентификаторов:**
| Имя переменной | Тип данных     | Смысловое обозначение |
|----------------|----------------|-----------------------|
| box            | Collection<T>  | Контейнер             |

**Код программы:**
```rust
struct Collection<T: Clone> {
    data: Vec<T>,
}

impl<T: Clone> Collection<T> {
    fn new() -> Self {
        Collection { data: Vec::new() }
    }
    
    fn put(&mut self, item: T) {
        self.data.push(item);
    }
    
    fn take(&self, pos: usize) -> Option<&T> {
        self.data.get(pos)
    }
    
    fn drop(&mut self, pos: usize) -> Option<T> {
        if pos < self.data.len() {
            Some(self.data.remove(pos))
        } else {
            None
        }
    }
    
    fn count(&self) -> usize {
        self.data.len()
    }
    
    fn is_empty(&self) -> bool {
        self.data.is_empty()
    }
}

fn main() {
    let mut box1 = Collection::new();
    
    box1.put("первый");
    box1.put("второй");
    box1.put("третий");
    
    println!("Количество элементов: {}", box1.count());
    println!("Пустая? {}", box1.is_empty());
    
    println!("\nПолучаем элементы:");
    for i in 0..box1.count() {
        if let Some(item) = box1.take(i) {
            println!("[{}] {}", i, item);
        }
    }
    
    println!("\nУдаляем элемент 1:");
    if let Some(removed) = box1.drop(1) {
        println!("Удален: {}", removed);
    }
    
    println!("Теперь количество: {}", box1.count());
    
    // Проверка с числами
    let mut nums = Collection::new();
    nums.put(10);
    nums.put(20);
    nums.put(30);
    
    println!("\nЧисловая коллекция:");
    for i in 0..nums.count() {
        if let Some(num) = nums.take(i) {
            println!("Число {}: {}", i, num);
        }
    }
}
```

**Результаты выполненной работы:**
```
Содержимое:
[0] "первый"
[1] "второй"
[2] "третий"

Удаляем элемент 1:
[0] "первый"
[1] "третий"

Элемент 0: первый
```

## Задача 7. Трейт Summable для структуры Pair<T>

**Постановка задачи:**
Создайте трейт Summable, который требует реализации метода sum(&self) -> u32 для типов, поддерживающих сложение. Реализуйте этот трейт для структуры Pair<T>, которая содержит два поля типа T (сумма чисел в этой паре). Добавьте ограничение на тип T для поддержки сложения.

**Математическая модель:**
sum(Pair(a, b)) = a + b

**Список идентификаторов:**
| Имя переменной | Тип данных | Смысловое обозначение |
|----------------|------------|-----------------------|
| duo            | Pair<T>    | Двойка значений       |

**Код программы:**
```rust
trait Summable {
    fn total(&self) -> u32;
}

#[derive(Debug)]
struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn make(x: T, y: T) -> Self {
        Pair { x, y }
    }
}

impl<T> Summable for Pair<T>
where
    T: std::ops::Add<Output = T> + Into<u32> + Copy,
{
    fn total(&self) -> u32 {
        (self.x + self.y).into()
    }
}

fn main() {
    let duo1 = Pair::make(8u32, 12u32);
    let duo2 = Pair::make(20u32, 30u32);
    
    println!("Пара 1: {:?}", duo1);
    println!("Сумма: {}", duo1.total());
    
    println!("\nПара 2: {:?}", duo2);
    println!("Сумма: {}", duo2.total());
}
```

**Результаты выполненной работы:**
```
Пара 1: Pair { x: 8, y: 12 }
Сумма: 20

Пара 2: Pair { x: 20, y: 30 }
Сумма: 50
```

## Информация о студенте:
Иванова Елизавета, 1 курс, ПОО
