# easy-code

写出简洁的JS代码   
[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)  
[airbnb / javascript](https://github.com/airbnb/javascript)  

_见名知意_

`bad`
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

`better`
```javascript
const yearMonthDay = moment().format('YYYY/MM/DD'); // 更清晰表达年月日的概念
```

_同一类型变量使用相同单词表示_

`bad`
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

`better`
```javascript
getUser();  // 都表示User这个数据
```

_变量名易于阅读和检索_

`bad`
```javascript
// What the heck is 525600 for?
for (let i = 0; i < 525600; i++) {
  runCronJob();
}
```

`better`
```javascript
const MINUTES_IN_A_YEAR = 525600;
for (let i = 0; i < MINUTES_IN_A_YEAR; i++) {   // 明确一年有多少分钟的意思
  runCronJob();
}
```
_说明性变量_

`bad`
```javascript
const cityStateRegex = /^(.+)[,\\s]+(.+?)\s*(\d{5})?$/;
saveCityState(cityStateRegex.match(cityStateRegex)[1], cityStateRegex.match(cityStateRegex)[2]);
```

`better`
```javascript
const cityStateRegex = /^(.+)[,\\s]+(.+?)\s*(\d{5})?$/;
const match = cityStateRegex.match(cityStateRegex)
const city  = match[1];       // match[1] 表示city
const state = match[2];       // match[]2 表示state
saveCityState(city, state);
```
_具体化变量_

`bad`
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

`better`
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  // ...
  dispatch(location); // 明确了变量作用
});
```

_避免冗余_

`bad`
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

`better`
```javascript
const Car = {
  make: 'Honda',      // 减少累赘
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red'; // 减少累赘
}
```

_巧用逻辑或逻辑与_

`bad`
```javascript
function createMicrobrewery(name) {
  let breweryName;
  if (name) {
    breweryName = name;
  } else {
    breweryName = 'Hipster Brew Co.';
  }
}

```

`better`
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.'
}
```

`bad`
```javascript
function showIndex(callback) {
  if(callback) callback()
}

```

`better`
```javascript
function showIndex(callback) {
  callback && callback()
}
```

`bad`
```javascript
function calculateRate(score) {
  let rate;
  if(score < 60) rate = '😢';
  else rate = '😁'
}

```

`better`
```javascript
function calculateRate(score) {
  let rate = score < 60 ? '😢' : '😁';
}
```

_参数个数少为优_

`bad`
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

```

`better`
```javascript
const menuConfig = {
  title       : 'Foo',
  body        : 'Bar',
  buttonText  : 'Baz',
  cancellable : true
}

function createMenu(config) {
  // ...
}
```

_函数功能单一职责_

`bad`
```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}

```

`better`
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

_函数名语义明确_

`bad`
```javascript
function dateAdd(date, month) {
  // ...
}

const date = new Date();

// It's hard to to tell from the function name what is added
dateAdd(date, 1);
```

`better`
```javascript
function dateAddMonth(date, month) {
  // ...
}

const date = new Date();
dateAddMonth(date, 1);
```

_函数应只封装一层_

`bad`
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    })
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  })
}
```

`better`
```javascript
function tokenize(code) { // 抽离一层方法只关注一件事
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    })
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) { // 
  const tokens = tokenize(code);
  const ast    = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  })
}
```
_删除重复代码_

`bad`
```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary: expectedSalary,
      experience: experience,
      githubLink: githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary: expectedSalary,
      experience: experience,
      portfolio: portfolio
    };

    render(data);
  });
}
```

`better`
```javascript
function showList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary: expectedSalary,
      experience: experience,
      portfolio: portfolio
    };

    render(data);
  });
}

```

_默认值优于短路判断_

`bad`
```javascript
function writeForumComment(subject, body) {
  subject = subject || 'No Subject';
  body = body || 'No text';
}
```

`better`
```javascript
function writeForumComment(subject = 'No subject', body = 'No text') {
  // ...
}
```

_合并默认值优于短路判断_

`bad`
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
}

function createMenu(config) {
  config.title = config.title || 'Foo'
  config.body = config.body || 'Bar'
  config.buttonText = config.buttonText || 'Baz'
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;

}

createMenu(menuConfig);
```

`better`
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
}

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);

```
_避免附带影响_

`bad`
```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

`better`
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott'
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
_避免全局对象修改_

`bad`
```javascript
Array.prototype.diff = function(comparisonArray) {
  const values = [];
  const hash = {};

  for (const i of comparisonArray) {
    hash[i] = true;
  }

  for (const i of this) {
    if (!hash[i]) {
      values.push(i);
    }
  }

  return values;
}
```

`better`
```javascript
class SuperArray extends Array {
  constructor(...args) {
    super(...args);
  }

  diff(comparisonArray) {
    const values = [];
    const hash = {};

    for (const i of comparisonArray) {
      hash[i] = true;
    }

    for (const i of this) {
      if (!hash[i]) {
        values.push(i);
      }
    }

    return values;
  }
}
```
_衷于函数式编程_

`bad`
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

`better`
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, 0);
```
_简化条件_

`bad`
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

`better`
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
_避免取反条件判断_

`bad`
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

`better`
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
_减少参数类型检查_

`bad`
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.peddle(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

`better`
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```

`bad`
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

`better`
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

_善用setter getter_

- When you want to do more beyond getting an object property, you don't have to look up and change every accessor in your codebase.
Makes adding validation simple when doing a set.
- Encapsulates the internal representation.
- Easy to add logging and error handling when getting and setting.
- Inheriting this class, you can override default functionality.
- You can lazy load your object's properties, let's say getting it from a server.

`bad`
```javascript
class BankAccount {
  constructor() {
    this.balance = 1000;
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= 100;
```

`better`
```javascript
class BankAccount {
  constructor(balance = 1000) {
    this._balance = balance;
  }

  // It doesn't have to be prefixed with `get` or `set` to be a getter/setter
  set balance(amount) {
    if (verifyIfAmountCanBeSetted(amount)) {
      this._balance = amount;
    }
  }

  get balance() {
    return this._balance;
  }

  verifyIfAmountCanBeSetted(val) {
    // ...
  }
}

const bankAccount = new BankAccount();

// Buy shoes...
bankAccount.balance -= shoesPrice;

// Get balance
let balance = bankAccount.balance;
```

_es6类优于es5函数构造_

`bad`
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

`better`
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```

_巧用链式方法_

`bad`
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

`better`
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```

_Async/Await比Promises更简洁_

`bad`
```javascript
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });
```

`better`
```javascript
async function getCleanCodeArticle() {
  try {
    const request = await require('request-promise');
    const response = await request.get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    const fileHandle = await require('fs-promise');

    await fileHandle.writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```

_注释也许用在刀刃上_

`bad`
```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

`better`
```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

_引用对象使用const定义_

> Why? This ensures that you can't reassign your references, which can lead to bugs and difficult to comprehend code.

`bad`
```javascript
var a = 1;
var b = 2;
```

`better`
```javascript
const a = 1;
const b = 2;
```

_let 优于 var_

> let是块作用域，var是函数作用域

`bad`
```javascript
var count = 1;
if (true) {
  count += 1;
}
```

`better`
```javascript
let count = 1;
if (true) {
  count += 1;
}
```

_es6 类函数声明简写_

`bad`
```javascript
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};
```

`better`
```javascript
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};
```

_es6 属性声明简写_

`bad`
```javascript
const obj = {
  lukeSkywalker: lukeSkywalker,
};
```

`better`
```javascript
const obj = {
  lukeSkywalker,
};
```

_es6 属性简写放在前面_

`bad`
```javascript
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};
```

`better`
```javascript
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

_es6 ...扩展优于Object.assign()_

`bad`
```javascript
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 });
```

`better`
```javascript
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 };
```

_数组使用扩展符... 合并_

`bad`
```javascript
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}
```

`better`
```javascript
const itemsCopy = [...items];
```
_善用es6解构_

`bad`
```javascript
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}
```


`better`
```javascript
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}
```

`nice`
```javascript
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```
_字符串定义使用单引号_

`bad`
```javascript
const name = "Capt. Janeway";
```

`better`
```javascript
const name = 'Capt. Janeway';

// template literals should contain interpolation or newlines
const name = `Capt. Janeway`;
```
_字符拼接使用`_

`bad`
```javascript
'How are you, ' + name + '?'
```

`better`
```javascript
`How are you, ${name}?`
```
_import引入多个属性分行显示_

`bad`
```javascript
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';
```

`better`
```javascript
import {
  longNameA,
  longNameB,
  longNameC,
  longNameD,
  longNameE,
} from 'path';
```
_属性访问使用`.`_

`bad`
```javascript
const luke = {
  jedi: true,
  age: 28,
};

const isJedi = luke['jedi'];
```

`better`
```javascript
const luke = {
  jedi: true,
  age: 28,
};

const isJedi = luke.jedi;
```

_每一个常量都使用const分开声明_

`bad`
```javascript
const items = getItems(),
    goSportsTeam = true,
    dragonball = 'z';
```

`better`
```javascript
const items = getItems();
const goSportsTeam = true;
const dragonball = 'z';
```
_避免多余表达式_

`bad`
```javascript
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;
```

`better`
```javascript
const foo = a || b;
const bar = !!c;
const baz = !c;
```
_使用标记_

`better`
```javascript
// FIXME
// TODO
```