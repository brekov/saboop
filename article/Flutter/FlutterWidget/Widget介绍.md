# Flutter Widget介绍

## 概念

Flutter 中几乎所有的对象都是 `Widget`，Flutter 中的 `widget` 的概念相对于原生的`控件`更广泛，它不仅可以表示UI元素，也可以表示一些功能性的组件如：用于手势检测的 `GestureDetector widget`、用于应用主题数据传递的`Theme`等等。

## Widget 与 Element

在 Flutter 中，`Widget` 的功能是“描述一个 UI 元素的配置数据”，也就说，`Widget` 所表示并不是最终绘制在设备屏幕上的 UI 元素，而是显示元素的配置数据。Flutter 中真正代表屏幕上显示元素的类是 `Element`，而 `Widget` 是描述 `Element` 的配置，并且一个 `Widget` 可以对应多个 `Element` ，这是因为同一个 `Widget` 对象可以被添加到 UI 树的不同部分，而真正渲染时，UI 树的每一个 `Widget` 节点都会对应一个 `Element` 对象。总结一下：

* `Widget` 实际上是 `Element` 的配置数据， `Widget` 树实际上是一个配置树，而真正的UI渲染树是由 `Element` 构成；由于 `Element` 是通过 `Widget` 生成，所以它们之间有对应关系，所以在大多数场景，我们可以宽泛地认为 `Widget` 树就是指 UI 控件树或 UI 渲染树。
* 一个 `Widget` 对象可以对应多个 `Element` 对象。这很好理解，根据同一份配置（Widget），可以创建多个实例（Element）。


## 主要接口

我们先来看一下Widget类的声明：
```dart
@immutable
abstract class Widget extends DiagnosticableTree {
  const Widget({ this.key });
  final Key key;

  @protected
  Element createElement();

  @override
  String toStringShort() {
    return key == null ? '$runtimeType' : '$runtimeType-$key';
  }

  @override
  void debugFillProperties(DiagnosticPropertiesBuilder properties) {
    super.debugFillProperties(properties);
    properties.defaultDiagnosticsTreeStyle = DiagnosticsTreeStyle.dense;
  }

  static bool canUpdate(Widget oldWidget, Widget newWidget) {
    return oldWidget.runtimeType == newWidget.runtimeType
        && oldWidget.key == newWidget.key;
  }
}
```

* `Widget` 类继承自 `DiagnosticableTree` (“诊断树”，主要作用是提供调试信息)。

* `Key`: 类似于 React / Vue 中的 key，主要的作用是决定是否在下一次 build 时复用旧的 `widget` ，决定的条件在 `canUpdate()` 方法中。

* `createElement()`：正如前文所述“一个 Widget 可以对应多个 Element ”；Flutter Framework 在构建UI树时，会先调用此方法生成对应节点的 `Element` 对象。此方法是 Flutter Framework 隐式调用的，在我们开发过程中基本不会调用到。

* `debugFillProperties(...)`：复写父类的方法，主要是设置诊断树的一些特性。

* `canUpdate(...)`：一个静态方法，它决定了在 `widget` 树重新构建时，是否可以用 `newWidget` 去更新 UI 树上所对应的 `Element` 的配置对象`oldWidget`。只要 `newWidget` 与 `oldWidget` 的 `runtimeType` 和 `key` 同时相等时就会用 `newWidget` 去更新 `Element` 对象的配置，否则就会创建新的Element。

有关 `Key` 和 `Widget` 复用的细节将会在后面高级部分深入讨论，现在只需知道，为 `Widget` 显式添加 `key` 的话**可能**会使UI在重新构建时变的高效。

另外 `Widget` 类本身是一个抽象类，其中最核心的就是定义了 `createElement()` 接口。在Flutter开发中，我们通常会通过继承 `StatelessWidget` 和 `StatefulWidget` 来间接继承 `Widget` 类来实现，这两个类则是 `Flutter` 中非常重要的两个抽象类，它们引入了两种 `Widget` 模型，接下来我们将重点介绍一下这两个类。

## Stateless Widget

`StatelessWidget` 相对比较简单，它继承自 `Widget`，重写了 `createElement()` 方法：

```dart
@override
StatelessElement createElement() => new StatelessElement(this);
```
`StatelessElement` 间接继承自 `Element` 类，与 `StatelessWidget` 相对应（`StatelessWidget`作为其配置数据）。

`StatelessWidget` 用于不需要维护状态的场景，它通常在 `build` 方法中通过嵌套其它 `Widget` 来构建 UI ，在构建过程中会递归的构建其嵌套的 `Widget`。我们看一个简单的例子：

```dart
class Echo extends StatelessWidget {
  const Echo({
    Key key,  
    @required this.text,
    this.backgroundColor:Colors.grey,
  }):super(key:key);

  final String text;
  final Color backgroundColor;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        color: backgroundColor,
        child: Text(text),
      ),
    );
  }
}
```
上面的代码，实现了一个回显字符串的Echo widget。

> 按照惯例，`widget` 的构造函数应使用命名参数，命名参数中的必要参数要添加 `@required` 标注，这样有利于静态代码分析器进行检查，另外，在继承 `widget` 时，第一个参数通常应该是 `Key`。

然后我们可以通过如下方式使用它：

```dart
Widget build(BuildContext context) {
  return Echo(text: "hello world");
}
```

## StatefulWidget
和`StatelessWidget`一样，`StatefulWidget`也是继承自`widget`类，并重写了`createElement()`方法，不同的是返回的`Element`对象并不相同；另外`StatefulWidget`类中添加了一个新的接口`createState()`，下面我们看看`StatefulWidget`的类定义：

```dart
abstract class StatefulWidget extends Widget {
  const StatefulWidget({ Key key }) : super(key: key);

  @override
  StatefulElement createElement() => new StatefulElement(this);

  @protected
  State createState();
}
```

* `StatefulElement`间接继承自`Element`类，与`StatefulWidget`（作为其配置数据）相对应。`StatefulElement`中可能会多次调用`createState()`来创建状态(`State`)对象。

* `createState()`用于创建和`StatefulWidget`相关的状态，它在`StatefulWidget`的生命周期中可能会被多次调用。例如，当一个`StatefulWidget`同时插入到`widget`树的多个位置时，`Flutter framework`就会调用该方法为每一个位置生成一个独立的`State`实例，其实，本质上就是一个`StatefulElement`对应一个`State`实例。

## State
一个`StatefulWidget`类会对应一个`State`类，`State`表示与其对应的`StatefulWidget`要维护的状态，`State`中的保存的状态信息可以：

* 在`widget build`时可以被同步读取。
* 在`widget`生命周期中可以被改变，当`State`被改变时，可以手动调用其`setState()`方法通知`Flutter framework`状态发生改变，`Flutter framework`在收到消息后，会重新调用其`build`方法重新构建`widget`树，从而达到更新UI的目的。

State中有两个常用属性：

* `widget`，它表示与该`State`实例关联的`widget`实例，由`Flutter framework`动态设置。注意，这种关联并非永久的，因为在应用声明周期中，UI树上的某一个节点的`widget`实例在重新构建时可能会变化，但`State`实例只会在第一次插入到树中时被创建，当在重新构建时，如果`widget`被修改了，`Flutter framework`会动态设置`State.widget`为新的`widget`实例。

* `context`，它是`BuildContext`类的一个实例，表示构建`widget`的上下文，它是操作`widget`在树中位置的一个句柄，它包含了一些查找、遍历当前`Widget`树的一些方法。每一个`widget`都有一个自己的`context`对象。


## State生命周期
理解`State`的生命周期对`flutter`开发非常重要，为了加深读者印象，本节我们通过一个实例来演示一下`State`的生命周期。在接下来的示例中，我们实现一个计数器widget，点击它可以使计数器加1，由于要保存计数器的数值状态，所以我们应继承`StatefulWidget`，代码如下：

```dart
class CounterWidget extends StatefulWidget {
  const CounterWidget({
    Key key,
    this.initValue: 0
  });

  final int initValue;

  @override
  _CounterWidgetState createState() => new _CounterWidgetState();
}
```

`CounterWidget`接收一个`initValue`整形参数，它表示计数器的初始值。下面我们看一下`State`的代码：

```dart
class _CounterWidgetState extends State<CounterWidget> {  
  int _counter;

  @override
  void initState() {
    super.initState();
    //初始化状态  
    _counter = widget.initValue;
    print("initState");
  }

  @override
  Widget build(BuildContext context) {
    print("build");
    return Center(
      child: FlatButton(
        child: Text('$_counter'),
        //点击后计数器自增  
        onPressed:()=>setState(()=> ++_counter),
      ),
    );
  }

  @override
  void didUpdateWidget(CounterWidget oldWidget) {
    super.didUpdateWidget(oldWidget);
    print("didUpdateWidget");
  }

  @override
  void deactivate() {
    super.deactivate();
    print("deactive");
  }

  @override
  void dispose() {
    super.dispose();
    print("dispose");
  }

  @override
  void reassemble() {
    super.reassemble();
    print("reassemble");
  }

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    print("didChangeDependencies");
  }

}
```

接下来，我们创建一个新路由，在新路由中，我们只显示一个`CounterWidget`：

```dart
Widget build(BuildContext context) {
  return CounterWidget();
}
```

我们运行应用并打开该路由页面，在新路由页打开后，屏幕中央就会出现一个数字0，然后控制台日志输出：

```
I/flutter ( 5436): initState
I/flutter ( 5436): didChangeDependencies
I/flutter ( 5436): build
```

可以看到，在`StatefulWidget`插入到`Widget`树时首先`initState`方法会被调用。

然后我们点击⚡️按钮热重载，控制台输出日志如下：

```
I/flutter ( 5436): reassemble
I/flutter ( 5436): didUpdateWidget
I/flutter ( 5436): build
```

可以看到此时`initState`和`didChangeDependencies`都没有被调用，而此时`didUpdateWidget`被调用。

接下来，我们在widget树中移除CounterWidget，将路由build方法改为：

```dart
Wid get build(BuildContext context) {
  //移除计数器 
  //return CounterWidget();
  //随便返回一个Text()
  return Text("xxx");
}
```
然后热重载，日志如下：

```
I/flutter ( 5436): reassemble
I/flutter ( 5436): deactive
I/flutter ( 5436): dispose
```

我们可以看到，在`CounterWidget`从`widget`树中移除时，`deactive`和`dispose`会依次被调用。

下面我们来看看各个回调函数：

* initState：当`Widget`第一次插入到`Widget`树时会被调用，对于每一个`State`对象，`Flutter framework`只会调用一次该回调，所以，通常在该回调中做一些一次性的操作，如状态初始化、订阅子树的事件通知等。不能在该回调中调用`BuildContext.inheritFromWidgetOfExactType`（该方法用于在Widget树上获取离当前widget最近的一个父级`InheritFromWidget`，关于InheritedWidget我们将在后面章节介绍），原因是在初始化完成后，`Widget`树中的`InheritFromWidget`也可能会发生变化，所以正确的做法应该在在`build()`方法或`didChangeDependencies()`中调用它。

* didChangeDependencies()：当`State`对象的依赖发生变化时会被调用；例如：在之前`build()` 中包含了一个`InheritedWidget`，然后在之后的`build()` 中`InheritedWidget`发生了变化，那么此时`InheritedWidget`的子`widget`的`didChangeDependencies()`回调都会被调用。典型的场景是当系统语言Locale或应用主题改变时，`Flutter framework`会通知`widget`调用此回调。

* build()：此回调读者现在应该已经相当熟悉了，它主要是用于构建`Widget`子树的，会在如下场景被调用：

  1. 在调用initState()之后。
  2. 在调用didUpdateWidget()之后。
  3. 在调用setState()之后。
  4. 在调用didChangeDependencies()之后。
  5. 在State对象从树中一个位置移除后（会调用deactivate）又重新插入到树的其它位置之后。
* reassemble()：此回调是专门为了开发调试而提供的，在热重载(hot reload)时会被调用，此回调在Release模式下永远不会被调用。
* didUpdateWidget()：在`widget`重新构建时，`Flutter framework`会调用`Widget.canUpdate`来检测`Widget`树中同一位置的新旧节点，然后决定是否需要更新。`Widget.canUpdate`会在新旧`widget`的`key`和`runtimeType`同时相等时会返回`true`。
* deactivate()：当`State`对象从树中被移除时，会调用此回调。在一些场景下，`Flutter framework`会将`State`对象重新插到树中，如包含此`State`对象的子树在树的一个位置移动到另一个位置时（可以通过`GlobalKey`来实现）。如果移除后没有重新插入到树中则紧接着会调用`dispose()`方法。
* dispose()：当`State`对象从树中被永久移除时调用；通常在此回调中释放资源。


## 状态管理

响应式的编程框架中都会有一个永恒的主题——“状态管理”，StatefulWidget的状态应该被谁管理？widget本身？父widget？都会？还是另一个对象？答案是取决于实际情况！以下是管理状态的最常见的方法：

* Widget管理自己的state。
* 父widget管理子widget状态。
* 混合管理（父widget和子widget都管理状态）。

如何决定使用哪种管理方法？以下原则可以帮助你决定：

1. 如果状态是用户数据，如复选框的选中状态、滑块的位置，则该状态最好由父widget管理。
2. 如果状态是有关界面外观效果的，例如颜色、动画，那么状态最好由widget本身来管理。
3. 如果某一个状态是不同widget共享的则最好由它们共同的父widget管理。

在widget内部管理状态封装性会好一些，而在父widget中管理会比较灵活。有些时候，如果不确定到底该怎么管理状态，那么推荐的首选是在父widget中管理（灵活会显得更重要一些）。

接下来，我们将通过创建三个简单示例TapboxA、TapboxB和TapboxC来说明管理状态的不同方式。 这些例子功能是相似的——创建一个盒子，当点击它时，盒子背景会在绿色与灰色之间切换。状态 _active确定颜色：绿色为true ，灰色为false。

下面的例子将使用GestureDetector来识别点击事件，关于该GestureDetector的详细内容我们将在后面“事件处理”一章中介绍。

### Widget管理自身状态
_TapboxAState 类:

* 管理TapboxA的状态。
* 定义_active：确定盒子的当前颜色的布尔值。
* 定义_handleTap()函数，该函数在点击该盒子时更新_active，并调用setState()更新UI。
* 实现widget的所有交互式行为。

```dart
// TapboxA 管理自身状态.

//------------------------- TapboxA ----------------------------------

class TapboxA extends StatefulWidget {
  TapboxA({Key key}) : super(key: key);

  @override
  _TapboxAState createState() => new _TapboxAState();
}

class _TapboxAState extends State<TapboxA> {
  bool _active = false;

  void _handleTap() {
    setState(() {
      _active = !_active;
    });
  }

  Widget build(BuildContext context) {
    return new GestureDetector(
      onTap: _handleTap,
      child: new Container(
        child: new Center(
          child: new Text(
            _active ? 'Active' : 'Inactive',
            style: new TextStyle(fontSize: 32.0, color: Colors.white),
          ),
        ),
        width: 200.0,
        height: 200.0,
        decoration: new BoxDecoration(
          color: _active ? Colors.lightGreen[700] : Colors.grey[600],
        ),
      ),
    );
  }
}
```

### 父widget管理子widget的state
对于父widget来说，管理状态并告诉其子widget何时更新通常是比较好的方式。 例如，IconButton是一个图片按钮，但它是一个无状态的widget，因为我们认为父widget需要知道该按钮是否被点击来采取相应的处理。

在以下示例中，TapboxB通过回调将其状态导出到其父项。由于TapboxB不管理任何状态，因此它的父类为StatelessWidget。

ParentWidgetState 类:

* 为TapboxB 管理_active状态.
* 实现_handleTapboxChanged()，当盒子被点击时调用的方法.
* 当状态改变时，调用setState()更新UI.

TapboxB 类:

* 继承StatelessWidget类，因为所有状态都由其父widget处理。
* 当检测到点击时，它会通知父widget。

```dart
// ParentWidget 为 TapboxB 管理状态.

//------------------------ ParentWidget --------------------------------

class ParentWidget extends StatefulWidget {
  @override
  _ParentWidgetState createState() => new _ParentWidgetState();
}

class _ParentWidgetState extends State<ParentWidget> {
  bool _active = false;

  void _handleTapboxChanged(bool newValue) {
    setState(() {
      _active = newValue;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Container(
      child: new TapboxB(
        active: _active,
        onChanged: _handleTapboxChanged,
      ),
    );
  }
}

//------------------------- TapboxB ----------------------------------

class TapboxB extends StatelessWidget {
  TapboxB({Key key, this.active: false, @required this.onChanged})
      : super(key: key);

  final bool active;
  final ValueChanged<bool> onChanged;

  void _handleTap() {
    onChanged(!active);
  }

  Widget build(BuildContext context) {
    return new GestureDetector(
      onTap: _handleTap,
      child: new Container(
        child: new Center(
          child: new Text(
            active ? 'Active' : 'Inactive',
            style: new TextStyle(fontSize: 32.0, color: Colors.white),
          ),
        ),
        width: 200.0,
        height: 200.0,
        decoration: new BoxDecoration(
          color: active ? Colors.lightGreen[700] : Colors.grey[600],
        ),
      ),
    );
  }
}
```

### 混合管理
对于一些widget来说，混合管理的方式非常有用。在这种情况下，widget自身管理一些内部状态，而父widget管理一些其他外部状态。

在下面TapboxC示例中，按下时，盒子的周围会出现一个深绿色的边框。抬起时，边框消失；点击生效，盒子的颜色改变。 TapboxC将其_active状态导出到其父widget中，但在内部管理其_highlight状态。这个例子有两个状态对象_ParentWidgetState和_TapboxCState。

_ParentWidgetStateC 对象:

* 管理_active 状态。
* 实现 _handleTapboxChanged() ，当盒子被点击时调用。
* 当点击盒子并且_active状态改变时调用setState()更新UI。

_TapboxCState 对象:

* 管理_highlight state。
* GestureDetector监听所有tap事件。当用户点下时，它添加高亮（深绿色边框）；当用户释放时，会移除高亮。
* 当按下、抬起、或者取消点击时更新_highlight状态，调用setState()更新UI。
* 当点击时，将状态的改变传递给父widget.

```dart
//---------------------------- ParentWidget ----------------------------

class ParentWidgetC extends StatefulWidget {
  @override
  _ParentWidgetCState createState() => new _ParentWidgetCState();
}

class _ParentWidgetCState extends State<ParentWidgetC> {
  bool _active = false;

  void _handleTapboxChanged(bool newValue) {
    setState(() {
      _active = newValue;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Container(
      child: new TapboxC(
        active: _active,
        onChanged: _handleTapboxChanged,
      ),
    );
  }
}

//----------------------------- TapboxC ------------------------------

class TapboxC extends StatefulWidget {
  TapboxC({Key key, this.active: false, @required this.onChanged})
      : super(key: key);

  final bool active;
  final ValueChanged<bool> onChanged;

  _TapboxCState createState() => new _TapboxCState();
}

class _TapboxCState extends State<TapboxC> {
  bool _highlight = false;

  void _handleTapDown(TapDownDetails details) {
    setState(() {
      _highlight = true;
    });
  }

  void _handleTapUp(TapUpDetails details) {
    setState(() {
      _highlight = false;
    });
  }

  void _handleTapCancel() {
    setState(() {
      _highlight = false;
    });
  }

  void _handleTap() {
    widget.onChanged(!widget.active);
  }

  Widget build(BuildContext context) {
    // 在按下时添加绿色边框，当抬起时，取消高亮  
    return new GestureDetector(
      onTapDown: _handleTapDown, // 处理按下事件
      onTapUp: _handleTapUp, // 处理抬起事件
      onTap: _handleTap,
      onTapCancel: _handleTapCancel,
      child: new Container(
        child: new Center(
          child: new Text(widget.active ? 'Active' : 'Inactive',
              style: new TextStyle(fontSize: 32.0, color: Colors.white)),
        ),
        width: 200.0,
        height: 200.0,
        decoration: new BoxDecoration(
          color: widget.active ? Colors.lightGreen[700] : Colors.grey[600],
          border: _highlight
              ? new Border.all(
                  color: Colors.teal[700],
                  width: 10.0,
                )
              : null,
        ),
      ),
    );
  }
}
```

另一种实现可能会将高亮状态导出到父widget，同时保持_active状态为内部，但如果你要将该TapBox给其它人使用，可能没有什么意义。 开发人员只会关心该框是否处于Active状态，而不在乎高亮显示是如何管理的，所以应该让TapBox内部处理这些细节。

### 全局状态管理
当应用中包括一些跨widget（甚至跨路由）的状态需要同步时，上面介绍的方法很难胜任了。比如，我们有一个设置页，里面可以设置应用语言，但是我们为了让设置实时生效，我们期望在语言状态发生改变时，我们的`APP Widget`能够重新`build`一下，但我们的`APP Widget`和设置页并不在一起。正确的做法是通过一个全局状态管理器来处理这种“相距较远”的`widget`之间的通信。目前主要有两种办法：

* 实现一个全局的事件总线，将语言状态改变对应为一个事件，然后在`APP Widget`所在的父`widgetinitState` 方法中订阅语言改变的事件，当用户在设置页切换语言后，我们触发语言改变事件，然后`APP Widget`那边就会收到通知，然后重新`build`一下即可。
* 使用redux这样的全局状态包，读者可以在pub上查看其详细信息。

## Flutter widget库介绍
Flutter提供了一套丰富、强大的基础widget，在基础widget库之上Flutter又提供了一套Material风格（Android默认的视觉风格）和一套Cupertino风格（iOS视觉风格）的widget库。要使用基础widget库，需要先导入：

```dart
import 'package:flutter/widgets.dart';
```


### 基础widget
* `Text`：该 `widget` 可让您创建一个带格式的文本。
* `Row`、 `Column`： 这些具有弹性空间的布局类`Widget`可让您在水平（`Row`）和垂直（`Column`）方向上创建灵活的布局。
* `Stack`： 取代线性布局 (译者语：和`Android`中的`FrameLayout`相似)，`Stack`允许子 `widget` 堆叠， 你可以使用 `Positioned` 来定位他们相对于`Stack`的上下左右四条边的位置。
* `Container`： `Container` 可让您创建矩形视觉元素。`container` 可以装饰一个`BoxDecoration`, 如 background、一个边框、或者一个阴影。 `Container` 也可以具有边距（`margins`）、填充(`padding`)和应用于其大小的约束(`constraints`)。另外， `Container`可以使用矩阵在三维空间中对其进行变换。

### Material widget
`Flutter`提供了一套丰富的`Material widget`，可帮助您构建遵循`Material Design`的应用程序。`Material`应用程序以`MaterialApp widget`开始， 该`widget`在应用程序的根部创建了一些有用的`widget`，比如一个`Theme`，它配置了应用的主题。 是否使用`MaterialApp`完全是可选的，但是使用它是一个很好的做法。在之前的示例中，我们已经使用过多个`Material widget`了，如：`Scaffold`、`AppBar`、`FlatButton`等。要使用`Material widget`，需要先引入它：
```dart
import 'package:flutter/material.dart';
```
### Cupertino widget
`Flutter`也提供了一套丰富的`Cupertino`风格的`widget`，尽管目前还没有`Material widget`那么丰富，但也在不断的完善中。值得一提的是在`Material widget`库中，有一些`widget`可以根据实际运行平台来切换表现风格，比如`MaterialPageRoute`，在路由切换时，如果是`Android`系统，它将会使用`Android`系统默认的页面切换动画(从底向上)，如果是`iOS`系统时，它会使用`iOS`系统默认的页面切换动画（从右向左）。由于在前面的示例中还没有`Cupertino widget`的示例，我们实现一个简单的Cupertino页面：

```dart
//导入cupertino widget库
import 'package:flutter/cupertino.dart';

class CupertinoTestRoute extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return CupertinoPageScaffold(
      navigationBar: CupertinoNavigationBar(
        middle: Text("Cupertino Demo"),
      ),
      child: Center(
        child: CupertinoButton(
            color: CupertinoColors.activeBlue,
            child: Text("Press"),
            onPressed: () {}
        ),
      ),
    );
  }
}
```