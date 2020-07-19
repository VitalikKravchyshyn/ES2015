# ES2015 коротко про зміни, які з'явились.

## 1. let, const та блочна область видимості

Ключеве слово let - дозволяє створити змінну в області видимості 
обмеженій {}, наприклад:

```javascript    
    var b = 1;
    {
        let b = 20
        console.log('b =' , b); // b = 20 
    }

    console.log('b =' , b); // b = 1
```

Важливо 

```javascript
    let n = 10
``` 

При повторному присвоєнні змінній n через let n ми отримаємо

```javascript    
    let n = 10 // Uncaught SyntaxError: Identifier 'n' has already been declared
```

При оголошенні через var такої помилки не виникає

Ключеве слово const - дозволяє створити константу, по факту, якщо ви 
створили const ARR =  [], ви вже не зможете переприсвоїти const ARR =  [2, 3] 
чи const ARR = 10, але у вас є можливість зробити наступне:

```javascript
    {
        const ARR = [];

        ARR.push(10);

        console.log('ARR =', ARR); // ARR = [10]

        ARR = 10; // TypeError

        ARR[2] = 20; //значення можна міняти, тобто 

        console.log('ARR =',ARR); // ARR = [10,empty,20]
    }
```

Важливо також пам'ятати, що :
    - якщо говорити про підняття змінних (hoisting), то поведінка let, const відрізняється від традиційної поведінки var та function, оскільки вони не існують до свого оголошення, як звичайні змінні та функції;
    - областю видимості let, const є найближчий блок в який вони вкладені;
    - В const разом з оголошенням змінної повинне бути одночасне присвоєння їй якогось значення.


## 2. Стрілкові функції:

Стрілкові фукнції являють собою коротку форму запису звичайних функцій в JS і при цьому мають певні особливості. Стрілкова функція складається  з списку параметрів (arguments) за яким іде знак => і тіло функції, як:

```javascript
    // Класичний запис функції
    let addition = function(a, b) {
        return a + b;
    };

    // Стрілкова функція
    let addition = (a, b) => a + b;
    console.log(addition(2,2))  // 4
```

**Варто звернути увагу, що при короткому записі, поданому вище, не потрібно навіть явно вказувати що ми хочемо повернути результат**

Другий приклад з використанням фігурних дужок: 

```javascript    
    let fruits = ['orange', 'apple', 'banana']
    let fruitsCount = fruits.map(fruit => {
        return '1 ' +  fruit;
    });

    console.log('fruitsCount = ', fruitsCount); // fruitsCount = (3) ["1 orange", "1 apple", "1 banana"]
```

Важливо також пам'ятати, що:

* Cтрілкові функції немають свого контексту, вони використовують зовнішній контекст. Тобто на відмінно від звичайних функцій, кожна з яких має свій контекст this, всередині стрілкових функцій значення this те ж що і ззовні. Наприклад:

```javascript
    var Car = function()
    {
        this.price = 1000;

        this.increasePrice = perstange => this.price * ((perstange + 100) / 100);

        this.increase = (value) => {

            console.log(this.price);  // 1000

            return this.price + value;
        }

        // Коли функція як метод, this - зовнішнього об'єкта
        this.changePrice = function(value)
        {
            console.log(this.price);  // 1000

            return this.price + value;
        }

        // Свій власний this, якщо функція конструктор;
        // Свій власний this == undefined, якщо функція конструктор в strict mode
        var priceChange = function(value)
        {
            console.log(this.price);  // undefined
            return this.price + value; // NaN
        }
    }

    var skoda = new Car();

    console.log(skoda.increasePrice(20));  // 1200
    console.log(skoda.increase(2000));  // 3000
    console.log(skoda.changePrice(2000));  // 3000
```

* Cтрілкові функції завжди анонімні; 
* Використання bind, call, apply: 
       виклик стрілкових функцій через call, apply, навіть якщо передати аргументи в ці методи (тобто передати контекст виконання) не повпливає на значення this. Тобто: 

```javascript
    var Sum = {
        baseNumber: 20,
        add: function(a)
        {
            let f = (v) => v + this.baseNumber;

            return f(a);
        },

        addUseCall: function(s)
        {
            let f = (v) => v + this.baseNumber;

            let context = {
                baseNumber: 10
            }

            return f.call(context, s);
        }, 

        addUseBind: function(s)
        {
            let f = (v) => v + this.baseNumber;

            let context = {
                baseNumber: 10
            }

            return f.bind(context, s)(); // bind на відмінно від call i apply не ініціює виклик функції, bind лише підміняє контекст і передає параметри.
        }
    }

    console.log(Sum.add(2));  // 22
    console.log(Sum.addUseCall(2));  // 22
    console.log(Sum.addUseBind(2));  // 22
```

* Cтрілкові функції не мають власного об'єкта arguments, в тілі стрілкових функцій arguments буде вказувати на змінну в зовнішньому середовищі. Наприклад 

```javascript
    var arguments = [1,2,3];

    var f = () => arguments[0];

    f(); // 1

    function test(n) {
        var arrow = () => arguments[0] + n;

        return arrow(10);
    }

    console.log(test(1)); // 2
```

* Для заміни arguments в стрілкових функціях використовують rest параметри:

```javascript
    function test(n)
    {
        var arrow = (...args) => args[0] + n;

        return arrow(10);
    }

    console.log(test(1)); // 11
```

* При використанні стрілкових функцій, як методів об'єкта слід пам'ятати, що вони не будуть мати доступу до парметрів об'єкта. Іншими словами якщо стрілкові функції використовувати як методи будемо мати наступне:

```javascript
    let obj = {
        a: 10,
        arrow: () => (console.log(this.a, this)),
        func: function(){
            console.log(this.a, this)
        }
    };

    obj.arrow();  // underfined, Window
    obj.func();   // 10, {a: 10, arrow: f, func: f}
```

* Cтрілкові функції не можуть використоуватись як конструктор, тому для них не можна використовувати слово new, вони не мають прототипу.

```javascript 
    var a = new (function() {}) // відпрацює нормально
    var b = new (() => {}) // помилка Uncaught TypeError: (intermediate value) is not a constructor

    var Foo = () => {};
    console.log(Foo.prototype); // undefined
```

* Cтрілкові функції не можуть використовуватись як генератори;
* В стрілкових функціях потрібно не забувати обгортати в дужки об'єктні стрічки. Приклад:

```javascript
    var func = () => { foo: 1 };

    func(); // underfined
    
    var func = () => ({ foo: 1 });

    func(); // { foo: 1 }
```

* В стрілкових функціях не дозволяється розрив строки між параметрами і стрілкою:

```javascript
    var func = ()
       => 1; // SyntaxError: expected expression, got '=>'
```

* Пріорітет операцій, для валідного опрацювання стрілкових функцій важливо пам'ятати, що стрілка не є оператором і при парсингу таких функцій необхідно враховувати взаємодію з пріорітетами операцій, для прикладу:

```javascript
    let callback;

    callback = callback || function() {}; // ok
    callback = callback || () => {};  // SyntaxError: invalid arrow-function arguments
    callback = callback || (() => {});   // ok
```

## 3. Параметри по замовчуванню.
В ES6 зявилася можливість встановлювати параметри по замовчуванню при оголошенні функції.
Наприклад:

```javascript    
    let addTax = (price, tax = 1.2) => price * tax;
    console.log(addTax(1000)); // 1200
    console.log(addTax(1000, 2)); // 2000
```

## 4. Spread / Rest оператор   
Оператор ... використовується для обєднання набору значень в один масив, в такому випадку він працює як Rest. Приклад використання

```javascript
    function foo(...args)
    {
        console.log(args)
    }

    foo(1,2, [3, 4], 5); // Result: [1,2, [3, 4], 5]
```

Оператор ... також використовується для розбивання масиву значень на окремі елементи (spead)

```javascript
    function foo(x, y, z, ...args)
    {
        console.log(x, y, z, args);
    }

    var arr = [1,2,3,4,5];

    foo(...arr); // Result: 1 2 3 [4, 5]
```

**Застосовується також в стрілкових функціях замість arguments.**

Spread оператор:

- Використовується як заміна  Function.prototype.apply

```javascript
    function myFunction(x, y, z) { }

    var args = [0, 1, 2];

    myFunction.apply(null, args);
    //---------------------------->
    myFunction(...args);
```

- Для копіювання одновимірного масиву замість arr.slice()

```javascript
    var arr = [1, 2, 3];
    var arr2 = [...arr]; // like arr.slice()

    arr2.push(4);  // arr2 becomes [1, 2, 3, 4]
```

- Для конкатенації масивів, замість Array.concat:

```javascript
    var arr1 = [0, 1, 2];
    var arr2 = [3, 4, 5];
    // Append all items from arr2 onto arr1
    arr1 = arr1.concat(arr2);
    //------------------------->
    arr1 = [...arr1, ...arr2];
```

- Для вставки в початок масиву, замість Array.unshift

```javascript
    var arr1 = [0, 1, 2];
    var arr2 = [3, 4, 5];

    // Prepend all items from arr2 onto arr1
    Array.prototype.unshift.apply(arr1, arr2); 
    // arr1 is now [3, 4, 5, 0, 1, 2]
    // ------------------------>
    arr1 = [...arr2, ...arr1]; // arr1 is now [3, 4, 5, 0, 1, 2]
```

## 5. Розширення можливостей літералів об'єкта
В ES6 появилась можливість оголошення літералів об'єкту за допомогою короткого синтаксису. 

```javascript
    var obj = {
        make, 
        good,
        choice
    }
```

аналогічно до 

```javascript
    var obj = {
        make: make, 
        good: good,
        choice: choice
    }
```

тобто з скороченим синтаксисом появилась можливість пропускати значення властивості, якщо вона співпадає з ім'ям змінної.
Також стандарт дозволяє обчислення властивостей просто в літералі об'єкту. Приклад:

```javascript
    var obj = {
        ['make' + make]: true
    }
```

І короткий запис методів об'єкту, без ключового слова function і :

```javascript
    function getCar(make, model, value)
    {
        return {
            make, 
            model, 
            value,
            [make + ' octavia']: true,
            depreciate(){
                this.value -= 1000;
            }
        }
    };

    let skoda = getCar('skoda', 'octavia', 15000);
```

## 6. Вісімкові і двійкові літерари

У ES2015 появились вісімкові та двійкові літерари, як записуються як:
- 0o чи 0O - на початку, перетворює число в вісімкову систему
- 0b чи 0B - на початку, перетворює число в двійкову систему;
Приклад:

```javascript
    var oValue = 0o1000;
    console.log(oValue); // 512

    var bValue = 0b11111;
    console.log(bValue); // 31
```

## 7. Деструктуризація, це розбивання складної структури на прості частини.
В ES2015 зявилася можливість деструктурувати масиви та об'єкти.
Приклад

```javascript
    let [a,c,v] = [1, 'aaaa', [1,2,4,5]];

    function bar() {
        return {
            x: 4,
            y: 5,
            z: 6
        };
    }
    var { x, y, z } = bar();
    console.log(x); // 4 5 6

    // Присвоєння решти масиву змінній
    [a, b, ...rest] = [10, 20, 30, 40, 50];
    console.log(a); // 10
    console.log(b); // 20
    console.log(rest); // [30, 40, 50]


    ({a: x, b:y} = {a: 10, b: 20});
    console.log(x); // 10
    console.log(y); // 20

    Обмін змінних:

    var [a, b] = [10, 20];

    [a, b] = [b, a];
    console.log(a, b); // 20, 10
```

Значення за замовчуванням:

```javascript
    var a, b;

    [a=5, b=7] = [1];
    console.log(a); // 1
    console.log(b); // 7
```

Розбір масиву, поверненого з функції:

```javascript
    function f()
    {
        return [1,2,3]
    }


    var [a, b, c] = f();
    // Можна пропустити повернуті значення, які тобі не цікаві:
    var [d, , e] = f();

    console.log(a, b, c) // 1, 2, 3
    console.log(d, e) // 1, 3
```

Значення може бути отримане з об’єкту та присвоєне змінній з іменем, інакшим від назви властивості об’єкта.

```javascript
    var o = {p: 42, q: true};
    var {p: foo, q: bar} = o;
     
    console.log(foo); // 42 
    console.log(bar); // true  
```

Гарний приклад деструктуризації в циклі

```javascript
    var people = [
      {
        name: 'Mike Smith',
        family: {
          mother: 'Jane Smith',
          father: 'Harry Smith',
          sister: 'Samantha Smith'
        },
        age: 35
      },
      {
        name: 'Tom Jones',
        family: {
          mother: 'Norah Jones',
          father: 'Richard Jones',
          brother: 'Howard Jones'
        },
        age: 25
      }
    ];

    for (var {name: n, family: {father: f}} of people) {
      console.log('Name: ' + n + ', Father: ' + f);
    }

    // "Name: Mike Smith, Father: Harry Smith"
    // "Name: Tom Jones, Father: Richard Jones"
```

Змінній може бути присвоєне значення за замовчанням у випадку, коли витягнуте значення з об’єкту є undefined.

```javascript
    var {a = 10, b = 5} = {a: 3};

    console.log(a); // 3
    console.log(b); // 5
```

або скорочений варіант:

```javascript
    ({a, b} = {a: 1, b: 2})
```

## 8. Ключове слово super для об’єктів

Ключеве слово super використовується для виклику функцій, які належать батьківсьому об'єкту. 

В конструкторі, super() використовуєься як функція, яка викликає батьківський конструктор. Її необхідно викликати до першого звернення до ключового слова this в тілі конструктора, інакше буде ReferenceError.
Приклад:

```javascript
    class Car {
        constructor(price, model, type) {
            this.name = 'Car';
            this.price  = price || 0;
            this.model = model;
            this.type = type;
        }
    }

    // Наслідування класів
    class Skoda extends Car {
        constructor (price, type) {
            this.price = 1000; // ReferenceError, оскільки звернення
            // до this перед викликом super();
            
            super(price, 'Octavia', 'sedan');

            this.name = 'Skoda'
        }
    }
```

* Super можна викликати в статичних методах, наприклад 

```javascript
    class Human {
        constructor() {

        }

        static ping () {
            return 'ping ';
        }
    }

    class Computer extends Human {
        constructor() {}
        static pingpong() {
            return super.ping() + 'pong';
        }
    }

Computer.pingpong(); // 'return => ping pong'
```

* **Не можна використовувати super для видалення свойств батьівського класу чи об'єкта**

```javascript
    class Base {
        constructor() {}
        foo() {}
    }

    class Derived extends Base {
        constructor() {}
        delete() {
            delete super.foo;
        }
    }

    new Derived.delete(); // ReferenceError: invalid delete involving 'super'.
```

* Super.prop не може перевизначати свойства, які захищені від запису через Object.defineProperty

```javascript
    class X {
      constructor() {
        Object.defineProperty(this, "prop", {
          configurable: true,
          writable: false, 
          value: 1
        });
      } 
      f() { 
        super.prop = 2;
      }
    }

    var x = new X();
    x.f();
    console.log(x.prop); // 1
```

* super.prop можна використовувати в літералах:

    У випадку коли в нас є два об'єкти, і в другому об'єкті 
    метод method2 використовує метод method1 першого, super може знайти 
    method1 першого об'єкта. Це працює через використання Object.setPrototypeOf(). Приклад 

```javascript
        var obj1 = {
          method1() {
            console.log("method 1");
          }
        }

        var obj2 = {
          method2() {
           super.method1();
          }
        }

        Object.setPrototypeOf(obj2, obj1);

        obj2.method2(); // logs "method 1"
```

## 9. Інтерполяція ${variable}
В  ES 2015 появилась можливість вставки змінних, або результатів якихось 
обрахунків в текст (інтерполяція) для цього використовується конструкція `${variable}`

```javascript
    ${} - використовується для обрахунку значення змінної
```

Приклад:

```javascript
    var name = 'Nastya';

    console.log(`I love ${name} so much`);

    var price = 10000;

    console.log(`New price of this car ${price - 1000}`);
```

## 10. Цикл for in, а також for of

В ES 2015 появився новий цикл for of який використовується для перебору всіх значень ітерабильних об'єктів, в тому числі: вбудовані String, Array, подібні до масиву об'єкти (наприклад, arguments або NodeList), TypedArray, Map, Set, а також визначені користувачем ітерабельні об'єкти).
Приклад:

```javascript
    var user = ['Vtalii', 'IC3', 27];

    for (let value of user)
        console.log(value); // Vtalii IC3 27

    Перебір об'єкта String:

    let iterable = 'ok';

    for (let value of iterable) {
      console.log(value);
    } 
    // "o"
    // "k"
```

* Перебір об'єкта Map

  P.S. об'єкт Map зберігає пари ключ-значення. Будь-яке значення (і об'єкт, і примітивне значення) може бути використано і як ключ, і як значення.

```javascript
    var a = new Map([[1, 'one'], [2, 'two']])

    for (let val of a){
        console.log(val);
    }

    // [1, "one"]
    // [2, "two"]

    for (let val of a.values()){
        console.log(val);  // "one" "two"
    }

    for (let val of a.keys()){
        console.log(val);  // 1 2
    }
```

1. Основні методи Map це: set(key, value), get(key).

2. З цікавого в Map, ключом може бути NaN, функція, об'єкти та примітиви.

3. Кількість елементів Map можна отримати за допомогою .size.

4. length завжди рівне 0.

* Перебір об'єкта arguments.

Ви можете перебирати об'єкт arguments, щоб дослідити усі параметри, передані у функцію JavaScript:

```javascript
    (function() {
      for (let argument of arguments) {
        console.log(argument); // 1 2 3
      }
    })(1, 2, 3);
```

## 11. Ітератори

* Ітератором називається об'єкт який вміє звертатися до елементів колекції по одному за раз і при цьому відлідковує свою позицію (всередині цієї клдекції). В JS ітератором є об'єкт яий має метод next() який повертає наступний елемент послідовності, а саме повертає об'єкт з двома властивостями (value, done)

* В JS Об'єкти String, Array, TypedArray, Map і Set є ітерованими, оскільки   Symbol.iterator.

Приклад: 

```javascript
    function iterator(array)
    {
        var index = 0;
        array = array || [];

        this.next = function()
        {
            return index < array.length ? 
                {
                    value : array[index++],
                    done : false
                } :
                {
                    done : true
                };
        }
    }


    var itr = new iterator([1,3,5]);

    console.log(itr.next()); // {value: 1, done: false}
    console.log(itr.next()); // {value: 3, done: false}
    console.log(itr.next()); // {value: 5, done: false}
    console.log(itr.next()); // {done: true}
```

## 12. Генератори 

* Фукнції генератори function* це функції з можливістю виходу і після цього наступного входу. Контекст їхнього виконання збергіається  для наступних входів. Для генераторв не можна створити інстансу через new.

* Коли викликається функція генератор, вона виконується не зразу, замість цього повертається об'єкт ітератор. І при подальшому виклику методу next() ітератора, тіло функції виконується до першого yield який зустрічається або делегує подальше виконання іншгому генератору чи ітератору, якщо першим зустрінеться yield* anotherGenerator(). Якщо передати в метод next(arg), якийсь аргумент, він зупиняє генератор і yield заміняється на  аргумент який був переданий.

* Ключеве слово yield використовується для зупинки і відновлення функції-генератора

```javascript
    yield [значення, вираз]; (якщо не вказано то повертає undefined)
    yield повертає об'єкт з двома властивостями (value, done)
```

* Ключове слово yield* використовується для делегування генератору або ітератору.

```javascript
    Generator.prototype.next() //- повертає значення, отримане з yield
    Generator.prototype.return() //- повертає задане значення та закінчує генератор
```

* Аналогічно з генератором

```javascript
    function* generator(array)
    {
        i = 0;
        array = array || []

        while (i < array.length
        {
            yield array[i++];
        }    
    }

    var gnr = generator([1,3,5]);

    або

    function* generator(array)
    {
        yield* array;
    }

    var gnr = generator([1,3,5]);
    // {value: 1, done: false}
    // {value: 3, done: false}
    // {value: 5, done: false}
    // {value: undefined, done: true}
```

## 13. Map та WeakMap
    
### Map - це розширена форма об'єкта в JS. В Map ключом може бути будь-яке значення (і об'єкти і примітиви), в той час як в об'єкта лише строки

```javascript
    var map = new Map();

    var keyString = 'key string';
    var keyObj = {};
    var keyFunc = function() {};

    map.set(keyString, 'ключ стрічка');
    map.set(keyObj, 'ключ об'єкт');
    map.set(keyFunc, 'ключ функція');

    map.size; // 3

    map.get(keyString); // 'ключ стрічка'
    map.get(keyObj); // 'ключ об'єкт'
    map.get(keyFunc); // 'ключ функція'

for (let value of map)
    console.log(value); // ["key string", "ключ стрічка"] ... 
```

* Буде вертати пари ключ значення;

* Якщо пробувати засетати ідентичне значення з ключом який вже є,
воно проігнорується;

* Якщо пробувати засетати якесь нове значення з ключом який вже є,
в Map існуюче проапдейтиться;

### WeakMap

WeakMap на відмінно від Map дає змогу створювати тільки "ключі-об'єкти" (не стрінг чи щось інше), а значеннями можуть бути значення будь-якого типу. Має чотири основні методи set, get, has, delete. WeakMap це Map, в якому ключі мають нестійкі звязки, що дозволяє не заважати збирачеві сміття видаляти елементи WeakMap.

```javascript
        var w = new WeakMap();

         w.set('a', 'b'); // Uncaught TypeError: Invalid value used as weak map key

        var o1 = {},
            o2 = function(){},
            o3 = window;

        w.set(o1, 37);
        w.set(o2, "azerty");
        w.set(o3, undefined);

        w.get(o3); // undefined, тому що задане таке значеннч
        w.has(o1); // true
        w.delete(o1);
        w.has(o1); // false
```

**Крім того в WeakMap немає проперті length і size.**

## 14. Set та WeakSet

Об'єкт Set дає можливість зберігати значення будь-якого типу, будь то примітивні значення чи посилання на об'єкт. Об'єкти Set  - це колекції унікальних значень. Ви можете перебирати елементи Set у порядку вставки. Одне значення в Set може зустрічатися лише один раз; воно є унікальним в колекції Set.

Приклади:

```javascript
    const mySet = new Set([1, 2, 3, 4, 5]);

    console.log(mySet.has(1)); // expected output: true

    var o = {a: 1, b: 2};

    mySet.add(o);
    mySet.add(o) // не додасть бо такий елемент вже є
    mySet.add({a: 1, b: 2}); // o має посилання на інший об'єкт, тому це ок
    mySet.add({a: 1, b: 2}) // додасть ще один елемент

    mySet.has(o) // true
    mySet.has({a: 1, b: 2}); // false, бо посилається на новий об'єкт

    mySet.delete(5); // видаляє 5 з set

    Щоб вивести всі елементи

    set2.forEach(function(value) {
      console.log(value);
    });
```

або

```javascript
    for (let value of set2)
        console.log(value);


    var myArray = ['value1', 'value2', 'value3'];
    // Використовуйте звичайний конструктор Set для трансформації Array у Set
    var mySet = new Set(myArray);
    // Використовуйте оператор spread для трансформації Set у Array.
    console.log([...mySet]); // Виведе у точності такий самий Array як і myArray


    var text = 'India';
    var mySet = new Set(text);  // Set {'I', 'n', 'd', 'i', 'a'}
    mySet.size;  // 5
```

* WeakSet - колекція, елементами якої можуть бути тільки об'єкти. 

* Посилання на ці об'єкти в WeakSet є слабкими. 

* Кожен об'єкт може бути  доданий в WeakSet тільки один раз.

```javascript
    var ws = new WeakSet();
    var obj = {};
    var foo = {};

    ws.add(window);
    ws.add(obj);

    ws.has(window); // true
    ws.has(foo);    // false, foo не додано в WeakSet

    ws.delete(window); // видаляє window из WeakSet
    ws.has(window);    // false, window був видалкений
```

**Крім того в WeakMap та WeakSet немає проперті length і size а також не ітеруються, тому викликати для них for of або forEach не вдасться.***

## 15. Класи в ES2015

Класи в JavaScript були додані в стандарті ECMAScript 6 і є синтаксичним цукром для вже існуючого прототипного унаслідовання в JavaScript. 

Приклад оголошення класу:

```javascript
    class Rectangle {
        constructor(width, height) {
            this.height = height;
            this.width = width;
        }

        // приклад getter
        get square() {
            return this.calcArea()
        }

        calcArea(){
            return this.height * this.width;
        }

        // приклад setter
        set color(color) {
            this.color = color;
        }

        getInstance() {
            return this;
        });
    }

    var rect = new Rectangle(10, 10);
    console.log(rect.square);
```

* Важлива відмінність між оголошенням функції та класу в тому, що оголошення функції є hoisted, тобто можно створити нову функцію, а потім ії описати, а оголошення класу не є таким. Спочатку необхідно  оголосити методи і властивості класу, і тільки потім (нижче по коду) використовувати його. У іншому випадку код, наведенний нижче, поверне помилку ReferenceError.

* Не потрібно писати слово function для кожного нового методу всередині класу className.name поверне ім'я класу.

* Інколи є потреба, щоб екземпляр класу повертав якесь значення, яке залежить від інших властивостей об'єкта. Також, ми можемо хотіти "сховати" алгоритм отримання цього значення. Для цього можна використати ключове слово get (геттер). У прикладі нижче описують геттер square().

* Ключове слово static вказує на статичні методи класу. Це по факту методи,які не потребують створення екземпляру класу, до них можна звертатись напряму.

```javascript
    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }

        static distance(a, b) {
            const dx = a.x - b.x;
            const dy = a.y - b.y;

            return Math.sqrt(dx*dx + dy*dy);
        }

        draw() {
          return this;
        }
    }

    const p1 = new Point(5, 5);
    const p2 = new Point(10, 10);

    console.log(Point.distance(p1, p2));
```

Якщо метод викликається без привязки до об'єкту (класа), то в середині методу this буде мати значення undefined. Щоб виправити це, потрібно привязати метод до об'єкту за допомогою bind(). Приклад:

```javascript
    let obj = new Point();
    let draw = obj.draw;
    draw(); // undefined

    Щоб виправити це потрібно забайндити obj

    let draw = obj.draw.bind(obj);
    draw(); //  context : obj
```

Зауважте, що "this" отримує значення undefined, бо всередині класу використовується строгий режим ('use strict'). Якщо переписати наш приклад на традиційний опис класів у вигляді функцій (без 'use strict'), то замісь undefined в this буде посилання на глобальний scope (window):

```javascript

    function Animal() {};

    Animal.prototype.eat = function() {
      return this;
    }
    
    Animal.prototype.speak = function() {
      return this;
    }

    let obj = new Animal();
    let speak = obj.speak;
    speak(); // global object window

    let eat = Animal.eat;
    eat(); // global object window
```

Наслідування відбувається за допомогою extends. Приклад

```javascript
    class Dog extends Animal {}
```

## 16. Тип даних Symbol

Symbol - примітивний тип даних для створення унікальних ідентифікаторів. Часто використовується для створення прихованих властивостей об'єкта. 

Символи створюються викликом Symbol(), в яку можна передати параметр (опис символа).

До символа не можна звертатись через new, як до інших примітивних типів. Навіть якщо задати для символів один і той же опис, це всерівно різні.Символи

```javascript
    var s1 = Symbol('id');
    var s2 = Symbol('id');

    s1 === s2 // false
    Symbol('id') ===  Symbol('id'); // false
```

##### Глобальні символи

Якщо ми хочемо щоб символи з однаковим описом були рівні ми можемо створити глобальні символи.

Symbol.for(key) - створює глобальний символ з іменем key. І при всіх наступних зверненнях до Symbol.for(key) ми будемо отримувати один і той же символ. Тобто по факту створюється глобальний словник символів.

```javascript
    var id = Symbol.for('id'); // Створюється символ з іменем id, і додвється в словник
    var idAgain = Symbol.for('id'); // Зі словника витягується символ з іменем id
    id === idAgain; // true;
```

Для глобальних символів крім Symbol.for(key) - який шукає символ по імені є ще Symbol.keyFor(sym) - який навпаки приймає символ та повертає його ім'я в словнику.

```javascript
    var sym = Symbol.for("name");
    var sym2 = Symbol.for("id");

    console.log(Symbol.keyFor(sym)) // name
    console.log(Symbol.keyFor(sym2)) // id
```

Крім того для всіх символів достпним є властивість description
    
```javascript        
    console.log(sym.description) // name
    console.log(sym2.description) // id
```

##### Символи мають два основних варіанти застосування:

1. Прихованння деяких властивостей (свойств) об'єкта. Символьна властивість не появляється в for ... in, такщо воно не буде випадково опрацьовано разом зі всіма властивостями.

Крім того воно не буде модифікуватись прямим зверненням, оскільки ніхто крім нас не знає про існування символа. 

Тому використовуючи Symbol-льні властивості м можемо приховати, те що нам потрібно і що інші бачити не мають.

**Технічно символи не є прихованими на всі сто відсотків.** 

```javascript
Object.getOwnPropertySymbols(obj) //- дає нам можливість отримати всі символьні властивості об'єкта obj.
```

Приклад:

```javascript
    var user = {
        name : 'John'
    };

    var id = Symbol(id)

    user[id] = 123;

    console.log(user[id]) // 123

    //=============================================

    let id = Symbol("id");

    let user = {
      name: "Вася",
      [id]: 123 // просто "id: 123" не спрацює
    };
```

Потрібно включати його в квадратні дужки інакше воно додасть строкову властвість id.

2. Існує велика кількість системних символів, які доступні через 'Symbol.*', їх можна використовувати для зміни поведінки ряду об'єктів.

```javascript
    Symbol.hasInstance
    Symbol.isConcatSpreadable
    Symbol.iterator
    Symbol.toPrimitive

    ...

    var arr = [11,12,13];
    var itr = arr[Symbol.iterator]();

    itr.next(); // { value: 11, done: false }
    itr.next(); // { value: 12, done: false }
    itr.next(); // { value: 13, done: false }

    itr.next(); // { value: undefined, done: true }
```

## 17. Проміси
Об'єкти Promise (обіцянка) використовуються для асинхронних операцій і обчислень. Promise повертає значення, яке може бути доступним зараз, або за деякий час, або ніколи.

Promise - це спеціальний об'єкт, який містить свій стан. Спочатку pending («очікування»), потім - одне з: fulfilled («виконано успішно») або rejected («виконано з помилкою»).

##### Спосіб використання, в загальних рисах, такий:

1. Код, якому треба зробити щось асинхронно, створює об'єкт promise і повертає його.

2. Зовнішній код, отримавши promise, навішує на нього обробники.

3. По завершенні процесу, асинхронний код переводить promise в стан fulfilled (з результатом) або rejected (з помилкою). При цьому автоматично викликаються відповідні обробники в
зовнішньому коді.

Оскільки методи Promise.prototype.then() та Promise.prototype.catch() повертають "обіцянки", вони можуть бути зєднані в ланцюжки.

Метод Promise.all (iterable) повертає обіцянку, яку виконається тоді, коли будуть виконані всі обіцянки, передані у вигляді перераховується аргументу, або відхилено будь-яке з переданих обіцянок.

```javascript
    var p1 = Promise.resolve(3);
    var p2 = 1337;
    var p3 = new Promise((resolve, reject) => {
        setTimeout(resolve, 100, "foo");
    }); 

    Promise.all([p1, p2, p3]).then(values => { 
        console.log(values); 
    });  

    //  Результат [3, 1337, "foo"] 
```

* Promise.race (iterable) - Повертає обіцянку, яку буде виконано або відхилено з результатом виконання першого виконаного або відхиленого ітерованого promise.

* Promise.reject (reason) - Повертає  Promise, який відхилений з вказаною причиною.

* Promise.resolve (value) - Повертає  Promise, який виконаний з вказаним значенням. 


* Promise.prototype.then(resolveFn, rejectFn);

* Promise.prototype.finally(onFinally) - виконується коли Promise був виконаний, не залежно успішно чи з помилкою, зазначена функція буде виконана. Це дає можливість запустити один раз певний код який повинен виконатися незалежно від того, з яким результатом виконався Promise.


Приклад

```javascript
    'use strict';

    // Створюємо об'єкт проміс
    let promise = new Promise((resolve, reject) => {
        setTimeout(() => {
            // переведе проміс в стан fulfilled з результатом "result"
            resolve("result");
        }, 1000);
    });

    // promise.then навішу обробники на успішне виконання або помилку
    promise
        .then(
            result => {
                // запускається при виклику resolve
                alert("Fulfilled: " + result); // result - аргумент resolve
            },
            error => {
                // запускається при виклику reject
                alert("Rejected: " + error); // error - аргумент reject
            }
        )
        .finally(onFinally)(() => { 
            // завершений (успішно чи з помилкою)
        });
```

 
