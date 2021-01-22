# Dart 核心库
## dart:core
Dart core 库提供了少量的但是非常核心的功能。每个 Dart 应用都会自动导入这个 库。

### Numbers

dart:core 库定义了 num, int, 和 double 类，这些类 定义一些操作数字的基础功能。

#### Strings and regular expressions（字符串和正则表达式）

`Dart` 中的字符串是一个不可变的 `UTF-16` 码元（`code units`）序列。在语言预览中对 字符串有详细的介绍。 你可以使用正则表达式（`RegExp` 对象）来搜索字符串中的内容或者替换部分字符串。

#### Collections

Dart 提供了一些核心的集合 API，包含 lists, sets, 和 maps。

#### Lists

在语言预览中已经介绍过如何 创建 lists了。另外还可以 使用 List 构造函数来创建 List 对象。 List 类来定义了一些函数可以添加或者删除里面的数据。

#### URIs

Uri 类 提供了编码和解码 `URI`（`URL`） 字符的功能。 这些函数处理 `URI` 特殊的字符，例如 `&` 和 `=`。 `Uri` 类还可以解析和处理 `URI` 的每个部分，比如 `host`, `port`, `scheme` 等。

#### Dates and times

DateTime 对象代表某个时刻。时区是 UTC 或者 本地时区。

#### Utility classes（工具类）

核心库还包含了一些常用的工具类，比如排序、 值映射以及遍历数据等。

#### Exceptions

`Dart` 核心库定义了很多常见的异常和错误类。异常通常是一些可以预知和预防的例外情况。错误这是无法预料的并且不需要预防的情况。

## dart:async

异步编程通常使用回调函数，但是 `Dart` 提供了另外的 选择： `Future` 和 `Stream` 对象。 `Future` 和 `JavaScript` 中的 `Promise` 类似，代表在将来某个时刻会返回一个结果。`Stream` 是一种用来获取一些列数据的方式，例如事件流。

## dart:math

`Math` 库提供了常见的数学运算功能，例如 `sine` 和 `cosine`， 最大值、最小值等，还有各种常量 例如 `pi` 和 `e` 等。`Math` 库中的大部分函数都是顶级方法。

#### Trigonometry

Math 库中提供了常见的三角运算功能，这些函数是基于弧度的不是基于角度的。

#### Maximum and minimum
    
Math 库提供了 max() 和 min() 函数用来计算最大值和最小值。

#### Math constants
    
Math 库中提供各种数学常量，例如 `pi`, `e` 等。

#### Random numbers
    
使用 Random 类可以生成随机数。 在 Random 构造函数中还可以提供一个随机种子：
```dart
var random = new math.Random();
random.nextDouble(); // Between 0.0 and 1.0: [0, 1)
random.nextInt(10);  // Between 0 and 9.
```
也可以生成随机的布尔值：
```dart
var random = new math.Random();
random.nextBool();  // true or false
```

## dart:html

如果要和浏览器打交道则需要使用 `dart:html` 库， 访问 `DOM` 元素和使用 `HTML5` `API`。 

`dart:html` 还可以用来操作样式表（`CSS`）、用 `HTTP` 请求来获取数据，使用 `WebSockets` 来获取数据。

> 只有 Web 应用可以使用 dart:html，命令行应用无法使用该库。

## dart:io
`dart:io` 库 提供了一些和 文件、目录、进程、`sockets`、 `WebSockets`、和 `HTTP` 客户端以及服务器的 `API`。 

> 只有命令行应用可以使用 `dart:io` 库，web app 无法使用。

一般而言，`dart:io` 库实现和提供的是异步 `API`。 同步函数很容易阻塞应用，后期扩展起来非常麻烦。 因此，大部分的操作返回值都是 `Future` 或者 `Stream` 对象。

`dart:io` 里面也有一小部分同步方法，这些方法都使用 `sync` 前缀命名方法名字。 

#### Files and directories
`I/O` 库可以让命令行应用读写文件和查看目录。 读取文件有两种方式：一次读完或者通过流的方式来读取。 一次读完需要把文件内容读到内存中，如果文件 非常大或者你希望一边读文件一边处理，则应该 使用 `Stream，` 在流式读取文件中介绍。

1. Reading a file as text
    
    对于编码为 UTF-8 的文本，可以使用函数 readAsString() 一次性 的读取整个文本。如果单行文字比较重要，则可以 使用 readAsLines() 来读取。 这两个函数返回一个 Future 对象，当文件 读取完的时候，可以从 Future 对象获取一个或者多个字符串。

    ```dart
    import 'dart:io';

    main() async {
        var config = new File('config.txt');
        var contents;

        // Put the whole file in a single string.
        contents = await config.readAsString();
        print('The entire file is ${contents.length} characters long.');

        // Put each line of the file into its own string.
        contents = await config.readAsLines();
        print('The entire file is ${contents.length} lines long.');
    }
    ```

2. Reading a file as binary

    `readAsBytes()` 函数返回值为 Future， 当读完文件后，可以从 Future 中获取数据。
    ```dart
    import 'dart:io';

    main() async {
        var config = new File('config.txt');

        var contents = await config.readAsBytes();
        print('The entire file is ${contents.length} bytes long');
    }
    ```

3. Handling errors

    在 Future 上注册一个 catchError 来处理异常， 还可以在 async 方法中使用 try-catch 来 处理异常：
    ```dart
    import 'dart:io';

    main() async {
        var config = new File('config.txt');
        try {
            var contents = await config.readAsString();
            print(contents);
        } catch (e) {
            print(e);
        }
    }
    ```
    4. Streaming file contents

    使用 `Stream` 读取文件的时候， 使用 `Stream API` 或者 `await for` 可以一点点的读取。

    ```dart
    import 'dart:io';
    import 'dart:convert';
    import 'dart:async';

    main() async {
        var config = new File('config.txt');
        Stream<List<int>> inputStream = config.openRead();

        var lines = inputStream
            .transform(UTF8.decoder)
            .transform(new LineSplitter());
        try {
            await for (var line in lines) {
            print('Got ${line.length} characters from stream');
            }
            print('file is now closed');
        } catch (e) {
            print(e);
        }
    }
    ```

5. Writing file contents

    使用 `IOSink` 可以往文件 写入内容。使用 `File` 的 `openWrite()` 函数获取到一个 `IOSink。` 默认的写模式为 `FileMode.WRITE`，新写入的数据会完全覆盖文件之前的内容。

    ```dart
    var logFile = new File('log.txt');
    var sink = logFile.openWrite();
    sink.write('FILE ACCESSED ${new DateTime.now()}\n');
    sink.close();
    ```
    如果想在文件末尾追加内容，则可以使用 `mode` 可选参数，参数取值为 `FileMode.APPEND`.

    使用 `add(List<int> data)` 函数可以写二进制数据到文件。

6. Listing files in a directory

    查找目录中的所有文件和子目录是一个异步操作。 `list()` 函数返回一个 `Stream`，当遇到文件或者子目录的时候， `Stream` 就发射一个对象。

    ```dart
    import 'dart:io';

    main() async {
        var dir = new Directory('/tmp');

        try {
            var dirList = dir.list();
            await for (FileSystemEntity f in dirList) {
            if (f is File) {
                print('Found file ${f.path}');
            } else if (f is Directory) {
                print('Found dir ${f.path}');
            }
            }
        } catch (e) {
            print(e.toString());
        }
    }
    ```

7. Other common functionality
    File 和 Directory 类包含其他的一些文件操作， 下面只是一些常见的函数：

    创建文件或者目录： create() in File and Directory

    删除文件或者目录： delete() in File and Directory

    获取文件的长度： length() in File

    随机位置访问文件： open() in File


####  HTTP clients and servers
    
`dart:io` 库提供了一些命令行应用可以用来访问 `HTTP` 资源和运行 `HTTP` 服务器的类。

1. HTTP server

    `HttpServer` 类提供了用来建构 `Web` 服务器的底层方法。可以匹配请求处理、设置 `header`、处理数据等。

    下面的示例项目只能返回简单的文本信息。 服务器监听本机地址 127.0.0.1 的 8888 端口， 响应来自于 /languages/dart 路径的请求。所有其他的 请求都有默认的请求处理器处理（返回 404 页面没发现的错误提示）。

    ```dart
    import 'dart:io';

    main() async {
        dartHandler(HttpRequest request) {
            request.response.headers.contentType =
                new ContentType('text', 'plain');
            request.response.write('Dart is optionally typed');
            request.response.close();
        }

        var requests = await HttpServer.bind('127.0.0.1', 8888);
        await for (var request in requests) {
            print('Got request for ${request.uri.path}');
            if (request.uri.path == '/languages/dart') {
            dartHandler(request);
            } else {
            request.response.write('Not found');
            request.response.close();
            }
        }
    }
    ```
2. HTTP client
    
    `HttpClient` 类可以 在命令行应用程序或者服务器应用程序中使用，用来请求 `HTTP` 资源。 可以设置请求头、`HTTP` 请求方式和读写数据。`HttpClient` 无法在 `web` 应用中使用。 在 `web` 应用中可以使用 `HttpRequest` `class。` 下面是使用 HttpClient 的一个示例：

    ```dart
    import 'dart:io';
    import 'dart:convert';

    main() async {
        var url = Uri.parse(
            'http://127.0.0.1:8888/languages/dart');
        var httpClient = new HttpClient();
        var request = await httpClient.getUrl(url);
        print('have request');
        var response = await request.close();
        print('have response');
        var data = await response.transform(UTF8.decoder).toList();
        var body = data.join('');
        print(body);
        httpClient.close();
    }
    ```

## dart:convert

`dart:convert` 库里面有一些用来转换 `JSON` 和 `UTF-8` 的转换器，还可以自定义新的转换器。 `JSON` 是非常流行的数据格式。 `UTF-8` 是一种非常流行的编码格式， 能够代表所有 `Unicode` 字符集。

####  Decoding and encoding JSON

使用 JSON.decode() 函数把 JSON 字符串解码为 Dart 对象：
```dart
import 'dart:convert' show JSON;

main() {
    // NOTE: Be sure to use double quotes ("),
    // not single quotes ('), inside the JSON string.
    // This string is JSON, not Dart.
    var jsonString = '''
    [
        {"score": 40},
        {"score": 80}
    ]
    ''';

    var scores = JSON.decode(jsonString);
    assert(scores is List);

    var firstScore = scores[0];
    assert(firstScore is Map);
    assert(firstScore['score'] == 40);
}
```
使用 JSON.encode() 可以把 `Dart` 对象 编码为 `JSON` 字符串：

```dart
import 'dart:convert' show JSON;

main() {
    var scores = [
        {'score': 40},
        {'score': 80},
        {'score': 100, 'overtime': true, 'special_guest': null}
    ];

    var jsonText = JSON.encode(scores);
    assert(jsonText == '[{"score":40},{"score":80},'
                        '{"score":100,"overtime":true,'
                        '"special_guest":null}]');
}
```
默认只支持 `int`、`double`、`String`、`bool`、`null`、`List`或者 `Map`（`key` 需要为 `string`） 这些类型转换为 `JSON`。 集合对象会使用递归的形式来转换每个对象。

对于默认不支持的对象，可以有两种选择： 一，调用 `encode()` 并指定第二个参数，该参数是一个函数用来返回一个默认支持的对象； 二，不指定第二个参数，则会 调用该对象的 `toJson()` 函数。

####  Decoding and encoding UTF-8 characters
    
使用 UTF8.decode() 来解码 UTF8-encoded 字节流为 Dart 字符串：

```dart
import 'dart:convert' show UTF8;

main() {
    var string = UTF8.decode([
        0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
        0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
        0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
        0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
        0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
    ]);
    print(string);//Îñţérñåţîöñåļîžåţîờñ
}
```

如果是 `stream` 字节流则可以在 `Stream` 的 `transform()` 函数上指定 `UTF8.decoder `：
```dart
var lines = inputStream
    .transform(UTF8.decoder)
    .transform(new LineSplitter());
await for (var line in lines) {
    print('Got ${line.length} characters from stream');
}
```

使用 `UTF8.encode()` 把字符串编码为 `UTF8` 字节流：

```dart
import 'dart:convert' show UTF8;

main() {
    List<int> expected = [
        0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
        0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
        0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
        0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
        0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
    ];

    List<int> encoded = UTF8.encode('Îñţérñåţîöñåļîžåţîờñ');

    assert(() {
        if (encoded.length != expected.length) return false;
        for (int i = 0; i < encoded.length; i++) {
            if (encoded[i] != expected[i]) return false;
        }
        return true;
    });
}
```

## dart:mirrors - reflection

dart:mirrors 库提供了基本的反射支持。 使用 `mirror` 来查询程序的结构，也可以在运行时动态的调用方法或者函数。

> 警告： 使用 `dart:mirrors` 可能会导致 `dart2js` 生成的 `JavaScript` 代码文件非常大！
>
> 目前的解决方式是在导入 dart:mirrors 之前添加一个 @MirrorsUsed 注解。 详情请参考 MirrorsUsed API 文档。由于 dart:mirrors 库依然还在开发中，所以这个解决方案以后很有可能发生变化。 

### Symbols

`mirror` 系统使用 `Symbol` 类对象 来表达定义的 `Dart` 标识符名字。 `Symbols` 在混淆后的代码也可以使用。

如果在写代码的时候，已经知道 `symbol` 的名字了，则可以使用 `#符号名字` 的方式直接使用。 直接使用的 `symbol` 对象是编译时常量，多次定义引用的是同一个对象。 如果名字不知道，则可以通过 `Symbol` 构造函数来创建：

```dart
import 'dart:mirrors';

// If the symbol name is known at compile time.
const className = #MyClass;

// If the symbol name is dynamically determined.
var userInput = askUserForNameOfFunction();
var functionName = new Symbol(userInput);
```
在混淆代码的时候，编译器可能使用更加简短的名字来替代原来的符号（`symbol`）名字。 要获取原来的 `symbol` 名字，使用 `MirrorSystem.getName()` 函数。该函数在代码混淆的情况下，也能返回正确的 `symbol` 名字。

```dart
import 'dart:mirrors';

const className = #MyClass;
assert('MyClass' == MirrorSystem.getName(className));
```

### Introspection

使用 `mirror` 功能来检查程序的结构。可以检查类、库以及对象等。

下面的示例使用 Person 类：

```dart
class Person {
    String firstName;
    String lastName;
    int age;

    Person(this.firstName, this.lastName, this.age);

    String get fullName => '$firstName $lastName';

    void greet(String other) {
        print('Hello there, $other!');
    }
}
```
在开始使用之前，需要在一个类或者对象上调用 `reflect` 函数来获取到 `mirror`.

### Class mirrors

可以在任何 `Type` 上面调用 `reflect` 函数来获取 `ClassMirror` ：

```dart
ClassMirror mirror = reflectClass(Person);

assert('Person' == MirrorSystem.getName(mirror.simpleName));
```

也可以在实例上调用 `runtimeType` 获取该对象的 `Type`.

```dart
var person = new Person('Bob', 'Smith', 33);
ClassMirror mirror = reflectClass(person.runtimeType);
assert('Person' == MirrorSystem.getName(mirror.simpleName));
```
获取到 `ClassMirror` 后，就可以查询类的构造函数、成员变量等信息。 下面是列出类的所有构造函数的示例：
```dart
showConstructors(ClassMirror mirror) {
    var constructors = mirror.declarations.values
        .where((m) => m is MethodMirror && m.isConstructor);

    constructors.forEach((m) {
        print('The constructor ${m.simpleName} has '
            '${m.parameters.length} parameters.');
    });
}
```
下面是列出类的成员变量的示例：

```dart
showFields(ClassMirror mirror) {
    var fields = mirror.declarations.values
        .where((m) => m is VariableMirror);

    fields.forEach((VariableMirror m) {
        var finalStatus = m.isFinal ? 'final' : 'not final';
        var privateStatus = m.isPrivate ?
            'private' : 'not private';
        var typeAnnotation = m.type.simpleName;

        print('The field ${m.simpleName} is $privateStatus ' +
            'and $finalStatus and is annotated as ' +
            '$typeAnnotation.');
    });
}
```

###  Instance mirrors

在对象上调用 reflect 函数可以获取到一个 InstanceMirror 对象。
```dart
var p = new Person('Bob', 'Smith', 42);
InstanceMirror mirror = reflect(p);
```
如果你已经有个 `InstanceMirror` 对象了，但是想知道该对象反射的目标对象，则需要调用 `reflectee`。

```dart
var person = mirror.reflectee;
assert(identical(p, person));
```

### Invocation
    
获取到 `InstanceMirror` 后，就可以调用里面的函数、`getter`和`setter`了。 

### Invoke methods
    
使用 `InstanceMirror` 的 `invoke()` 函数来调用对象的函数。 第一个参数为要调用的函数名字，第二个参数为该函数的一个位置参数列表。第三个参数为可选参数，用来指定命名参数。

```dart
var p = new Person('Bob', 'Smith', 42);
InstanceMirror mirror = reflect(p);

mirror.invoke(#greet, ['Shailen']);
```

### Invoke getters and setters

使用 `InstanceMirror` 的 `getField()` 和 `setField()` 函数来 查询和设置对象的属性。

```dart
var p = new Person('Bob', 'Smith', 42);
InstanceMirror mirror = reflect(p);

// Get the value of a property.
var fullName = mirror.getField(#fullName).reflectee;
assert(fullName == 'Bob Smith');

// Set the value of a property.
mirror.setField(#firstName, 'Mary');
assert(p.firstName == 'Mary');
```