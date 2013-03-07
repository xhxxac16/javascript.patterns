<a name="a1"></a>
# 第三章 直接量和构造函数

JavaScript中的直接量模式更加简洁、富有表现力，且在定义对象时不容易出错。本章将对直接量展开讨论，包括对象、数组和正则表达式直接量，以及为什么要使用等价的内置构造器函数来创建它们，比如Object()和Array()等。本章同样会介绍JSON格式，JSON是使用数组和对象直接量的形式定义的一种数据转换格式。本章还会讨论自定义构造函数，包括如何强制使用new以确保构造函数的正确执行。

本章还会补充讲述一些基础知识，比如内置包装对象Number()、String()和Boolean()，以及如何将它们和原始值（数字、字符串和布尔值）比较。最后，快速介绍一下Error()构造函数的用法。

<a name="a2"></a>
## 对象直接量

我们可以将JavaScript中的对象简单的理解为名值对组成的散列表（hash table），在其他编程语言中被称作“关联数组”。其中的值可以是原始值也可以是对象，不管是什么类型，它们都是“属性”（properties），属性值同样可以是函数，这时属性就被称为“方法”（methods）。

JavaScript中自定义的对象（用户定义的本地对象）任何时候都是可变的。内置本地对象的属性也是可变的。你可以先创建一个空对象，然后在需要时给它添加功能。“对象直接量写法（object literal notation）”是按需创建对象的一种理想方式。

看一下这个例子：

	// start with an empty object
	var dog = {};

	// add one property
	dog.name = "Benji";

	// now add a method
	dog.getName = function () {
		return dog.name;
	};

在这个例子中，我们首先定义了一个空对象，然后添加了一个属性和一个方法，在程序的生命周期内的任何时刻都可以：

1.更改属性和方法的值，比如：

	dog.getName = function () {
		// redefine the method to return
		// a hardcoded value
		return "Fido";
	};

2.完全删除属性/方法

	delete dog.name;

3.添加更多的属性和方法

	dog.say = function () {
		return "Woof!";
	};
	dog.fleas = true;

其实不必每次开始都创建空对象，对象直接量模式可以直接在创建对象时添加功能，就像下面这个例子所展示的：

	var dog = {
		name: "Benji",
		getName: function () {
			return this.name;
		}
	};

> 在本书中多次提到“空对象”（“blank object”和“empty object”）。这只是某种简称，要知道JavaScript中根本不存在真正的空对象，理解这一点至关重要。即使最简单的`{}`对象包含从Object.prototype继承来的属性和方法。我们提到的“空（empty）对象”只是说这个对象没有自己的属性，不考虑它是否有继承来的属性。

<a name="a3"></a>
### 对象直接量语法

如果你从来没有接触过对象直接量写法，第一次碰到可能会感觉怪怪的。但越到后来你就越喜欢它。本质上讲，对象直接量语法包括：

- 将对象主体包含在一对花括号内（`{` and `}`）。
- 对象内的属性或方法之间使用逗号分隔。最后一个名值对后也可以有逗号，但在IE下会报错，所以尽量不要在最后一个属性或方法后加逗号。
- 属性名和值之间使用冒号分隔
- 如果将对象赋值给一个变量，不要忘了在右括号`}`之后补上分号

<a name="a4"></a>
### 通过构造函数创建对象

JavaScript中没有类的概念，这给JavaScript带来了极大的灵活性，因为你不必提前知晓关于对象的任何信息，也不需要类的“蓝图”。但JavaScript同样具有构造函数，它的语法和Java或其他语言中基于类的对象创建非常类似。

你可以使用自定义的构造函数来创建实例对象，也可以使用内置构造函数来创建，比如Object()、Date()、String()等等。

下面这个例子展示了用两种等价的方法分别创建两个独立的实例对象：

	// one way -- using a literal
	var car = {goes: "far"};

	// another way -- using a built-in constructor
	// warning: this is an antipattern
	var car = new Object();
	car.goes = "far";

从这个例子中可以看到，直接量写法的一个明显优势是，它的代码更少。“创建对象的最佳模式是使用直接量”还有一个原因，它可以强调对象就是一个简单的可变的散列表，而不必一定派生自某个类。

另外一个使用直接量而不是Object构造函数创建实例对象的原因是，对象直接量不需要“作用域解析”（scope resolution）。因为新创建的实例有可能包含了一个本地的构造函数，当你调用Object()的时候，解析器需要顺着作用域链从当前作用域开始查找，直到找到全局Object构造函数为止。

<a name="a5"></a>
### 获得对象的构造器

创建实例对象时能用对象直接量就不要使用new Object()构造函数，但有时你希望能继承别人写的代码，这时就需要了解构造函数的一个“特性”（也是不使用它的另一个原因），就是Object()构造函数可以接收参数，通过参数的设置可以把实例对象的创建委托给另一个内置构造函数，并返回另外一个实例对象，而这往往不是你所希望的。

下面的示例代码中展示了给new Object()传入不同的参数：数字、字符串和布尔值，最终得到的对象都是由不同的构造函数生成的：

	// Warning: antipatterns ahead

	// an empty object
	var o = new Object();
	console.log(o.constructor === Object); // true

	// a number object
	var o = new Object(1);
	console.log(o.constructor === Number); // true
	console.log(o.toFixed(2)); // "1.00"

	// a string object
	var o = new Object("I am a string");
	console.log(o.constructor === String); // true
	// normal objects don't have a substring()
	// method but string objects do
	console.log(typeof o.substring); // "function"

	// a boolean object
	var o = new Object(true);
	console.log(o.constructor === Boolean); // true

Object()构造函数的这种特性会导致一些意想不到的结果，特别是当参数不确定的时候。最后再次提醒不要使用new Object()，尽可能的使用对象直接量来创建实例对象。

<a name="a6"></a>
## 自定义构造函数

除了对象直接量和内置构造函数之外，你也可以通过自定义的构造函数来创建实例对象，正如下面的代码所示：

	var adam = new Person("Adam");
	adam.say(); // "I am Adam"

这里用了“类”Person创建了实例，这种写法看起来很像Java中的实例创建。两者的语法的确非常接近，但实际上JavaScript中没有类的概念，Person是一个函数。

Person构造函数是如何定义的呢？看下面的代码：

	var Person = function (name) {
		this.name = name;
		this.say = function () {
			return "I am " + this.name;
		};
	};

当你通过关键字new来调用这个构造函数时，函数体内将发生这些事情：

- 创建一个空对象，将它的引用赋给this，继承函数的原型。
- 通过this将属性和方法添加至这个对象
- 最后返回this指向的新对象（如果没有手动返回其他的对象）

用代码表示这个过程如下：

	var Person = function (name) {
		// create a new object
		// using the object literal
		// var this = {};

		// add properties and methods
		this.name = name;
		this.say = function () {
			return "I am " + this.name;
		};

		//return this;
	};

正如这段代码所示，say()方法添加至this中，结果是，不论何时调用new Person()，在内存中都会创建一个新函数（译注：所有Person的实例对象中的方法都是独占一块内存的）。显然这是效率很低的，因为所有实例的say()方法是一模一样的，因此没有必要“拷贝”多份。最好的办法是将方法添加至Person的原型中。

	Person.prototype.say = function () {
		return "I am " + this.name;
	};

我们将会在下一章里详细讨论原型和继承。现在只要记住将需要重用的成员和方法放在原型里即可。

关于构造函数的内部工作机制也会在后续章节中有更细致的讨论。这里我们只做概要的介绍。刚才提到，构造函数执行的时候，首先创建一个新对象，并将它的引用赋给this：

	// var this = {};

事实并不完全是这样，因为“空”对象并不是真的空，这个对象继承了Person的原型，看起来更像：

	// var this = Object.create(Person.prototype);

在后续章节会进一步讨论Object.create()。

<a name="a7"></a>
### 构造函数的返回值

用new调用的构造函数总是会返回一个对象，默认返回this所指向的对象。如果构造函数内没有给this赋任何属性，则返回一个“空”对象（除了继承构造函数的原型之外，没有“自己的”属性）。

尽管我们不会在构造函数内写return语句，也会隐式返回this。但我们是可以返回任意指定的对象的，在下面的例子中就返回了新创建的that对象。

	var Objectmaker = function () {

		// this `name` property will be ignored
		// because the constructor
		// decides to return another object instead
		this.name = "This is it";

		// creating and returning a new object
		var that = {};
		that.name = "And that's that";
		return that;
	};

	// test
	var o = new Objectmaker();
	console.log(o.name); // "And that's that"

我们看到，构造函数中其实是可以返回任意对象的，只要你返回的东西是对象即可。如果返回值不是对象（字符串、数字或布尔值），程序不会报错，但这个返回值被忽略，最终还是返回this所指的对象。

<a name="a8"></a>
## 强制使用new的模式

我们知道，构造函数和普通的函数无异，只是通过new调用而已。那么如果调用构造函数时忘记new会发生什么呢？漏掉new不会产生语法错误也不会有运行时错误，但可能会造成逻辑错误，导致执行结果不符合预期。这是因为如果不写new的话，函数内的this会指向全局对象（在浏览器端this指向window）。

当构造函数内包含this.member之类的代码，并直接调用这个函数（省略new），实际会创建一个全局对象的属性member，可以通过window.member或member访问到它。这必然不是我们想要的结果，因为我们要努力确保全局命名空间的整洁干净。

	// constructor
	function Waffle() {
		this.tastes = "yummy";
	}

	// a new object
	var good_morning = new Waffle();
	console.log(typeof good_morning); // "object"
	console.log(good_morning.tastes); // "yummy"

	// antipattern:
	// forgotten `new`
	var good_morning = Waffle();
	console.log(typeof good_morning); // "undefined"
	console.log(window.tastes); // "yummy"

ECMAScript5中修正了这种非正常的行为逻辑。在严格模式中，this是不能指向全局对象的。如果在不支持ES5的JavaScript环境中，仍然后很多方法可以确保构造函数的行为即便在省略new调用时也不会出问题。

<a name="a9"></a>
### 命名约定

最简单的选择是使用命名约定，前面的章节已经提到，构造函数名首字母大写（MyConstructor），普通函数和方法名首字母小写（myFunction）。

<a name="a10"></a>
### 使用that

遵守命名约定的确能帮上一些忙，但约定毕竟不是强制，不能完全避免出错。这里给出了一种模式可以确保构造函数一定会按照构造函数的方式执行。不要将所有成员挂在this上，将它们挂在that上，并返回that。

	function Waffle() {
	var that = {};
		that.tastes = "yummy";
		return that;
	}

如果要创建简单的实例对象，甚至不需要定义一个局部变量that，可以直接返回一个对象直接量，就像这样：

	function Waffle() {
		return {
			tastes: "yummy"
		};
	}

不管用什么方式调用它（使用new或直接调用），它同都会返回一个实例对象：

	var first = new Waffle(),
		second = Waffle();
	console.log(first.tastes); // "yummy"
	console.log(second.tastes); // "yummy"

这种模式的问题是丢失了原型，因此在Waffle()的原型上的成员不会继承到这些实例对象中。

> 需要注意的是，这里用的that只是一种命名约定，that不是语言的保留字，可以将它替换为任何你喜欢的名字，比如self或me。

<a name="a11"></a>
### 调用自身的构造函数

为了解决上述模式的问题，能够让实例对象继承原型属性，我们使用下面的方法。在构造函数中首先检查this是否是构造函数的实例，如果不是，再通过new调用构造函数，并将new的结果返回：

	function Waffle() {

		if (!(this instanceof Waffle)) {
			return new Waffle();
		}
		this.tastes = "yummy";

	}
	Waffle.prototype.wantAnother = true;

	// testing invocations
	var first = new Waffle(),
		second = Waffle();

	console.log(first.tastes); // "yummy"
	console.log(second.tastes); // "yummy"

	console.log(first.wantAnother); // true
	console.log(second.wantAnother); // true

另一种检查实例的通用方法是使用arguments.callee，而不是直接将构造函数名写死在代码中：

	if (!(this instanceof arguments.callee)) {
		return new arguments.callee();
	}

这里需要说明的是，在任何函数内部都会自行创建一个arguments对象，它包含函数调用时传入的参数。同时arguments包含一个callee属性，指向它所在的正在被调用的函数。需要注意，ES5严格模式中是禁止使用arguments.callee的，因此最好对它的使用加以限制，并删除任何你能在代码中找到的实例（译注：这里作者的表述很委婉，其实作者更倾向于全面禁止使用arguments.callee）。

<a name="a12"></a>
## 数组直接量

和其他的大多数一样，JavaScript中的数组也是对象。可以通过内置构造函数Array()来创建数组，类似对象直接量，数组也可以通过直接量形式创建。而且更推荐使用直接量创建数组。

这里的实例代码给出了创建两个具有相同元素的数组的两种方法，使用Array()和使用直接量模式：

	// array of three elements
	// warning: antipattern
	var a = new Array("itsy", "bitsy", "spider");

	// the exact same array
	var a = ["itsy", "bitsy", "spider"];

	console.log(typeof a); // "object", because arrays are objects
	console.log(a.constructor === Array); // true

<a name="a13"></a>
### 数组直接量语法

数组直接量写法非常简单：整个数组使用方括号括起来，数组元素之间使用逗号分隔。数组元素可以是任意类型，也包括数组和对象。

数组直接量语法简单直接、高雅美观。毕竟数组只是从位置0开始索引的值的集合，完全没必要包含构造器和new运算符的内容（代码会更多），保持简单即可。

<a name="a14"></a>
### 有意思的数组构造器

我们对new Array()敬而远之原因是为了避免构造函数带来的陷阱。

如果给Array()构造器传入一个数字，这个数字并不会成为数组的第一个元素，而是设置数组的长度。也就是说，new Array(3)创建了一个长度为3的数组，而不是某个元素是3。如果你访问数组的任意元素都会得到undefined，因为元素并不存在。下面示例代码展示了直接量和构造函数的区别：

	// an array of one element
	var a = [3];
	console.log(a.length); // 1
	console.log(a[0]); // 3

	// an array of three elements
	var a = new Array(3);
	console.log(a.length); // 3
	console.log(typeof a[0]); // "undefined"

尽管构造器的行为并不像我们想象的那样，当给new Array()传入一个浮点数时情况就更糟糕了。这时结果就会出错（译注：给new Array()传入浮点数会报“范围错误”RangError，new Array(3.00)则不会报错），因为数组长度不可能是浮点数。

	// using array literal
	var a = [3.14];
	console.log(a[0]); // 3.14

	var a = new Array(3.14); // RangeError: invalid array length
	console.log(typeof a); // "undefined"

为了避免在运行时动态创建数组时出现这种错误，强烈推荐使用数组直接量来代替new Array()。

>有些人用Array()构造器来做一些有意思的事情，比如用来生成重复字符串。下面这行代码返字符串包含255个空格（请读者思考为什么不是256个空格）。`var white = new Array(256).join(' ');`

<a name="a15"></a>
### 检查是不是数组

如果typeof的操作数是数组的话，将返回“object”。

	console.log(typeof [1, 2]); // "object"

这个结果勉强说得过去，毕竟数组是一种对象，但对我们用处不大。往往你需要知道一个值是不是真正的数组。你可能见到过这种检查数组的方法：检查length属性、检查数组方法比如slice()等等。但这些方法非常脆弱，非数组的对象也可以拥有这些同名的属性。还有些人使用instanceof Array来判断数组，但这种方法在某些版本的IE里的多个iframe的场景中会出问题（译注：原因就是在不同iframe中创建的数组不会相互共享其prototype属性）。

ECMAScript 5定义了一个新的方法Array.isArray()，如果参数是数组的话就返回true。比如：

	Array.isArray([]); // true

	// trying to fool the check
	// with an array-like object
	Array.isArray({
		length: 1,
		"0": 1,
		slice: function () {}
	}); // false

如果你的开发环境不支持ECMAScript5，可以通过Object.prototype.toString()方法来代替。如调用toString的call()方法并传入数组上下文，将返回字符串“[object Array]”。如果传入对象上下文，则返回字符串“[object Object]”。因此可以这样做：

	if (typeof Array.isArray === "undefined") {
		Array.isArray = function (arg) {
			return Object.prototype.toString.call(arg) === "[object Array]";
		};
	}

<a name="a16"></a>
## JSON

上文我们刚刚讨论过对象和数组直接量，你已经对此很熟悉了，现在我们将目光转向JSON。JSON（JavaScript Object Notation）是一种轻量级的数据交换格式。很多语言中都实现了JSON，特别是在JavaScript中。

JSON格式及其简单，它只是数组和对象直接量的混合写法，看一个JSON字符串的例子：

	{"name": "value", "some": [1, 2, 3]}

JSON和对象直接量在语法上的唯一区别是，合法的JSON属性名均用引号包含。而在对象直接量中，只有属性名是非法的标识符时采用引号包含，比如，属性名中包含空格`{"first name": "Dave"}`。

在JSON字符串中，不能使用函数和正则表达式直接量。

<a name="a17"></a>
### 使用JSON

在前面的章节中讲到，出于安全考虑，不推荐使用eval()来“粗糙的”解析JSON字符串。最好使用JSON.parse()方法，ES5中已经包含了这个方法，而且在现代浏览器的JavaScript引擎中已经内置支持JSON了。对于老旧的JavaScript引擎来说，你可以使用JSON.org所提供的JS文件（http://www.json.org/json2.js）来获得JSON对象和方法。

	// an input JSON string
	var jstr = '{"mykey": "my value"}';
	
	// antipattern
	var data = eval('(' + jstr + ')');

	// preferred
	var data = JSON.parse(jstr);

	console.log(data.mykey); // "my value"

如果你已经在使用某个JavaScript库了，很可能库中提供了解析JSON的方法，就不必再额外引入JSON.org的库了，比如，如果你已经使用了YUI3，你可以这样：

	// an input JSON string
	var jstr = '{"mykey": "my value"}';

	// parse the string and turn it into an object
	// using a YUI instance
	YUI().use('json-parse', function (Y) {
		var data = Y.JSON.parse(jstr);
		console.log(data.mykey); // "my value"
	});

如果你使用的是jQuery，可以直接使用它提供的parseJSON()方法：

	// an input JSON string
	var jstr = '{"mykey": "my value"}';

	var data = jQuery.parseJSON(jstr);
	console.log(data.mykey); // "my value"

和JSON.parse()方法相对应的是JSON.stringify()。它将对象或数组（或任何原始值）转换为JSON字符串。

	var dog = {
		name: "Fido",
		dob:new Date(),
		legs:[1,2,3,4]
	};

	var jsonstr = JSON.stringify(dog);

	// jsonstr is now:
	// {"name":"Fido","dob":"2010-04-11T22:36:22.436Z","legs":[1,2,3,4]}

<a name="a18"></a>
## 正则表达式直接量

JavaScript中的正则表达式也是对象，可以通过两种方式创建它们：

- 使用new RegExp()构造函数
- 使用正则表达式直接量

下面的示例代码展示了创建正则表达式的两种方法，创建的正则用来匹配一个反斜杠（\）：

	// regular expression literal
	var re = /\\/gm;

	// constructor
	var re = new RegExp("\\\\", "gm");

显然正则表达式直接量写法的代码更短，且不必强制按照类构造器的思路来写。因此更推荐使用直接量写法。

另外，如果使用RegExp()构造函数写法，还需要考虑对引号和反斜杠进行转义，正如上段代码所示的那样，用了四个反斜杠来匹配一个反斜杠。这会增加正则表达式的长度，而且让正则变得难于理解和维护。刚开始学习正则表达式不是很容易，所以不要放弃任何一个简化它们的机会，所以要尽量使用直接量而不是通过构造函数来创建正则。

<a name="a19"></a>
### 正则表达式直接量语法

正则表达式直接量使用两个斜线包裹起来，正则的主体部分不包括两端的斜线。在第二个斜线之后可以指定模式匹配的修饰符用以高级匹配，修饰符不需要引号引起来，JavaScript中有三个修饰符：

- g，全局匹配
- m，多行匹配
- i，忽略大小写的匹配

修饰符可以自由组合，而且顺序无关：

	var re = /pattern/gmi;

使用正则表达式直接量可以让代码更加简洁高效，比如当调用String.prototype.replace()方法时，可以传入正则表达式参数：

	var no_letters = "abc123XYZ".replace(/[a-z]/gi, "");
	console.log(no_letters); // 123

有一种不得不使用new RegExp()的情形，有时正则表达式是不确定的，直到运行时才能确定下来。

正则表达式直接量和Regexp()构造函数的另一个区别是，正则表达式直接量只在解析时创建一次正则表达式对象（译注：多次解析同一个正则表达式，会产生相同的实例对象）。如果在循环体内反复创建相同的正则表达式，则每个正则对象的所有属性（比如lastIndex）只会设置一次（译注：由于每次创建相同的实例对象，每个循环中的实例对象都是同一个，属性也自然相同），下面这个例子展示了两次都返回了相同的正则表达式的情形（译注：这里作者的表述只是针对ES3规范而言，下面这段代码在NodeJS、IE6-IE9、FireFox4、Chrome10、Safari5中运行结果和作者描述的不一致，Firefox 3.6中的运行结果和作者描述是一致的，原因可以在ECMAScript5规范第24页和第247页找到，也就是说在ECMAScript3规范中，用正则表达式创建的RegExp对象会共享同一个实例，而在ECMAScript5中则是两个独立的实例。而最新的Firefox4、Chrome和Safari5都遵循ECMAScript5标准，至于IE6-IE8都没有很好的遵循ECMAScript3标准，不过在这个问题上反而处理对了。很明显ECMAScript5的规范更符合开发者的期望）。

	function getRE() {
		var re = /[a-z]/;
		re.foo = "bar";
		return re;
	}

	var reg = getRE(),
		re2 = getRE();

	console.log(reg === re2); // true
	reg.foo = "baz";
	console.log(re2.foo); // "baz"

>在ECMAScript5中这种情形有所改变，相同正则表达式直接量的每次计算都会创建新的实例对象，目前很多现代浏览器也对此做了纠正（译注：比如在Firefox4就纠正了Firefox3.6的这种“错误”）。

最后需要提一点，不带new调用RegExp()（作为普通的函数）和带new调用RegExp()是完全一样的。

<a name="a20"></a>
## 原始值的包装对象

JavaScript中有五种原始类型：数字、字符串、布尔值、null和undefined。除了null和undefined之外，其他三种都有对应的“包装对象”（wrapper objects）。可以通过内置构造函数来生成包装对象，Number()、String()、和Boolean()。

为了说明数字原始值和数字对象之间的区别，看一下下面这个例子：

	// a primitive number
	var n = 100;
	console.log(typeof n); // "number"

	// a Number object
	var nobj = new Number(100);
	console.log(typeof nobj); // "object"

包装对象带有一些有用的属性和方法，比如，数字对象就带有toFixed()和toExponential()之类的方法。字符串对象带有substring()、chatAt()和toLowerCase()等方法以及length属性。这些方法非常方便，和原始值相比，这让包装对象具备了一定优势。其实原始值也可以调用这些方法，因为原始值会首先转换为一个临时对象，如果转换成功，则调用包装对象的方法。

	// a primitive string be used as an object
	var s = "hello";
	console.log(s.toUpperCase()); // "HELLO"

	// the value itself can act as an object
	"monkey".slice(3, 6); // "key"

	// same for numbers
	(22 / 7).toPrecision(3); // "3.14"

因为原始值可以根据需要转换成对象，这样的话，也不必为了用包装对象的方法而将原始值手动“包装”成对象。比如，不必使用new String("hi")，直接使用"hi"即可。

	// avoid these:
	var s = new String("my string");
	var n = new Number(101);
	var b = new Boolean(true);

	// better and simpler:
	var s = "my string";
	var n = 101;
	var b = true;

不得不使用包装对象的一个原因是，有时我们需要对值进行扩充并保持值的状态。原始值毕竟不是对象，不能直接对其进行扩充（译注：比如`1.property = 2`会报错）。

	// primitive string
	var greet = "Hello there";

	// primitive is converted to an object
	// in order to use the split() method
	greet.split(' ')[0]; // "Hello"

	// attemting to augment a primitive is not an error
	greet.smile = true;

	// but it doesn't actually work
	typeof greet.smile; // "undefined"

在这段示例代码中，greet只是临时转换成了对象，以保证访问其属性/方法时不会出错。另一方面，如果greet通过new String()定义为一个对象，那么扩充smile属性就会按照期望的那样执行。对字符串、数字或布尔值的扩充并不常见，除非你清楚自己想要什么，否则不必使用包装对象。

当省略new时，包装器将传给它的参数转换为原始值：

	typeof Number(1); // "number"
	typeof Number("1"); // "number"
	typeof Number(new Number()); // "number"
	typeof String(1); // "string"
	typeof Boolean(1); // "boolean"

<a name="a21"></a>
## Error 对象

JavaScript中有很多内置的Error构造函数，比如Error()、SyntaxError()，TypeError()等等，这些“错误”通常和throw语句一起使用。这些构造函数创建的错误对象包含这些属性：

**name**

name属性是指创建这个对象的构造函数的名字，通常是“Errora”，有时会有特定的名字比如“RangeError”

**message**

创建这个对象时传入构造函数的字符串

错误对象还有其他一些属性，比如产生错误的行号和文件名，但这些属性是浏览器自行实现的，不同浏览器的实现也不一致，因此出于兼容性考虑，并不推荐使用这些属性。

另一方面，throw可以抛出任何对象，并不限于“错误对象”，因此你可以根据需要抛出自定义的对象。这些对象包含属性“name”和“message”或其他你希望传递给异常处理逻辑的信息，异常处理逻辑由catch语句指定。你可以灵活运用抛出的错误对象，将程序从错误状态恢复至正常状态。

	try {
		// something bad happened, throw an error
		throw {
			name: "MyErrorType", // custom error type
			message: "oops",
			extra: "This was rather embarrassing",
			remedy: genericErrorHandler // who should handle it
		};
	} catch (e) {
		// inform the user
		alert(e.message); // "oops"

		// gracefully handle the error
		e.remedy(); // calls genericErrorHandler()
	}

通过new调用和省略new调用错误构造函数是一模一样的，他们都返回相同的错误对象。

<a name="a22"></a>
## 小结

在本章里，我们讨论了多种直接量模式，它们是使用构造函数写法的替代方案，本章讲述了这些内容：

- 对象直接量写法——一种简洁优雅的定义对象的方法，名值对之间用逗号分隔，通过花括号包装起来
- 构造函数——内置构造函数（内置构造函数通常都有对应的直接量语法）和自定义构造函数。
- 一种强制函数以构造函数的模式执行（不管用不用new调用构造函数，都始终返回new出来的实例）的技巧
- 数组直接量写法——数组元素之间使用逗号分隔，通过方括号括起来
- JSON——是一种轻量级的数据交换格式
- 正则表达式直接量
- 避免使用其他的内置构造函数：String()、Number()、Boolean()以及不同种类的Error()构造器

通常除了Date()构造函数之外，其他的内置构造函数并不常用，下面的表格中对这些构造函数以及它们的直接量语法做了整理。

<table>
	<tr>
		<td>内置构造函数（不推荐）</td>
		<td>直接量语法和原始值（推荐）</td>
	</tr>
	<tr>
		<td>var o = new Object();</td>
		<td>var o = {};
		</td>
	</tr>
	<tr>
		<td>var a = new Array();
		</td>
		<td>var a = [];
		</td>
	</tr>
	<tr>
		<td>var re = new RegExp("[a-z]","g"); 
		</td>
		<td>var re = /[a-z]/g;
		</td>
	</tr>
	<tr>
		<td>var s = new String(); 
		</td>
		<td>var s = "";
		</td>
	</tr>
	<tr>
		<td>var n = new Number(); 
		</td>
		<td>var n = 0;
		</td>
	</tr>
	<tr>
		<td>var b = new Boolean();
		</td>
		<td>var b = false;
		</td>
	</tr>
	<tr>
		<td>throw new Error("uh-oh");
		</td>
		<td>throw { name: "Error",message: "uh-oh"};或者throw Error("uh-oh");
		</td>
	</tr>
</table>


