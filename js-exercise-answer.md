 [cry](https://github.com/Cruyun/JS-exercise/blob/master/Lab/test.md)

1、
 ```
var a = function(){} //一个空的对象
a.b = 1
a.prototype.b = 2
a.prototype.c = 3
a.prototype.d = 4
console.log(a.b)  // -->1
console.log(new a().b) //-->2

var foo = new a() //一个新对象被创建。它继承自a.prototype
foo.c = 5
console.log(foo.c) //-->5
console.log(foo.d) // -->4
console.log(foo.b) // ->2
 ```
 2、
 ```
 var Foo = function(){
  this.a = 1
  return {
    a:2
  }
}

var bar = new Foo()
console.log(bar.a) //--> 2
 ```
 [详解JS构造函数中this和return](https://www.jb51.net/article/123812.htm)

3、
```
var map = Object.create(null); // 创建一个没有原型的对象
console.log("toString" in map); // false
```
空对象没有原型链，所以没有“toString”属性。
```
var map = Object.create({a:1});
console.log("toString" in map); // true
console.log("a" in map); // true
```
* **in操作符**只要通过对象能够访问到属性就返回true
* JavaScript会区分“可枚举（enumerable)”与“不可枚举（nonenumberable）”属性。 我们创建并赋予对象的所有属性都是可枚举的。
* Object.create()方法里面有两个参数，第一个参数是该创建对象要继承的原型，第二个参数是被创建对象的属性。该题括号中只写了被创建对象的属性但是并没有写要继承的原型，所以它自动继承到了Object对象，所以其中包含的toString属性也出现了

4、
```
function foo(obj){
  return Object.prototype.toString.call(obj).slice(8,-1)
}
```

检测一个对象的数据类型并返回。该对象是截取obj的第八位到倒数第一位（包含第八不包含倒数第一）

`Object.prototype.toString.call(obj)` // "[object Array]"

`Object.prototype.toString.call([]).slice(8, -1)` // "array"

`Object.prototype.toString()`：一个表示该对象的字符串。

`call()`：apply()和call()这两个方法用途都是在特定的作用域中调用函数，实际上等于设值函数体内this对象的值。

`slice()`：提取字符串的某个部分，并以新的字符串返回被提取的部分。返回值是一个新的字符串（包括字符串 stringObject 从 start 开始（包括 start）到 end 结束（不包括 end）为止的所有字符。）
5、
```
var a = {}
a.bar = 2

Object.defineProperty(a, "foo",
                      { value: "hi"});

console.log(delete a.foo) //false  writable :false
console.log(delete a.bar) //true 

a.foo = "world"
console.log(a.foo) // "hi"


for (var key in a){
  console.log(key);  // undefined
}

console.log("foo" in a); // true
console.log("bar" in a); // false
```
* [Object.defineProperty()方法](https://blog.csdn.net/weixin_43870127/article/details/103301341)
* **in操作符**只要通过对象能够访问到属性就返回true
* JavaScript会区分“可枚举（enumerable)”与“不可枚举（nonenumberable）”属性。 我们创建并赋予对象的所有属性都是可枚举的。

6、
```
var a = 1;
 
function test() {
	
	a = 0;
	
	console.log(a);
	
	console.log(this.a);
	
	var a;
	
	console.log(a);
	
}
test(); // 0 1 0
new test(); // 0 undefined 0
```