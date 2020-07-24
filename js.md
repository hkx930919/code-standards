# 变量声明

- 1.1 使用 `const` 或者 `let` 来定义变量，切优先使用`const`定义变量，当`const`不能胜任时才使用`let`。 不这样做将创建一个全局变量， 我们希望避免污染全局命名空间，eslint: `no-undef` `prefer-const`

```js
// bad
var count = 1;
var MAX = 10;
var MIN = 1;
var NUM = 5;
if (count > NUM) {
  count += MAX;
} else {
  count += MIN;
}

// good, use the let.
let count = 1;
const MAX = 10;
const MIN = 1;
const NUM = 5;
if (count > NUM) {
  count += MAX;
} else {
  count += MIN;
}
```

- 1.2 使用 `const` 或者 `let` 声明每一个变量。 eslint `one-var`
  > 为什么? 这样更容易添加新的变量声明，而且你不必担心是使用 ; 还是使用 , 或引入标点符号的差别。 你可以通过 debugger 逐步查看每个声明，而不是立即跳过所有声明

```js
// bad
const items = getItems(),
  goSportsTeam = true,
  dragonball = 'z';

// bad
// (compare to above, and try to spot the mistake)
const items = getItems(),
  goSportsTeam = true;
dragonball = 'z';

// good
const items = getItems();
const goSportsTeam = true;
const dragonball = 'z';
```

- 1.3 把 `const` 声明的放在一起，把 `let` 声明的放在一起。
  > 为什么? 这在后边如果需要根据前边的赋值变量指定一个变量时很有用。

```js
// bad
let i,
  len,
  dragonball,
  items = getItems(),
  goSportsTeam = true;

// bad
let i;
const items = getItems();
let dragonball;
const goSportsTeam = true;
let len;

// good
const goSportsTeam = true;
const items = getItems();
let dragonball;
let i;
let length;
```

- 1.4 在你需要的使用定义变量，但是要把它们放在一个合理的地方。
  > 什么? let 和 const 是块级作用域而不是函数作用域。

```js
// bad - 不必要的函数调用
function checkName(hasName) {
  const name = getName();

  if (hasName === 'test') {
    return false;
  }

  if (name === 'test') {
    this.setName('');
    return false;
  }

  return name;
}

// good
function checkName(hasName) {
  if (hasName === 'test') {
    return false;
  }

  const name = getName();

  if (name === 'test') {
    this.setName('');
    return false;
  }

  return name;
}
```

- 1.5 不要链式变量赋值。 eslint `no-multi-assign`
  > 为什么? 链式变量赋值会创建隐式全局变量。

```js
// bad
(function example() {
  // JavaScript 把它解释为
  // let a = ( b = ( c = 1 ) );
  // let 关键词只适用于变量 a ；变量 b 和变量 c 则变成了全局变量。
  let a = (b = c = 1);
})();

console.log(a); // throws ReferenceError
console.log(b); // 1
console.log(c); // 1

// good
(function example() {
  let a = 1;
  let b = a;
  let c = a;
})();

console.log(a); // throws ReferenceError
console.log(b); // throws ReferenceError
console.log(c); // throws ReferenceError

// 对于 `const` 也一样
```

- 1.6 避免使用不必要的递增和递减 (++, --)。 eslint `no-plusplus`
  > 什么? 在 eslint 文档中，一元递增和递减语句以自动分号插入为主题，并且在应用程序中可能会导致默认值的递增或递减。它还可以用像`num += 1`这样的语句来改变您的值，而不是使用 `num++` 或 `num ++` 。不允许不必要的增量和减量语句也会使您无法预先递增/预递减值，这也会导致程序中的意外行为。

```js
// bad

const array = [1, 2, 3];
let num = 1;
num++;
--num;

let sum = 0;
let truthyCount = 0;
for (let i = 0; i < array.length; i++) {
  let value = array[i];
  sum += value;
  if (value) {
    truthyCount++;
  }
}

// good

const array = [1, 2, 3];
let num = 1;
num += 1;
num -= 1;

const sum = array.reduce((a, b) => a + b, 0);
const truthyCount = array.filter(Boolean).length;
```

- 1.7 避免在赋值语句 `=`前后换行。如果你的代码违反了 `max-len`， 使用括号包裹。 eslint `operator-linebreak`

```js
// todo 代码自动格式化了，等写完后再处理
// bad
const foo = superLongLongLongLongLongLongLongLongFunctionName();

// bad
const foo = 'superLongLongLongLongLongLongLongLongString';

// good
const foo = superLongLongLongLongLongLongLongLongFunctionName();

// good
const foo = 'superLongLongLongLongLongLongLongLongString';
```

# 对象

- 2.1 使用字面语法来创建对象。 eslint `no-new-object`

```js
// bad
const item = new Object();

// good
const item = {};
```

- 2.2 在创建具有动态属性名称的对象时使用计算属性名。
  > 为什么? 它允许你在一个地方定义对象的所有属性。

```js
function getKey(k) {
  return `a key named ${k}`;
}

// bad
const obj = {
  id: 5,
  name: 'San Francisco',
};
obj[getKey('enabled')] = true;

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
};
```

- 2.3 使用对象方法的缩写。 eslint `object-shorthand`

```js
// bad
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```

- 2.4 使用属性值的缩写。 eslint `object-shorthand`
  > 为什么? 它的写法和描述较短。

```js
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```

- 2.5 在对象声明的时候将简写的属性进行分组。

```js
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

- 2.6 只使用引号标注无效标识符的属性。 eslint: `quote-props`
  > 为什么? 总的来说，我们认为这样更容易阅读。 它提升了语法高亮显示，并且更容易通过许多 JS 引擎优化。

```js
// bad
const bad = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

- 2.7 不能直接调用 `Object.prototype` 的方法，如： `hasOwnProperty` 、 `propertyIsEnumerable` 和 `isPrototypeOf。`
  > 为什么? 这些方法可能被一下问题对象的属性追踪 - 相应的有 { hasOwnProperty: false } - 或者，对象是一个空对象 (Object.create(null))。

```js
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // 在模块范围内的缓存中查找一次
/* or */
import has from 'has'; // https://www.npmjs.com/package/has
// ...
console.log(has.call(object, key));
```

- 2.8 更喜欢对象扩展操作符，而不是用 `Object.assign` 浅拷贝一个对象。 使用对象的 `rest` 操作符来获得一个具有某些属性的新对象。

```js
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // 变异的 `original` ಠ_ಠ
delete copy.a; // 这....

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

# 数组

- 3.1 使用字面语法创建数组。 eslint `no-array-constructor`

```js
// bad
const items = new Array();

// good
const items = [];
```

- 3.2 使用 `Array#push` 取代直接赋值来给数组添加项。

```js
const someStack = [];

// bad
someStack[someStack.length] = 'abracadabra';

// good
someStack.push('abracadabra');
```

- 3.3 使用数组展开方法 `...` 来拷贝数组。

```js
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

- 3.4 将一个类数组对象转换成一个数组， 使用展开方法 `...` 代替 `Array.from`。

```js
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

- 3.5 对于对迭代器的映射，使用 `Array.from` 替代展开方法 `...` ， 因为它避免了创建中间数组。

```js
// bad
const baz = [...foo].map(bar);

// good
const baz = Array.from(foo, bar);
```

- 3.6 在数组回调方法中使用 `return` 语句。 如果函数体由一个返回无副作用的表达式的单个语句组成，那么可以省略返回值。eslint `array-callback-return`

```js
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => x + 1);

// bad - 没有返回值，意味着在第一次迭代后 `acc` 没有被定义
[
  [0, 1],
  [2, 3],
  [4, 5],
].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
  acc[index] = flatten;
});

// good
[
  [0, 1],
  [2, 3],
  [4, 5],
].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
  acc[index] = flatten;
  return flatten;
});

// bad
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});

// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }

  return false;
});
```

# 解构

- 4.1 在访问和使用对象的多个属性的时候使用对象的解构。 eslint: `prefer-destructuring`
  > 为什么? 解构可以避免为这些属性创建临时引用。

```js
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

- 4.2 使用数组解构。 eslint: `prefer-destructuring`

```js
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```

- 4.3 对于多个返回值使用对象解构，而不是数组解构。
  > 为什么? 你可以随时添加新的属性或者改变属性的顺序，而不用修改调用方。

```js
// bad
function processInput(input) {
  // 处理代码...
  return [left, right, top, bottom];
}

// 调用者需要考虑返回数据的顺序。
const [left, __, top] = processInput(input);

// good
function processInput(input) {
  // 处理代码...
  return { left, right, top, bottom };
}

// 调用者只选择他们需要的数据。
const { left, top } = processInput(input);
```

# 字符串

- 5.1 当以编程模式构建字符串时，使用字符串模板代替字符串拼接。 eslint: `prefer-template` `template-curly-spacing`
  > 为什么? 字符串模板为您提供了一种可读的、简洁的语法，具有正确的换行和字符串插值特性。

```js
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// bad
function sayHi(name) {
  return `How are you, ${name}?`;
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

- 5.2 不要在字符串上使用 `eval()` ，它打开了太多漏洞。 eslint: `no-eval`
- 5.3 不要转义字符串中不必要的字符。 eslint: `no-useless-escape`
  > 为什么? 反斜杠损害了可读性，因此只有在必要的时候才会出现。

```js
// bad
const foo = '\'this\' is "quoted"';

// good
const foo = '\'this\' is "quoted"';
const foo = `my name is '${name}'`;
```

# 函数

- 6.1 使用命名的函数表达式代替函数声明。 eslint: `func-style`
- 6.2 切记不要在非功能块中声明函数 (if, while, 等)。 将函数赋值给变量。 浏览器允许你这样做，但是他们都有不同的解释，这是个坏消息。 eslint: `no-loop-func`
- 6.3 永远不要定义一个参数为 `arguments`。 这将会优先于每个函数给定范围的 `arguments` 对象。

```js
// bad
function foo(name, options, arguments) {
  // ...
}

// good
function foo(name, options, args) {
  // ...
}
```

- 6.4 不要使用 `arguments`, 选择使用 rest 语法 `...` 代替。 eslint: `prefer-rest-params`
  > 为什么? ... 明确了你想要拉取什么参数。 更甚, rest 参数是一个真正的数组，而不仅仅是类数组的 arguments 。

```js
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```

- 6.5 使用默认的参数语法，而不是改变函数参数。

```js
// really bad
function handleThings(opts) {
  // No! We shouldn’t mutate function arguments.
  // Double bad: if opts is falsy it'll be set to an object which may
  // be what you want but it can introduce subtle bugs.
  opts = opts || {};
  // ...
}

// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
  // ...
}

// good
function handleThings(opts = {}) {
  // ...
}
```

- 6.6 避免使用默认参数的副作用。\
  > 为什么? 他们很容易混淆。

```js
var b = 1;
// bad
function count(a = b++) {
  console.log(a);
}
count(); // 1
count(); // 2
count(3); // 3
count(); // 3
```

- 6.7 总是把默认参数放在最后。

```js
// bad
function handleThings(opts = {}, name) {
  // ...
}

// good
function handleThings(name, opts = {}) {
  // ...
}
```

- 6.8 永远不要使用函数构造器来创建一个新函数。 eslint: `no-new-func`
  > 为什么? 以这种方式创建一个函数将对一个类似于 eval() 的字符串进行计算，这将打开漏洞。

```js
// bad
var add = new Function('a', 'b', 'return a + b');

// still bad
var subtract = Function('a', 'b', 'return a - b');
```

- 6.9 函数签名中的间距。eslint: `space-before-function-paren` `space-before-blocks`
  > 为什么? 一致性很好，在删除或添加名称时不需要添加或删除空格。

```js
// bad
const f = function () {};
const g = function () {};
const h = function () {};

// good
const x = function () {};
const y = function a() {};
```

- 6.10 不要再分配参数。 eslint: `no-param-reassign`
  > 为什么? 重新分配参数会导致意外的行为，尤其是在访问 arguments 对象的时候。 它还可能导致性能优化问题，尤其是在 V8 中。

```js
// bad
function f1(a) {
  a = 1;
  // ...
}

function f2(a) {
  if (!a) {
    a = 1;
  }
  // ...
}

// good
function f3(a) {
  const b = a || 1;
  // ...
}

function f4(a = 1) {
  // ...
}
```

- 6.11 优先使用扩展运算符 `...` 来调用可变参数函数。 eslint: `prefer-spread`
  > 为什么? 它更加干净，你不需要提供上下文，并且你不能轻易的使用 apply 来 new 。

```js
// bad
const x = [1, 2, 3, 4, 5];
console.log.apply(console, x);

// good
const x = [1, 2, 3, 4, 5];
console.log(...x);

// bad
new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]))();

// good
new Date(...[2016, 8, 5]);
```

- 6.12 具有多行签名或者调用的函数应该像本指南中的其他多行列表一样缩进：在一行上只有一个条目，并且每个条目最后加上逗号。 eslint: `function-paren-newline`

```
// bad
function foo(bar,
             baz,
             quux) {
  // ...
}

// good
function foo(
  bar,
  baz,
  quux,
) {
  // ...
}

// bad
console.log(foo,
  bar,
  baz);

// good
console.log(
  foo,
  bar,
  baz,
);
```
