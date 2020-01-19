### JavaScript中要避免踩的一些坑和使用技巧

#### 01、“==”和“===”的使用

- `==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；

  ```javascript
  false == 0; // true
  ```

- `===`比较，它不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比

  ```javascript
  false === 0; // false
  ```

#### 02、变量

​		JavaScript在声明变量时，没有强制要求使用 var，这样的话，如果一个变量没有通过`var`申明就被使用，那么该变量就自动被申明为全局变量。在同一个页面的不同的JavaScript文件中，如果都不用`var`申明，恰好都使用了变量`i`，将造成变量`i`互相影响，产生难以调试的错误结果。

​		为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，强制通过`var`申明变量，未使用`var`申明变量就使用的，将导致运行错误。

​		启用strict模式的方法是在JavaScript代码的第一行写上：

```
'use strict'
```

#### 03、多行字符串

由于多行字符串用`\n`写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用反引号表示：

```
console.log(`多行     //ES6标准
字符串
测试`);
```

#### 04、模板字符串

如果有很多变量需要连接，用`+`号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量：

```javascript
var name = '小明';
var age = 20;
var message = `你好, ${name}, 你今年${age}岁了!`;
```

结果：

```
你好, 小明, 你今年20岁了!
```

#### 05、对象

访问属性是通过`.`操作符完成的，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须用`''`括起来：

```javascript
var xiaohong = {
     name: '小红',
    'middle-school': 'No.1 Middle School'
};
```

`xiaohong`的属性名`middle-school`不是一个有效的变量，就需要用`''`括起来。访问这个属性也无法使用`.`操作符，必须用`['xxx']`来访问：

```javascript
xiaohong['middle-school']; // 'No.1 Middle School'
xiaohong['name']; // '小红'
xiaohong.name; // '小红'
```

如果我们要检测`xiaoming`是否拥有某一属性(自己的和继承的)，可以用`in`操作符：

```javascript
'name' in xiaoming; // true
```

要判断一个属性是否是`xiaoming`自身拥有的，而不是继承得到的，可以用`hasOwnProperty()`方法：

```javascript
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```

#### 06、条件判断

JavaScript把`null`、`undefined`、`0`、`NaN`和空字符串`''`视为`false`，其他值一概视为`true`，因此上述代码条件判断的结果是`true`。

#### 07、修饰器

利用`apply()`，我们还可以动态改变函数的行为。

JavaScript的所有对象都是动态的，即使内置的函数，我们也可以重新指向新的函数。

现在假定我们想统计一下代码一共调用了多少次`parseInt()`，可以把所有的调用都找出来，然后手动加上`count += 1`，不过这样做太傻了。最佳方案是用我们自己的函数替换掉默认的`parseInt()`：

```javascript
'use strict';
var count = 0;
var oldParseInt = parseInt; // 保存原函数
window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};
// 测试:
parseInt('10');
parseInt('20');
parseInt('30');
console.log('count = ' + count); // 3
```

#### 08、map()

`map()`方法定义在JavaScript的`Array`中，我们调用`Array`的`map()`方法，传入我们自己的函数，就得到了一个新的`Array`作为结果：

```
'use strict';
function pow(x) {
    return x * x;
}
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var results = arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
console.log(results); 
//数字转字符串
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.map(String); // ['1', '2', '3', '4', '5', '6', '7', '8', '9']
```

#### 09、reduce()

reduce的用法。Array的`reduce()`把一个函数作用在这个`Array`的`[x1, x2, x3...]`上，这个函数必须接收两个参数，`reduce()`把结果继续和序列的下一个元素做累积计算，其效果就是

```
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x + y;
}); // 25
```

#### 10、filter()

filter也是一个常用的操作，它用于把`Array`的某些元素过滤掉，然后返回剩下的元素。

和`map()`类似，`Array`的`filter()`也接收一个函数。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`true`还是`false`决定保留还是丢弃该元素。

利用`filter`，可以巧妙地去除`Array`的重复元素：

```
var
    r,
    arr = ['apple', 'strawberry', 'banana', 'pear', 'apple', 'orange', 'orange', 'strawberry'];
    r = arr.filter(function (element, index, self) {
    return self.indexOf(element) === index;
});
```

#### 11、sort()

```javascript
[10, 20, 1, 2].sort(); // [1, 10, 2, 20]
```

`Array`的`sort()`方法默认把所有元素先转换为String再排序，结果`'10'`排在了`'2'`的前面，因为字符`'1'`比字符`'2'`的ASCII码小。

幸运的是，`sort()`方法也是一个高阶函数，它还可以接收一个比较函数来实现自定义的排序。

```javascript
var arr = [10, 20, 1, 2];
arr.sort(function (x, y) {
    if (x < y) {
        return -1;
    }
    if (x > y) {
        return 1;
    }
    return 0;
});
console.log(arr); // [1, 2, 10, 20]
```

#### 12、Array中常见的高阶函数

##### a、every

`every()`方法可以判断数组的所有元素是否满足测试条件。

```javascript
var arr = ['Apple', 'pear', 'orange'];
console.log(arr.every(function (s) {
    return s.length > 0;
})); // true, 因为每个元素都满足s.length>0
```

##### b、find

`find()`方法用于查找符合条件的第一个元素，如果找到了，返回这个元素，否则，返回`undefined`：

```javascript
var arr = ['Apple', 'pear', 'orange'];
console.log(arr.find(function (s) {
    return s.toLowerCase() === s;
})); // 'pear', 因为pear全部是小写

console.log(arr.find(function (s) {
    return s.toUpperCase() === s;
})); // undefined, 因为没有全部是大写的元素

```

##### c、findIndex

`findIndex()`和`find()`类似，也是查找符合条件的第一个元素，不同之处在于`findIndex()`会返回这个元素的索引，如果没有找到，返回`-1`：

##### d、forEach

`forEach()`和`map()`类似，它也把每个元素依次作用于传入的函数，但不会返回新的数组。`forEach()`常用于遍历数组，因此，传入的函数不需要返回值：

```javascript
var arr = ['Apple', 'pear', 'orange'];
arr.forEach(console.log); // 依次打印每个元素
```

#### 13、箭头函数

在深入了解箭头函数前，需要弄明白this的指向和改变this指向的方法。

##### 01、this指向

**情况1：**如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是window，这里需要说明的是在js的严格版中this指向的不是window，但是我们这里不探讨严格版的问题(在严格版中的默认的this不再是window，而是undefined。)

**情况2：**如果一个函数中有this，这个函数有被上一级的对象所调用，那么this指向的就是上一级的对象。

**情况3：**如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也只是它上一级的对象

**特殊情况：**

**01、**如果返回值是一个对象，那么this指向的就是那个返回的对象，如果返回值不是一个对象那么this还是指向函数的实例。 
还有一点就是虽然null也是对象，但是在这里this还是指向那个函数的实例，因为null比较特殊。 

**02、**这里this指向的是window，是不是有些蒙了？其实是因为你没有理解一句话，这句话同样至关重要。

　　this永远指向的是最后调用它的对象，也就是看它执行的时候是谁调用的，例子4中虽然函数fn是被对象b所引用，但是在将fn赋值给变量j的时候并没有执行所以最终指向的是window 。

##### 02、改变this指向的方法

**第一种： new关键字改变this指向**

```javascript
//构造函数版this
function Fn(){
    this.user = "root";
}
var a = new Fn();
console.log(a.user); //root
```

用变量a创建了一个Fn的实例（相当于复制了一份Fn到对象a里面），此时仅仅只是创建，并没有执行，而调用这个函数Fn的是对象a，那么this指向的自然是对象a，那么为什么对象a中会有user，因为你已经复制了一份Fn函数到对象a中，用了new关键字就等同于复制了一份

**第二种： call（）**

```javascript
var a = {
    user:"追梦子",
    fn:function(){
        console.log(this.user); //追梦子
    }
}
var b = a.fn;
b.call(a);  //若不用call，则b()执行后this指的是Window对象
```

把b添加到第一个参数的环境中，简单来说，this就会指向那个对象。

**第三种：apply（）**

```javascript
var a = {
    user:"追梦子",
    fn:function(){
        console.log(this.user); //追梦子
    }
}
var b = a.fn;
b.apply(a);
```

**第四种：bind（）** 

```javascript
var a = {
    user:"root",
    fn:function(){
        console.log(this.user); //root
    }
}
var b = a.fn;
var c = b.bind(a);
c();
```

#### 14、包装对象

我们在使用`Number`、`Boolean`和`String`时，没有写`new`时，`Number()`、`Boolean`和`String()`被当做普通函数，把任何类型的数据转换为`number`、`boolean`和`string`类型（注意不是其包装类型）：

需要遵循的规则：

- 不要使用`new Number()`、`new Boolean()`、`new String()`创建包装对象；

- 用`parseInt()`或`parseFloat()`来转换任意类型到`number`；

- 用`String()`来转换任意类型到`string`，或者直接调用某个对象的`toString()`方法；

- 通常不必把任意类型转换为`boolean`再判断，因为可以直接写`if (myVar) {...}`；

- `typeof`操作符可以判断出`number`、`boolean`、`string`、`function`和`undefined`；

- 判断`Array`要使用`Array.isArray(arr)`；

- 判断`null`请使用`myVar === null`；

- 判断某个全局变量是否存在用`typeof window.myVar === 'undefined'`；

- 函数内部判断某个变量是否存在用`typeof myVar === 'undefined'`。

  `number`对象调用`toString()`报SyntaxError

  ```javascript
  123.toString(); // SyntaxError
  ```

  遇到这种情况，要特殊处理一下：

  ```javascript
  123..toString(); // '123', 注意是两个点！
  (123).toString(); // '123'
  ```

#### 15、json

JavaScript利用天气api获取天气情况（对json数据进行解析，然后获取需要的数据）

```javascript
var url = 'https://api.openweathermap.org/data/2.5/forecast?q=Beijing,cn&appid=800f49846586c3ba6e7052cfc89af16c';
$.getJSON(url, function (data) {
	    var info = {
        city: data.city.name,
        weather: data.list[0].weather[0].main,
        time: data.list[0].dt_txt,
        temp: parseInt(data.list[0].main.temp - 273.15) +"℃"
    };
    alert(JSON.stringify(info, null, '  '));
});
```

#### 16、ajax

jQuery在全局对象`jQuery`（也就是`$`）绑定了`ajax()`函数，可以处理AJAX请求。`ajax(url, settings)`函数需要接收一个URL和一个可选的`settings`对象，常用的选项如下：

- async：是否异步执行AJAX请求，默认为`true`，千万不要指定为`false`；
- method：发送的Method，缺省为`'GET'`，可指定为`'POST'`、`'PUT'`等；
- contentType：发送POST请求的格式，默认值为`'application/x-www-form-urlencoded; charset=UTF-8'`，也可以指定为`text/plain`、`application/json`；
- data：发送的数据，可以是字符串、数组或object。如果是GET请求，data将被转换成query附加到URL上，如果是POST请求，根据contentType把data序列化成合适的格式；
- headers：发送的额外的HTTP头，必须是一个object；
- dataType：接收的数据格式，可以指定为`'html'`、`'xml'`、`'json'`、`'text'`等，缺省情况下根据响应的`Content-Type`猜测。























