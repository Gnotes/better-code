# easy-code

写出简洁的JS代码 [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

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
