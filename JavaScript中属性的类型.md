## 1、数据属性
数据属性包含一个数据值的位置。在这个位置可以读取和写入值。数据属性有4个描述其行为的特性：
* [[Configurable]]：表示能否通过`delete`删除属性，能否修改属性的特性，或者能否把属性修改为访问器属性。
* [[Enumerable]]：表示能否通过`for-in`循环返回属性
* [[Writable]]：表示能否修改属性的值
* [[Value]]：包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值把偶才能在这个卫视。这个特性的默认值为`undefined`

#### Object.defineProperty()方法
要修改属性默认的特性，必须使用`Object.defineProperty()`方法。这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中描述符对象必须是以上四个特性`configurable`, `enumerable`, `writable`, `value`中的一个或多个。
```
var person = {};
Object.defineProperty{person, "name", {
	writable: false,
	value: "Nicholas",
});

alert(person.name);
person.name = "Greg";
alert(person.name);
```
以上name的属性值是只读的，不可修改的，如果尝试为它定义一个新的值，在非严格模式下赋值操作将被忽略；在严格模式下，赋值操作将会导致抛出错误。
```
var person = {};
Object.defineProperty{person, "name", {
	configurable: false,
	value: "Nicholas",
});

alert(person.name);
delete person.name;
alert(person.name);
```
把`configurable`设置为`false`，表示不能从对象中删除属性。而且一旦把属性定义为不可配置的，就不能再把它变回可配置了。
**在调用`Object.defineProperty()`方法时，如果不指定，`configurable`, `enumerable`和`writable`特性的默认值都是`false`**

## 2、访问器属性
访问器属性不包含数据值，它们包含一对儿`getter`和`setter`函数，访问器属性具有如下四个特性：
* [[Configurable]]：表示能否通过`delete`删除属性而重新定义属性，能否修改属性的特性或者能否把属性修改为数据属性。对于直接在对象上定义的属性，这个特性的默认值为`true`。
* [[Enumerable]]：表示能否通过`for-in`循环返回属性。对于直接在对象上定义的属性，这个特性的默认值为`true`。
* [[Get]]：在读取属性的时候调用的函数。默认值为`undefined`
* [[Set]]：在读取属性的时候调用的函数。默认值为`undefined`
访问器属性不能直接定义，必须使用`Object.defineProperty()`来定义。