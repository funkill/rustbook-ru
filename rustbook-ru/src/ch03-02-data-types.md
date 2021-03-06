## Типы данных

Любое значение в Rust является определённым *типом данных* (data type), которое говорит о том, какие данные указаны, так что Rust знает как с ними работать. Рассмотрим два подмножества тип данных: скалярные (простые) и составные (сложные).

Не забывайте, что Rust является *статически типизированным* (statically typed) языком. Это означает, что он должен знать типы всех переменных во время компиляции. Обычно компилятор может вывести (infer) какой тип мы хотим использовать, основываясь на значении и на том, как мы с ним работаем. В случаях, когда может быть выведено несколько типов, необходимо вручную  добавлять аннотацию типа. Например, когда мы конвертировали `String` в число с помощью вызова `parse` в разделе ["Сравнение предположения с загаданным номером"](ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number)<comment> ignore </comment> главы 2, мы должны добавить такую аннотацию:

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

Если её не добавить, то Rust покажет следующую ошибку, означающую, что компилятору необходимо больше информации, чтобы знать какой тип мы хотим использовать:

```console
{{#include ../listings/ch03-common-programming-concepts/output-only-01-no-type-annotations/output.txt}}
```

Вы увидите различные аннотации типов для разных типов данных.

### Скалярные типы данных

*Скалярный* тип представляет единственное значение. В Rust есть четыре скалярных типа: целые и вещественные числа, логический тип и символы. Вы можете узнать по другим языкам программирования. Посмотрим на то, как они работают в Rust.

#### Целые числа

Целое  *integer* является числом без дробной составляющей. Мы использовали целый тип в главе 2, это был `u32`. Данное объявление типа указывает, что значение связанное с ним должно быть беззнаковым целым (unsigned integer). Типы знаковых целых (signed integer) начинаются с буквы `i`, вместо буквы `u` и занимают до 32 бит памяти. Таблица 3-1 показывает встроенные целые типы Rust. Каждый вариант в колонках Знаковый (Signed) и Беззнаковый (Unsigned) (к примеру `i16`) может использоваться для объявления типа целого значения.

<span class="caption">Таблица 3-1: целые типы Rust</span>

Размер | Знаковый | Беззнаковый
--- | --- | ---
8-bit | `i8` | `u8`
16-bit | `i16` | `u16`
32-bit | `i32` | `u32`
64-bit | `i64` | `u64`
128-bit | `i128` | `u128`
arch | `isize` | `usize`

Каждый вариант может быть или со знаком, или без знака, но он всегда имеет точный размер. *Знаковый* и *без знаковый* означает, может ли число быть отрицательным или только положительным. Другими словами, нужно ли числу иметь знак (знаковое) или оно будет только положительным и может быть представлено без знака (без знаковое). Это похоже на написание чисел на бумаге: когда знак важен, то число отображено со знаком плюс или минус. Однако, когда можно с уверенностью предположить, что число положительное, то оно отображается без знака. Знаковые числа сохраняются при помощи [дополнительного кода](https://en.wikipedia.org/wiki/Two%27s_complement).

Каждый знаковый вариант может хранить числа от -(2<sup>n - 1</sup>) до 2<sup>n -1</sup> - 1 включительно, где *n* является количеством используемых бит. Так `i8` может хранить числа от -(2<sup>7</sup>) до 2<sup>7</sup> - 1, что равно от -128 до 127. Беззнаковые варианты могут хранить числа от 0 до 2<sup>n</sup> - 1, так `u8` может хранить числа от 0 до 2<sup>8</sup> - 1, т.е. от 0 до 255.

Также есть типы `isize` и `usize`, размер которых зависит от компьютера, на котором работает ваша программа: они имеют размер 64 бит, если вы на 64-битной архитектуре, и 32 бита, если вы на 32-битной.

Вы можете записывать целочисленные литералы в любой форме из таблицы 3-2. Заметим, что все числовые литералы, кроме байтового, позволяют использовать суффиксы, такие как `57u8` и `_` в качестве визуального разделителя, например `1_000`.

<span class="caption">Таблица 3-2: целочисленные литералы в Rust</span>

Числовые литералы | Пример
--- | ---
Десятичный | `98_222`
Шестнадцатеричный | `0xff`
Восьмеричный | `0o77`
Бинарный | `0b1111_0000`
Байтовый (только `u8`) | `b'A'`

Как узнать, какой литерал необходимо использовать? Если вы не уверены, то варианты по умолчанию в Rust являются хорошим выбором. Для целых чисел типом по умолчанию является `i32`: в общем случае, данный тип самый быстрый даже на 64-битных системах. Основной ситуацией, когда вам необходимо использование `isize` или `usize`, является индексирование некоторых коллекций.

> ##### Целочисленное переполнение
> Предположим, у нас есть переменная типа `u8`, которая может сохранять значения между 0 и 255. Если вы попытаетесь задать переменной значение вне данного диапазона, например в 256, то произойдёт *целочисленное переполнение*. В Rust есть несколько интересных правил, связанных с этим поведением. При компиляции кода в режиме отладки, компилятор Rust включает проверки, которые приводят к *панике* во время выполнения, если случится целочисленное переполнение. В Rust термин "паниковать" означает, что программа сразу завершается с ошибкой. Мы обсудим "панику" более детально разделе ["Необрабатываемые ошибки с помощью макроса `panic!`"]<!-- ignore --> главы 9.<br><br>При компиляции кода в финальную версию при помощи флага <code>--release</code>, Rust <em>не</em> включает проверки на целочисленное переполнение, приводящие к панике. Вместо этого, в случае переполнения Rust выполняет <em>оборачивание дополнительного кода</em>. Если кратко, то значения больше, чем максимальное значение, которое может хранить тип, превращается в минимальное значение данного типа. Для типа <code>u8</code>, число 256 превращается в 0, 257 станет 1 и так далее. Программа не будет "паниковать", но переменная получит значение, которое вы возможно не ожидали. Полагаться на такое поведение считается ошибкой. Если вам нужно явно обернуть значение, то можно использовать тип <a><code>Wrapping</code></a> из стандартной библиотеки.
> При компиляции кода в финальную версию при помощи флага `--release`, Rust *не* включает проверки на целочисленное переполнение, приводящие к панике. Вместо этого, в случае переполнения Rust выполняет *оборачивание дополнительного кода*. Если кратко, то значения больше, чем максимальное значение, которое может хранить тип, превращается в минимальное значение данного типа. Для типа `u8`, число 256 превращается в 0, 257 станет 1 и так далее. Программа не будет "паниковать", но переменная получит значение, которое вы возможно не ожидали. Полагаться на такое поведение считается ошибкой. Если вам нужно явно обернуть значение, то можно использовать тип [`Wrapping`] из стандартной библиотеки.

#### Числа с плавающей запятой

В Rust есть два примитивных типа для *чисел с плавающей точкой* (floating-point numbers), которые являются числами с десятичными точками. Числа с плавающей точкой в Rust представлены типами `f32` и `f64`, имеющими размер 32 и 64 бита соответственно. Типом по умолчанию является `f64`, потому что все современные CPU работают с ним приблизительно с такой же скоростью, как и с `f32`, но с большей точностью.

Пример для чисел с плавающей точкой в действии:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-06-floating-point/src/main.rs}}
```

Числа с плавающей точкой представлены согласно стандарту IEEE-754. Тип `f32` является числом с плавающей точкой одинарной точности, а `f64` имеет двойную точность.

#### Числовые операции

Rust поддерживает базовые математические операции, ожидаемые для всех числовых типов: сложение, вычитание, умножение, деление и получение остатка. Следующий код показывает использование каждой операции с помощью выражения `let`:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-07-numeric-operations/src/main.rs}}
```

Каждое из этих выражений использует математические операции и вычисляет значение, которое присваивается переменной. Приложение Б содержит список всех операторов, имеющихся в Rust.

#### Логический тип данных

Как и в большинстве языков программирования, логический тип в Rust может иметь два значения: `true` и `false`, и занимает в памяти один байт. Логический тип в Rust аннотируется при помощи `bool`. Например:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-08-boolean/src/main.rs}}
```

Основной способ использования значений логического типа - это условные конструкции, такие как выражение `if`. Мы расскажем про работу выражения `if` в разделе ["Условные конструкции"]<!-- ignore -->.

#### Символьный тип данных

До этого момента мы работали только с числами, но Rust поддерживает также и буквы. Тип `char` является самым примитивным буквенным типом и следующий код показывает как им пользоваться. (Заметим, что литералы `char` определяются с помощью одинарных кавычек, в отличии от строк где используются двойные кавычки.)

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-09-char/src/main.rs}}
```

Тип `char` имеет размер в четыре байта и представляет собой скалярное юникод значение (Unicode Scalar Value), а значит, он может представить больше символов, чем есть в ASCII. Акцентированные буквы, китайские, японские и корейские символы, эмодзи и пробелы нулевой длины все являются корректными значениями `char` в Rust. Скалярное юникод значение имеет диапазон от `U+0000` до `U+D7FF` и от `U+E000` до  `U+10FFFF` включительно. Тем не менее, "символ" на самом деле не является концептом в Юникод, так что человеческая интуиция о том, что такое "символ" может не совпадать с тем, чем является тип `char` в Rust. Более детально мы обсудим тему в разделе ["Сохранение UTF-8 текста в строки"]<!-- ignore --> главы 8.

### Сложные типы данных

*Сложные типы* могут группировать несколько значений в один тип. В Rust есть два примитивных комбинированных типа: кортежи и массивы.

#### Кортежи

Кортеж является общим способом совместной группировки нескольких значений различного типа в единый комбинированный тип. Кортежи имеют фиксированную длину: после объявления они не могут расти или уменьшаться в размере.

Кортеж создаётся записью списка значений, перечисленных через запятую, внутри круглых скобок. Каждая позиция в кортеже имеет тип. Типы различных значений в кортеже могут не быть одинаковыми. В примере мы добавили не обязательные аннотации типов:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-10-tuples/src/main.rs}}
```

Переменной с именем `tup` привязывается весь кортеж, потому что кортеж является единым комбинированным элементом. Чтобы получить отдельные значения из кортежа, можно использовать сопоставление с образцом для деструктурирования значений кортежа, как в примере:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-11-destructuring-tuples/src/main.rs}}
```

Программа создаёт кортеж, привязывает его к переменной `tup`. Затем в `let` используется шаблон для превращения `tup` в три отдельных переменные: `x`, `y` и `z`. Это называется *деструктурирование* (destructuring), потому что оно разрушает один кортеж на три части. В конце программа печатает значение `y`, которое равно `6.4`.

В дополнение к деструктурированию с помощью сопоставления шаблонов, можно напрямую получить доступ к элементам кортежа с помощью символа `.` и указанием индекса значения, к которому мы хотим обратиться. Например:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-12-tuple-indexing/src/main.rs}}
```

Программа создаёт кортеж с именем `x`, а затем создаёт новые переменные для каждого элемента, используя соответствующие индексы. Как и в большинстве языков, первый индекс в кортеже - это ноль.

#### Массивы

Другим способом получения коллекции из множества значений является *массив*. В отличии от кортежа, каждый элемент массива имеет одинаковый тип. Массивы в Rust отличаются от массивов в некоторых других языках тем, что в Rust они, подобно кортежам, имеют фиксированную длину.

В Rust, значения, попадающие в массив, записываются как список разделённых запятой значений внутри квадратных скобок:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-13-arrays/src/main.rs}}
```

Массивы являются полезными, когда вы хотите разместить данные в стеке, вместо выделения памяти в куче (мы обсудим стек и кучу в главе 4) или когда хотите быть уверенными, что есть всегда фиксированное количество элементов. Массив не такой гибкий, как вектор. Вектор является похожим типом для коллекций, предоставленным стандартной библиотекой, которому *позволено* увеличиваться или уменьшаться в размере. Если вы не уверены, использовать массив или вектор, то возможно следует воспользоваться вектором. В главе 8 обсуждаются вектора более детально.

Примером, когда возможно лучше воспользоваться массивом вместо вектора, является программа в которой нужно знать названия месяцев в году. Вряд ли в такой программе необходимо добавлять или удалять месяцы, поэтому можно воспользоваться массивом, так как вы знаете, что он всегда включает в себя 12 элементов:

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

Для записи типа массива используются квадратные скобки, внутри которых указывается тип каждого из элементов и через точку с запятой идёт количество элементов массива, как тут:

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

Здесь, `i32` является типом каждого элемента. После точки с запятой указано число `5` показывающее, что массив содержит 5 элементов.

Такая запись типа массив выглядит аналогичной альтернативному синтаксису инициализации массива, когда вы хотите создать массив состоящий из одинаковых значений для каждого элемента. Можно указать начальное значение элемента перед точкой запятой, а затем длину массива внутри квадратных скобок, как в примере:

```rust
let a = [3; 5];
```

Массив в переменной `a` будет включать `5` элементов, значение которых будет равно `3`. Данная запись аналогична коду `let a = [3, 3, 3, 3, 3];`, но является более краткой.

##### Доступ к элементам массива

Массив является единым блоком памяти выделенным на стеке. Можно получать доступ к элементам используя индекс:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-14-array-indexing/src/main.rs}}
```

В данном примере, переменная `first` получит значение равное `1`, потому что это значение доступно по индексу `[0]` из массива. Переменная с именем `second` получит значение равное `2` из массива по индексу `[1]`.

##### Некорректный доступ к элементу массива

Что произойдёт, если вы попробуете получить доступ к элементу вне массива, который находится за границами массива? Например, вы изменили пример на следующий код, который будет компилироваться, но завершится с ошибкой при запуске:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore,panics
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access/src/main.rs}}
```

Запуск команды `cargo run` для этого кода выдаёт следующий результат:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access/output.txt}}
```

Компиляция не вызвала ошибок, но программа завершилась ошибкой *времени выполнения* (runtime) и не выполнилась успешно. Когда вы пробуете получить доступ к элементу используя индекс, Rust проверит, что указанный индекс меньше, чем длина массива. Если индекс больше или равен длине массива, Rust вызовет панику.

Это первый пример на деле демонстрирующий принципы безопасности в Rust. Во многих низкоуровневых языках, такие типы проверок не выполняются, и когда вы предоставляете некорректный индекс, можно получить доступ к не корректной памяти. Rust защищает вас от такого рода ошибок тем, что немедленно завершает программу, вместо того, чтобы позволить получить такой доступ и продолжить выполнение. Обсуждение обработки ошибок в Rust ведётся в главе 9.


["Условные конструкции"]: ch03-05-control-flow.html#control-flow
["Сохранение UTF-8 текста в строки"]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
["Необрабатываемые ошибки с помощью макроса `panic!`"]: ch09-01-unrecoverable-errors-with-panic.html
[`Wrapping`]: ../std/num/struct.Wrapping.html