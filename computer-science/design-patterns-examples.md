# 23种设计模式的完整例子和分类

## 几个重要概念

#### 设计模式的四个要素：
模式名、问题、解决方案、效果


#### 面向对象设计的因素：
封装、粒度、依赖关系、灵活性、性能、演化、复用等。

#### 接口：
对象声明的每一个操作指定操作名、作为参数的对象和返回值，是所谓的操作的[**型构**]。对象操作所定义的所有操作型构的集合被称为该对象的接口。

#### 多态：
为不同数据类型的实现提供统一的接口。

#### 面向对象设计的原则：
- a)针对接口编程，而不是针对实现编程 
- b) 优先使用对象组合，而不是类继承(组合和继承也经常同时使用)

#### 鸭子类型：
只关心对象的行为，不关心对象本身（起源：意大利软件工程师、Python软件基金会研究员Alex Martelli 于2000年左右在Python的邮件组中最早将这个概念引入了程序设计范畴中。）
    


## 分类

| 创建型 | 结构型 | 行为型 |
| --- | --- | --- |
| `抽象工厂` | `适配器` | `职责链` |
| `生成器` | `桥接` | `命令` |
| `工厂方法` | `组合` | `解释器` |
| `原型` | `装饰` | `迭代器` |
| `单例/单件` | `外观` | `中介者` |
| - | `享元` | `备忘录` |
| - | `代理` | `观察者` |
| - | - | `状态` |
| - | - | `策略` |
| - | - | `模板方法` |
| - | - | `访问者` |

## 目录

 - [Singleton](#Singleton)
 - [Abstract Factory](#Abstract-Factory)
 - [Builder](#Builder)
 - [Factory Method](#Factory-Method)
 - [Prototype](#Prototype)
 - [Adapter](#Adapter)
 - [Bridge](#Bridge)
 - [Composite](#Composite)
 - [Decorator](#Decorator)
 - [Facade](#Facade)
 - [Flyweight](#Flyweight)
 - [Proxy](#Proxy)
 - [Chain of Responsibility](#Chain-of-Responsibility)
 - [Command](#Command)
 - [Interpreter](#Interpreter)
 - [Iterator](#Iterator)
 - [Mediator](#Mediator)
 - [Memento](#Memento)
 - [Observer](#Observer)
 - [State](#State)
 - [Strategy](#Strategy)
 - [Template Method](#Template-Method)
 - [Visitor](#Visitor)

## Singleton
**● 中文名 ●**
 
单例

**● 可变的方面 ●** 

一个类的唯一实例（提供一个访问它的全局访问点）


**● 使用场景 ●** 

当我创建了一个JS库，为了方便全站使用，并且有一个醒目的调用名字，可以把它导出为一个唯一的实例，这个实例可以是惰性单例(是指在需要的时候才创建,即使用new关键字)，但是我常常会直接把工具类的单例new之后再导出，这样就可以在脚本中直接调用。

重要特点是每次请求只能创建一个实例。



```js
const Singleton = (function () {
    let instance;

    function createInstance() {
        const object = new Object("I am the instance");
        return object;
    }

    return {
        getInstance: function () {
            if (!instance) {
                instance = createInstance();
            }
            return instance;
        }
    };
})();

function run() {
    const instance1 = Singleton.getInstance();
    const instance2 = Singleton.getInstance();
    console.log("Same instance? " + (instance1 === instance2));  
}
run();  // Same instance? true
```


## Abstract Factory
**● 中文名 ●**

抽象工厂

**● 可变的方面 ●** 

产品对象家族（创建相关或相互依赖的结构，无需指定具体类）

**● 使用场景 ●** 

不在 JavaScript 中直接使用,  而是声明一个用于创建产品的接口。


```js
function Employee(name) {
    this.name = name;

    this.say = function () {
        console.log("I am employee " + name);
    };
}

function EmployeeFactory() {

    this.create = function (name) {
        return new Employee(name);
    };
}

function Vendor(name) {
    this.name = name;

    this.say = function () {
        console.log("I am vendor " + name);
    };
}

function VendorFactory() {

    this.create = function (name) {
        return new Vendor(name);
    };
}


function run() {
    const persons = [];
    const employeeFactory = new EmployeeFactory();
    const vendorFactory = new VendorFactory();

    persons.push(employeeFactory.create("Joan DiSilva"));
    persons.push(employeeFactory.create("Tim O'Neill"));
    persons.push(vendorFactory.create("Gerald Watson"));
    persons.push(vendorFactory.create("Nicole McNight"));

    for (let i = 0, len = persons.length; i < len; i++) {
        persons[i].say();
    }
}
run();
/*
I am employee Joan DiSilva
I am employee Tim O'Neill
I am vendor Gerald Watson
I am vendor Nicole McNight
*/

```


## Builder
**● 中文名 ●**

创建者

**● 可变的方面 ●** 

如何创建一个组合对象

**● 使用场景 ●** 

不在 JavaScript 中直接使用,  builder用于声明用于创建复杂产品的多个接口（也可以说成需要多个步骤才能创建出一个复杂的产品）。

```js
class Shop {
    init(builder) {
        builder.step1();
        builder.step2();
        return builder.get();
    }
}

function CarBuilder() {
    this.car = null;

    this.step1 = () => this.car = new Car();
    this.step2 = () => this.car.addParts();
    this.get = () => this.car;
}

function TruckBuilder() {
    this.truck = null;

    this.step1 = () => this.truck = new Truck();
    this.step2 = () => this.truck.addParts();
    this.get = () => this.truck;
}

function Car() {
    this.doors = 0;

    this.addParts = () => this.doors = 4;
    this.say = () => console.log("I am a " + this.doors + "-door car");
}

function Truck() {
    this.doors = 0;

    this.addParts = () => this.doors = 2;
    this.say = () => console.log("I am a " + this.doors + "-door truck");
}

function run() {
    const shop = new Shop();
    const carBuilder = new CarBuilder();
    const truckBuilder = new TruckBuilder();
    const car = shop.init(carBuilder);
    const truck = shop.init(truckBuilder);

    car.say();
    truck.say();
}
run();
/*
I am a 4-door car
I am a 2-door truck
*/
```


## Factory Method 
**● 中文名 ●**

工厂方法

**● 可变的方面 ●** 

被实例化的子类（定义一个用于创建对象的接口，让子类决定将哪一个类实例化）


**● 使用场景 ●** 

Factory创建新产品的“工厂”对象, 所有产品都支持相同的接口（属性和方法）。


```js
const Factory = function () {
    this.createEmployee = function (type) {
        let employee;

        switch(type) {
            case 'fulltime' :
                employee = new FullTime();
                break;
            case 'parttime' :
            employee = new PartTime();
            break;

        }

        employee.type = type;
        employee.say = function () {
            console.log(this.type + ": rate " + this.hourly + "/hour");
        }

        return employee;
    }
}

const FullTime = function () {
    this.hourly = "$25";
};

const PartTime = function () {
    this.hourly = "$8";
};


function run() {

    const employees = [];
    const factory = new Factory();

    employees.push(factory.createEmployee("fulltime"));
    employees.push(factory.createEmployee("parttime"));

    for (let i = 0, len = employees.length; i < len; i++) {
        employees[i].say();
    }
}
run();
/*
fulltime: rate $25/hour
parttime: rate $8/hour
*/
```



## Prototype 
**● 中文名 ●**

原型

**● 可变的方面 ●** 

被实例化的类


**● 使用场景 ●** 

可以通过原型克隆来创建一个新对象。

```js
class CustomerPrototype {
    constructor(product) {
        this.productAttrs = product;
    }

    clone() {
        const customer = new Customer();
        customer.first = this.productAttrs.first;
        customer.last = this.productAttrs.last;
        customer.status = this.productAttrs.status;

        return customer;
    }
    
}


function Customer(first, last, status) {

    this.first = first;
    this.last = last;
    this.status = status;

    this.say = function () {
        return `name: ${this.first} ${this.last}, status: ${this.status}`;
    };
}

function run() {

    const product = new Customer("F", "L", "pending");
    console.log( 'product: ', product.say() );

    //
    const newProduct = new CustomerPrototype(product);
    const customer = newProduct.clone();
    console.log( 'newProduct: ', customer.say() );
    
}
run();
/*
product:  name: F L, status: pending
newProduct:  name: F L, status: pending
*/
```



## Adapter 
**● 中文名 ●**

适配器

**● 可变的方面 ●** 

对象的接口（将一个类的接口转化为另一个解决兼容性）

**● 使用场景 ●** 

客户端调用适配器请求服务后，实现客户期望的接口。同样的输入，可以变成不同的输出或者界面。


```js
// old interface
function Shipping() {
    this.request = function (weight) {
        return parseFloat(weight) * 1.5;
    }
}

// new interface
function AdvancedShipping() {
    this.login = function (credentials) { /* ... */ };
    this.calculate = function (weight) { 
        return parseFloat(weight) * 2.5;
    };
}

// adapter interface
function ShippingAdapter(credentials) {
    const shipping = new AdvancedShipping();

    shipping.login(credentials);

    return {
        request: function (weight) {
            return shipping.calculate(weight);
        }
    };
}

function run() {

    const shipping = new Shipping();
    const credentials = { token: "30a8-6ee1" };
    const adapter = new ShippingAdapter(credentials);

    let cost = shipping.request("2kg");
    console.log("Old cost: $" + cost);

    //
    cost = adapter.request("2kg");
    console.log("New cost: $" + cost);
}
run();
/*
Old cost: $3
New cost: $5
*/

```



## Bridge 
**● 中文名 ●**

桥接

**● 可变的方面 ●** 

对象的实现（抽象与实现分离）


**● 使用场景 ●** 

不在 JavaScript 中直接使用，实现和扩展抽象定义的接口。（属性和方法相同，可能存在接口覆盖，改变其输出的结果）

```js
// input devices
const EventGestures = function (output) {
    this.output = output;

    this.tap = function () { this.output.click(); }
    this.swipe = function () { this.output.move(); }
};

// output devices
const Screen = function () {
    this.click = function () { console.log("Screen select"); }
    this.move = function () { console.log("Screen move"); }
};

function run() {
    const screen = new Screen();
    const hand = new EventGestures(screen);

    hand.tap();
    hand.swipe();
}

run();
/*
Screen select
Screen move
*/

```


## Composite 
**● 中文名 ●**

组合

**● 可变的方面 ●** 

一个对象的结构和组成（单个对象和组合对象的使用一致性）

**● 使用场景 ●** 

使用Node(结点)来表示组合中的分支（或子树），维护子组件的集合。

```js
class Node {
    constructor(name) {
        this.children = [];
        this.name = name;
    }

    add(child) {
        this.children.push(child);
    }
    remove(child) {
        const length = this.children.length;
        for (let i = 0; i < length; i++) {
            if (this.children[i] === child) {
                this.children.splice(i, 1);
                return;
            }
        }
    }
    getChild(i) {
        return this.children[i];
    }

}


function run() {
    const tree = new Node("root");
    const left = new Node("left")
    const right = new Node("right");

    tree.add(left);
    tree.add(right);
    tree.remove(right);
    tree.add(right);

    console.log(tree);

}

run();
/*
{
    "children": [
        {
            "children": [],
            "name": "left"
        },
        {
            "children": [],
            "name": "right"
        }
    ],
    "name": "root"
}
*/
```



## Decorator 
**● 中文名 ●**

装饰

**● 可变的方面 ●** 

对象的职责，不生成子类（动态添加职责）

**● 使用场景 ●** 

定义一个新的接口(符合原始组件的接口)，来扩展原组件的功能。

```js
const User = function (name) {
    this.name = name;

    this.say = function () {
        console.log("User: " + this.name);
    };
}

const DecoratedUser = function (user, street, city) {
    this.user = user;
    this.name = user.name;  // ensures interface stays the same
    this.street = street;
    this.city = city;

    this.say = function () {
        console.log("Decorated User: " + this.name + ", " +
            this.street + ", " + this.city);
    };
}

function run() {

    const user = new User("Kelly");
    user.say();

    const decorated = new DecoratedUser(user, "Broadway", "New York");
    decorated.say();
}
run();
/*
User: Kelly
Decorated User: Kelly, Broadway, New York
*/
```



## Facade 
**● 中文名 ●**

外观

**● 可变的方面 ●** 

一个子系统的接口（定义一个高层接口，一致的界面）

**● 使用场景 ●** 

将目标请求委托给适当的子系统对象(例如：A对象传入，分别用函数a,b,c来运行A对象)，实现并执行专门的子系统功能，得出不同的结果。

```js
const Mortgage = function (name) {
    this.name = name;
}

Mortgage.prototype = {

    applyFor: function (amount) {
        // access multiple subsystems...
        const status = "approved";
        if (!new Bank().verify(this.name, amount)) {
            status = "denied";
        } else if (!new Credit().get(this.name)) {
            status = "denied";
        } else if (!new Background().check(this.name)) {
            status = "denied";
        }
        return `name: ${this.name}, status:  ${status}, amount: ${amount}`;
    }
}

const Bank = function () {
    this.verify = function (name, amount) {
        // complex logic ...
        return true;
    }
}

const Credit = function () {
    this.get = function (name) {
        // complex logic ...
        return true;
    }
}

const Background = function () {
    this.check = function (name) {
        // complex logic ...
        return true;
    }
}

function run() {
    const mortgage = new Mortgage("David");
    const result = mortgage.applyFor("$100,000");

    console.log(result);
}
run();  // name: David, status:  approved, amount: $100,000

```


## Flyweight 
**● 中文名 ●**

享元

**● 可变的方面 ●** 

对象的存储开销（共享技术）

**● 使用场景 ●** 

调用享元工厂获取享元对象(享元工厂内可能会有多个对象)，根据对象是否存在，来维护要在应用程序之间共享的内部数据（可以输出数据，也可以处理数据）。也就是将原数据传递下去，共用，分析和处理它们的异同，根据异同来输出不同的结果，于此同时，不需要在其它函数上再次录入新的数据或者原数据。

```js
const Flyweight = function(make, model, processor) {
    this.make = make;
    this.model = model;
    this.processor = processor;
};


class FlyWeightFactory {
    static data = {};

    static get(make, model, processor) {
        if ( this.data[`${make}-${model}`] === undefined ) {
            this.data[`${make}-${model}`] = new Flyweight(make, model, processor);
        }
        return this.data[`${make}-${model}`];
    }

    static getCount() {
        let count = 0;
        for (const f in this.data) count++;
        return count;
    }
}

function ComputerCollection() {
    const computers = {};
    let count = 0;

    
    const Computer = function (make, model, processor, memory, tag) {
        this.flyweight = FlyWeightFactory.get(make, model, processor);
        this.memory = memory;
        this.tag = tag;
        this.getMake = function () {
            return this.flyweight.make;
        }
        // ...
    };

    return {
        add: function (make, model, processor, memory, tag) {
            computers[tag] = new Computer(make, model, processor, memory, tag);
            count++;
        },

        getCount: function () {
            return count;
        }
    };
}



function run() {
    const computers = new ComputerCollection();

    computers.add("Dell", "XPS", "Intel", "5G", "Y755P");
    computers.add("Dell", "XPS", "Intel", "6G", "X997T");
    computers.add("HP", "Envy", "Intel", "4G", "CNU883701");

    console.log("Computers: " + computers.getCount());
    console.log("Flyweights: " + FlyWeightFactory.getCount());
}
run();
/*
Computers: 3
Flyweights: 2
*/
```


## Proxy 
**● 中文名 ●**

代理

**● 可变的方面 ●** 

如何访问一个对象；该对象的位置

**● 使用场景 ●** 

客户端调用代理请求操作，代理提供了最后的结果，并维护一个允许代理访问真实对象的引用。代理会处理各种请求，并转发到真正的对象中。

代理缓存了频繁请求，如果对象未被缓存则执行真正的服务并将结果存储在缓存中。

```js
function RealOperation() {
    this.getLatLng = function (address) {
        switch (address) {
            case 'Amsterdam' : return "52.3700° N, 4.8900° E";
            case 'London' : return "51.5171° N, 0.1062° W";
            default : return '';
        }
    };
}

function GeoProxy() {
    const geocoder = new RealOperation();
    const geocache = {};

    return {
        getLatLng: function (address) {
            if (!geocache[address]) {
                geocache[address] = geocoder.getLatLng(address);
            }
            console.log(address + ": " + geocache[address]);
            return geocache[address];
        },
        getCount: function () {
            let count = 0;
            for (const code in geocache) { count++; }
            return count;
        }
    };
};

function run() {

    const geo = new GeoProxy();

    geo.getLatLng("Paris");
    geo.getLatLng("London");
    geo.getLatLng("Amsterdam");
    geo.getLatLng("Amsterdam");
    geo.getLatLng("London");
    geo.getLatLng("London");
    geo.getLatLng("London");

    console.log("\nCache size: " + geo.getCount());

}
run();
/*

Paris: 
London: 51.5171° N, 0.1062° W
Amsterdam: 52.3700° N, 4.8900° E
Amsterdam: 52.3700° N, 4.8900° E
London: 51.5171° N, 0.1062° W
London: 51.5171° N, 0.1062° W
London: 51.5171° N, 0.1062° W

Cache size: 3
*/
```



## Chain of Responsibility 
**● 中文名 ●**

职责链

**● 可变的方面 ●** 

满足一个请求的对象（解除请求的发送者和接收者之间的耦合，使多个对象都有机会处理这个请求）

**● 使用场景 ●** 

就算很常用的原型链的写法，一个关键点就是方法中的 return, 要返回这个对象，这样才能形成后继的结果链。

```js
const Request = function (amount) {
    this.amount = amount;
    console.log("Requested: $" + amount + "\n");
}

Request.prototype = {
    get: function (bill) {
        const count = Math.floor(this.amount / bill);
        this.amount -= count * bill;
        console.log("Dispense " + count + " $" + bill + " bills");
        return this; //关键
    }
}
function run() {
    const request = new Request(378);
    request.get(100).get(50).get(20);
}
run();
/*

Requested: $378

Dispense 3 $100 bills
Dispense 1 $50 bills
Dispense 1 $20 bills
*/
```



## Command 
**● 中文名 ●**

命令

**● 可变的方面 ●** 

何时、怎样满足一个请求（封装请求，参数化）

**● 使用场景 ●** 

客户端引用一个命令接收器(对象)，接收器知道如何执行与命令相关的操作。把这些命令和相关的函数绑定即可。

```js
function add(x, y) { return x + y; }
function sub(x, y) { return x - y; }

const Command = function (execute, undo, value) {
    this.execute = execute;
    this.undo = undo;
    this.value = value;
}

const AddCommand = function (value) {
    return new Command(add, sub, value);
};

const SubCommand = function (value) {
    return new Command(sub, add, value);
};


const Calculator = function () {
    let current = 0;
    const commands = [];

    function action(command) {
        const name = command.execute.toString().substr(9, 3);
        return name.charAt(0).toUpperCase() + name.slice(1);
    }

    return {
        execute: function (command) {
            current = command.execute(current, command.value);
            commands.push(command);
            console.log(action(command) + ": " + command.value);
        },

        undo: function () {
            const command = commands.pop();
            current = command.undo(current, command.value);
            console.log("Undo " + action(command) + ": " + command.value);
        },

        getCurrentValue: function () {
            return current;
        }
    }
}

function run() {

    const calculator = new Calculator();

    // issue commands
    calculator.execute(new AddCommand(100));
    calculator.execute(new SubCommand(24));

    // reverse last two commands
    calculator.undo();

    console.log("\nValue: " + calculator.getCurrentValue());
}
run();
/*

Add: 100
Sub: 24
Undo Sub: 24

Value: 100
*/

```




## Interpreter 
**● 中文名 ●**

解释器

**● 可变的方面 ●** 

一个语言的文法及解释

**● 使用场景 ●** 

输入一个字符串，构建出这个字符串的相应的语法树，然后使用相应的表达式输出最后的结果。


```js
const Context = function (input) {
    this.input = input;
    this.output = 0;
}

Context.prototype = {
    startsWith: function (str) {
        return this.input.substr(0, str.length) === str;
    }
}

const Expression = function (name, one, four, five, nine, multiplier) {
    this.name = name;
    this.one = one;
    this.four = four;
    this.five = five;
    this.nine = nine;
    this.multiplier = multiplier;
}

Expression.prototype = {
    interpret: function (context) {
        if (context.input.length == 0) {
            return;
        }
        else if (context.startsWith(this.nine)) {
            context.output += (9 * this.multiplier);
            context.input = context.input.substr(2);
        }
        else if (context.startsWith(this.four)) {
            context.output += (4 * this.multiplier);
            context.input = context.input.substr(2);
        }
        else if (context.startsWith(this.five)) {
            context.output += (5 * this.multiplier);
            context.input = context.input.substr(1);
        }
        while (context.startsWith(this.one)) {
            context.output += (1 * this.multiplier);
            context.input = context.input.substr(1);
        }
    }
}

function run() {
    const roman = "MCMXXVIII"
    const context = new Context(roman);
    const tree = [];

    tree.push(new Expression("thousand", "M", " ", " ", " ", 1000));
    tree.push(new Expression("hundred", "C", "CD", "D", "CM", 100));
    tree.push(new Expression("ten", "X", "XL", "L", "XC", 10));
    tree.push(new Expression("one", "I", "IV", "V", "IX", 1));

    for (let i = 0, len = tree.length; i < len; i++) {
        tree[i].interpret(context);
    }

    console.log(roman + " = " + context.output);
}
run();
/*

MCMXXVIII = 1928
*/
```


## Iterator 
**● 中文名 ●**

迭代器

**● 可变的方面 ●** 

如何遍历、访问一个聚合的各元素

**● 使用场景 ●** 

客户端调用迭代器(通常传入一个数组)，迭代器使用方法 first()、next() 等实现迭代器接口遍历对象。

```js

const Iterator = function (items) {
    this.index = 0;
    this.items = items;
}

Iterator.prototype = {
    first: function () {
        this.reset();
        return this.next();
    },
    next: function () {
        return this.items[this.index++];
    },
    hasNext: function () {
        return this.index <= this.items.length;
    },
    reset: function () {
        this.index = 0;
    },
    each: function (callback) {
        for (let item = this.first(); this.hasNext(); item = this.next()) {
            callback(item);
        }
    }
}

function run() {

    const items = ["one", 2, "three", true];
    const iter = new Iterator(items);

    // using for loop
    for (let item = iter.first(); iter.hasNext(); item = iter.next()) {
        console.log(item);
    }
    console.log("-----");

    // using Iterator's each method
    iter.each(function (item) {
        console.log(item);
    });
}
run();
/*

one
2
three
true
-----
one
2
three
true
*/
```


## Mediator
**● 中文名 ●**

中介者

**● 可变的方面 ●** 

对象间怎样交互、和谁交互（用一个中介对象封装交互）

**● 使用场景 ●** 

中介者提供一个通信的接口(会有多个参与者加入，参与者作为参数传递给中介者)，维护和管理一个或者多个对象的引用。


```js
const Participant = function (name) {
    this.name = name;
    this.chatroom = null;
};

Participant.prototype = {
    send: function (message, to) {
        this.chatroom.send(message, this, to);
    },
    receive: function (message, from) {
        console.log(from.name + " to " + this.name + ": " + message);
    }
};

const Chatroom = function () {  // Mediator
    const participants = {};

    return {

        register: function (participant) {
            participants[participant.name] = participant;
            participant.chatroom = this;
        },

        send: function (message, from, to) {
            if (to) {                      // single message
                to.receive(message, from);
            } else {                       // broadcast message
                for (key in participants) {
                    if (participants[key] !== from) {
                        participants[key].receive(message, from);
                    }
                }
            }
        }
    };
};

function run() {

    const yoko = new Participant("A");
    const john = new Participant("B");

    const chatroom = new Chatroom();
    chatroom.register(yoko);
    chatroom.register(john);

    yoko.send("I am A.");
    yoko.send("I am 15.");
    john.send("Hello", yoko);
}
run();
/*
A to B: I am A.
A to B: I am 15.
B to A: Hello
*/
```



## Memento 
**● 中文名 ●**

备忘录

**● 可变的方面 ●** 

一个对象中那些私有信息存放在该对象之外，以及在什么时候进行存储（不破坏封装，捕获对象内部的状态）

**● 使用场景 ●** 

通过一个对象来存储原来的对象的属性，它并不改变原来的对象。这样可以方便在不同的状态时使用存储的数据。

```js
const Person = function (name, state) {
    this.name = name;
    this.state = state;
}

Person.prototype = {

    hydrate: function () {
        const memento = JSON.stringify(this);
        return memento;
    },

    dehydrate: function (memento) {
        const m = JSON.parse(memento);
        this.name = m.name;
        this.state = m.state;
    }
}

const CareTaker = function () {
    this.mementos = {};
    this.add = function (key, memento) {
        this.mementos[key] = memento;
    }
    this.get = function (key) {
        return this.mementos[key];
    }
}

function run() {

    const mike = new Person("AB", "TX");
    const john = new Person("CD", "CA");
    const caretaker = new CareTaker();

    // save state
    caretaker.add(1, mike.hydrate());
    caretaker.add(2, john.hydrate());

    // mess up their names
    mike.name = "GGG";
    john.name = "MMM";

    // restore original state
    mike.dehydrate(caretaker.get(1));
    john.dehydrate(caretaker.get(2));

    console.log(mike.name);
    console.log(john.name);
}
run();
/*
AB
CD

注释掉 dehydrate方法则输出：

GGG
MMM

*/
```


## Observer 
**● 中文名 ●**

观察者

**● 可变的方面 ●** 

多个对象依赖于另外一个对象，而这些对象又如何保持一致（得到通知并自动刷新）

**● 使用场景 ●** 

这种模式实现了松耦合，任意数量的观察者对象都可以观察一个目标对象，实现一个让观察者对象订阅或取消订阅的接口。

当它的状态改变时向它的观察者发送通知，然后可以调用观察者的绑定的函数来触发事件。

它也可以说是发布订阅模式，他们都可以增加一个第三者(经纪人：Broker)，来通知发布者/订阅者，它们也可以完全解耦，实质上都是为了让对象之间互不相识却可以相互通信。

```js
function Click() {
    this.handlers = [];  // observers
}

Click.prototype = {

    subscribe: function (fn) {
        this.handlers.push(fn);
    },

    unsubscribe: function (fn) {
        this.handlers = this.handlers.filter(
            function (item) {
                if (item !== fn) {
                    return item;
                }
            }
        );
    },

    fire: function (o, thisObj) {
        const scope = thisObj || window;
        this.handlers.forEach(function (item) {
            item.call(scope, o);
        });
    }
}

function run() {

    const clickHandler = function (item) {
        console.log("fired: " + item);
    };

    const click = new Click();

    click.subscribe(clickHandler);
    click.fire('event #1');
    click.unsubscribe(clickHandler);
    click.fire('event #2');
    click.subscribe(clickHandler);
    click.fire('event #3');
}
run();
/*
fired: event #1
fired: event #3
*/
```


## State 
**● 中文名 ●**

状态

**● 可变的方面 ●** 

对象的状态（状态改变时改变它的行为）

**● 使用场景 ●** 

创建一个函数 (上下文，即起到一个承上启下的作用，作为一个中间量，根据输入控制输出) 公开支持目标对象的接口，它用来维护这个引用的对象，并允许改变对象的状态属性。根据这些不同的状态，会有不同的事件触发。

```js
const TrafficLight = function () {
    let count = 0;
    let currentState = new Red(this);

    this.change = function (state) {
        // limits number of changes
        if (count++ >= 10) return;
        currentState = state;
        currentState.go();
    };

    this.start = function () {
        currentState.go();
    };
}

const Red = function (light) {
    this.light = light;

    this.go = function () {
        console.log("Red --> for 1 minute");
        light.change(new Green(light));
    }
};

const Yellow = function (light) {
    this.light = light;

    this.go = function () {
        console.log("Yellow --> for 10 seconds");
        light.change(new Red(light));
    }
};

const Green = function (light) {
    this.light = light;

    this.go = function () {
        console.log("Green --> for 1 minute");
        light.change(new Yellow(light));
    }
};

function run() {

    const light = new TrafficLight();
    light.start();
}
run();
/*
Red --> for 1 minute
Green --> for 1 minute
Yellow --> for 10 seconds
Red --> for 1 minute
Green --> for 1 minute
Yellow --> for 10 seconds
Red --> for 1 minute
Green --> for 1 minute
Yellow --> for 10 seconds
Red --> for 1 minute
Green --> for 1 minute
*/
```


## Strategy 
**● 中文名 ●**

策略

**● 可变的方面 ●** 

算法（封装一系列算法，独立于使用它的客户）

**● 使用场景 ●** 

创建一个函数 (上下文，即起到一个承上启下的作用，作为一个中间量，根据输入控制输出) 公开支持目标对象的接口，针对当前引用的对象(这个对象可以是纯数据，也可以是其它)，接口允许客户端请求策略计算。

即相同的数据，提供了不同的算法，来输出不同的结果。

```js
const Shipping = function () {
    this.company = "";
};

Shipping.prototype = {
    setStrategy: function (company) {
        this.company = company;
    },

    calculate: function (package) {
        return this.company.calculate(package);
    }
};

const UPS = function () {
    this.calculate = function (package) {
        // calculations...
        return "$15";
    }
};


const Fedex = function () {
    this.calculate = function (package) {
        // calculations...
        return "$25";
    }
};

function run() {

    const package = { from: "76712", to: "10012", weigth: "lkg" };

    // the 3 strategies
    const ups = new UPS();
    const fedex = new Fedex();

    const shipping = new Shipping();

    shipping.setStrategy(ups);
    console.log("UPS Strategy: " + shipping.calculate(package));
    shipping.setStrategy(fedex);
    console.log("Fedex Strategy: " + shipping.calculate(package));
}
run();
/*
UPS Strategy: $15
Fedex Strategy: $25
*/
```


## Template Method 
**● 中文名 ●**

模板方法

**● 可变的方面 ●** 

算法中的某些步骤（定义骨架）

**● 使用场景 ●** 

首先为一个函数提供一些接口，这些接口是实现定义算法基本步骤的钩子(也可以为空，也可以指定默认的代码)，开发人员可以轻松通过覆盖的方式定义自己的方法(也就是定义一个自定义的模版)

```js
const datastore = {
    process: function () {
        this.connect();
        this.select();
        this.disconnect();
        return true;
    }
};

function inherit(proto) {
    const F = function () { };
    F.prototype = proto;
    return new F();
}

function run() {
    const mySql = inherit(datastore);

    // implement template steps

    mySql.connect = function () {
        console.log("MySQL: connect step");
    };

    mySql.select = function () {
        console.log("MySQL: select step");
    };

    mySql.disconnect = function () {
        console.log("MySQL: disconnect step");
    };

    mySql.process();
}
run();
/*
MySQL: connect step
MySQL: select step
MySQL: disconnect step
*/
```


## Visitor 
**● 中文名 ●**

访问者

**● 可变的方面 ●** 

某些可作用于一个(组)对象上的操作，但不修改这些对象的类（如回调函数，回调传参）

**● 使用场景 ●** 

让对象可以通过访问者查询，让对象也可以使用访问者的方法，从而可以改变对象的属性值。

这样做的好处是无需重新创建一次相同的(不同属性值)对象来使用。

```js
const Employee = function (name, salary, vacation) {
    const self = this;

    this.accept = function (visitor) {
        visitor.visit(self);
    };

    this.getName = function () {
        return name;
    };

    this.getSalary = function () {
        return salary;
    };

    this.setSalary = function (sal) {
        salary = sal;
    };
};

const ExtraSalary = function () {
    this.visit = function (emp) {
        emp.setSalary(emp.getSalary() * 1.1);
    };
};


function run() {

    const employees = [
        new Employee("A", 10000, 10),
        new Employee("B", 20000, 21),
    ];

    const visitorSalary = new ExtraSalary();

    for (let i = 0, len = employees.length; i < len; i++) {
        const emp = employees[i];

        emp.accept(visitorSalary);
        console.log(`Employee Name: ${emp.getName()} - $${emp.getSalary()} `);
    }
}
run();
/*
Employee Name: A - $11000 
Employee Name: B - $22000 

注释  accept方法后，输出：

Employee Name: A - $10000 
Employee Name: B - $20000 
*/
```

