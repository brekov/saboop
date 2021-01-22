# Dart语言概览

## 变量

### var

类似于JavaScript中的var，它可以接收任何类型的变量，但最大的不同是Dart中var变量一旦赋值，类型便会确定，则不能再改变其类型，如：

```dart
var str = "haha";
String str1 = "haha";
int n = 10;
var n1 = 10;
var t="hi world";
// 下面代码在dart中会报错，因为变量t的类型已经确定为String，类型一旦确定后则不能再更改其类型。
t=1000;
```
在Dart中，当用var声明一个变量后，Dart在编译时会根据第一次赋值数据的类型来推断其类型，编译结束后其类型就已经被确定，而JavaScript是纯粹的弱类型脚本语言，var只是变量的声明方式而已。

### dynamic和Object

`Dynamic` 和 `Object` 与 `var` 功能相似，都会在赋值时自动进行类型推断，不同在于，赋值后可以改变其类型，如：

```dart
dynamic t;
t="hi world";
//下面代码没有问题
t=1000;
```
`Object` 是 `dart`所有对象的基类，也就是说所有类型都是 `Object` 的子类，所以任何类型的数据都可以赋值给 `Object` 声明的对象，所以表现效果和 `dynamic` 相似。

### final 和 const

使用  `final` 和 `const` 关键字都可以声明一个常量，但是与 `final` 不同的是 `const` 是编译期常量，也就是说在编译器编译时使用 `const` 声明的常量必须是已经确定的值。使用 `final` 和 `const` 修饰的变量不可以重新赋值。被 `final` 或者 `const` 修饰的变量，变量类型可以省略，如：
```dart
//可以省略类型的声明
final str = "hi world";
final str1;
str1 = "hello world!"
const str1 = "hi world"; //const String str1 = "hi world";
```
### 内置类型

num(包括int和double类型)

String

bool

List (和数组是一个概念)

Map 字典

Runes ( Unicode 字符串)

symbols 符号

下面看下简单的例子:

```dart
var str1 = "str1";s
var num1 = 1;
var double1 = 10.2;
var flag1 = false;
var list1 = [1, 3, 5, 7, 9];
var map1 = {"name": "flyou", "age": 12};
```

## 函数
Dart作为完全的面向对象的语言，函数也是对象，并且有一个类型Function。函数可以赋值给变量或作为参数传递给其他函数，这是函数式编程的典型特征。

### 函数声明

```dart
bool isNoble(int atomicNumber) {
    return _nobleGases[atomicNumber] != null;
}
```
如果没有显式声明返回值类型时会默认当做dynamic处理，注意，函数返回值没有类型推断：

```dart
typedef bool CALLBACK();

//不指定返回类型，此时默认为dynamic，不是bool
isNoble(int atomicNumber) {
    return _nobleGases[atomicNumber] != null;
}

void test(CALLBACK cb){
    print(cb()); 
}
//报错，isNoble不是bool类型
test(isNoble);
```
### 对于只包含一个表达式的函数，可以使用简写语法

```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] ！= null ;
```
### 函数作为变量

```dart
var say = (str){
    print(str);
};
say("hi world");
```
### 函数作为参数传递
```dart
void execute(var callback) {
    callback();
}
execute(()=>print("xxx"))
```
### 可选参数

包装一组函数参数，用[]标记为可选的位置参数：
```dart
String say(String from, String msg, [String device]) {
    var result = '$from says $msg';
    if (device != null) {
        result = '$result with a $device';
    }
    return result;
}
```
下面是一个不带可选参数调用这个函数的例子：
```dart
say('Bob', 'Howdy'); //结果是： Bob says Howdy
```
下面是用第三个参数调用这个函数的例子：
```dart
say('Bob', 'Howdy', 'smoke signal'); //结果是：Bob says Howdy with a smoke signal
```
如果函数有多个相同类型的函数参数，这个时候只输入一个值，它默认会赋值给第一个参数如：
```dart
void printStr([String name,String sex,String address]){
    print("Name is $name age is $sex address is $address");
}
...
printStr2("男");
```
输出：
```dart
Name is 男 sex is null address is null
```
当然这并不是我们想要的，所以就需要看下可选择名称的函数

### 可选的命名参数

定义函数时，使用{param1, param2, …}，用于指定命名参数。例如：
```dart
//设置[bold]和[hidden]标志
void enableFlags({bool bold, bool hidden}) {
    // ... 
}
```
调用函数时，可以使用指定命名参数。例如：paramName: value
```dart
enableFlags(bold: true, hidden: false);
```
可选命名参数在Flutter中使用非常多。

### 可选参数的默认值
    
函数的默认值，顾名思义就是你就算不输入这个参数我也会默认给你一个值的，当然前提是这个参数你可以省略的前提下（上面两类函数）

1. 可选参数

    ```dart
    void printStr([String name,int age=25]){
        print("Name is $name age is $age");
    }
    ...
    printStr("男");
    ```
    输出：
    ```dart
    Name is 男 age is 25
    ```
2. 具名函数
    ```dart
    void printStr({String name,int age=25}){
        print("Name is $name age is $age");
    }
    ...
    printStr("男");
    ```
    输出：
    ```dart
    Name is 男 age is 25
    ```

## 操作符

|  描述 | 操作符  |
|:-:|:-:|
|一元后缀|  expr++   expr–   ()   []   .   ?.（null安全） |	
|一元前缀|	-expr   !expr   ~expr   ++expr   –expr
|加减乘除	|+   -    *   /   %   ~/
|位移|	<<   >>|
|按位与	|  &  |
|按位XOR	|  ^  |
|按位或	|  \|  |
|关系判断	|>=   >   <=   <  ==   !=|
|类型转换和判断|as   is   is!|
|逻辑与	|&&|
|逻辑OR	| \|\| |
|如果为null	| ?? |
|三目运算	|expr1 ? expr2 : expr3|
|级联	|..|
|||

## 异步支持

Dart类库有非常多的返回 `Future` 或者 `Stream` 对象的函数。 这些函数被称为异步函数。

通过 `async` 和 `await` 关键词实现异步执行和同步代码很像的异步代码。

### Future

`Future` 与JavaScript中的 `Promise` 非常相似，表示一个异步操作的最终完成（或失败）及其结果值的表示。简单来说，它就是用于处理异步操作的，异步处理成功了就执行成功的操作，异步处理失败了就捕获错误或者停止后续操作。一个Future只会对应一个结果，要么成功，要么失败。

由于本身功能较多，这里我们只介绍其常用的API及特性。`Future` 的所有API的返回值仍然是一个 `Future` 对象，所以可以很方便的进行链式调用。

1. Future.then

    为了方便示例，在本例中我们使用Future.delayed 创建了一个延时任务（实际场景会是一个真正的耗时任务，比如一次网络请求），即2秒后返回结果字符串"hi world!"，然后我们在then中接收异步结果并打印结果，代码如下：

    ```dart
    Future.delayed(new Duration(seconds: 2),(){
        return "hi world!";
    }).then((data){
        print(data);
    });
    ```
2. Future.catchError

    如果异步任务发生错误，我们可以在catchError中捕获错误，我们将上面示例改为：

    ```dart
    Future.delayed(new Duration(seconds: 2),() {
        //return "hi world!";
        throw AssertionError("Error");  
    }).then((data){
        //执行成功会走到这里  
        print("success");
    }).catchError((e) {
        //执行失败会走到这里  
        print(e);
    });
    ```

    在本示例中，我们在异步任务中抛出了一个异常，then的回调函数将不会被执行，取而代之的是 `catchError` 回调函数将被调用；并不是只有 `catchError` 回调才能捕获错误，`then` 方法还有一个可选参数 `onError`，我们也可以它来捕获异常：

    ```dart
    Future.delayed(new Duration(seconds: 2), () {
        //return "hi world!";
        throw AssertionError("Error");
    }).then((data) {
        print("success");
    }, onError: (e) {
        print(e);
    });
    ```
    > `onError` 优先级要高于 `catchError`
3. Future.whenComplete

    有些时候，我们会遇到无论异步任务执行成功或失败都需要做一些事的场景，比如在网络请求前弹出加载对话框，在请求结束后关闭对话框。这种场景，有两种方法，第一种是分别在then或catch中关闭一下对话框，第二种就是使用Future的whenComplete回调，我们将上面示例改一下：

    ```dart
    Future.delayed(new Duration(seconds: 2),(){
        //return "hi world!";
        throw AssertionError("Error");
    }).then((data){
        //执行成功会走到这里 
        print(data);
    }).catchError((e){
        //执行失败会走到这里   
        print(e);
    }).whenComplete((){
        //无论成功或失败都会走到这里
    });
    ```
4. Future.wait

    有些时候，我们需要等待多个异步任务都执行结束后才进行一些操作，比如我们有一个界面，需要先分别从两个网络接口获取数据，获取成功后，我们需要将两个接口数据进行特定的处理后再显示到UI界面上，应该怎么做？答案是Future.wait，它接受一个Future数组参数，只有数组中所有Future都执行成功后，才会触发then的成功回调，只要有一个Future执行失败，就会触发错误回调。下面，我们通过模拟Future.delayed 来模拟两个数据获取的异步任务，等两个异步任务都执行成功时，将两个异步任务的结果拼接打印出来，代码如下：

    ```dart
    Future.wait([
      // 2秒后返回结果
      Future.delayed(new Duration(seconds: 2), () {
        return "hello";
      }),
      // 4秒后返回结果
      Future.delayed(new Duration(seconds: 4), () {
        return " world";
      })
    ]).then((results) {
      print(results[0] + results[1]);
    }).catchError((e) {
      print(e);
    });
    ```
    执行上面代码，4秒后你会在控制台中看到“hello world”。

### Async/await

Dart中的`async`/`await` 和JavaScript中的`async`/`await`功能和用法是一样的，如果已经了解JavaScript中的`async`/`await`的用法，可以直接跳过本节。

1. 回调地狱(Callback hell)

    如果代码中有大量异步逻辑，并且出现大量异步任务依赖其它异步任务的结果时，必然会出现`Future.then`回调中套回调情况。举个例子，比如现在有个需求场景是用户先登录，登录成功后会获得用户Id，然后通过用户Id，再去请求用户个人信息，获取到用户个人信息后，为了使用方便，我们需要将其缓存在本地文件系统，代码如下：

    ```dart
    //先分别定义各个异步任务
    Future<String> login(String userName, String pwd){
        ...
        //用户登录
    };
    Future<String> getUserInfo(String id){
        ...
        //获取用户信息 
    };
    Future saveUserInfo(String userInfo){
        ...
        // 保存用户信息 
    };
    ```
    接下来，执行整个任务流：

    ```dart
    login("alice","******").then((id){
        //登录成功后通过，id获取用户信息    
        getUserInfo(id).then((userInfo){
            //获取用户信息后保存 
            saveUserInfo(userInfo).then((){
            //保存用户信息，接下来执行其它操作
                ...
            });
        });
    })
    ```

    如果业务逻辑中有大量异步依赖的情况，将会出现上面这种在回调里面套回调的情况，过多的嵌套会导致的代码可读性下降以及出错率提高，并且非常难维护，这个问题被形象的称为回调地狱（Callback hell）。回调地狱问题在之前JavaScript中非常突出，也是JavaScript被吐槽最多的点，但随着 `ECMAScript6`和 `ECMAScript7`标准发布后，这个问题得到了非常好的解决，而解决回调地狱的两大神器正是 `ECMAScript6` 引入了 `Promise`，以及 `ECMAScript7` 中引入的 `async`/`await`。 而在Dart中几乎是完全平移了JavaScript中的这两者：`Future` 相当于 `Promise`，而 `async`/`await` 连名字都没改。接下来我们看看通过 `Future` 和 `async`/`await` 如何消除上面示例中的嵌套问题。

2. 使用Future消除callback hell
    ```dart
    login("alice","******").then((id){
        return getUserInfo(id);
    }).then((userInfo){
        return saveUserInfo(userInfo);
    }).then((r){
        //执行接下来的操作 
    }).catchError((e){
        //错误处理  
        print(e);
    });
    ```

    正如上文所述， “`Future` 的所有API的返回值仍然是一个 `Future` 对象，所以可以很方便的进行链式调用” ，如果在 `then` 中返回的是一个 `Future` 的话，该 `Future` 会执行，执行结束后会触发后面的 `then` 回调，这样依次向下，就避免了层层嵌套。

3. 使用async/await消除callback hell

    通过 `Future` 回调中再返回 `Future` 的方式虽然能避免层层嵌套，但是还是有一层回调，有没有一种方式能够让我们可以像写同步代码那样来执行异步任务而不使用回调的方式？答案是肯定的，这就要使用 `async`/`await` 了，下面我们先直接看代码，然后再解释，代码如下：

    ```dart
    task() async {
        try{
            String id = await login("alice","******");
            String userInfo = await getUserInfo(id);
            await saveUserInfo(userInfo);
            //执行接下来的操作   
        } catch(e){
            //错误处理   
            print(e);   
        }  
    }
    ```

    `async` 用来表示函数是异步的，定义的函数会返回一个 `Future` 对象，可以使用 `then` 方法添加回调函数。

    `await` 后面是一个 `Future` ，表示等待该异步任务完成，异步完成后才会往下走；`await` 必须出现在 `async` 函数内部。

    可以看到，我们通过async/await将一个异步流用同步的代码表示出来了。

    其实，无论是在JavaScript还是Dart中，`async`/`await` 都只是一个语法糖，编译器或解释器最终都会将其转化为一个 `Promise（Future）` 的调用链。

### Stream

`Stream` 也是用于接收异步事件数据，和 `Future` 不同的是，它可以接收多个异步操作的结果（成功或失败）。 也就是说，在执行异步任务时，可以通过多次触发成功或失败事件来传递结果数据或错误异常。 `Stream` 常用于会多次读取数据的异步任务场景，如网络内容下载、文件读写等。举个例子：

```dart
Stream.fromFutures([
    // 1秒后返回结果
    Future.delayed(new Duration(seconds: 1), () {
        return "hello 1 seconds";
    }),
    // 抛出一个异常
    Future.delayed(new Duration(seconds: 2),(){
        throw AssertionError("error 2 seconds");
    }),
    // 3秒后返回结果
    Future.delayed(new Duration(seconds: 3), () {
        return "hello 3 seconds";
    })
]).listen((data){
    print(data);
}, onError: (e){
    print(e.message);
},onDone: () {
});
```

上面的代码依次会输出：
```
hello 1 seconds
error 2 seconds
hello 3 seconds
```

## 异常
和 `Java` 不同，所有的 Dart 异常是非检查异常。方法不一定声明了所抛出的异常，并且要求捕获任何异常。

`Dart` 提供了 `Exception` 和 `Error` 类型， 以及一些子类型。也可以定义自己的异常类型。但是， `Dart` 抛出任何非 `null` 对象为异常。

### 抛出异常

下面是抛出或者 扔出一个异常的示例：
```dart
throw new Exception('this is a Exception');
```

还可以抛出任意的对象：
```dart
throw 'this is a string';
```

### 异常捕获

可以使用 `on` 或者 `catch` 来声明捕获语句，也可以同时使用。使用 `on` 来指定异常类型，使用 `catch` 来捕获异常对象。

捕获异常可以避免异常继续传递（重新抛出异常除外）：
```dart
try {
    doSomethingWithFormatException();
} on FormatException {
    doSomething();
}
```
对于可以抛出多种类型异常的代码，可以指定多个捕获语句。每个语句分别对应一个异常类型，如果捕获语句没有指定异常类型，则可以捕获任何异常类型：
```dart
try {
    doSomethingWithFormatException();
} on FormatException {
    // 捕获特定异常
    doSomething();
} on Exception catch (e) {
    // 捕获非特定异常
    print('Unknown exception: $e');
} catch (e) {
    // 捕获抛出的任意数据
    print('Something unknown: $e');
}
```

函数 `catch()` 可以带有一个或者两个参数， 第一个参数为抛出的异常对象， 第二个为堆栈信息 ( `StackTrace` 对象)。
```dart
...
} on Exception catch (e) {
    print('Exception details:\n $e');
} catch (e, s) {
    print('Exception details:\n $e');
    print('Stack trace:\n $s');
}
```

### 重新抛出异常
    
`rethrow` 关键字可以 把捕获的异常给 重新抛出。

```dart
final foo = '';

void misbehave() {
    try {
        foo = "You can't change a final variable's value.";
    } catch (e) {
        print('misbehave() partially handled ${e.runtimeType}.');
        rethrow; // Allow callers to see the exception.
    }
}

void main() {
    try {
        misbehave();
    } catch (e) {
        print('main() finished handling ${e.runtimeType}.');
    }
}
```
### Finally

要确保某些代码执行，不管有没有出现异常都需要执行，可以使用 `finally` 语句来实现。如果没有 `catch` 语句来捕获异常， 则在执行完 `finally` 语句后， 异常被抛出：
```dart
try {
    doSomethingWithFormatException();
} finally {
    // Always doSomething, even if an exception is thrown.
    doSomething();
}
```
定义的 `finally` 语句在任何匹配的 `catch` 语句之后执行：
```dart
try {
    doSomethingWithFormatException();
} catch(e) {
    print('Error: $e');  // Handle the exception first.
} finally {
    doSomething();  // Then doSomething.
}
```

## Classes
`Dart` 是一个面向对象编程语言，同时支持基于 `mixin` 的继承机制。 每个对象都是一个类的实例，所有的类都继承于 `Object` 。 基于 `mixin` 的继承机制，意味着每个类（`Object` 除外）都只有一个超类，一个类的代码可以在其他多个类继承中重复使用。

1### 命名构造函数

使用命名构造函数可以为一个类实现多个构造函数， 或者使用命名构造函数来更清晰的表明你的意图：
```dart
class Point {
    num x;
    num y;

    Point(this.x, this.y);

    // Named constructor
    Point.fromJson(Map json) {
        x = json['x'];
        y = json['y'];
    }
}
```
> 注意：构造函数不能继承，所以超类的命名构造函数也不会被继承。如果希望子类也有超类一样的命名构造函数， 必须在子类中实现该构造函数。

### 常量构造函数

如果你的类提供一个状态不变的对象，可以把这些对象定义为编译时常量。要实现这个功能，需要定义一个 `const` 构造函数， 并且声明所有类的变量为 `final` 。
```dart
class ImmutablePoint {
    final num x;
    final num y;
    const ImmutablePoint(this.x, this.y);
    static final ImmutablePoint origin = const ImmutablePoint(0, 0);
}
```

### 初始化列表

在构造函数体执行之前除了可以调用超类构造函数之外，还可以 初始化实例参数。 使用逗号分隔初始化表达式。
``` dart
class Point {
    num x;
    num y;
    Point(this.x, this.y);

    // Initializer list sets instance variables before
    // the constructor body runs.
    Point.fromJson(Map jsonMap)
        : x = jsonMap['x'],
        y = jsonMap['y'] {
        print('In Point.fromJson(): ($x, $y)');
    }
}
```
### 调用超类构造函数

构造函数参数后使用冒号 (`:`) 可以调用超类构造函数，由于超类构造函数的参数在构造函数执行之前执行，所以参数可以是一个表达式或者 一个方法调用：
```dart
class Employee extends Person {
    // ...
    Employee(int age) : _age = age, super.fromJson(findDefaultData());
}
```
> 不管你在何处调用 `super()` ， 超类的构造函数只有在所有成员初始化完成后才会执行。所以应该把 `super()` 放到其他成员初始化之后。

### 工厂方法构造函数

如果一个构造函数并不总是返回一个新的对象，则使用 factory 来定义 这个构造函数。例如，一个工厂构造函数 可能从缓存中获取一个实例并返回，或者 返回一个子类型的实例。
```dart
class Logger {
    final String name;
    bool mute = false;

    // _cache is library-private
    static final Map<String, Logger> _cache = <String, Logger>{};

    factory Logger(String name) {
        if (_cache.containsKey(name)) {
            return _cache[name];
        } else {
            final logger = new Logger._internal(name);
            _cache[name] = logger;
            return logger;
        }
    }

    Logger._internal(this.name);

    void log(String msg) {
        if (!mute) {
            print(msg);
        }
    }
}
```
> 注意： 工厂构造函数无法访问 this。

### 抽象类

使用 abstract 修饰符定义一个抽象类 —— 一个不能被实例化的类。 抽象类通常用来定义接口， 以及部分实现。如果你希望你的抽象类是可示例化的，则定义一个工厂 构造函数。

抽象类通常具有 `抽象函数`。 下面是定义具有抽象函数的抽象类的示例：
```dart
// This class is declared abstract and thus
// can't be instantiated.
abstract class AbstractContainer {
    // ...Define constructors, fields, methods...

    void updateChildren(); // Abstract method.
}
```
下面的类不是抽象的，但是定义了一个抽象函数，这样 的类是可以被实例化的：

```dart
class SpecializedContainer extends AbstractContainer {
    // ...Define more constructors, fields, methods...

    void updateChildren() {
        // ...Implement updateChildren()...
    }

    // Abstract method causes a warning but
    // doesn't prevent instantiation.
    void doSomething();
}
```

### 隐式接口
每个类都隐式的定义了一个包含所有实例成员的接口， 并且这个类实现了这个接口。如果你想创建类 `A` 来支持类 `B` 的 api，而不想继承 `B` 的实现，则类 `A` 应该实现 `B` 的接口。

一个类可以通过 `implements` 关键字来实现一个或者多个接口， 并实现每个接口定义的 API。 例如：
```dart
// A person. The implicit interface contains greet().
class Person {
    // In the interface, but visible only in this library.
    final _name;

    // Not in the interface, since this is a constructor.
    Person(this._name);

    // In the interface.
    String greet(who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Imposter implements Person {
    // We have to define this, but we don't use it.
    final _name = "";

    String greet(who) => 'Hi $who. Do you know who I am?';
}

greetBob(Person person) => person.greet('bob');

main() {
    print(greetBob(new Person('kathy')));
    print(greetBob(new Imposter()));
}
```
下面是实现多个接口的示例：

```dart
class Point implements Comparable, Location {
    // ...
}
```
> 注意： 从 Dart 1.13 开始， 这两个限制在 Dart VM 上 没有那么严格了. 
> * `Mixins` 可以继承其他类，不再限制为继承 `Object`. 
> * `Mixins` 可以调用 `super()`。
>
>这种 “super mixins” 还 无法在 `dart2js` 中使用并且需要在 `artanalyzer` 中使用 `--supermixin` 参数。

### 为类添加新的功能

Mixins 是一种在多类继承中重用一个类代码的方法。

使用 `with` 关键字后面为一个或者多个 `mixin` 名字来使用 `mixin`。 下面是示例显示了如何使用 `mixin`:

```dart
class Musician extends Performer with Musical {
    // ...
}

class Maestro extends Person with Musical, Aggressive, Demented {
    Maestro(String maestroName) {
        name = maestroName;
        canConduct = true;
    }
}
```
定义一个类继承 Object，该类没有构造函数， 不能调用 `super` ，则该类就是一个 mixin。例如：

```dart
abstract class Musical {
    bool canPlayPiano = false;
    bool canCompose = false;
    bool canConduct = false;

    void entertainMe() {
        if (canPlayPiano) {
            print('Playing piano');
        } else if (canConduct) {
            print('Waving hands');
        } else {
            print('Humming to self');
        }
    }
}
```

### 可调用的类

如果 Dart 类实现了 call() 函数则 可以当做方法来调用。

### 其他

1. `?. `来替代 `.` 来调用对象的变量或者方法，可以避免当左边对象为 `null` 时候抛出异常

    ```dart
    // If p is non-null, set its y value to 4.
    p?.y = 4;
    ````

2. 有些类提供了常量构造函数。使用常量构造函数可以创建编译时常量，要使用常量构造函数只需要用 `const` 替代 `new` 即可：
    ```dart
    var p = const ImmutablePoint(2, 2);
    ```
    两个一样的编译时常量是同一个对象：
    ```dart
    var a = const ImmutablePoint(1, 1);
    var b = const ImmutablePoint(1, 1);

    assert(identical(a, b)); // They are the same instance!
    ```

3. 可以使用 `Object` 的 `runtimeType` 属性来判断实例 的类型，该属性返回一个 `Type` 对象。
    ```dart
    print('The type of a is ${a.runtimeType}');
    ```

## 泛型

泛型可以避免重复的代码，类似于 `Java` 里面的泛型。你只需要创建一个接口即可：
```dart
abstract class Cache<T> {
  T getByKey(String key);
  setByKey(String key, T value);
}
```
> 在上面的代码中，T 是一个备用类型。这是一个类型占位符， 在开发者调用该接口的时候会指定具体类型。

`Dart` 的泛型类型是固化的，在运行时有也 可以判断具体的类型。例如在运行时（甚至是成产模式） 也可以检测集合里面的对象类型：

```dart
var names = new List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```
> 注意：`is` 表达式只是判断集合的类型，而不是集合里面具体对象的类型。 在生产模式，`List<String>` 变量可以包含非字符串类型对象。对于这种情况，你可以选择分别判断每个对象的类型或者处理类型转换异常。

> 注意： Java 中的泛型信息是编译时的，泛型信息在运行时是不纯在的。 在 `Java` 中你可以测试一个对象是否为 `List` ，但是你无法测试一个对象是否为 `List<String>`。

> 版本说明： 一开始，泛型只能在 `Dart` 类中使用。在 `SDK` 版本号为 `1.21` 或者更高版本，新的语法也支持在函数和方法上使用泛型了。


## 函数

### Getters and setters

`getters` 和 `setters` 是用来设置和访问对象属性的特殊 函数。每个实例变量都隐含的具有一个 `getter，` 如果变量不是 `final` 的则还有一个 `setter`。 你可以通过实行 `getter` 和 `setter` 来创建新的属性， 使用 get 和 set 关键字定义 `getter` 和 `setter`：

```dart
class Rectangle {
    num left;
    num top;
    num width;
    num height;

    Rectangle(this.left, this.top, this.width, this.height);

    // Define two calculated properties: right and bottom.
    num get right             => left + width;
        set right(num value)  => left = value - width;
    num get bottom            => top + height;
        set bottom(num value) => top = value - height;
}

main() {
    var rect = new Rectangle(3, 4, 20, 15);
    assert(rect.left == 3);
    rect.right = 12;
    assert(rect.left == -8);
}
```

### 抽象函数

实例函数、 `getter`、和 `setter` 函数可以为抽象函数， 抽象函数是只定义函数接口但是没有实现的函数，由子类来实现该函数。如果用分号来替代函数体则这个函数就是抽象函数。
```dart
abstract class Foo {
    // Define instance variables and methods...
    void doSomething(); // Define an abstract method.
}

class Bar extends Foo {
    void doSomething() {
        // Provide an implementation, so the method is not abstract here...
    }
}
```
> 调用一个没实现的抽象函数会导致运行时异常。

## 可覆写的操作符

下表中的操作符可以被覆写。 例如，如果你定义了一个 `Vector` 类， 可以定义一个 `+` 函数来实现两个向量相加。

|||||
|:-:|:-:|:-:|:-:|
| < | + | \| | [] |
| >	| \/ | ^ | []= | 
| <= | ~/ | & | ~ |
| >= | * | << | == |
| –	|  % | >> | |

下面是覆写了 `+` 和 `-` 操作符的示例：

```dart
class Vector {
    final int x;
    final int y;
    const Vector(this.x, this.y);

    /// Overrides + (a + b).
    Vector operator +(Vector v) {
        return new Vector(x + v.x, y + v.y);
    }

    /// Overrides - (a - b).
    Vector operator -(Vector v) {
        return new Vector(x - v.x, y - v.y);
    }
}

main() {
    final v = new Vector(2, 3);
    final w = new Vector(2, 2);

    // v == (2, 3)
    assert(v.x == 2 && v.y == 3);

    // v + w == (4, 5)
    assert((v + w).x == 4 && (v + w).y == 5);

    // v - w == (0, 1)
    assert((v - w).x == 0 && (v - w).y == 1);
}
```
> 如果你覆写了 `==` ，则还应该覆写对象的 `hashCode` 的 `getter` 函数。 

## 库和可见性

使用 `import` 和 `library` 指令可以帮助你创建 模块化的可分享的代码。库不仅仅提供 API， 还是一个私有单元：以下划线 (`_`) 开头的标识符只有在库 内部可见。每个 `Dart app` 都是一个库， 即使没有使用 `library` 命令也是一个库。

### 使用库

使用 `import` 来指定一个库如何使用另外一个库。

```dart    
import 'dart:html';
import 'dart:io';
import 'package:mylib/mylib.dart';
import 'package:utils/utils.dart';
```
### 指定库前缀(别名)

如果你导入的两个库具有冲突的标识符， 则你可以使用库的前缀来区分。 例如，如果 library1 和 library2 都有一个名字为 Element 的类， 你可以这样使用：

```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;
// ...
Element element1 = new Element();           // Uses Element from lib1.
lib2.Element element2 = new lib2.Element(); // Uses Element from lib2.
```

### 导入库的一部分

如果你只使用库的一部分功能，则可以选择需要导入的内容。例如：
```dart
// Import only foo.
import 'package:lib1/lib1.dart' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;
```

### 延迟载入库

Deferred loading (也称之为 lazy loading) 可以让应用在需要的时候再加载库。 下面是一些使用延迟加载库的场景：

* 减少 APP 的启动时间。
* 执行 A/B 测试，例如尝试各种算法的不同实现。
* 加载很少使用的功能，例如可选的屏幕和对话框。

要延迟加载一个库，需要先使用 `deferred as` 来 导入：

```dart
import 'package:deferred/hello.dart' deferred as hello;
```

当需要使用的时候，使用库标识符调用 `loadLibrary()` 函数来加载库：

```dart
greet() async {
    await hello.loadLibrary();
    hello.printGreeting();
}
```
在前面的代码， 使用 `await` 关键字暂停代码执行一直到库加载完成。 关于 `async` 和 `await` 的更多信息请参考`异步支持`。

> 同一个库可以多次调用 `loadLibrary()` 函数。 但是该库只是载入一次。

使用延迟加载库的时候，请注意一下问题：

* 延迟加载库的常量在导入的时候是不可用的。 只有当库加载完毕的时候，库中常量才可以使用。
* 在导入文件的时候无法使用延迟库中的类型。 如果你需要使用类型，则考虑把接口类型移动到另外一个库中，让两个库都分别导入这个接口库。
* Dart 隐含的把 `loadLibrary()` 函数导入到使用 `deferred as` 的命名空间中。 `loadLibrary()` 方法返回一个 `Future`。


## Isolates

[x] todo

## Typedefs

在 `Dart` 语言中，方法也是对象。 使用 `typedef`, 或者 `function-type alias` 来为方法类型命名， 然后可以使用命名的方法。 当把方法类型赋值给一个变量的时候，`typedef` 保留类型信息。

下面的代码没有使用 typedef：
```dart
class SortedCollection {
  Function compare;

  SortedCollection(int f(Object a, Object b)) {
    compare = f;
  }
}

 // Initial, broken implementation.
 int sort(Object a, Object b) => 0;

main() {
  SortedCollection coll = new SortedCollection(sort);

  // 我们只知道 compare 是一个 Function 类型，
  // 但是不知道具体是何种 Function 类型？
  assert(coll.compare is Function);
}
```

当把 `f` 赋值给 `compare` 的时候， 类型信息丢失了。 `f` 的类型是 `(Object, Object) → int` (这里 `→` 代表返回值类型)， 当然该类型是一个 `Function`。如果我们使用显式的名字并保留类型信息， 可以使用 `typedef`：

```dart
typedef int Compare(Object a, Object b);

class SortedCollection {
  Compare compare;

  SortedCollection(this.compare);
}

 // Initial, broken implementation.
 int sort(Object a, Object b) => 0;

main() {
  SortedCollection coll = new SortedCollection(sort);
  assert(coll.compare is Function);
  assert(coll.compare is Compare);
}
```
> 注意： 目前，typedefs 只能使用在 function 类型上，但是将来 可能会有变化。


由于 typedefs 只是别名，他们还提供了一种 判断任意 function 的类型的方法。例如：

```dart
typedef int Compare(int a, int b);

int sort(int a, int b) => a - b;

main() {
  assert(sort is Compare); // True!
}
```

## 元数据注解

定义自己的元数据注解。 下面的示例定义了一个带有两个参数的 `@todo` 注解：

```dart
library todo;

class todo {
  final String who;
  final String what;

  const todo(this.who, this.what);
}
```
使用 @todo 注解的示例：

```dart
import 'todo.dart';

@todo('seth', 'make this do something')
void doSomething() {
  print('do something');
}
```
元数据可以在 `library`、 `class`、 `typedef`、 `type parameter`、 `constructor`、 `factory`、 `function`、 `field`、 `parameter`、或者 `variable` 声明之前使用，也可以在 `import` 或者 `export` 指令之前使用。 使用反射可以在运行时获取元数据信息。
