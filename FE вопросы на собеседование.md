# Вопросов по JavaScript

### 1. Расскажи какме особености типизации есть в языке JS

### 2. В чем разница между приметивными типами и объектом

### 3. Какие способы задания функций есть в JS

### 4. В чем разница стрелочной функции от обычной

### 7. В чем разница между ключевыми словами «var», «let» и «const»?

Переменные, объявленные с помощью ключевого слова «var», являются глобальными. Это означает, что они доступны из любого места в коде:

```js
function giveMeX(showX) {
  if (showX) {
    var x = 5;
  }
  return x;
}

console.log(giveMeX(false));
console.log(giveMeX(true));
```

Результатом первого console.log будет undefined, второго — 5. Мы имеем доступ к переменной «x» из-за ее всплытия в глобальную область видимости. Код из примера выше интерпретируется следующим образом:

```js
function giveMeX(showX) {
  var x; // имеет значение undefined
  if (showX) {
    x = 5;
  }
  return x;
}
```

Результатом первого console.log является undefined, поскольку объявленные переменные, которым не присвоено значения, имеют значение undefined по умолчанию.

Переменные, объявленные с помощью ключевых слов «let» и «const» имеют блочную область видимости. Это означает, что они доступны только внутри блока ({ }):

```js
function giveMeX(showX) {
  if (showX) {
    let x = 5;
  }
  return x;
}

function giveMeY(showY) {
  if (showY) {
    let y = 5;
  }
  return y;
}
```

Вызов этих функций с параметром false приведет к ошибке ReferenceError, потому что к переменным «x» и «y» нет доступа снаружи блока и их значения не возвращаются (не всплывают).

Разница между «let» и «const» состоит в том, что в первом случае мы может менять значение переменной, а во втором — нет (константа). При этом, мы можем менять значение свойства объекта, объявленного с помощью const, но не само свойство (переменную).

---

### 5. Пасскажи про наследование в JS и про цепочку прототипов.

### 6. Какое значение имеет this?

Обычно this ссылается на значение объекта, который в данный момент выполняет или вызывает функцию. «В данный момент» означает, что значение this меняется в зависимости от контекста выполнения, от того места, где мы используем this.

```js
const carDetails = {
    name: 'Ford Mustang',
    yearBought: 2005,
    getName() {
        return this.name
    }
    isRegistered: true
}

console.log(carDetails.getName()) // Ford Mustang
```

В данном случае метод getName возвращает this.name, а this ссылается на carDetails, объект, в котором выполняется getName, который является ее «владельцем».

Добавим после console.log три строчки:

```js
var name = "Ford Ranger";
var getCarName = carDetails.getName;

console.log(getCarName()); // Ford Ranger
```

Второй console.log выдает Ford Ranger, и это странно. Причина такого поведения заключается в том, что «владельцем» getCarName является объект window. Переменные, объявленные с помощью ключевого слова «var» в глобальной области видимости, записываются в свойства объекта window. this в глобальной области видимости ссылается на объект window (если речь не идет о строгом режиме).

```js
console.log(getCarName === window.getCarName); // true
console.log(getCarName === this.getCarName); // true
```

В этом примере this и window ссылаются на один объект.

Одним из способов решения данной проблемы является использование методов call или apply:

```js
console.log(getCarName.apply(carDetails)); // Ford Mustang
console.log(getCarName.call(carDetails)); // Ford Mustang
```

Call и apply принимают в качестве первого аргумента объект, который будет являться значением this внутри функции.

В IIFE, функциях, которые создаются в глобальном области видимости, анонимных функциях и внутренних функциях методов объекта значением this по умолчанию является объект window.

```js
(function () {
  console.log(this);
})(); // window

function iHateThis() {
  console.log(this);
}
iHateThis(); // window

const myFavouriteObj = {
  guessThis() {
    function getName() {
      console.log(this.name);
    }
    getName();
  },
  name: "Marko Polo",
  thisIsAnnoying(callback) {
    callback();
  },
};

myFavouriteObj.guessThis(); // window
myFavouriteObj.thisIsAnnoying(function () {
  console.log(this); // window
});
```

Существует два способа получить «Marko Polo».

Во-первых, мы можем сохранить значение this в переменной:

```js
const myFavoriteObj = {
  guessThis() {
    const self = this; // сохраняем значение this в переменной self
    function getName() {
      console.log(self.name);
    }
    getName();
  },
  name: "Marko Polo",
  thisIsAnnoying(callback) {
    callback();
  },
};
```

Во-вторых, мы можем использовать стрелочную функцию:

```js
const myFavoriteObj = {
  guessThis() {
    const getName = () => {
      // копируем значение this из внешнего окружения
      console.log(this.name);
    };
    getName();
  },
  name: "Marko Polo",
  thisIsAnnoying(callback) {
    callback();
  },
};
```

Стрелочные функции не имеют собственного значения this. Они копируют значение this из внешнего лексического окружения.

---

### 8. Что такое деструктуризация объекта (Object Destructuring)?

Деструктуризация — относительно новый способ получения (извлечения) значений объекта или массива.

Допустим, у нас есть такой объект:

```js
const employee = {
  firstName: "Marko",
  lastName: "Polo",
  position: "Software Developer",
  yearHired: 2017,
};
```

Раньше для получения свойств объекта мы создавали переменные для каждого свойства. Это было очень скучно и сильно раздражало:

```js
var firstName = employee.firstName;
var lastName = employee.lastName;
var position = employee.position;
var yearHired = employee.yearHired;
```

Использование деструктуризации позволяет сделать код чище и отнимает меньше времени. Синтаксис деструктуризации следующий: заключаем свойства объекта, которые хотим получить, в фигурные скобки ({ }), а если речь идет о массиве — в квадратные скобки ([ ]):

```js
let { firstName, lastName, position, yearHired } = employee;
```

Для изменения имени переменной следует использовать «propertyName: newName»:

```js
let { firstName: fName, lastName: lName, position, yearHired } = employee;
```

Для присвоения переменным значения по умолчанию следует использовать «propertyName = 'defaultValue'»:

```js
let { firstName = "Mark", lastName: lName, position, yearHired } = employee;
```

---

### 6. Расскажи про методы Object.entries()

Object.entries() метод возвращает массив собственных перечисляемых свойств указанного объекта в формате [key, value], в том же порядке, что и в цикле for...in (разница в том, что for-in перечисляет свойства из цепочки прототипов). Порядок элементов в массиве который возвращается Object.entries() не зависит от того как обьект обьявлен. Если существует необходиомсть в определенном порядке, то массив должен быть отсортирован до вызова метода, например Object.entries(obj).sort((a, b) => a[0] - b[0]);.

### 9. Что такое объект Set?

Объект Set позволяет хранить уникальные значения, примитивы и ссылки на объекты. Еще раз: в Set можно добавлять только уникальные значения. Он проверяет хранящиеся в нем значения с помощью алгоритма SameZeroValue.

Экземпляр Set создается с помощью конструктора Set. Мы также можем передать ему некоторые значения при создании:

```js
const set1 = new Set();
const set2 = new Set(["a", "b", "c", "d", "d", "e"]); // вторая "d" не добавится
```

Мы можем добавлять значения в Set, используя метод add. Поскольку метод add является возвращаемым, мы может использовать цепочку вызовов:

```js
set2.add("f");
set2.add("g").add("h").add("i").add("j").add("k").add("k"); // вторая "k" не добавится
```

Мы можем удалять значения из Set, используя метод delete:

```js
set2.delete("k"); // true
set2.delete("z"); // false, потому что в set2 нет такого значения
```

Мы можем проверить наличие свойства в Set, используя метод has:

```js
set2.has("a"); // true
set2.has("z"); // false
```

Для получения длины Set используется метод size:

```js
set2.size; // 10
```

Метод clear очищает Set:

```js
set2.clear(); // пусто
```

Мы можем использовать Set для удаления повторяющихся значений в массиве:

```js
const nums = [1, 2, 3, 4, 5, 6, 6, 7, 8, 8, 5];
const uniqNums = [...new Set(nums)]; // [1,2,3,4,5,6,7,8]
```

---

### 10. Что такое объект Map?

### 11. Что такое промисы (Promises)?

Промисы — это один из приемов работы с асинхронным кодом в JS. Они возвращают результат асинхронной операции.

```js
fs.readFile("somefile.txt", function (e, data) {
  if (e) {
    console.log(e);
  }
  console.log(data);
});
```

Проблемы при таком подходе начинаются, когда нам необходимо добавить еще одну асинхронную операцию в первую (внутрь первой), затем еще одну и т.д. В результате мы получаем беспорядочный и нечитаемый код:

```js
fs.readFile("somefile.txt", function (e, data) {
  // код
  fs.readFile("directory", function (e, files) {
    // код
    fs.mkdir("directory", function (e) {
      // код
    });
  });
});
```

А вот как это выглядит с промисами:

```js
promReadFile("file/path")
  .then((data) => {
    return promReaddir("directory");
  })
  .then((data) => {
    return promMkdir("directory");
  })
  .catch((e) => {
    console.error(e);
  });
```

У промиса есть четыре состояния:

- Ожидание — начальное состояние промиса. Результата промиса неизвестен, поскольку операция не завершена.
- Выполнено — асинхронная операция выполнена, имеется результат.
- Отклонено — асинхронная операция не выполнена, имеется причина.
- Завершено — выполнено или отклонено.

В качестве параметров конструктор промиса принимает resolve и reject. В resolve записывается результат выполнения операции, в reject — причина невыполнения операции. Результат может быть обработан в методе .then, ошибка — в методе .catch. Метод .then также возвращает промис, поэтому мы можем использовать цепочку, состоящую из нескольких .then.

```js
const myPromiseAsync = (...args) => {
  return new Promise((resolve, reject) => {
    doSomeAsync(...args, (error, data) => {
      if (error) {
        reject(error);
      } else {
        resolve(data);
      }
    });
  });
};

myPromiseAsync()
  .then((result) => {
    console.log(result);
  })
  .catch((reason) => {
    console.error(reason);
  });
```

Мы можем создать вспомогательную функцию для преобразования асинхронной операции с callback в промис. Она будет работать наподобие util из Node.js («промисификация»):

```js
const toPromise = (asyncFuncWithCallback) => {
  return (...args) => {
    return new Promise((res, rej) => {
      asyncFuncWithCallback(...args, (e, result) => {
        return e ? rej(e) : res(result);
      });
    });
  };
};

const promiseReadFile = toPromise(fs.readFile);

promiseReadFile("file/path")
  .then((data) => {
    console.log(data);
  })
  .catch((e) => console.error(e));
```

---

### 12. Что такое async/await?

Async/await — относительно новый способ написания асинхронного (неблокирующего) кода в JS. Им оборачивают промис. Он делает код более читаемым и чистым, чем промисы и функции обратного вызова. Однако для использования async/await необходимо хорошо знать промисы.

```js
// промис
function callApi() {
  return fetch("url/to/api/endpoint")
    .then((resp) => resp.json())
    .then((data) => {
      // работаем с данными
    })
    .catch((err) => {
      // работаем с ошибкой
    });
}

// async/await
// для перехвата ошибок используется try/catch
async function callApi() {
  try {
    const resp = await fetch("url/to/api/endpoint");
    const data = await res.json();
    // работаем с данными
  } catch (e) {
    // работаем с ошибкой
  }
}
```

Использование ключевого слова «async» перед функцией заставляет ее возвращать промис:

```js
const giveMeOne = (async() = 1);

giveMeOne().then((num) => {
  console.log(num); // 1
});
```

Ключевое слово «await» можно использовать только внутри асинхронной функции. Использование «await» внутри другой функции приведет к ошибке. Await ожидает завершения выражения справа, чтобы вернуть его значение перед выполнением следующей строчки кода.

```js
const giveMeOne = async() => 1

function getOne(){
    try{
        const num = await giveMeOne()
        console.log(num)
    } catch(e){
        console.log(e)
    }
}
// Uncaught SyntaxError: await is only valid in an async function

async function getTwo(){
    try{
        const num1 = await giveMeOne()
        const nm2 = await giveMeOne()
        return num1 + num2
    } catch(e){
        console.log(e)
    }
}

await getTwo() // 2
```

---

# Практические задания по JavaScript

## БАЗОВЫЕ

### 1. Что будет в консоли?

```javascript
function sayHi() {
  console.log(name);
  console.log(age);
  var name = "Lydia";
  let age = 21;
}

sayHi();
```

- A: `Lydia` и `undefined`
- B: `Lydia` и `ReferenceError`
- C: `ReferenceError` и `21`
- D: `undefined` и `ReferenceError`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: D

Внутри функции мы сперва определяем переменную `name` с помощью ключевого слова `var`. Это означает, что переменная будет поднята (область памяти под переменную будет выделена во время фазы создания) со значением `undefined` по умолчанию, до тех пора пока исполнение кода не дойдет до строчки, где определяется переменная. Мы еще не определили значение `name` когда пытаемся вывести её в консоль, поэтому в консоли будет `undefined`.

Переменные, определенные с помощью `let` (и `const`), также поднимаются, но в отличие от `var`, не <i>инициализируются</i>. Доступ к ним не возможен до тех пор, пока не выполнится строка их определения (инициализации). Это называется "временная мертвая зона". Когда мы пытаемся обратиться к переменным до того момента как они определены, JavaScript выбрасывает исключение `ReferenceError`.

</p>
</details>

---

### 3. Что будет в консоли?

```javascript
const shape = {
  radius: 10,
  diameter() {
    return this.radius * 2;
  },
  perimeter: () => 2 * Math.PI * this.radius,
};

shape.diameter();
shape.perimeter();
```

- A: `20` и `62.83185307179586`
- B: `20` и `NaN`
- C: `20` и `63`
- D: `NaN` и `63`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: B

Заметь, что `diameter` это обычная функция, в то время как `perimeter` это стрелочная функция.

У стрелочных функций значение `this` указывает на окружающую область видимости, в отличие от обычных функций! Это значит, что при вызове `perimeter` значение `this` у этой функции указывает не на объект `shape`, а на внешнюю область видимости (например, window).

У этого объекта нет ключа `radius`, поэтому возвращается `undefined`.

</p>
</details>

---

### 4. Что будет в консоли?

```javascript
let c = { greeting: "Hey!" };
let d;

d = c;
c.greeting = "Hello";
console.log(d.greeting);
```

- A: `Hello`
- B: `Hey!`
- C: `undefined`
- D: `ReferenceError`
- E: `TypeError`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: A

В JavaScript все объекты являются _ссылочными_ типами данных.

Сперва переменная `c` указывает на объект. Затем мы указываем переменной `d` ссылаться на тот же объект, что и `c`.

<img src="https://i.imgur.com/ko5k0fs.png" width="200">

Когда ты изменяешь один объект, то изменяются значения всех ссылок, указывающих на этот объект.

</p>
</details>

---

### 5. Что будет в консоли?

```javascript
let number = 0;
console.log(number++);
console.log(++number);
console.log(number);
```

- A: `1` `1` `2`
- B: `1` `2` `2`
- C: `0` `2` `2`
- D: `0` `1` `2`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

**Постфиксный** унарный оператор `++`:

1. Возвращает значение (`0`)
2. Инкрементирует значение (теперь число равно `1`)

**Префиксный** унарный оператор `++`:

1. Инкрементирует значение (число теперь равно `2`)
2. Возвращает значение (`2`)

Результат: `0 2 2`.

</p>
</details>

---

### 6. Что будет в консоли?

```javascript
function checkAge(data) {
  if (data === { age: 18 }) {
    console.log("Ты взрослый!");
  } else if (data == { age: 18 }) {
    console.log("Ты все еще взрослый.");
  } else {
    console.log(`Хмм.. Кажется, у тебя нет возраста.`);
  }
}

checkAge({ age: 18 });
```

- A: `Ты взрослый!`
- B: `Ты все еще взрослый.`
- C: `Хмм.. Кажется, у тебя нет возраста.`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

В операциях сравнения примитивы сравниваются по их _значениям_, а объекты по _ссылкам_. JavaScript проверяет, чтобы объекты указывали на одну и ту же область памяти.

Сравниваемые объекты в нашем примере не такие: объект, переданный в качестве параметра, указывает на другую область памяти, чем объекты, используемые в сравнениях.

Поэтому `{ age: 18 } === { age: 18 }` и `{ age: 18 } == { age: 18 }` возвращают `false`.

</p>
</details>

---

### 8. Каким будет результат?

```javascript
[..."Lydia"];
```

- A: `["L", "y", "d", "i", "a"]`
- B: `["Lydia"]`
- C: `[[], "Lydia"]`
- D: `[["L", "y", "d", "i", "a"]]`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: A

Строка является итерируемой сущностью. Оператор распространения преобразовывает каждый символ в отдельный элемент.

</p>
</details>

---

## БАЗОВЫЕ+

### 1. Какой будет вывод?

```javascript
const add = () => {
  const cache = {};
  return (num) => {
    if (num in cache) {
      return `From cache! ${cache[num]}`;
    } else {
      const result = num + 10;
      cache[num] = result;
      return `Calculated! ${result}`;
    }
  };
};

const addFunction = add();
console.log(addFunction(10));
console.log(addFunction(10));
console.log(addFunction(5 * 2));
```

- A: `Calculated! 20` `Calculated! 20` `Calculated! 20`
- B: `Calculated! 20` `From cache! 20` `Calculated! 20`
- C: `Calculated! 20` `From cache! 20` `From cache! 20`
- D: `Calculated! 20` `From cache! 20` `Error`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

Функция `add` является функцией _запоминателем_. С помощью запоминания мы можем кэшировать результаты функции, чтобы ускорить ее выполнение. В этом случае мы создаем объект `cache`, в котором хранятся ранее возвращенные значения.

Если мы снова вызываем функцию `addFunction` с тем же аргументом, она сначала проверяет, получило ли оно уже это значение в своем кеше. В этом случае будет возвращено значение кэша, что экономит время выполнения. Иначе, если он не кэшируется, он вычислит значение и сохранит его после.

Мы вызываем функцию `addFunction` три раза с одним и тем же значением: при первом вызове значение функции, когда `num` равно `10`, еще не кэшировано. Условие оператора if `num in cache` возвращает `false`, и выполняется блок else: `Calculated! 20` регистрируется, и значение результата добавляется в объект кеша. `cache` теперь выглядит как `{10: 20}`.

Во второй раз объект `cache` содержит значение, возвращаемое для `10`. Условие оператора if `num in cache` возвращает `true`, а `'From cache! 20'` выводится в лог.

В третий раз мы передаем `5 * 2` в функцию, которая оценивается как `10`. Объект `cache` содержит значение, возвращаемое для `10`. Условие оператора if `num in cache` возвращает `true`, а `'From cache! 20'` регистрируется.

</p>
</details>

---

### 2. Какой будет вывод?

```javascript
const getList = ([x, ...y]) => [x, y]
const getUser = user => { name: user.name, age: user.age }

const list = [1, 2, 3, 4]
const user = { name: "Lydia", age: 21 }

console.log(getList(list))
console.log(getUser(user))
```

- A: `[1, [2, 3, 4]]` and `undefined`
- B: `[1, [2, 3, 4]]` and `{ name: "Lydia", age: 21 }`
- C: `[1, 2, 3, 4]` and `{ name: "Lydia", age: 21 }`
- D: `Error` and `{ name: "Lydia", age: 21 }`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: A

Функция `getList` получает массив в качестве аргумента. Между скобками функции `getList` мы сразу же деструктурируем этот массив. Вы можете увидеть это как:

`[x, ...y] = [1, 2, 3, 4]`

С помощью оставшихся параметров `... y` мы помещаем все "оставшиеся" аргументы в массив. Остальные аргументы - это `2`, `3` и `4` в этом случае. Значение `y` является массивом, содержащим все остальные параметры. В этом случае значение `x` равно `1`, поэтому, мы видим в логе `[x, y]`, `[1, [2, 3, 4]]`.

Функция `getUser` получает объект. В случае функций со стрелками мы не можем писать фигурные скобки, если мы просто возвращаем одно значение. Однако, если вы хотите вернуть _объект_ из стрелочной функции, вы должны написать его в скобках, в противном случае никакое значение не возвращается! Следующая функция вернула бы объект:

`const getUser = user => ({ name: user.name, age: user.age })`

Поскольку в этом случае значение не возвращается, функция возвращает значение `undefined`.

</p>
</details>

---

### 3. Какое значение будет на выходе?

```javascript
const one = false || {} || null;
const two = null || false || "";
const three = [] || 0 || true;

console.log(one, two, three);
```

- A: `false` `null` `[]`
- B: `null` `""` `true`
- C: `{}` `""` `[]`
- D: `null` `null` `true`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

С помощью оператора `||` мы можем вернуть первый истинный операнд. Если все значения ложны, последний операнд возвращается.

`(false || {} || null)`: пустой объект `{}` является истинным значением. Это первое (и единственное) истинное значение, которое возвращается. `one` содержит `{}`.

`(null || false ||" ")`: все операнды являются ложными значениями. Это означает, что прошедший операнд `""` возвращается. `two` содержит `""`.

`([] || 0 ||" ")`: пустой массив `[]` является истинным значением. Это первое истинное значение, которое возвращается. `three` присвоено `[]`.

</p>
</details>

---

## МАССИВЫ

### 1. Каким будет результат?

```javascript
[
  [0, 1],
  [2, 3],
].reduce(
  (acc, cur) => {
    return acc.concat(cur);
  },
  [1, 2]
);
```

- A: `[0, 1, 2, 3, 1, 2]`
- B: `[6, 1, 2]`
- C: `[1, 2, 0, 1, 2, 3]`
- D: `[1, 2, 6]`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

`[1, 2]` - начальное значение, с которым инициализируется переменная `acc`. После первого прохода `acc` будет равно `[1,2]`, а `cur` будет `[0,1]`. После конкатенации результат будет `[1, 2, 0, 1]`.

Затем `acc` равно `[1, 2, 0, 1]`, а `cur` равно `[2, 3]`. После слияния получим `[1, 2, 0, 1, 2, 3]`.

</p>
</details>

---

### 2. Каким будет результат?

```javascript
[1, 2, 3].map((num) => {
  if (typeof num === "number") return;
  return num * 2;
});
```

- A: `[]`
- B: `[null, null, null]`
- C: `[undefined, undefined, undefined]`
- D: `[ 3 x empty ]`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

При использовании метода map, значение `num` равно элементу, над которым он в данный момент зацикливается. В этом случае элементы являются числами, поэтому условие оператора if `typeof num === "number"` возвращает `true`. Функция map создает новый массив и вставляет значения, возвращаемые функцией.

Однако мы не возвращаем значение. Когда мы не возвращаем значение из функции, функция возвращает значение `undefined`. Для каждого элемента в массиве вызывается функциональный блок, поэтому для каждого элемента мы возвращаем `undefined`.

</p>
</details>

---

### 3. Какой будет вывод?

```javascript
const numbers = [1, 2, 3, 4, 5];
const [y] = numbers;

console.log(y);
```

- A: `[[1, 2, 3, 4, 5]]`
- B: `[1, 2, 3, 4, 5]`
- C: `1`
- D: `[1]`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

Мы можем распаковать значения из массивов или свойств из объектов путем деструктуризации. Например:

```javascript
[a, b] = [1, 2];
```

<img src="https://i.imgur.com/ADFpVop.png" width="200">

Значение `a` теперь равно `1`, а значение `b` теперь равно `2`. Что мы на самом деле сделали в этом вопросе, так это:

```javascript
[y] = [1, 2, 3, 4, 5];
```

<img src="https://i.imgur.com/NzGkMNk.png" width="200">

Это означает, что значение `y` равно первому значению в массиве, которое является числом`1`. Когда мы регистрируем `y`, возвращается `1`.

</p>
</details>

---

### 4. Какой будет вывод?

```javascript
[1, 2, 3, 4].reduce((x, y) => console.log(x, y));
```

- A: `1` `2`, `3` `3` и `6` `4`
- B: `1` `2`, `2` `3` и `3` `4`
- C: `1` `undefined`, `2` `undefined`, `3` `undefined` и `4` `undefined`
- D: `1` `2`, `undefined` `3` и `undefined` `4`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: D

Первым аргументом, который получает метод `reduce`, является _аккумулятором_, в данном случае `x`. Второй аргумент - это _текущее значение_, `y`. С помощью метода `reduce` мы выполняем функцию обратного вызова для каждого элемента в массиве, что в конечном итоге может привести к единственному значению.

В этом примере мы не возвращаем никаких значений, мы просто регистрируем значения аккумулятора и текущее значение.

Значение аккумулятора равно ранее возвращенному значению функции обратного вызова. Если вы не передадите необязательный аргумент `initialValue` методу `reduce`, аккумулятор будет равен первому элементу при первом вызове.

При первом вызове аккумулятор (`x`) равен `1`, а текущее значение (`y`) равно `2`. Мы не возвращаемся из функции обратного вызова, мы регистрируем аккумулятор и текущее значение: `1` и `2` регистрируются.

Если вы не возвращаете значение из функции, она возвращает значение `undefined`. При следующем вызове аккумулятор равен `undefined`, а текущее значение равно 3. `undefined` и `3` будут зарегистрированы.

При четвертом вызове мы снова не возвращаемся из функции обратного вызова. Аккумулятор снова равен `undefined`, а текущее значение равно `4`. `undefined` и`4` будут зарегистрированы.

</p>
</details>

---

### 5. Какой будет вывод?

```javascript
function addToList(item, list) {
  return list.push(item);
}

const result = addToList("apple", ["banana"]);
console.log(result);
```

- A: `['apple', 'banana']`
- B: `2`
- C: `true`
- D: `undefined`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: B

Метод `.push()` возвращает _длину_ нового массива! Ранее массив содержал один элемент (строка `"banana"`) и имел длину `1`. После добавления в массив строки `"apple"`, массив содержит два элемента и имеет длину `2`. Это возвращается из функции `addToList`.

Метод `push` изменяет исходный массив. Если вы хотите вернуть _массив_ из функции, а не _длину массива_, вы должны были вернуть `list` после добавления в нее `item`.

</p>
</details>

---

### 6. Какой из этих методов модифицирует исходный массив?

```javascript
const emojis = ["✨", "🥑", "😍"];

emojis.map((x) => x + "✨");
emojis.filter((x) => x !== "🥑");
emojis.find((x) => x !== "🥑");
emojis.reduce((acc, cur) => acc + "✨");
emojis.slice(1, 2, "✨");
emojis.splice(1, 2, "✨");
```

- A: `All of them`
- B: `map` `reduce` `slice` `splice`
- C: `map` `slice` `splice`
- D: `splice`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: D

Используя метод `splice`, мы модифицируем исходный массив, удаляя, заменяя или добавляя элементы. В этом случае мы удалили 2 элемента из индекса 1 (мы удалили `'🥑'` и `'😍'`) и добавили `✨` emoji.

`map`, `filter` и `slice` возвращают новый массив, `find` возвращает элемент, а `reduce` возвращает аккумулированное значение.

</p>
</details>

---

## ГЕНЕРАТОРЫ

### 1. Каким будет результат?

```javascript
function* generator(i) {
  yield i;
  yield i * 2;
}

const gen = generator(10);

console.log(gen.next().value);
console.log(gen.next().value);
```

- A: `[0, 10], [10, 20]`
- B: `20, 20`
- C: `10, 20`
- D: `0, 10 and 10, 20`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

Обычные функции не могут быть остановлены на полпути после вызова. Однако функцию генератор можно "остановить" на полпути, а затем продолжить с того места, где она остановилась. Каждый раз, когда в функции-генераторе встречает ключевое слово `yield`, функция возвращает значение, указанное после него. Обратите внимание, что функция генератора в этом случае не _return_ значение, оно _yields_ значение.

Сначала мы инициализируем функцию генератор с `i`, равным `10`. Мы вызываем функцию генератор, используя метод `next ()`. Когда мы в первый раз вызываем функцию генератора, `i` равно `10`. Он встречает первое ключевое слово `yield`, получая значение `i`. Генератор теперь "приостановлен", и `10` выводится в консоль.

Затем мы снова вызываем функцию с помощью метода `next ()`. Она запускается с того места, где остановилась ранее, все еще с `i`, равным `10`. Теперь он встречает следующее ключевое слово `yield` и возвращает `i * 2`. `i` равно `10`, поэтому он возвращает `10 * 2`, то есть `20`. Это приводит к 10, 20.

</p>
</details>

---

### 2. Как мы можем вывести в лог значения, которые закомментированы после оператора console.log?

```javascript
function* startGame() {
  const answer = yield "Do you love JavaScript?";
  if (answer !== "Yes") {
    return "Oh wow... Guess we're gone here";
  }
  return "JavaScript loves you back ❤️";
}

const game = startGame();
console.log(/* 1 */); // Do you love JavaScript?
console.log(/* 2 */); // JavaScript loves you back ❤️
```

- A: `game.next("Yes").value` и `game.next().value`
- B: `game.next.value("Yes")` и `game.next.value()`
- C: `game.next().value` и `game.next("Yes").value`
- D: `game.next.value()` и `game.next.value("Yes")`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

Функция генератора "приостанавливает" выполнение, когда видит ключевое слово yield. Во-первых, мы должны позволить функции выдать строку "Do you love JavaScript?", что можно сделать, вызвав `game.next().value`.

Каждая строка выполняется до тех пор, пока не найдет первое ключевое слово `yield`. В первой строке функции есть ключевое слово `yield` на первом месте: выполнение останавливается с первым выходом! _Это означает, что переменная `answer` еще не определена!_

Когда мы вызываем `game.next("Yes").value`, предыдущий `yield` заменяется значением параметров, переданных функции `next()`, в данном случае `"Yes"`. Значение переменной `answer` теперь равно `"Yes"`. Условие if возвращает `false`, а `JavaScript loves you back ❤️`, регистрируется.

</p>
</details>

---

### 3. Какое значение будет на выходе?

```javascript
function* generatorOne() {
  yield ["a", "b", "c"];
}

function* generatorTwo() {
  yield* ["a", "b", "c"];
}

const one = generatorOne();
const two = generatorTwo();

console.log(one.next().value);
console.log(two.next().value);
```

- A: `a` and `a`
- B: `a` and `undefined`
- C: `['a', 'b', 'c']` and `a`
- D: `a` and `['a', 'b', 'c']`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

Используя ключевое слово `yield`, мы получаем значения в функции генератора. С помощью ключевого слова `yield*` мы можем получить значения из другой функции-генератора или итерируемого объекта (например, массива).

В `generatorOne` мы получаем весь массив `[' a ',' b ',' c ']`, используя ключевое слово `yield`. Значение свойства `value` для объекта, возвращаемого методом `next` для `one` (`one.next().value`), равно всему массиву `['a', 'b', 'c']`.

```javascript
console.log(one.next().value); // ['a', 'b', 'c']
console.log(one.next().value); // undefined
```

В файле `generatorTwo` мы используем ключевое слово `yield*`. Это означает, что первое полученное значение `two` равно первому полученному значению в итераторе. Итератор - это массив `['a', 'b', 'c']`. Первым полученным значением является `a`, поэтому в первый раз, когда мы вызываем `two.next().value`, возвращается `a`.

```javascript
console.log(two.next().value); // 'a'
console.log(two.next().value); // 'b'
console.log(two.next().value); // 'c'
console.log(two.next().value); // undefined
```

</p>
</details>

---

## ПРОМИСЫ И АСИНХРОННЫЙ КОД

### 1. Что будет в консоли?

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1);
}

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1);
}
```

- A: `0 1 2` и `0 1 2`
- B: `0 1 2` и `3 3 3`
- C: `3 3 3` и `0 1 2`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: C

Из-за очереди событий в JavaScript, функция `setTimeout` вызывается _после_ того как цикл будет завершен. Так как переменная `i` в первом цикле была определена с помощью `var`, она будет глобальной. В цикле мы каждый раз увеличиваем значение `i` на `1`, используя унарный оператор `++`. К моменту выполнения функции `setTimeout` значение `i` будет равно `3` в первом примере.

Во втором цикле переменная `i` определена с помощью `let`. Такие переменные (а также `const`) имеют блочную область видимости (блок это что угодно между `{ }`). С каждой итерацией `i` будет иметь новое значение, и каждое значение будет замкнуто в своей области видимости внутри цикла.

</p>
</details>

---

### 2. Каким будет результат?

```javascript
const firstPromise = new Promise((res, rej) => {
  setTimeout(res, 500, "один");
});

const secondPromise = new Promise((res, rej) => {
  setTimeout(res, 100, "два");
});

Promise.race([firstPromise, secondPromise]).then((res) => console.log(res));
```

- A: `"один"`
- B: `"два"`
- C: `"два" "один"`
- D: `"один" "два"`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: B

Когда мы передаем несколько промисов методу `Promise.race`, он разрешает/отклоняет _первый_ промис, который разрешается/отклоняется. В метод `setTimeout` мы передаем таймер: 500 мс для первого промиса (`firstPromise`) и 100 мс для второго промиса (`secondPromise`). Это означает, что `secondPromise` разрешается первым со значением `'два'`. `res` теперь содержит значение `'два'`, которое выводиться в консоль.

</p>
</details>

---

### 2. Что будет в консоли?

```javascript
function output() {
  console.log("1");

  setTimeout(() => console.log("2"), 0);

  Promise.resolve().then(() => setTimeout(() => console.log("3"), 0));

  Promise.resolve().then(() => console.log("4"));

  console.log("5");
}

output();
```

<details><summary><b>Ответ</b></summary>
<p>
1, 5, 4, 2, 3
</p>
</details>

---

### 3. Какое значение будет на выходе?

```javascript
const myPromise = () => Promise.resolve("I have resolved!");

function firstFunction() {
  myPromise().then((res) => console.log(res));
  console.log("second");
}

async function secondFunction() {
  console.log(await myPromise());
  console.log("second");
}

firstFunction();
secondFunction();
```

- A: `I have resolved!`, `second` and `I have resolved!`, `second`
- B: `second`, `I have resolved!` and `second`, `I have resolved!`
- C: `I have resolved!`, `second` and `second`, `I have resolved!`
- D: `second`, `I have resolved!` and `I have resolved!`, `second`

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ: D

С обещанием мы в основном говорим: "Я хочу выполнить эту функцию и откладываю ее, пока она выполняется, поскольку это может занять некоторое время". Только когда определенное значение разрешено (или отклонено), и когда стек вызовов пуст, я хочу использовать это значение.

Мы можем получить это значение с помощью ключевого слова `.then` и `await` в функции `async`. Хотя мы можем получить значение обещания с помощью `.then` и `await`, они работают немного по-разному.

В `firstFunction` мы (вроде) отложили функцию `myPromise` во время ее работы, но продолжили выполнение другого кода, в данном случае `console.log ('second')`. Затем функция разрешается строкой `I have resolved`, которая затем логируется после того, как она увидела, что стек вызовов пуст.

Используя ключевое слово `await` в `secondFunction`, мы буквально приостанавливаем выполнение асинхронной функции до тех пор, пока значение не будет разрешено до перехода на следующую строку.

Это означает, что мы ожидали разрешения `myPromise` со значением `I have resolved`, и только когда это произошло, мы перешли к следующей строке: `second` была выведена в консоль последней.

</p>
</details>

---

## НАПИШИ КОД

### 1. Написать функцию, которая сортирует массив по возростанию

```javascript
const array = [8, 4, 5, 7, 2, 3];

function sort(arr) {
  return [];
}

console.log(sort(array));
```

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ

```javascript
function sort(arr) {
  return [...arr].sort((a, b) => a - b);
}
```

</p>
</details>

---

### 2. Написать функцию, которая отфильтрует нечетные числа массив и сортирует массив по возростанию

```javascript
const array = [8, 4, 5, 7, 2, 3];

function filterAndSort(arr) {
  return [];
}

console.log(filterAndSort(array));
```

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ

```javascript
function filterAndSort(arr) {
  return arr.filter((n) => n % 2 === 0).sort((a, b) => a - b);
}
```

</p>
</details>

---

### 3. Написать функцию, которая отфильтрует дубликаты в массиве

```javascript
const array = [8, 4, 4, 2, 2, 1];

function removeDuplicates(arr) {
  return [];
}

console.log(removeDuplicates(array));
```

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ

```javascript
function removeDuplicates(arr) {
  return [...new Set(arr)];
}
```

</p>
</details>

---

### 4. Написать функцию, которая преобразует массив в объект. Ключами результируещего объекта должны быть айдишники элементов массива, а значениемя элемент массива

```javascript
const array = [
  {
    id: "id_1",
    text: "text 1",
  },
  {
    id: "id_2",
    text: "text 2",
  },
  {
    id: "id_3",
    text: "text 3",
  },
];

function arrayToObject(arr) {
  return {};
}

console.log(arrayToObject(array));
/*
  {
    "id_1":{
      id: "id_1",
      text: "text 1"
    },
    "id_2":{
      id: "id_2",
      text: "text 2"
    },
    "id_3":{
      id: "id_3",
      text: "text 3"
    }
  }
*/
```

<details><summary><b>Ответ</b></summary>
<p>

#### Ответ

```javascript
function arrayToObject(arr) {
  return array.reduce( (acc, el)=>{
    acc[el.id] = el;
    return acc;
  }, {});
}
```

</p>
</details>

---

### 5. Напишите собственную реализацию метода map?

```js
function map(arr, mapCallback) {
  // проверяем переданные параметры
  if (!Array.isArray(arr) || !arr.length || typeof mapCallback !== "function") {
    return [];
  } else {
    let result = [];
    // мы создаем массив с результатами при каждом вызове функции
    // поскольку мы не хотим менять оригинальный массив
    for (let i = 0, len = arr.length; i < len; i++) {
      result.push(mapCallback(arr[i], i, arr));
      // помещаем результаты mapCallback в result
    }
    return result;
  }
}
```

Метод map создает новый массив с результатом вызова указанной функции для каждого элемента массива.

---

### 6. Напишите собственную реализацию метода reduce?

```js
function reduce(arr, reduceCallbak, initialValue) {
    // ..
    if (!Array.isArray(arr) || !arr.length || typeof filterCallback !== 'function') {
        return []
    } else {
        // если в функцию не было передано значения initialValue, то
        let hasInitialValue = initialValue !== undefined
        let value = hasInitialValue ? initialValue : arr[0]
        // мы будем использовать первый элемент initialValue

        // затем мы перебираем массив, начиная с 1, если в функцию не передавалось значения initialValue, либо с 0, если значение было передано
        for (let i = hasInitialValue ? 0 : 1, len = arr.length; i < len; i++) {
            // затем на каждой итерации мы присваиваем результат вызова reduceCallback переменной
            value = reduceCallback(value, arr[i], i, arr)
        }
        return value
    }
```

Метод reduce применяет функцию reducer к каждому элементу массива (слева-направо), возвращая одно результирующее значение.

---

# Вопросов по React

## ТЕОРИЯ React

### 1. Какую проблему решает React?

### 2. Особености метода setState

### 3. Методы жизненного цикла есть у классовых компонентов? В каких методах ЖЦ стоит выполнять запросы и подписки?

### 4. Какие возможности предлагает реакт для оптимизации рендеринга классовых и функциональных компонентов?

### 5. Рассказать про правила хуков

## Redux

### 1. Какую проблему решает Redux?

### 2. Какую состояние хранить в Redux?

### 3. Из каких сущностей состоит redux?

<details><summary><b>Ответ</b></summary>
<p>

actions, reducers, middleware

Рассказать о каждом подробнее

</p>
</details>

---

### 4. Что можно сказать про редьюсер, какие принципы в него заложены?

### 5. Что такое middleware?

### 5. Что такое селекторы и для чего они нужны?

## Redux Saga

### 1. Что такое Redux Saga?
