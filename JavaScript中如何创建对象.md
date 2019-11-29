> JavaScript支持面向对象编程，但是不使用类或者接口，在没有类的情况下，可以使用下列模式创建对象：
> ①工厂模式
> ②构造函数模式
> ③原型模式

# 如何创建自定义对象？
## 1、创建一个Object的实例
创建自定义对象最简单的方式就是创建一个Object的实例，然后再为它添加属性和方法，如下所示：
```
var person = new Object();
person.name = "Nicholas";
person.age = 29;
person.job = "Software Engineer";

person.sayName = function(){
	alert(this.name);
}	
```

## 2、对象字面量
```
var person = {
	name: "Nicholas",
	age: 29,
	job: "Software Engineer",

	sayName: function(){
		alert(this.name);
	}
};	
```
*虽然Object构造函数或对象字面量都可以用来创建单个对象且语法非常简单，但是这两个方式有一个明显的**缺点：使用同一个接口创建很多对象，会产生大量重复代码**，为了解决这个问题，我们引入下一个方式：*

## 3、工厂模式
工厂模式抽象了创建具体对象的过程，考虑到在js中无法创建类，开发人员就发明了一种函数，用来封装以特定接口创建对象的细节：
```
function createPerson(name, age, job){
	var o = new Object();
	o.name = name;
	o.age = age;
	o.sayName = function(){
		alert(this.name);
	};
	return o;
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```
**优势：** 函数`createPerson()`能够根据接受的参数来构建一个包含所有必要信息的`Person`对象。可以通过无数次调用这个函数来创建对象。
**不足：** 工厂模式虽然解决了创建多个相似对象的问题，但是没有解决对象识别的问题（即怎样知道一个对象的类型）。

## 4、构造函数模式
我们知道js中的构造函数可以用来创建特定类型的对象，像Object和Array这样的原生构造函数，在运行时会自动出现在执行环境中。此外，也可以创建自定义的构造函数，从而定义自定义对象类型的属性和方法。前面的例子可用构造函数模式重写：
```
function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = function(){
		alert(this.name);
	};
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
在这个例子中，`Person()`函数替代了`createPerson()`函数，`Person()`函数除了与`createPerson()`中相同的部分外，还存在以下的不同之处：
* 没有显示地创建对象
* 直接将属性和方法给了`this`对象
* 没有`return`语句
person1和person2的constructor属性都指向Person
***
### 构造函数
> 构造函数与其它函数的唯一区别，就在于调用他们的方式不同。任何函数，只要通过new操作符来调用，它就可以作为构造函数；而任何函数，如果不通过new操作符来调用，那它和普通函数也不会有什么两样。
作为构造函数调用：
```
var person =
new("Nicholas", 29, "Software Engineer");
```
作为普通函数调用：
```
Person("Greg", 27,
"Doctor");
```
#### 不同调用方式时候this的指向：
##### ①作为构造函数：
new操作符调用构造函数实际上会经历以下步骤：
1、创建一个新对象
2、将构造函数作用域赋给新对象（一次this就指向了这个新对象）
3、执行构造函数中的代码（为这个而对象添加属性）
4、返回新对象
##### ②作为普通函数：
当在全局作用域中调用一个函数时，this对象总是指向Global对象（在浏览器中就是window对象）。
***
**优势：** 创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型，这正是构造函数模式胜过工厂模式的地方。（instanceof）

**不足：** 使用构造函数的主要问题，就是每个方法都要在每个实例上重新创建一遍，每个Person实例都包含一个不同的Function实例，然而创建两个完成同样任务的Funcition实例是没有必要的

## 5、把函数转移到构造函数之外
```
function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = sayName;
}
function sayName(){
	alert(this.name);	
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
**优势：** 这样一来，sayName包含的是一个指向函数的指针，因此person1和person2对象就共享了全局作用域中定义 一个sayName函数。
**不足：** 在全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不副实。而更让人无法接受的是：如果对象需要定义很多方法，那就要定义多个全局函数，于是我们这个自定义的引用类型就丝毫没有封装性可言了。

## 6、原型模式
**prototype：** 我们创建的每个函数都有一个prototype（原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。如果按照字面意思来理解，那么prototype就是通过调用构造函数而创建的那个对象实例的原型对象。使用原型对象的好处是可以让所有对象示例共享它所包含的属性和方法。
```
function Person(){
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Soft Engineer";
Person.prototype.sayName = function(){
	alert(this.name)
};

var person1 = new Person();
var person2 = new Person();
person1.sayName();
person2.sayName();
```
#### ①理解原型对象
无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性，这个属性指向函数的原型对象。在默认情况下所有的原型对象都会自动获得一个constructor（构造函数）属性，这个属性包含一个指向prototype属性所在在函数的指针。也就是Person.prototype.constructor指向Person。
创建了自定义的构造函数之后，其原型对象默认只会取得constructor属性，至于其他方法都是从object继承而来的。当调用构造函数创建一个新的实例之后，该实例内部将包含一个指针（内部属性），指向构造函数的原型对象。这个指针叫[[Prototype]]，虽然在脚本中没有标准的方式来访问[[Prototype]]，但Firefox、Safari和Chrome在每个对象上都支持一个属性_proto_。**但是要明确非常重要的一点：这个连接存在于实力与构造函数的原型对象之间，而不是存在于实例与构造函数之间。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191128140523587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MDEyNw==,size_16,color_FFFFFF,t_70)
Person的每个实例——person1和person2都包含一个内部属性，该属性仅仅指向了Person.prototype;换句话说，它与构造函数没有直接的关系。虽然这两个实例都不包含属性和方法，但是我们却可以调用person1.sayName()，这是通过查找对象属性的过程来实现的：
每当代码读取某个对象的没个属性的时候，都会执行一次搜索，米表示具有给定名字的属性。搜索首先从对象实例本身开始。如果在实例中找到了具有给定名字的属性，则返回该属性的值了如果没有找到，则继续搜索指针指向的原型对象，在原型对象中查找具有给定名字的属性。
 
#### ②屏蔽
当为对象实例添加一个属性的时候，这个属性就会**屏蔽**原型对象中保存的同名属性；换句话说，添加这个属性只会阻止我们访问原型中的那个属性，但不会修改那个属性。即使将这个属性设置为null，也只会在实例中设置这个属性，而不会恢复其指向原型的连接。
不过，使用delete操作符则可以完全删除实例属性，从而让我们能够重新访问原型中的属性。
```
function Person(){
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Soft Engineer";
Person.prototype.sayName = function(){
	alert(this.name)
};

var person1 = new Person();
var person2 = new Person();

person1.name = "Greg"
console.log(person1.name);
console.log(person2.name);

delete person1.name;
console.log(person1.name);
console.log(person2.name);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019112814212526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MDEyNw==,size_16,color_FFFFFF,t_70)

#### ③更简单的原型语法
你也许注意到了，前面例子中每添加一个属性和方法就要敲一遍Person.prototype。为了减少不必要的输入，也为了从视觉上更好地封装原型的功能，更常见的做法是用一个包含所有属性和方法的对象字面量来重写整个原型对象。
```
funciton Person(){}
Person.prototype = {
	name: "Nicholas",
	age: 29,
	job: "Software Engineer"
	sayName: fucntion(){
		alert(this.name);
	}
};	
```
用以上方法创建对象，最终结果相同，但有一个例外：constructor属性不再指向Person了。我们用对象字面量的语法，本质上完全重写了默认的prototype对象，因此constructor属性就变成了新的对象的constructor属性（指向Object构造函数），不再指向Person函数。（此时尽管instanceof操作符还能返回正确的结果，但通过constructor已经无法确定对象的类型了）

#### ④原型的动态性
```
function Person(){
}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Soft Engineer";
Person.prototype.sayName = function(){
	alert(this.name)
};

var friend = new Person();

Person.prototype.sayHi = function(){
	alert("Hi");
};

friend.sayHi();
```
原型中查找值的过程。实例与原型之间的链接是一个指针，而非一个副本。
但如果是重写了整个原型对象，那情况就不一样了，这相当于切断了构造函数与最初原型对象之间的联系。请记住：实例中的指针仅指向原型，而不指向构造函数。
```
funciton Person(){}

Person.prototype = {
	name: "Nicholas",
	age: 29,
	job: "Software Engineer"
	sayName: fucntion(){
		alert(this.name);
	}
};	

var friend = new Person();

Person.prototype.sayHi = function(){
	alert("Hi");
};

friend.sayHi();
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191128143448521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MDEyNw==,size_16,color_FFFFFF,t_70)

#### ⑤原型对象的问题
* 原型对象省略了为构造函数传递参数这一环节，结果所有实例在默认情况下都会取得相同的属性值。
* 原型对象的共享性
```
funciton Person(){}

Person.prototype = {
	name: "Nicholas",
	age: 29,
	job: "Software Engineer",
	friends: ["a", "b"],
	sayName: fucntion(){
		alert(this.name);
	}
};	

var person1 = new Person();
var person2 = new Person();

person1.friends.push("c");

console.log(person1.friends);
console.log(person2.friends);
console.log(person1.friends == person2.friends)
```

### ⑥组合使用构造函数模式和原型模式
```
funciton Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.friends = ["a", "b"];
}

Person.prototype = {
	constructor: Person,
	sayName: function(){
		alert(this.name)
	}
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.friends.push("c);
console.log(person1.friends);
console.log(person2.friends);
cosnole.log(person1.friends === person2.friends);
console.log(person1.sayName === person2.sayName);
```

### 小结
* 工厂模式：使用简单的函数常见对象，为对象添加属性和方法，然后返回对象，这个方法后来被构造函数模式所取代
* 构造函数模式：可以创建自定义引用类型，可以向创建内置对象实例一样使用new操作符。但是构造函数的每个成员都无法复用，包括函数。由于函数可以不局限于对象，因此没有理由不在多个对象之间共享函数。
* 原型模式：使用构造函数的prototype属性来指定哪些应该共享的属性和方法。组合使用构造函数模式和原型模式时，使用构造函数定义实例属性，而使用原型定义共享的属性和方法。