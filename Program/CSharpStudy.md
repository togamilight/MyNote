# 类型

### 值类型

​	结构体`struct`、枚举`enum`

### 引用类型

​	类`class`、接口`interface` 、委托`delegate`

* 引用类型在内存栈中存放地址，占4/8个字节（32/64位CLR）




### 装箱和拆箱

```CSharp
int i = 5;
object o = i;		//装箱
int j = (int) o;	//拆箱
```

* 当调用值类型的`GetType()`方法是总伴随着装箱过程，因为这个方法不能被重载。同理，为一个类型的值调用`ToString()/Equals()/GetHashCode()`时，若该类型没覆盖这些方法，也会发生装箱。
* 将值类型赋给一个接口类型的变量或者把它作为接口类型的参数来传递时也会装箱

```CSharp
IComparable x = 5;
```

* 装箱拆箱会消耗性能


# 结构体

* **结构体是值类型**
* **字段声明时不能初始化值**
* **必有默认无参构造函数，将所有字段赋默认值（0/null），不能覆盖**
* **声明后不用new也可以使用，可以直接给字段赋值**
* **不能声明析构函数**
* ​




# 枚举

* `enum Name : type {}`可以将枚举代表的值由int改为其它整数类型，用于优化(一般用不到)
* `enum Name{a = 10}`：枚举默认从0开始往后排。可以这样指定值


# 类

在命名空间下的类，默认访问权限是internal(同项目内访问)，可以显示定义为public，但不能是其它

### 静态类

* 只能包含静态成员，字段方法等都需要用`static`修饰（除了嵌套类和常量）
* 不能实例化，不能包含实例构造方法
* 密封的，无法被继承，编译器编译时自动生成`sealed`标记，但不能显示声明为`abstract`或`sealed`
* 不能实现接口和继承类
* 不能包含任何操作符
* 不能有`protected`或`protected internal`成员
* 可以用来声明静态方法

### 静态构造方法

* 只执行一次，由.NET调用，是在创建第一个类实例或任何静态成员被引用时，.NET将自动调用静态构造函数来初始化类
* 一个类只能有一个静态构造函数
* 既没有访问修饰符，也没有参数
* 不可以被继承
* 如果没有写静态构造函数，而类中包含带有初始值设定的静态成员，那么编译器会自动生成默认的静态构造函数

### 静态方法

* 不能调用非静态成员



# 接口

* 接口不能包含字段（java可以，但是这些字段隐式地是static和final的）

* 所有方法都是public，但不可写明，也不能加abstract，实现时也必须是public（写明），不能是static

* 无构造方法

* 显示实现接口，只能通过接口调用，实现的方法不能加修饰符，适用于：

  当类实现多个接口时，并且接口中包含相同的方法签名，此时使用显式接口实现。即使没有相同的方法签名，仍推荐使用显式接口，因为可以标识出哪个方法属于哪个接口。

```CSharp
interface I1{
  void Show();
}
class Class1 : I1{
  void I1.Show(){}
}
I1 object1 = new Class1();
object1.Show();
```

* 显示实现接口后，仍可以非显示地再一次实现接口，在显示实现里也可以调用这个非显示实现。这时，当调用方法的对象是以接口声明时，调用显示实现，否则调用普通的实现。返回类型可以不同

```CSharp
interface MyInterface {
  void Show();
}

class MyClass : MyInterface {
  void MyInterface.Show() {
    Console.WriteLine("IShow");
    Show();
  }
  public string Show() {
    Console.WriteLine("CShow");
    return "";
  }
}

MyClass myInterface = new MyClass();	//打印CShow
MyInterface myInterface = new MyClass();	//打印IShow CShow
myInterface.Show();
Console.ReadKey();
```



# virtual/abstract，override/new

**virtual**修饰虚成员，在子类中可以用**override**重写

**abstract**修饰抽象方法，抽象方法只能出现在抽象类里，没有方法体，在子类中用**override**实现

**new**用来隐藏方法，隐藏后，只有用子类声明的对象调用到子类方法，父类声明的引用子类的对象调用到父类的方法

**override**重写virtual/abstract/override修饰的方法，父类声明的引用子类的对象也能调用到该方法

# 委托（C# in Depth 2.1）

### 委托的声明和实例的创建

被引用的方法的参数列表上的参数允许是委托参数列表参数的基类（逆变性）（第5章？）

被引用的方法的返回值类型允许是委托的返回值类型的子类（协变性）

```CSharp
//被引用的方法
static string Show(object o){return "";}
//声明委托
delegate object Display(string s);
//创建委托实例
Display display = Show;	//或	Display display = new Display(Show);
//委托的调用
display("abc");		//或	display.Invoke("abc");
```



### 最终的垃圾（P28）

假如委托实例本身不能被回收，其会阻止它的目标对象（委托实例引用的实例方法所属的对象）被回收，可能造成内存泄漏。比如，一个短命对象将它自身作为目标，订阅了一个长命对象的事件，长命对象间接容纳了短命对象的一个引用，延长了短命对象的寿命



### 合并和删除委托（2.1.2）

* 委托是不易变的，类似于string，创建委托实例后，关于它的一切就不能改变，就很安全。合并和删除委托实际是新建了一个委托实例，不会改变原来的委托实例。
* 将null与一个委托实例合并时，null将被视为带有空调用列表的一个委托
* 调用委托实例时，所有操作被依次调用，Invoke将返回最后一个操作的返回值。任何操作抛出异常时会阻止执行后续操作
* 可以通过`Delegate[] delegate.GetInvocationList()`获取操作列表，来显式调用某个操作

```CSharp
//两者等价
myDelegate1 += myDelegate2;
MyDelegate myDelegate3 = Delegate.Combine(myDelegate1, myDelegate2) as MyDelegate;
//两者等价
myDelegate1 -= myDelegate2;
MyDelegate myDelegate3 = Delegate.Remove(myDelegate1, myDelegate2) as MyDelegate;
```



### 方法组转换

方法组转换为Delegate类型要用显示转换

```CSharp
Delegate d = (MyDelegate)MyMethod;
//或者先隐式转换成委托，在赋给Delegate类型
MyDelegate md = MyMethod;
Delegate d = md;
```



### 匿名方法

* 逆变性不适用于匿名方法，必须指定和委托类型完全匹配的参数列表
* 在值类型中编写的匿名方法，不能引用this，可以在匿名方法外将this存放在临时变量中，再在匿名方法中使用该临时变量
* 当不需要关注参数时，可以去掉参数列表（但是对于重载成需要不同委托参数的方法会产生歧义）

```CSharp
Action<object, EventArgs> act = delegate{};
//可以通过给事件添加无参不执行动作的匿名参数，使其不用在每次调用前都检测是否为null
public event EventHandler Event = delegate{};	//会损失一点点性能
```

* 对于一个捕获变量，只要还有任何委托实例引用它，它就会一直存在



# 事件

事件用event修饰，订阅事件的方法的参数列表一般为`(object sender, EventArgs e)`，可以自己定义事件参数类，继承`EventArgs`

### 事件的声明

```CSharp
//事件的完整声明Start-----------
//此时，该事件只能+=/-= 	add和remove必须都定义
private MyEventHandler myEventHandler;
public event EventHandler MyEvent {
	add {
		myEventHandler += value;
	}
    remove {
        myEventHandler -= value;
    }
}
//事件的完整声明End------------

//事件的简略声明,此时该事件在类外可+=/-=，类内可Invoke/=
public event MyEventHandler myEvent;

//此外，还可以不需要自己声明委托类型
//此时，调用的方法的参数列表为标准的（object sender, MyEventArgs e）
public event EventHandler<MyEventArgs> myEvent;
```



# 操作符

### 类型转换

* 自定义类型转换

  ​	//在被转换的类中定义，explicit为显式，implicit为隐式

  ```CSharp
      public static explicit operator Monkey(Stone stone) {
          Monkey m = new Monkey(stone.age / 5);
          return m;
      }
  ```

### 数字

​	有两个特殊数字：非数字`NaN`	, 无穷大`Infinity`(浮点数的0除得无穷大)

### 位操作符

* 左移`<<`， 右移`>>` 	
* 不溢出时，左移相当于乘以2，右移相当于除以2
  * 左移补进0，右移正数补进0，负数补进1

### 类型检测操作符

* is

  * is检查的是变量所引用的实例的类型，检测父类也是true

    ```CSharp
    //result为false
    Object1 o = null; 
    bool result = o is Object1
    ```

    ```CSharp
    //result为true
    Child p = new Child();
    bool result = p is Parent;
    ```

* as

  * `Object1 o = a as Object1`,判断a是否为Object1，是返回a的引用，不是返回null

### null值合并操作符

​	`??`, 判断一个变量是否为null，是则返回操作符右边的值

```CSharp
int? x = null;	//int? 可空类型，也可写成Nullable<int>
int y = x ?? 1;
```



# 语句

### 声明语句

* `int x = 100;`：这是声明时追加了初始化器
  * `int x; x = 100`：这是声明后赋值，两者是不一样的
* `int[] array = {1, 2, 3};`  √
  * `int[] array; array = {1, 2, 3};`  ×
  * `int[] array; array = new int[]{1, 2, 3};`   √



### switch语句

* 不能筛选浮点类型，可以string，enum，整型，bool，char以及它们对应的可空类型
* 执行语句后必须有break
* case后必须跟常量值



### try-catch-finally

* try必须为1个，catch为0到多个，finally为0到1个，catch和finally至少有1个
* catch不加括号和参数时捕获所有异常
* 在方法中，在try和catch语句块中return，依旧会执行finally，在finally中不能return

### 循环语句

* `for(; ; ;)`相当于`while(true)`



# 字段

* `static 类名`为静态初始化器（不能加括号和参数列表），在类型加载时执行且仅执行一次
* 常量const
  * 定义时必须初始化，不能加static
  * 只能修饰基元类型，枚举和字符串类型，其它类型可用`static readOnly`代替


# 参数

* 引用形参`ref`在传参之前必须明确赋值
* 输出形参`out`传入时，在方法内部，最初是默认无赋值的，方法返回前必须明确赋值
* 数组形参`params int[] Array`，必须位于参数列表的最后一位，且只能有一个，可以接受任意个该类型参数形成数组
* 具名参数：调用方法时`Method(参数名:值,...)`可以具体的给某个形参赋值。调用时具名参数必须写在所有不具名参数之后



# 方法

### 方法重载(Overloading)

* 先找完全匹配的，找不到则找最接近，类型转换最少的

### 扩展方法

* 必须位于静态类(一般名为SomeTypeExtension)，且方法必须是`public static`，第一个参数必须是要扩展的类型，用`this`修饰，例子:

```CSharp
 static class IntExtension {
        public static void UtilMethod(this int a, string s) {}
    }
 //调用
 int a = 1;
 a.UtilMethod("1");
```





# 泛型

* 可用于方法和类，接口，结构体， 委托等(不能用于枚举)。
* 泛型方法调用时可以不加指明类型参数，由编译器自行通过参数判断，且要么全部指明，要么全部推断

```CSharp
void Add<T>(T a, T b){};
class Class1<T>{}
```

* 可以有多个，用逗号隔开`void Method<T, V>(T a, V b){}`
* 可以用`where`对泛型进行约束，约束类型必须是接口，非密封类(泛型为其子类或本身)，类型参数（`struct, class`），不能是`interface, enum, delegate`，不能是`int, string`等，不能是特殊类：`System.Object, System.Enum, System.ValueType, System.Delegate`
  * `class`：表示引用类型；`struct`：表示值类型（不含可空类型）。这两个约束必须位于第一位
    * `class`约束的参数可以用`==/!=`来比较（比较引用），`struct`约束的参数则不行
    * 没有约束的类型参数只能与`null`用`==/!=`进行比较
  * `new()`：表示必须含有无参构造方法，必须是最后一个约束，不能和`struct`约束一起使用。其适用于所用值类型，所有没有显示声明构造方法的非静态、非抽象类，所有显示声明了一个公共无参构造方法的非抽象类 

```CSharp
void Add<T>() where T : class{}
//还可以约束构造方法
void Add<T>() where T : new(){}	//必须含有无参构造方法
//多个约束用逗号隔开
void Add<T>() where T : class, new(){}
//多个泛型，用多个where
void Add<T, V>() where T : class wher V : struct{}
```

* 有多个泛型但传入一样的类型时可能发生混淆，编译会报错。与普通方法混淆时，普通方法会覆盖泛型方法

```CSharp
class Class1<T, V>{
  Add(T a, V b){}
  Add(V a, T b){}
  Add(int a, int b){}
}
Class1<int, int> object1 = new Class1<int, int>();	//声明和new时的<int, int>都不能省略
object1.Add(1, 2);
```

* 泛型接口实现时必须指定类型

```CSharp
interface Interface1<T> {}
class Class1 : Interface1<int>{}
class Class2<T> : Interface1<T> {}
```

* 泛型类型可以重载，只要类型参数的个数不同即可，可以放在同一个命名空间里，但这些类型除了名称相同外没有别的联系（不存在两类型之间的默认转换），泛型方法同理
* 当要对泛型取默认值时，可以用`default`操作符：`default(T)`
* 给泛型类赋不同的泛型实例出来的对象相当于属于不同的类，不共享静态成员
* 泛型迭代，见3.4.3

### 反射泛型

* 对泛型类型使用`typeof`：
  * `typeof(List<>); typeof(Dictionary<,>); typeof(List<int>); typeof(Dictionary<string, int>);`
* `System.Type`的属性和方法：
  * `GetGenericTypeDefinition()`：作用于已构造的类型，获取其泛型类型定义
  * `MakeGenericType(params Type[])`：作用于泛型类型定义，返回一个已构造类型
* 反射泛型方法：
  * `GetMethod(string MethodName)`：不能指定已构造的方法
  * 对于类型参数数量不同的重载方法，无法指定具体数量，只能通过`GetMethods()`得到所有方法再进行查找



* 泛型不支持可变性，即无法将`List<string>`转换成`List<object>`，数组则是支持的

# 数组

`int[,]`为二维数组，每行的个数必须相等，相当于C++的`int[][]`；`int[][]`则是交错数组，即数组的数组，每行为一个数组，每行的个数不必相等





# 可空类型

* 可空类型实际是把值类型与一个`bool`标志封装到另一个值类型里：

  ​`struct Nullable<T> where T : struct`

  可空类型虽然是值类型，但不符合泛型的值类型约束，不能用`Nullable<Nullable<int>>`

* 可空类型是不易变的，大多数数值类型都是不易变的
* T可以隐式转换为Nullable<T>，Nullable<T>可以显式转换为T，无值时抛出异常

### Nullable<T>的属性和方法

* 构造方法：两个，默认的创建无值实例，另一个接受一个参数为值


* `bool HasValue`：是否有值
* `T Value`：值
* `GetValueOrDefault()`：如果有值返回值，无值返回默认值。两种重载，一种返回类型默认值，一种可以自己指定默认值
* `GetHashCode()`：无值时返回0
* `ToString()`：无值时返回空字符串
* `a.Equals(object b)`：a无值，b为null，true；a无值，b不为null，false；a有值，b为null，false；a的值等于b，true；

### Nullable静态方法

* `int Compare<T>(Nullable<T> n1, Nullable<T> n2)`：比较大小（空值小于所有值）
* `bool Equals<T>(Nullable<T> n1, Nullable<T> n2)`：判断相等
* `Type GetUnderlyingType(Type nullable)`：获取可空类型的基础类型

### 装箱拆箱

无值的可空类型会装箱成空引用。可空类型装箱后可拆箱成T（拆箱空引用时异常）或者对应的可空类型



* 关于可空类型的操作符，见4.3.3
* `a ?? b`：如果a为null，返回b，否则返回a；当b是a（a必须是引用或可空类型）的基础类型时，返回的值是基础类型（非可空）
  * `??`为右结合操作符，`a??b??c == a??(b??c)`



# 迭代器

* 迭代器模式通过`IEnumerator`和`IEnumerable`接口以及它们的泛型等价物来封装。某个类实现了`IEnumerable`接口就表示它可以被迭代访问，调用`GetEnumerator()`将会返回`IEnumerator`的实现，这就是迭代器本身，即序列中的某个位置，迭代器只能在序列中向前移动，同一个序列可能同时存在多个迭代器操作
* `foreach`语句被编译后会调用`GetEnumerator()`和`MoveNext()`以及`Current`，如果`IDisposable`也实现了，程序最后还会自动销毁迭代器对象
* 迭代器的位置从序列第一个元素的前一位开始，所以第一次使用`Current`前（`Current代表当前迭代器所在位置的元素`），必须调用`MoveNext()`



### 迭代器块和yield return（6.2.1）

* 只能使用迭代器块来实现返回类型为`IEnumerable, IEnumerator`或其泛型等价物的方法
* 如果方法声明的返回类型是非泛型的，则迭代器块的生成类型是`object`，否则就是泛型接口的类型参数
* 迭代器块中不允许包含普通的`return`语句，只能是`yield return`。在代码块中，所有`yield return`语句必须返回和代码块的生成类型兼容的值
* 迭代器块不能实现具有`ref`或`out`参数的方法
* 限制：如果存在任何`catch`代码块，则不能在`try`代码块中使用`yield return`，不能在`finally`代码块中使用`yield return/break`
* 迭代器的工作流程见6.2.2
  * 在第一次调用`MoveNext()`前，迭代器块所在方法不会被调用（因此，如果需要验证参数，对不合法的参数抛出异常，则可以拆成两个方法，在第一个方法里验证参数，并调用含有迭代器块的第二个方法）
  * 所有工作在调用`MoveNext()`时就完成了，获取`Current`时不会执行任何代码
  * `yield return`的值即是`Current`
  * 在`yield return`的位置，代码就停止执行了，直到下一次调用`MoveNext()`
* `foreach`会在它自己的`finally`代码块中调用`IEnumerator`所提供的`Dispose()`。当迭代器完成迭代前，你如果调用有迭代器代码块创建的迭代器上的`Dispose()`，那么状态机会执行在代码当前暂停位置范围内的任何`finally`代码块
* 奇特之处：（6.2.4）
  * 第一次调用`MoveNext()`前，`Current`返回迭代器产生类型的默认值
  * `MoveNext()`返回false后，`Current`总是返回最后的生成值
  * `Reset()`总是抛出异常
  * 嵌套类总是实现`IEnumerator`的泛型和非泛型形式




# 分部类型

分部类型是指将一个类分成几个部分，放在不同的文件里，只需要加上`partial`的修饰即可

* 但是不能将一个成员拆开放在不同文件里，且任何部分的类声明要互相兼容，访问修饰符必须相同，且如果多个文件设定了基类，那么基类必须相同，如果多个文件设置了类型参数约束，则约束也必须一致（基类和约束都可以不写，写了就必须一致）。接口则不必一致，且声明后不必在此文件中实现

### 分部方法

* 在分部类型里使用，同样使用`partial`修饰符，在调用的文件里写方法声明，在其它文件里写完整实现。
* 分部方法只能返回`void`，且不能获取`out`参数，必须是私有的，但可以是静态或者泛型
* 如果方法最终没有被实现，任何调用语句都会被移除，包括任何参数计算，不会产生开销



# 命名空间别名

```CSharp
//通过using来定义别名
using WinForms = System.Windows.Forms;

//使用::来告知编译器使用别名
WinForms::Button

//使用全局命名空间别名
global::MyNameSpace

//外部别名，7.4.3
//假设有两个程序级First.dll和Second.dll，都包含了Demo.Example的类型
//1.指定两个外部别名
extern alias FirstAlias;
extern alias SecondAlias;
//2.使用命名空间别名来使用外部别名
using System;
using FD = FirstAlias::Demo;

class Test{
    static void Main(){
        Console.WriteLine(typeof(FD.Example));
      	Console.WriteLine(typeof(SecondAlias::Demo.Example));
    }
}
//编译此代码的命令行指令
//Compile with
//csc Test.cs /r:FirstAlias=First.dll /r:SecondAlias=Second.dll
//在vs中则只需要在解决方案视图中选择一个程序集引用，在其属性窗口设置Aliases值即可
```



# pragma指令（7.5不懂）

pragma指令就是一个由`#pragma`开头的代码行所表示的预处理指令，它后面能包含任何文本。pragma指令的结果无法把程序行为改为违反C#语言规范的任何东西。如果编译器不理解某个特别的pragma指令，那么只会发出一个警告而非错误。微软C#编译器理解两种pragma指令：警告（warning）、校验和（checknsum）

#非安全代码中固定大小的缓冲区（7.6不懂）

#把内部成员暴露给选定的程序集（7.7不懂）



# 自动实现的属性

```CSharp
public string Name{ get; private set; }
```

注意，使用了自动属性后，所有构造方法都必须显示调用无参构造方法`this()`，这样才能为所有字段赋初始值，因为字段是匿名的，无法直接赋值，赋值前也不能使用该属性

# 隐式类型var

只能在以下情况使用：

* 局部变量，而不是静态字段和实例字段
* 变量在声明的同时初始化
* 初始化表达式不是方法组，也不是匿名函数（匿名方法或Lambda表达式）（可以强制转换成委托）
* 初始化表达式不能是null
* 语句中只声明了一个变量
* 初始化表达式不包含正在声明的变量



# 简化的初始化

任何特定的属性只能最多指定一次

```CSharp
Person tom3 = new Person() { Name = "Tom", Age = 9 };
Person tom4 = new Person { Name = "Tom", Age = 9 };
Person tom5 = new Person("Tom") { Age = 9 };
//为嵌入对象设置属性
Person tom6 = new Person("Tom"){
    Age = 9,
  	//Home是只读的也可以这样设置
  	Home = { Country = "UK", Town = "Reading" }
};
```



### 集合初始化程序

类似数组初始化程序，任何实现了`IEnumerable`的类型，只要它为初始化列表中出现的每个元素都提供了一个恰当的公有的**Add**方法，就可以使用这个特性。Add方法可以接受多个参数，只要把值放在另一对大括号中

```CSharp
List<string> strs = new List<string>{	//加不加括号都可
    "John", "Tom", "Mario"
};
Dictionary<string, int> nameAgeMap = new Dictionary<string, int>{
  {"John", 60}, {"Tom", 20}, {"Mario", 34}
};
```



**初始化特性的应用**：8.3.5



### 隐式类型的数组

```CSharp
string[] name = {"a","b"};	//可行

void MyMethod(string[] names){}
MyMethod({"a", "b"});	//不可行
MyMethod(new []{"a", "b"});	//可行
```

在大括号内所有表达式的编译时类型的集合中，如果所有类型都能隐式转换成其中一种类型，该类型即为数组类型。只有表达式的类型才会成为一个候选的数组类型，所以偶尔需要将一个值显示转换为一个不具体的类型，如：

```CSharp
new[]{ new MemoryStream(), new StringWriter() }		//不行
new[]{ (IDisposable)new MemoryStream(), new StringWriter() }	//可以
```





#匿名类型

```CSharp
var tom = new {Name = "Tom", Age = 9};	//不能加string和int
```

在任何一个给定的程序集中，如果两个匿名对象初始化程序包含相同**数量**的属性，且这些属性具有相同的**名称和类型**，且以相同**顺序**出现，就认为它们是同一类型

匿名类型实际上是同一个泛型类型生成的，该泛型类型是参数化的，也是封闭的（不涉及来自外部的类型参数）

**匿名类型包含以下成员**：

* 一个获取所有初始值的构造方法，参数的顺序，名称和类型都与匿名对象初始化程序中的一样
* 共有的只读属性（所有属性都只有get没有set）
* 属性的私有只读字段
* 重写的`Equals、GetHsahCode和ToString`方法

因为**所有属性是只读**的，所以只要这些属性是不易变的，那么匿名类型就是不易变的



### 投影初始化程序

```CSharp
//自动将改对象的属性名Name作为匿名对象的属性名
var v = new {person.Name}
```



# Lambda表达式

* 与匿名方法类似，表达式的类型本身不是委托类型，但它可以通过多种方式隐式或显示转换为委托类型
* 参数列表必须全是显示类型或全是隐式类型



# 表达式树

是由对象构成的树，树中的每个节点本身是一个表达式

* `System.Linq.Expressions`命名空间包含了代表表达式的各个类，都继承自`Expression`——一个抽象的、主要包含一些静态工厂方法的类，这些方法用于创建其它表达式类的实例

  `Expression`类也包含两个属性：

  * **Type**：代表表达式求值后的.NET类型
  * **NodeType** ：返回所代表的表达式的种类，是`ExpressionType`枚举的成员，包括LessThan,Multiply,Invoke等



* Lambda表达式提供了编译时检查的能力，而表达式树可以将执行模型从你所需的逻辑中提取出来。两者合并，产生了"进程外"LINQ提供器，如LINQ to SQL，编译器将C#的Lambda表达式查询代码编译成使用表达式树的IL代码，使用时再转换为目标平台上的本地语言（SQL）
  * LINQ to Objects则是直接编译成使用委托的IL代码，直接在CLR中执行
  * 一般情况下，转换会试图在目标平台上执行所有逻辑，但也可能利用表达式树的编译功能在本地执行表达式的一部分，在别的地方执行另一部分
  * 编译器保证Lambda能转换成一个有效的表达式树，但无法保证该树在目标平台上有效使用



### 将表达式树编译成委托

`LambdaExpression`继承自`Expression`，又派生出`Expression<TDelegate>`。该泛型类以静态类型的方式标识了表达式的返回类型和参数

```CSharp
Expression firstArg = Expression.Constant(2);
Expression secondArg = Expression.Constant(3);
Expression add = Expression.Add(firstArg, secondArg);

Func<int> compiled = Expression.Lambda< Func<int> >(add).Compile();	//调用得到5
```

### 将Lambda表达式转换成表达式树

```CSharp
//简单的
Expression<Func<int>> return5 = () => 5;

//复杂的
Expression< Func<string, string, bool> > expression = (x, y) => x.StartsWith(y);
//相当于↓
//构造一个方法调用表达式树
MethodInfo method = typeof(string).GetMethod("StartsWith", new[]{typeof(string)});
var target = Expression.Parameter(typeof(string), "x");
var methodArg = Expression.Parameter(typeof(string), "y");

Expression call = Expression.Call(target, method, new[]{methodArg});
	
var lambdaParameters = new[]{target, methodArg};
var lambda = Expression.Lambda<Func<string, string, bool>>(call, lambdaParameters);
```

* **注意**：并非所有Lambda表达式都能转换为表达式树，带有一个语句块的（即使只有一个return语句）不行，带有赋值操作的也不行，只有对单个表达式进行求值的Lambda才可以。还有其它限制。。。



# 扩展方法

* 特征：
  * 必须在一个非嵌套的，非泛型的静态类中（所以必须是静态方法）
  * 至少有一个参数
  * **第一个参数**必须有`this`作前缀，不能有其它修饰符(ref/out)，不能是指针类型

* 当要调用一个实例方法时，编译器会先在真正的实例方法中寻找，找不到时就检查当前和导入的所有命名空间中的所有扩展方法，进行匹配

* 当变量的值为空引用时，不能调用实例方法，会报`NullReferenceException`异常，但可以调用扩展方法。可以写出类似`object.IsNull()`的方法

* 扩展方法的好处之一：可以将方法的调用链接在一起，如：

  ```CSharp
  var collection = Enumerable.Range(0, 10).Where(x => x % 2 != 0).Reverse();
  ```

  注意：这里将反转操作放在过滤操作后面可以提高效率

### 流式传输和缓冲传输

**流式传输**也叫**惰性求值**，**缓冲传输**也叫**热情求值**，.NET提供的扩展方法会尽量对数据进行**流式**，或者说**管道**传输，每要求一个数据时，才会从它链接的迭代器那里获取一个数据进行处理并返回。当然对于反转、排序等操作就必须用**缓冲**传输。这两种方式都属于**延迟执行**，在第一次调用`MoveNext()`前不做任何事情

有**延迟执行**，当然也有**立即执行**，一般来说，返回另外一个序列的操作(通常是`IEnumerable<T>`或`IQueryable<T>`使用**延迟执行**，返回单一值的运算使用**立即执行**



# LINQ

* **序列**是LINQ的基础
* 编译器会将查询表达式转译为C#代码(一般是扩展方法的调用)，是以一种机械的方式来转换的，不会去理解代码、应用类型引用、检查方法调用的有效性或执行编译器要执行的任何正常工作，这些都在**转换完成之后**执行
  * 通俗地说，LINQ表达式被粗暴地直接转换成方法的调用的代码，这转译不依赖于任何特定类型（不需要实现IEnumerable之类的接口），仅依赖于方法名称和参数，然后再来判断这个代码所能调用的是哪个方法，因此，我们可以自己实现一些同名方法来顶替原来的方法被调用



### Cast/OfType

这两个操作符都可以处理任意非类型化的序列（它们是非泛型IEnumerable类的扩展方法），并返回强类型的序列，把每个元素都转换成目标类型，遇到不是正确类型的元素时，**Cast**抛出异常，**OfType**跳过，只有**Cast**是LINQ表达式语法直接支持的

```CSharp
var list = new ArrayList{"First", "Second", "Third"};
IEnumerable<string> strs = list.Cast<string>();

list = new ArrayList{1, "NaN", 2, 3}
IEnumerable<int> ints = list.OfType<int>();

//LINQ
var list = new ArrayList{"First", "Second", "Third"};
var strs = from string entry in list select entry.Substring(0, 3);
//转译成
list.Cast<string>().Select(entry => entry.Substring(0,3));
```

* 两个操作符都对数据进行流处理，在获取元素时才转换
* 只允许**一致性、引用和拆箱**转换
* **Select**扩展方法只适用于**IEnumerable<T>**，而不能用于**IEnumerable**，需要用**Cast**进行转换



# Asp .Net MVC5

### 控制器Controller

* MVC控制器负责响应对ASP.NET MVC网站发起的请求。每一个浏览器请求都映射到了一个专门的控制器。举个例子，设想一下你在浏览器地址栏输入了下面的URL：

  http://localhost/product/index/3

  在这种情况下，将会调用一个名为`ProductController`的控制器。`ProductController`负责生成对浏览器请求的响应。举个例子，控制器可能会返回一个特定的视图，或者是将用户重定向到另一个控制器。

* 你可以通过在ASP.NET MVC应用程序的Controllers文件夹下添加一个新的控制器来创建一个新控制器。

* 控制器的名字必须含有Controller后缀

* 一个控制器是一个继承自`System.Web.Mvc.Controller`基类的类

* 控制器暴露出控制器动作。动作是控制器的一个方法，当你在浏览器地址栏输入某一特定的URL时，将会调用这个方法。举个例子，假设你对下面这个URL发出请求：

  http://localhost/Product/Index/3

  在本例中，`Index()`方法在`ProductController`类上被调用。`Index()`方法是控制器动作的一个例子。

* 一个控制器动作必须是控制器类的一个公共方法，意识到你添加到控制器类中的任何公共方法都会自动被暴露为控制器动作（你必须非常小心，因为控制器动作可以被全球的任何人调用，仅仅简单地通过在浏览器地址栏输入正确的URL）

* 控制器动作方法不能够重载，不能为静态方法，必须是公共的。

  * 方法必须是公共的。
  * 方法不能是静态方法。
  * 方法不能是扩展方法。
  * 方法不能是构造函数，访问器，或者设置器。
  * 方法不能拥有开放泛型类型（open generic types）。
  * 方法不能是控制器基类中的方法。
  * 方法不能含有ref或者out参数。

* 如果你需要在控制器中创建一个公共方法，但是你不想将这个方法发布为控制器动作，那么你可以通过使用`[NonAction]`特性来阻止该方法被外界调用。

* 控制器动作返回**动作结果**（Action Result）。动作结果是控制器动作返回给浏览器请求的东西。

  ASP.NET MVC框架支持六种标准类型的动作结果：

  1. ViewResult – 代表HTML及标记。
  2. EmptyResult – 代表无结果。
  3. RedirectResult – 代表重定向到一个新的URL。
  4. RedirectToRouteResult – 代表重定向到一个新的控制器动作。
  5. JsonResult – 代表一个JSON（Javascript Object Notation）结果，它可以用于AJAX应用程序。
  6. ContentResult – 代表着文本结果。

  所有这些动作结果都继承自ActionResult基类。

  通常情况下，你并不直接返回一个动作结果。而是调用Controller基类的下列方法之一：

  1. View – 返回一个ViewResult结果。
     * `new View(["ViewName"],[Model])` ：默认返回同名View，可按名字指定View，可传入Model对象
  2. Redirect – 返回一个RedirectResult 动作结果。
  3. RedirectToAction – 返回一个RedirectToAction动作结果。
  4. RedirectToRoute – 返回一个RedirectToRoute动作结果。
  5. Json – 返回一个JsonResult动作结果。
  6. Content – 返回一个ContentResult动作结果。

* 如果一个控制器动作返回了一个结果，而这个结果并非一个动作结果 – 例如，一个日期或者整数 – 那么结果将自动被包装在ContentResult（纯文本结果）中。当返回的是一个对象时，会调用其`ToString()`（默认得到`Namespace.ClassName`格式的类名）




### 视图View

* 可以使用`Controller.View(string ViewName)`的方法根据一个View文件创建一个`ViewResult`对象
* View文件一般放在以对应的控制器命名的文件夹下，公用的View文件则放在Shared文件夹下



###视图与控制器间的数据传输

* 使用`ViewData[string Name]`和`ViewBag.Name` ：

  * `ViewData[string Name]`存放数据，存进去之后是`object`类型，拿出来时需要转换类型；
  * `ViewBag.Name`是`ViewData[string Name]`的语法糖，是动态类型，不需要转换，速度比较慢
  * 二者可以共用（存进取出不必用同一个）

* 强类型View：

  1. 在View的顶部添加以下代码：`@model WebApplication1.Models.Employee`
  2. 之后就可以用`@Model.`的形式调用该类中的成员
  3. 在控制器中返回View时同时传入Model数据：`return View("MyView",emp);`

  * 一个View只能设置为一个Model的强类型

* ViewModel：可以在Model和View之间再创建一层——ViewModel，将Model的数据封装成适合View的数据，也可以整合多个Model

### 在服务器端（或Controller）获取Post数据

* Model Binder（根据`input`标签的`name`将其`value`绑定到调用的Action的同名参数（或对象参数的同名属性））：

  * 无论请求是否由带参的action方法生成，Model Binder都会自动执行。

  * Model Binder会通过方法的元参数迭代，然后会和接收到参数名称做对比。如果匹配，则响应接收的数据，并分配给参数。

  * 在Model Binder迭代完成之后，将类参数的每个属性名称与接收的数据做对比，如果匹配，则响应接收的数据，并分配给参数。

  * 元类型参数匹配不成功时，给予默认值（0/null）

  * 当参数为对象时，Model Binder将通过检索类所有的属性，将接收的数据与对象属性名称比较。

    当匹配成功时：

    * 如果接收的值是空，则会将空值分配给属性，如果无法执行空值分配，会设置缺省值，`ModelState.IsValid`将设置为false。
    * 如果空值分配成功，会考虑值是否合法，`ModelState.IsValid`将设置为false。
    * 如果匹配不成功，参数会被设置为缺省值。在本实验中`ModelState.IsValid`不会受影响。

* 还可以在Action方法中用`Request.Form["Name"]`的方式获取

* 还可以通过继承`DefaultModelBinder`自定义自己的Model Binder

```CSharp
public class MyEmployeeModelBinder : DefaultModelBinder{
  protected override object CreateModel(ControllerContext controllerContext, ModelBindingContext bindingContext, Type modelType){
    Employee e = new Employee();
    e.FirstName = controllerContext.RequestContext.HttpContext.Request.Form["FName"];
    e.LastName = controllerContext.RequestContext.HttpContext.Request.Form["LName"];
    e.Salary = int.Parse(controllerContext.RequestContext.HttpContext.Request.Form["Salary"]);
    return e;
  }
}
//使用
public ActionResult SaveEmployee([ModelBinder(typeof(MyEmployeeModelBinder))]Employee e, 
                                 string BtnSubmit){}
```

* 在Action方法的参数中，当调用时没有传参时，复杂类型会初始化（用无参构造方法初始化），其它的则会赋予null，而值类型无法赋予null会报异常

#### 传递空值转换为null问题
如果传递的值为空字符串，绑定时，MVC会自动将其转换为null，解决方法如下：
1. 在Model的不想转换的字段上添加注解`[DisplayFormat(ConvertEmptyStringToNull = false)]` 
2. 自定义ModelBinder
```CSharp
public class EmptyStringModelBinder : DefaultModelBinder {
    public override object BindModel(ControllerContext controllerContext, ModelBindingContext bindingContext) {
        bindingContext.ModelMetadata.ConvertEmptyStringToNull = false;
        Binders = new ModelBinderDictionary() { DefaultBinder = this };
        return base.BindModel(controllerContext, bindingContext);
    }
}
//使用
public ActionResult MyAction([ModelBinder(typeof(EmptyStringModelBinder))] MyModel model) {}
```


### Entity Framework

1. 连接SQL SERVER ,创建数据库 "SalesERPDB"

2. 创建连接字符串（**ConnectionString**）:

   打开Web.Config 文件，在< Configuration >标签内添加以下代码：

```xml
<connectionStrings>
	<add 
         connectionString="Data Source=(LocalDb)\MSSQLLocalDB;Initial Catalog=SalesERPDB;Integrated Security=True" 
         name="SalesERPDAL" providerName="System.Data.SqlClient"/>
</connectionStrings>
```

3. 添加EF引用，右击项目->管理Nuget 包。选择Entity Framework 并点击安装。

4. 创建数据访问层

   - 在根目录下，新建文件夹”Data Access Layer“，并在Data Access Layer文件夹中新建类” SalesERPDAL “
   - 在类文件顶部添加 Using System.Data.Entity代码。
   - 继承DbContext类

5. 创建Employee类的主键

   ```CSharp
   using System.ComponentModel.DataAnnotations;
   //添加Employee的属性，并使用Key 关键字标识主键。
   public class Employee{
       [Key]
       public int EmployeeId  { get; set; }
       public string FirstName { get; set; }
       public string LastName { get; set; }
       public int Salary { get; set; }
   }
   ```

6. 定义映射关系

7. 在 SalesERPDAL 类中添加新属性 Employee

   （完善SalesERPDAL 类）

   ```CSharp
   using System.Data.Entity;
   public class SalesERPDAL : DbContext{
     //DbSet表示数据库中能够被查询的所有Employee
     public DbSet<Employee> Employees{get;set;}
     protected override void OnModelCreating(DbModelBuilder modelBuilder){
   	modelBuilder.Entity<Employee>().ToTable("TblEmployee");	//TblEmployee为表名
   	base.OnModelCreating(modelBuilder);
     }
   }
   ```

8. 使用

   ```CSharp
   //从数据库获取Employee的List
   SalesERPDAL salesDal = new SalesERPDAL();
   List<Employee> emps = salesDal.Employees.ToList();
   //保存数据到数据库
   salesDal.Employees.Add(emp);
   salesDal.SaveChanges();
   ```

* 在Global.asax文件中在 Application_Start后添加以下语句，可以使模型类（Employee）改变时删除并重建数据库中对应的表：

  ```CSharp
  //使用Database类需要引用 “System.Data.Entity”命名空间
  Database.SetInitializer(new DropCreateDatabaseIfModelChanges<SalesERPDAL>());
  ```

* 当遇到异常`System.Data.Entity.Validation.DbEntityValidationException`，“对一个或多个实体的验证失败。有关详细信息，请参阅“EntityValidationErrors”属性”时，可以捕获并查看错误信息

  ```CSharp
  try {}
  catch (DbEntityValidationException dbEx) {
    foreach (var validationErrors in dbEx.EntityValidationErrors) {
      foreach (var validationError in validationErrors.ValidationErrors) {
        Debug.Write(string.Format("Class: {0}, Property: {1}, Error: {2}",
                              validationErrors.Entry.Entity.GetType().FullName,
                              validationError.PropertyName,
                              validationError.ErrorMessage), "error");
      }
    }
    throw;}
  ```

  ​


### 服务器端验证

1. 使用 **DataAnnotations**装饰属性

   ```CSharp
   public class Employee{
     [Required(ErrorMessage="Enter First Name")]
     public string FirstName { get; set; }
     
     [StringLength(5,ErrorMessage="Last Name length should not be greater than 5")]
     public string LastName { get; set; }
   }
   ```

2. 在控制器Action方法里用`ModelState.IsValid`判断参数是否绑定成功，不成功则用`RedirectToAction("View")`返回View 
- ** 也可以直接在Action方法里获取错误信息
    ```CSharp
    //校验数据
    if (!ModelState.IsValid) {
    	string msg = "";
    	foreach (var fieldState in ModelState) {
		foreach (var error in fieldState.Value.Errors) {
            		msg += error.ErrorMessage + "\n";
        	}
    	}
    	throw new ValidationException(msg);
    }
    ```

3. 在View里用`@Html.ValidationMessage("Name")`显示错误信息（`Html`是`HtmlHelper`类的实例。）

- **`UpdateModel`和 `TryUpdateModel` 方法之间的区别是什么？**

  `TryUpdateModel` 与`UpdateModel` 几乎是相同的，有点略微差别。

  如果Model调整失败，`UpdateModel`会抛出异常。就不会使用`UpdateModel`的 `ModelState.IsValid`属性。

  `TryUpdateModel`是将函数参数与Employee对象保持相同，如果更新失败，`ModelState.IsValid`会设置为False值。

- 自定义验证

  1. 创建自定义验证

  ```CSharp
  using System.ComponentModel.DataAnnotations;
  public class FirstNameValidation:ValidationAttribute{
    protected override ValidationResult IsValid(object value, 
                                                ValidationContext validationContext){
      if (value == null){
        return new ValidationResult("Please Provide First Name");
      }
      if (!value.ToString().Contains("@")){
        return new ValidationResult("First Name should contain @");
      }
      return ValidationResult.Success;
    }
  }
  ```

  2. 使用

  ```CSharp
  [FirstNameValidation]
  public string FirstName { get; set; }
  ```




#### 手动校验数据

```CSharp
var validResults = new List<ValidationResult>();
string msg = "";
//这里最后的true表示校验所有属性，false或无则只会校验Required
if (!Validator.TryValidateObject(t, new ValidationContext(t), validResults, true)) {
  foreach (var result in validResults) {
      msg += result.ErrorMessage + "\n";
  }
}
```






### 添加授权认证

* 先来了解ASP.NET是如何进行Form认证的。
  1. 终端用户在浏览器的帮助下，发送Form认证请求。
  2. 浏览器会发送存储在客户端的所有相关的用户数据。
  3. 当服务器端接收到请求时，服务器会检测请求，查看是否存在 “Authentication Cookie”的Cookie。
  4. 如果查找到认证Cookie，服务器会识别用户，验证用户是否合法。
  5. 如果未找到“Authentication Cookie”，服务器会将用户作为匿名（未认证）用户处理，在这种情况下，如果请求的资源标记着 `protected/secured`，用户将会重定位到登录页面。



1. 创建 `AuthenticationController`和 `Login`行为方法（直接返回`LoginView`）

2. 创建`UserDetails`的Model类（含`string UserName/Password`属性）

3. 创建登录页面`LoginView`，并将`UserDetails`转换为强View类型。

   ```html
   <!--在body内添加以下代码-->
   <div>
     <!--使用HtmlHelper类代替纯html代码，HtmlHelper类函数返回html字符串-->
     @using (Html.BeginForm("DoLogin", "Authentication", FormMethod.Post)){
   	@Html.LabelFor(c=>c.UserName)
     	@Html.TextBoxFor(x=>x.UserName)
   	<br />
   	@Html.LabelFor(c => c.Password)
   	@Html.PasswordFor(x => x.Password)
   	<br />
   	<input type="submit" name="BtnSubmit" value="Login" />
     }
   </div>

   <!--HtmlHelper类函数返回html字符串 例子-->
   1. @Html.TextBoxFor(x=>x.UserName)
   相当于	<input id="UserName" name="UserName" type="text" value="" />
   2. @using (Html.BeginForm("DoLogin", "Authentication", FormMethod.Post)){...}
   相当于	<form action="/Authentication/DoLogin" method="post">...</form>
   验证失败显示错误信息：@Html.ValidationMessageFor(x=>x.UserName)
   使用HtmlHelper类时，验证失败返回时，之前填写的数据会自动保留
   ```

4. 实现Form认证

   打开 Web.config文件，在System.Web部分，找到Authentication的子标签。如果不存在此标签，就在文件中添加Authentication标签。设置Authentication的Mode为Forms，loginurl设置为”Login”方法的URL

   ```xml
   <authentication mode="Forms">
   	<forms loginurl="~/Authentication/Login"></forms>
   </authentication>
   ```

   因为MVC5默认不再使用Forms验证 所以在web.config中注释掉下面的代码

   ```xml
   <system.webServer>
       <modules>
         <!--<remove name="FormsAuthentication" />-->
       </modules>
   </system.webServer>
   ```

5. 让Action 方法更安全，在Controller或Action方法前加`[Authorize]`，使其只能被满足授权要求的用户访问，否则跳转到上面设置的登录链接

6. 创建业务层功能，新建 `IsValidUser()`方法，用来验证账户密码准确性

7. 创建 `DoLogin()`  action 方法（给`DoLogin()`添加`[HttpPost]`特性，使其只能用Post形式访问）

   ```CSharp
   //1.通过调用业务层功能检测用户是否合法。

   //2.如果是合法用户，创建认证Cookie。可用于以后的认证请求过程中。
   //false表示创建非永久（即临时）cookie，浏览器关闭就会被删除
   FormsAuthentication.SetAuthCookie(u.UserName, false);

   //3.如果是非法用户，给当前的ModelState添加新的错误信息，将错误信息显示在View中。
   ModelState.AddModelError("CredentialError", "Invalid Username or Password");

   //返回各自对应的View
   ```

8. 在View 中显示验证信息

   ```CSharp
   @Html.ValidationMessage("CredentialError", new {style="color:red;" })
   ```

9. 获取已登录的用户名：`User.Identity.Name`

10. 注销：`FormsAuthentication.SignOut();`



- **`FormsAuthentication.SetAuthCookie`是必须写的吗？**

  是必须写的。让我们了解一些小的工作细节。

  - 客户端通过浏览器给服务器发送请求。
  - 当通过浏览器生成，所有相关的Cookies也会随着请求一起发送。
  - 服务器接收请求后，准备响应。
  - 请求和响应都是通过HTTP协议传输的，HTTP是无状态协议。每个请求都是新请求，因此当同一客户端发出二次请求时，服务器无法识别，为了解决此问题，服务器会在准备好的请求包中添加一个Cookie，然后返回。
  - 当客户端的浏览器接收到带有Cookie的响应，会在客户端创建Cookies。
  - 如果客户端再次给服务器发送请求，服务器就会识别。

  `FormsAuthentication.SetAuthCookie`将添加 “Authentication”特殊的Cookie来响应。

  ​

- **是否意味着没有Cookies，FormsAuthentication 将不会有作用？**

  不是的，可以使用URI代替Cookie。

  打开Web.Config文件，修改Authentication/Forms部分：

```xml
<forms cookieless="UseUri" loginurl="~/Authentication/Login"></forms>
```

​	授权的Cookie会使用URL传递。

​	通常情况下，Cookieless属性会被设置为“AutoDetect“，表示认证工作是通过Cookie完成的，是不支持URL传递的。



### 分部视图

从逻辑上看，分部视图是一种可重用的视图，不会直接显示，包含于其他视图中，作为其视图的一部分来显示。用法与用户控件类似，但不需要编写后台代码。

* 在创建视图时选择分部视图即可，也可以定义为强类型。内容直接写html和Razor语句

* 调用：`@{ Html.RenderPartial("Footer", Model.FooterData);}`

* 通过调用Action返回

  ```CSharp
  //View处
  @{Html.RenderAction("GetAddNewLink");}
  //Action
  return PartialView("AddNewLink");
  ```

  ​

*  **Html.Partial的作用是什么？与Html.RenderPartial区别是什么？**

  与Html.RenderPartial作用相同,Html.Partial会在View 中用来显示分部View。

  Html.RenderPartial会将分部View的结果直接写入HTTP 响应流中，而 Html.Partial会返回 MvcHtmlString值。

* **什么是MvcHtmlString，为什么 Html.Partial返回的是MvcHtmlString 而不是字符串？**

  根据MSDN规定，”MvcHtmlString”代表了一个 HTML编码的字符串，不需要二次编码。

* **Html.RenderAction 和 Html.Action两者之间有什么不同？更推荐使用哪种方法？**

  Html.RenderAction会将Action 方法的执行结果直接写入HTTP 响应请求流中，而 Html.Action会返回MVC HTML 字符串。更推荐使用Html.RenderAction，因为它更快。当我们想在显示前修改action执行的结果时，推荐使用Html.Action。

* 可以用`Session["Name"]`的方式存放或取出数据，取出时要转换类型



### 自定义Action过滤器实现直接URL 安全

* **什么是 ActionFilter ?**

  **与AuthorizationFilter类似，ActionFilter是ASP.NET MVC过滤器中的一种，允许在action 方法中添加预处理和后处理逻辑。**

1. 新建`AdminFilter`继承`ActionFilterAttribute`

   ```CSharp
   using System.Web.Mvc;
   public class AdminFilter : ActionFilterAttribute{
     //重写执行前事件
     public override void OnActionExecuting(ActionExecutingContext filterContext) {
       if (!Convert.ToBoolean(filterContext.HttpContext.Session["IsAdmin"])) {
         filterContext.Result = new ContentResult() {
           Content = "Unauthorized to access specified resource."
         };
       }
     }
   }
   ```

2. 在Action方法前添加`[AdminFilter]`绑定过滤器



### 实现项目外观的一致性--使用布局页面

布局页面，使所有页面具有相同的布局，比如都有相同的页眉和页脚

1. 创建布局页面MyLayout，基本就是普通的cshtml页面，也可以设置强类型

   ```CSharp
   //用此方法定义各部分
   //第二个参数表示该部分是否必须，是可选的，默认为true
   @RenderSection("ScetionName", [bool])
   ```

2. 创建使用布局的页面

   ```CSharp
   @{	
   	Layout = "~/Views/Shared/MyLayout.cshtml";	//设置使用的布局
   }
   //设置布局各部分的内容
   @section SectionName{...}
   ```

* 布局是可以嵌套的

- **最大的问题是什么？**

  带有数据的页脚和页眉作为ViewModel的一部分传从Controller传给View。

  解决方法：继承，定义一个ViewModel的基类，含有页面和页脚所需的数据，所有相同布局的View的ViewModel都继承此基类

* @Html.RenderBody()

  在内容页面，通常会定义Section，声明Layout页面。但是奇怪的是，Razor允许定义在Section外部定义一些内容。所有的非section内容会使用`@Html.RenderBody()`函数来渲染

* **全局设置**：在View文件夹下发现特殊的文件“__ViewStart.cshtml”，在其内部的Layout设置会应用所有的View

* 可以自定义ActionFilter对Action进行后处理，统一设置BaseViewModel

  ```CSharp
  using System.Web;
  using System.Web.Mvc;
  public class HeaderFooterFilter : ActionFilterAttribute{
    //重写执行后事件
    public override void OnActionExecuted(ActionExecutedContext filterContext) {
      ViewResult v = filterContext.Result as ViewResult;
      if(v != null) {
        BaseViewModel bvm = v.Model as BaseViewModel;
        if(bvm != null) {
          bvm.FooterData = new FooterViewModel() {
            CompanyName = "ABCompany",
            Year = DateTime.Now.Year.ToString()
          };
          //两种皆可
          //bvm.UserName = filterContext.HttpContext.User.Identity.Name;
          bvm.UserName = HttpContext.Current.User.Identity.Name;
        }
      }
    }
  }
  ```

  ​

### 上传文件

1. **创建 `FileUploadViewModel`**

   ```CSharp
   public class FileUploadViewModel: BaseViewModel{
     //提供客户端上传的单独文件的访问
     public HttpPostedFileBase fileUpload {get; set ;}
   }
   ```

2. **上传文件的表单**

   ```html
   <!--  enctype="multipart/form-data"
   不对字符编码。在使用包含文件上传控件的表单时，必须使用该值。-->
   <form action="/BulkUpload/Upload" method="post" enctype="multipart/form-data">
     Select File : <input type="file" name="fileUpload" value="" />
     <input type="submit" name="name" value="Upload" />
   </form>
   ```

3. 读取上传的文件(csv文件为例，csv是以逗号分隔数据的文本文件)

   ```CSharp
   public ActionResult Upload(FileUploadViewModel fileUpVM){
     ...
     var emps = new List<Employee>();
     StreamReader csvreader = new StreamReader(fileUpVM.FileUpload.InputStream);
     csvreader.ReadLine();
     while (!csvreader.EndOfStream) {
       string[] values = csvreader.ReadLine().Split(',');
       Employee e = new Employee();
       e.FirstName = values[0]; e.LastName = values[1]; e.Salary = int.Parse(values[2]);
       emps.Add(e);
     }
     ...
   }
   ```

* **是否会提供多文件的输入控件？**

  是，有两种方法可以实现：

  1. 创建多文件输入控件，每个控件有唯一的名称，`FileUploadViewModel`类会为每个控件创建 `HttpPostedFileBase`类型的属性，每个属性名称应该与控件名称匹配。
  2. 创建多文件输入控件，每个控件有相同的名称，创建类型的List列表，代替创建多个`HttpPostedFileBase`类型的属性

* **`enctype="multipart/form-data` 是用来做什么的？**

  该属性指定了post 数据的编码类型，默认属性值是`application/x-www-form-urlencoded`

  例如登录，默认enctype会给服务器发送以下Post 请求

  ```
  POST /Authentication/DoLogin HTTP/1.1
  Host: localhost:8870
  Connection: keep-alive
  Content-Length: 44
  Content-Type: application/x-www-form-urlencoded
  ......
  UserName=Admin&Passsword=Admin&BtnSubmi=Login
  ```

  所有输入值会被作为发送的值的一部分，以”key/value“的形式发送。

  当 enctype="multipart/form-data" 属性被加入Form标签中，以下post 请求会被发送到服务器。

  ```
  POST /Authentication/DoLogin HTTP/1.1
  Host: localhost:8870
  Connection: keep-alive
  Content-Length: 452
  Content-Type: multipart/form-data; boundary=----WebKitFormBoundarywHxplIF8cR8KNjeJ
  ......
  ------WebKitFormBoundary7hciuLuSNglCR8WC
  Content-Disposition: form-data; name="UserName"
  Admin
  ------WebKitFormBoundary7hciuLuSNglCR8WC
  Content-Disposition: form-data; name="Password"
  Admin
  ------WebKitFormBoundary7hciuLuSNglCR8WC
  Content-Disposition: form-data; name="BtnSubmi" 
  Login
  ------WebKitFormBoundary7hciuLuSNglCR8WC--
  ```

  如上所示，Form会在多部分post发送，每部分都是被分界线分割的，每部分包含单值。

  如果form标签包含文件输入控件的话，enctype必须被设置为”multipart/form-data“。

* **为什么有时候需要设置 encType 为 “multipart/form-data”，而有时候不需要设置？**

  当encType  设置为”multipart/form-data“，将会实现Post数据和上传文件的功能，当然也会增加请求的size 增加，请求size 越大意味着性能越低。因此得出的最佳实践经验需要设置为默认的”application/x-www-form-urlencoded“。

### 解决线程饥饿

程序中任何事件都是由线程执行的，请求事件也是

Asp.net  framework 维护线程池，每次当请求发送到webserver时，会从线程池中分配空闲的线程处理此请求。这种线程被称为worker线程。

当请求处理完成，该线程无法服务其他请求时，worker 线程会被阻塞。如果一个应用程序接收到很多请求，且处理每个请求都非常耗时。在这种情况下，我们就必须指定一个点来结束请求，当有新的请求进入状态时，没有worker 线程可使用，这种现象称为**线程饥饿**。

**线程饥饿的解决方法：**

使用**异步请求**来代替同步请求:

* 异步请求的情况下，会分配worker线程来服务请求。
* worker 线程初始化异步操作，并返回到线程池服务其他请求。异步操作可使用CLR 线程来继续执行。
* 存在的问题就是，CLR 线程无法返回响应，一旦它完成了异步操作，它会通知Asp.net。
* Webserver 再次获取一个worker线程来处理剩余的请求，并返回响应。

上述使用场景中，会获取**两次**worker 线程，这两次获取的线程**可能相同**，也**可能会不同**。

文件读取是I/O操作，不需要使用worker 线程处理。因此最好将同步请求转换为异步。

1. **创建异步控制器**

   ```CSharp
   //继承AsyncController类
   public class BulkUploadController : AsyncController{}
   ```

2. **转换同步Action方法**

   ```CSharp
   //该功能通过两个关键字就可实现：async 和 await
   //异步方法必须返回void、Task或Task<T>
   public async Task<ActionResult> Upload(FileUploadViewModel fileUpVM) {
     int t1 = Thread.CurrentThread.ManagedThreadId;  //获取线程id
     //异步执行读取文件操作
     //此处会释放worker线程，读取完后再重新获取worker线程，然后再继续执行之后代码
     List<Employee> emps = await Task.Factory.StartNew<List<Employee>>(
       () => GetEmployees(fileUpVM)
     );
     int t2 = Thread.CurrentThread.ManagedThreadId;  //获取线程id，两次id可能会不同
     ...
   }
   ```

理一下思路：

- 当上传按钮被点击时，新请求会被发送到服务器。
- Webserver从线程池中产生Worker线程 ，并分配给服务器请求。
- worker线程会使Action 方法执行
- Worker方法在 Task.Factory.StartNew方法的辅助下，开启异步操作
- 使用async关键字将Action 方法标记为异步方法，由此会保证异步操作一旦开启，Worker 线程就会释放。
- 使用await关键字也可标记异步操作，能够保证异步操作完成时才能够继续执行下面的代码。
- 一旦异步操作在Action 方法中完成执行，必须执行worker线程。因此webserver将会新建一个空闲worker 线程，并用来服务剩下的请求，提供响应。



### 异常处理

##### 显示自定义错误页面

* **异常过滤器的作用是什么？，是否有自动执行的异常过滤器？**

  一旦action 方法中出现异常，异常过滤器就会控制程序的运行过程，开始内部自动写入运行的代码。MVC为我们提供了编写好的异常过滤器：HandeError。

  当action方法中发生异常时，过滤器就会在“~/Views/[current controller]”或“~/Views/Shared”目录下查找到名称为”Error”的View，然后创建该View的ViewResult，并作为响应返回。

1. **激活异常过滤器**

   当自定义异常被捕获时，异常过滤器变为可用。为了能够获得自定义异常，打开Web.config文件，在System.Web.Section下方添加自定义错误信息。

   ```xml
   <system.web>
   	...
   	<customErrors mode="On"></customErrors>
   </system.web>
   ```

2. **创建Error View**

   在“~/Views/Shared”文件夹下，会发现存在“Error.cshtml”文件，该文件是由MVC 模板提供的，如果没有自动创建，该文件也可以手动完成。

3. **绑定异常过滤器**

   将过滤器绑定到action方法或controller上，不需要手动执行，打开 App_Start文件夹中的FilterConfig.cs文件。在 RegisterGlobalFilters 方法中会看到 HandleError 过滤器已经以全局过滤器绑定成功。

   ```CSharp
   [AllowAnonymous]	//使此Action跳过授权认证检查（AuthorizeAttribute）
   public static void RegisterGlobalFilters(GlobalFilterCollection filters){
   	filters.Add(new HandleErrorAttribute());//ExceptionFilter
   	filters.Add(new AuthorizeAttribute());
   }
   ```

4. **在View中显示错误信息**

   将Error View转换为HandleErrorInfo类的强类型View，并在View中显示错误信息。

   ```html
   @model System.Web.Mvc.HandleErrorInfo
   ...
   Error Message : @Model.Exception.Message <br />
   Controller : @Model.ControllerName <br />
   Action : @Model.ActionName <br />
   ```

5. **过滤404错误**

   Handle error属性能够确保无论是否出现异常，自定义View都能够显示，但是它的能力在controller和action 方法中是受限的。不会处理“Resource not found”这类型的错误。

   **创建 ErrorController控制器，并创建Index方法**

   ```CSharp
   public class ErrorController : Controller {
     public ActionResult Index(){
       Exception e=new Exception("Invalid Controller or/and Action Name");
       HandleErrorInfo eInfo = new HandleErrorInfo(e, "Unknown", "Unknown");
       return View("Error", eInfo);
     }
   }
   ```

6. **绑定404错误到该Action上**

   可在 web.config中之前写下的`<customErrors>`下定义“Resource not found error”的设置

   ```xml
   <system.web>
   	...
   	<customErrors mode="On">
   		<error statusCode="404" redirect="/Error/Index"></error>
   	</customErrors>
   </system.web>
   ```

* **Error View的名称是否可以修改？**

  可以修改，不一定叫Error，也可以指定其他名字。如果Error View的名称改变了，当绑定HandleError过滤器时，必须制定View的名称。

  ```CSharp
  [HandleError(View="MyError")]
  //Or
  filters.Add(new HandleErrorAttribute(){
  	View="MyError"
  });
  ```

* **是否可以为不同的异常获取不同的Error View？**

  可以，在这种情况下，必须多次应用Handle error filter。

  ```CSharp
  [HandleError(View="DivideError",ExceptionType=typeof(DivideByZeroException))]
  [HandleError(View = "NotFiniteError", ExceptionType = typeof(NotFiniteNumberException))]
  [HandleError]
  //OR
  filters.Add(new HandleErrorAttribute(){
  	ExceptionType = typeof(DivideByZeroException),
  	View = "DivideError"});

  filters.Add(new HandleErrorAttribute(){
    ExceptionType = typeof(NotFiniteNumberException),
    View = "NotFiniteError"});

  filters.Add(new HandleErrorAttribute());
  ```

  前两个Handle error filter都指定了异常，而最后一个更为常见更通用，会显示所有其他异常的Error View。

##### 记录异常日志

1. **创建 Logger 类**

   在根目录下，新建文件夹，命名为Logger。在Logger 文件夹下新建类 FileLogger

   ```CSharp
   public class FileLogger {
     public void LogException(Exception e) {
       //3种得到服务器物理路径的方法
       //string filePath1 = AppDomain.CurrentDomain.BaseDirectory + "ErrorLog/" +
       	//DateTime.Now.ToString("dd-MM-yy mm hh ss") + ".txt";
       string filePath2 = HttpContext.Current.Server.MapPath("~/ErrorLog/" + DateTime.Now.ToString("dd-MM-yy mm hh ss") + ".txt");
       //string filePath3 = HostingEnvironment.MapPath
       	//(@"~/ErrorLog/" + DateTime.Now.ToString("dd-MM-yy mm hh ss") + ".txt");
       File.WriteAllLines(filePath2, new string[] {
         "Message : " + e.Message,
         "Stacktrace" + e.StackTrace});
     }
   }
   ```

2. **创建 EmployeeExceptionFilter 类**

   ```CSharp
   public class EmployeeExceptionFilter : HandleErrorAttribute{
     public override void OnException(ExceptionContext filterContext) {
       FileLogger logger = new FileLogger();
       logger.LogException(filterContext.Exception);
       //执行基类方法，返回响应
       base.OnException(filterContext);
     }
   }
   ```

3. **修改默认的异常过滤器**

   打开 FilterConfig.cs文件，删除 HandErrorAtrribute，添加上步中创建的。

   ```CSharp
   public static void RegisterGlobalFilters(GlobalFilterCollection filters){
     //filters.Add(new HandleErrorAttribute());//ExceptionFilter
     filters.Add(new EmployeeExceptionFilter());
   }
   ```

   ​

* **3种得到服务器物理路径的方法**

  ```CSharp
  string filePath1 = AppDomain.CurrentDomain.BaseDirectory + "ErrorLog/xx.txt";
  string filePath2 = HttpContext.Current.Server.MapPath("~/ErrorLog/xx.txt");
  string filePath3 = HostingEnvironment.MapPath(@"~/ErrorLog/xx.txt");
  ```

* **在 OnException 中，是否可以返回其他结果？**

  可以，代码如下：

  ```CSharp
  public override void OnException(ExceptionContext filterContext){
    FileLogger logger = new FileLogger();
    logger.LogException(filterContext.Exception);
    //base.OnException(filterContext);
    filterContext.ExceptionHandled = true;
    filterContext.Result = new ContentResult(){
      Content="Sorry for the Error"
    };
  }
  ```

  当返回自定义响应时，做的第一件事情就是通知MVC 引擎，手动处理异常，因此不需要执行默认的操作，不会显示默认的错误页面。使用以下语句可完成：

  ```
  filterContext.ExceptionHandled = true
  ```

  ​

### Server.MapPath()

Server.MapPath()的全名是System.Web.HttpContext.Current.Server.MapPath()。作用是返回与Web服务器上的指定虚拟路径相对应的物理文件路径。其参数path为Web 服务器的虚拟路径，返回结果是与path相对应的物理文件路径。但有时参数并非为虚拟路径，而是用户自定义的文件名。

* **Server.MapPath()应用**

  假设当前的网站目录为E:\wwwroot 应用程序虚拟目录为E:\wwwroot\company 浏览的页面路径为E:\wwwroot\company\news\ 下面的一个 aspx页面。

  在该页面中使用

  Server.MapPath("") ：返回当前页面所在的物理文件路径：E:\wwwroot\company\news

  Server.MapPath("/") ：返回应用程序根目录所在的物理文件路径：E:\wwwroot

  Server.MapPath("./") ：返回当前页面所在的物理文件路径：E:\wwwroot\company\news

  Server.MapPath("../")：返回当前页面所在的上一级的物理文件路径：E:\wwwroot\company

  Server.MapPath("~/")：返回应用程序的虚拟目录（路径）：E:\wwwroot\company

  Server.MapPath("~")：返回应用程序的虚拟目录（路径）：E:\wwwroot\company




### 路由

在Asp.net mvc中有RouteTable这个概念，是用来存储URL 路径的，简而言之，是保存已定义的应用程序的可能的URL pattern的集合。

默认情况下，路径是项目模板组成的一部分。可在 Global.asax 文件中检查到，在 Application_Start中会发现以下语句：

```CSharp
RouteConfig.RegisterRoutes(RouteTable.Routes);
```

App_Start文件夹下的 RouteConfig.cs文件，包含以下代码块：

```CSharp
public class RouteConfig{
  public static void RegisterRoutes(RouteCollection routes){
    routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
    //默认路由
    routes.MapRoute(
      name: "Default",
      url: "{controller}/{action}/{id}",
      defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
    );
  }
}
```

RegisterRoutes方法已经包含了由routes.MapRoute 方法定义的默认的路径。已定义的路径会在请求周期中确定执行的是正确的控制器和action 方法。如果使用 route.MapRoute创建了多个路径，那么内部路径的定义就意味着创建Route对象。

MapRoute 方法也可与 RouteHandler 关联。

##### **理解ASP.NET MVC 请求周期**

在本节中我们只讲解请求周期中重要的知识点

1. **UrlRoutingModule**

   当最终用户发送请求时，会通过UrlRoutingModule 对象传递，UrlRoutingModule 是HTTP 模块。


2. **Routing**

   UrlRoutingModule 会从route table集合中获取首次匹配的Route 对象，为了能够匹配成功，请求URL会与route中定义的URL pattern 匹配。

   **当匹配的时候必须考虑以下规则**：

   * 数字参数的匹配（请求URL和URL pattern中的数字）
   * URL pattern中的可选参数：url中未指定的参数会由`defaults`属性中的默认参数填充
   * 参数中定义的静态参数：如ABC/{controller}/PQR/{action}/XYZ，只匹配将静态参数全相同地写出来的url

3. **创建MVC Route Handler**

   一旦Route 对象被选中，UrlRoutingModule会获得 Route对象的 MvcRouteHandler对象。

4. **创建 RouteData 和 RequestContext**

   UrlRoutingModule使用Route对象创建RouteData，可用于创建RequestContext。RouteData封装了路径的信息如Controller名称，action名称以及route参数值。

   * **Route 参数** ：
     * 如{id}，可以被Action方法的同名参数id匹配接收为string或int类型
     * **不能连续包含两个参数，要用/或文本字符分隔**


5. **创建MVC Handler**

   MvcRouteHandler 会创建 MVCHandler的实例传递 RequestContext对象

6. **创建Controller实例**

   MVCHandler会根据 ControllerFactory的帮助创建Controller实例

7. **执行方法**

   MVCHandler调用Controller的执行方法，执行方法是由Controller的基类定义的。

8. **调用Action 方法**

   每个控制器都有与之关联的 ControllerActionInvoker对象。在执行方法中ControllerActionInvoker对象调用正确的action 方法。

9. **运行结果**

   Action方法会接收到用户输入，并准备好响应数据，然后通过返回语句返回执行结果，返回类型可能是ViewResult或其他。



#####定义自己的路由

```CSharp
public class RouteConfig {
  public static void RegisterRoutes(RouteCollection routes) {
    routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
    //自定义路由
    //自定义后，自定义和默认两种url形式都可以用
    routes.MapRoute(
                name : "Upload",
                url : "Employee/BulkUpload",
                defaults : new {controller = "BulkUpload", action = "Index"}
            );
    //越常用的路由，定义顺序越后
    //默认路由
    routes.MapRoute(
      name: "Default",
      url: "{controller}/{action}/{id}",
      defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
    );
  }
}
```

* **Route 参数和Query 字符串有什么区别？**(Query字符串是指URL请求中“问号”后面的部分)
  * Query 字符串本身是有大小限制的，而无法定义Route 参数的个数。
  * 无法在Query 字符串值中添加限制，但是可以在Route 参数中添加限制。
  * 可能会设置Route参数的默认值，而Query String不可能有默认值。
  * Query 字符串可使URL 混乱，而Route参数可保持它有条理。

* **如何在Route 参数中使用限制？**

  可使用正则表达式。如：

  ```CSharp
  routes.MapRoute(
    "MyRoute",
    "Employee/{EmpId}",
    new {controller=" Employee ", action="GetEmployeeById"},
    new { EmpId = @"\d+" }
  );
  ```

* **定义路径的顺序重要吗？**

  有影响，在上面的实验中，我们定义了两个路径，一个是自定义的，一个是默认的。默认的是最先定义的，自定义路径是在之后定义的。

  当用户输入“http://.../Employee/BulkUpload”地址后发送请求，UrlRoutingModule会搜索与请求URL 匹配的默认的route pattern ，它会将 Employee作为控制器的名称，“BulkUpload”作为action 方法名称。因此定义的顺序是非常重要的，更常用的路径应放在最后。

* **是否需要将action 方法中的参数名称与Route 参数名称保持一致？**

  Route Pattern 也许会包含一个或多个RouteParameter，为了区分每个参数，必须保证action 方法的参数名称与Route 参数名称相同。

* **是否有什么简便的方法来定义Action 方法的URL pattern？**

  我们可使用基于 routing 的特性。

  1. **基本的routing 属性可用**

     在 RegisterRoutes 方法中在 IgnoreRoute语句后输入代码如下：

     ```CSharp
     routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
     routes.MapMvcAttributeRoutes();	//这一句
     routes.MapRoute(...);
     ```

  2. **定义action 方法的 route pattern**

     ```CSharp
     [Route("Employee/List")]
     public ActionResult Index(){...}
     //routing 属性也可定义route 参数
     [Route("Employee/List/{id}")]
     public ActionResult Index (string id) { ... }
     ```

* **IgnoreRoutes 的作用是什么？**

  当我们不想使用routing作为特别的扩展时，会使用IgnoreRoutes。作为MVC模板的一部分，在RegisterRoute 方法中下列语句是默认的：

  ```
  routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
  ```

  这就是说如果用户发送以“.axd”为结束的请求，将不会有任何路径加载的操作，请求将直接定位到物理资源。




### 区域Areas

Areas是实现Asp.net MVC 项目模块化管理的一种简单方法。

每个项目由多个模块组成，如支付模块，客户关系模块等。在传统的项目中，采用“文件夹”来实现模块化管理的，你会发现在单个项目中会有多个同级文件夹，每个文件夹代表一个模块，并保存各模块相关的文件。

然而，在Asp.net MVC 项目中使用自定义文件夹实现功能模块化会导致很多问题。

下面是在Asp.Net MVC中使用文件夹来实现模块化功能需要注意的几点：

* DataAccessLayer, BusinessLayer, BusinessEntities和ViewModels的使用不会导致其他问题，在任何情况下，可视作简单的类使用。
* Controllers—只能保存在Controller 文件夹，但是这不是大问题，从MVC4开始，控制器的路径不再受限。现在可以放在任何文件目录下。
* 所有的Views必须放在“~/Views/ControllerName” or “~/Views/Shared”文件夹。

创建Area之后，在其文件夹中会有自己的Controller、View、Model文件夹，Model也可以使用项目非Area的那些

* **假设创建一个Area叫SPA，为什么url在控制器名前需要使用SPA关键字**

  在ASP.NET MVC应用中添加area时，Visual Studio会自动创建并命名为“[AreaName]AreaRegistration.cs”的文件，其中包含了AreaRegistration的派生类。该类定义了 AreaName属性和用来定义register路径信息的 RegisterArea 方法。

  在本次实验中你会发现nameSPAArealRegistration.cs文件被存放在“~/Areas/SPA”文件夹下，SPAArealRegistration类的代码如下：

  ```CSharp
  public class SPAAreaRegistration : AreaRegistration {
    public override string AreaName {get {return "SPA";}}
    //这重点
    public override void RegisterArea(AreaRegistrationContext context) {
      context.MapRoute(
        "SPA_default",
        "SPA/{controller}/{action}/{id}",
        new { action = "Index", id = UrlParameter.Optional }
      );
    }
  }
  ```

* **SPAAreaRegistration的RegisterArea方法是怎样被调用的？**

  打开global.asax文件，首行代码如下：

  ```CSharp
  AreaRegistration.RegisterAllAreas();
  ```

  RegisterAllAreas方法会找到应用程序域中所有AreaRegistration的派生类，并主动调用RegisterArea方法

* **是否可以不使用SPA关键字来调用MainController？**

  AreaRegistration类在不删除其他路径的同时会创建新路径。RouteConfig类中定义了新路径仍然会起作用。如之前所说的，Controller存放的路径是不受限制的，因此它可以工作，但可能不会正常的显示，因为无法找到合适的View。




###关于重定向

* **重定向与转发的区别**
  1. 重定向后，浏览器上的地址发生改变，转发则不变
  2. 重定向实际发生了两次请求，转发只有一次
     * 重定向：发送请求 -->服务器运行-->响应请求，返回给浏览器一个新的地址与响应码-->浏览器根据响应码，判定该响应为重定向，自动发送一个新的请求给服务器，请求地址为之前返回的地址-->服务器运行-->响应请求给浏览器
     * 转发：发送请求 -->服务器运行-->进行请求的重新设置，例如通过request.setAttribute(name,value)-->根据转发的地址，获取该地址的网页-->响应请求给浏览器
  3. 重定向时的网址可以是任何网址，转发的网址必须是本站点的网址
  4. 重定向：以前的request中存放的变量全部失效，并进入一个新的request作用域。
     转发：以前的request中存放的变量不会失效，就像把两个页面拼到了一起。

重定向后，是一个新的请求，原来放在request请求的**复杂对象会丢失**，比如：集合。但**简单的对象**可以传递，比如：字符串，数字

### WebAPI

* **什么是WebAPI？**

  Web API是一种用于创建基于HTTP的服务的ASP.NET技术。与web form和MVC一样，web API是ASP.NET的一种形式，它可以自动获取ASP.NET表单认证、异常处理等多种功能。

  Web应用程序是可以通过Web访问的应用程序。“Web应用程序必须有UI”不是正确的。它们可以只是一些通过Web公开数据的业务应用程序。我们可以使用WebAPI创建Web应用程序

* **Web API与Web Services和WCF Services有什么不同？**

  Web Services和WCF Services是基于SOAP的服务。这里，HTTP将仅仅用作传输协议。Web API纯粹是基于HTTP的服务。这里不会有SOAP的介入。

* **为什么WCF和Web Services需要SOAP？**

  假设我们想从Technology1发送一些数据到Technology2，其中Technology2是WCF / Web Services，通信必须通过互联网/网络进行

  Web意味着HTTP。HTTP将允许我们向特定URL发起请求，并让我们也传递一些数据。

  现在，Technology1将其数据转换为XML字符串（或大部分JSON字符串），然后将其发送到Technology2。	

  **注意**：当.Net Web Services或WCF Services是通信点时，数据将始终格式化为XML字符串。

  **值得讨论的点：**

  * 当开发者设计服务时，他实际上在服务内部创建一个或多个方法。每种方法都是为了一些特殊的功能。我们称之为web方法。当通信时，Technology1实际上会从服务中调用这些方法。现在在HTTP中，没有指定方法名称的规则。唯一的选择是，当客户端通过HTTP发送请求时，将方法名连接到数据，并将自己作为数据发送。在服务端，Service框架将从客户端接收到的数据分解为**实际的数据**和**方法名称**。然后调用服务中相应的方法传递实际数据作为参数。
  * 当Technology1将数据发送到服务时，服务如何确保数据是从有效客户端发送的？不允许访问服务的人可能会尝试发送数据。为了解决这个问题，就像上述方法名称解决方案一样，Technology1将**验证证书**连接到数据，并将它们作为一个发送。在服务端，Service框架将从客户端接收到的数据分解为**凭证**，**方法名称**和**实际数据**。它会验证凭据，并确保在进行Web方法调用之前请求是已验证的请求

* **服务如何从传入数据中提取不同的部分？**

  当Technology1发送数据到服务时，数据实际上将包含三件东西 - **实际数据**，**验证证书**和**方法名称**。

  在服务端，服务框架将很难独立地理解每个部分，因为服务是一个普遍的东西，它应该适用于每个人。其次客户端可以以任何格式发送数据。例如，客户端可以以“Data | Credential | MethodName”格式发送完整的数据，或者可以发送“Credential | Data | MethodName”格式。没有固定的格式。

* **解决方案-标准封装**

  为了解决这个问题，行业提出了一个名为**SOAP封装**的标准封装概念。

  SOAP不过是一个专门的XML，它将封装所有需要发送的内容，但是是以标准格式。HTTP将从客户端发送SOAP封装到服务，反之亦然。

  下面是引用的样例SOAP XML

  ````XML
  <?xml version="1.0"?>
  <soap: Envelope
  	xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
  	soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">
  <soap: Body xmlns:m="http://www.example.org/stock">
    <m: MyMethodName>
      <m: MyData>Data in Json or XML format</m: MyData>
    </m: MyMethodName>
  </soap: Body>
  </soap: Envelope>
  ````

  **SOAP有什么缺点?**

  * 它增加了数据的整体大小，从而影响了性能。
  * 轻量级框架（比如JavaScript，Mobile框架等等）创建和解析XML字符串比较困难。

* **真正的解决方案**

  解决方案将是纯粹的基于HTTP的服务。 没有任何SOAP封装的服务。 HTTP将直接将数据从一个位置传输到另一个位置。 这就是REST原理的来源。

  REST（即表述性状态传递）表示以更高效的方式使用网络的现有功能并创建服务。 以它们表示的格式传输数据，而不是将它们放在SOAP信封中。

  基于HTTP的服务完全基于REST原则，因此它们也被称为基于REST的服务。

* **如何实施解决方案**

  * **方法名称的解决方案**

    现在在基于SOAP的服务（Web和WCF服务）中，每个服务都由URL标识。 同样的，REST服务将通过URL的帮助来识别。

    对于REST服务，我们定义方法的方式将有所不同。

    1. 它将定义为标准方法。标准方法意味着方法名称将被固定。它要么GET,POST,PUT或者DELETE
    2. 或者它将定义任何名称的方法，但在这种情况下，每个自定义方法都会有一个URL。

    在任何一种情况下，不需要从客户端发送参数作为方法名称。

    * 在选择1的情况下，客户端将通过HTTP向REST服务发出请求。 请求的类型将是GET,POST,PUT或DELETE。 在服务端，服务框架将检查请求的类型并相应地调用服务中的方法。 方法名称问题得到解决。
    * 在选择2的情况下，客户端将通过HTTP直接请求REST服务方法。 这是可能的，因为每个方法将有一个唯一的URL。

  * **安全性的解决方案**

    HTTP头和HTTP主体，它们都具有携带一些信息的能力。我们可以始终将凭证作为头部的一部分和数据作为主体的一部分。为了确保在传输过程中没有人能够看到头和正文中的数据，我们可以实现SSL(https)。

  * 数据将通过HTTP直接以JSON或XML格式传递。大部分都是JSON。

* **WCF REST与Web API有什么不同？**

  WCF REST是用于创建基于REST的服务的早期Microsoft实现。

  WCF从来没有用于REST。 它唯一的目的是支持SOA。 使用WCF创建REST服务涉及太多步骤，未来添加新功能对于微软来说是个大问题。

  非WCF开发人员将无法创建WCF REST服务。Asp.Net Web API将会更简单，甚至一个非WCF开发者也能够使用它创建基于HTTP的服务。




##### 小示例

```CSharp
public class EmployeeController : ApiController{
  public Employee GET() {
    Employee e = new Employee() {
      FirstName = "Sukesh", LastName = "Marla", Salary = 25000
    };
    return e;
  }
}
```

调用的url：`http://localhost:13430/api/Employee`	(在浏览器直接输入url发起的请求总是GET)

返回的是xml

```xml
<Employee xmlns:i="http://www.w3.org/2001/XMLSchema-instance" 
          xmlns="http://schemas.datacontract.org/2004/07/WebApiTest.Models">
	<FirstName>Sukesh</FirstName>
  	<LastName>Marla</LastName>
  	<Salary>25000</Salary>
</Employee>
```

* 可以分别创建四个名为**GET，POST，PUT，DELETE**的方法，对应四种请求

* **如果我们想定义自定义方法呢？**

  实际中，我们总是希望创建多个Get / Post / Put / Delete方法。如 GetCustomers，GetCustomerById。在这种情况下，我们必须定义多个路由，或者我们必须更改默认路由。

  ```CSharp
  config.Routes.MapHttpRoute(
    name: "SecondRoute",
    routeTemplate: "api/GetCustomerById/{id} ",
    defaults: new {controller= "Customer", action= "GetCustomerById" }
  );
  config.Routes.MapHttpRoute(
    name: "ThridRoute",
    routeTemplate: "api/{controller}/{action}"
  );
  ```

* **为什么response返回的是xml？**

  Web API有一个非常好的功能，称为内容协商。客户可以协商输出内容。如果客户端请求XML，Web API将返回XML，如果客户端请求JSON，则Web API将返回JSON。

  在Chrome浏览器中获得xml，在IE浏览器中获得json

  ​

#####参数绑定

Web API的参数绑定和mvc不同！

一般情况下，Web API绑定参数符合如下规则：

- 如果参数为简单类型，Web API 尝试从URI中获取。简单参数类型包含.Net源生类型(int,bool,double...),加上TimeSpan,DateTime,Guid,decimal和string.加上任何包含string转化器的类型。（More about type converters later）
- 对复杂类型来说，Web API 试图从message body 中读取，使用[media-type](http://www.asp.net/web-api/overview/formats-and-model-binding/media-formatters) 类型。

典型Web API Controller方法的例子：`HttpResponseMessage Put(int id, Product item) { ... }`

从URI中获取值，WebAPI会从路由或者URI的查询参数中获取。路由如："api/{controller}/public/{category}/{id}"


* 在参数前添加**[FromUri]**属性可以强制Web API 从URI中读取复杂类型。
* 给参数添加**[FromBody]**属性可以迫使Web API从request body 中读取简单参数。
  * 当一个参数标记**[FromBody]**后Web API通过Content-Type header选择格式。如content type 是“application/json” （request body是原始的Json字符串，不是Json对象)。

  * 有而且只有一个参数允许从message body 中读取，因为request body 可能存储在一个只能读取一次的非缓冲的数据流中

  * ！且body带的参数不能有名字，如:

    ```js
    $.ajax({
      //contentType是application/x-www-form-urlencoded(默认)
      data:{"" : dataStr}
    });
    ```

    ```CSharp
    public HttpResponseMessage Post([FromBody] string dataStr){}
    ```

    也可以用dynamic接收

    ```js
    $.ajax({
      contentType: 'application/json',    //必须这样
      data: dataStr 	//必须这样
    });
    ```

    ```CSharp
    using Newtonsoft.Json.Linq;
    public HttpResponseMessage Post(dynamic data){
      //解析出复杂对象
      var myObject = ((JObject)data.myObject).ToObject<MyClass>();
    }
    ```

  * 在webApi中用post接收大量基础类型数据，包括数组

    ```CSharp
    public HttpResponseMessage PostXXX(dynamic data){
     int[] array = (data.array as JArray).ToObject<int[]>(); 
    }
    //数据只有数组时，用参数int[]直接接收就可以了,复杂类型的数组则用List<T>
    ```

#####WebApi中的NewtonSoft.Json

- 普通的dynamic对象，读取未存入的属性，会出现异常，而WebApi中通过post传参的到的dynamic对象则不会，大概是因为它是NewtonSoft.Json.Linq.JObject类型的

  ```CSharp
  dynamic data = new ExpandoObject();
  string str = data.str ?? "";	//异常：“System.Dynamic.ExpandoObject”未包含“str”的定义

  public HttpResponseMessage LockApply(dynamic data){ //data是NewtonSoft.Json.Linq.JObject类型
      string userCode = data.userCode ?? "";	//正常
  }
  ```

- 反序列化字符串

  ```CSharp
  //反序列化为匿名类型
  var data = new { his = new history_lock_ship_infor(), rec = new record_ship_gates(), 
                  userName = string.Empty, userCode = string.Empty };
  data = JsonConvert.DeserializeAnonymousType(dataStr, data);

  //反序列化为Object
  var object = JsonConvert.DeserializeObject(dataStr);
  ```

- 将dynamic转换为具体类型

  ```CSharp
  var user = ((JObject)dataStr).ToObject<vf_users>();
  ```

  ​

###Tip


* 在Action中，默认的，向get请求返回json是不允许的，这时需要使用

  `return Json(object, JsonRequestBehavior.AllowGet);`

* Json字符串，带引号的，用Content()返回可被浏览器解析为Json如

  ````CSharp
  return Content("{'key':'value'}");	//key可加可不加
  //但是在1.4之后的JQuery中，必须用双引号！
  return Content("{\"key\":\"value\"}");
  ````

* 后台调试输出使用`Debug.WriteLine()`

* Controller请求外网API并获取JSON

  ```CSharp
  //拼接要访问的api地址
  string url = "";
  //创建Request
  HttpWebRequest request = WebRequest.Create(url) as HttpWebRequest;
  request.Method = "get";
  string res = string.Empty;
  try{
    HttpWebResponse response = request.GetResponse() as HttpWebResponse;
    //WebResponse response = request.GetResponse();
    //Encoding.Default表示使用系统默认ANSI编码，解决中文乱码问题（StreamReader默认编码是UTF-8）
    StreamReader reader = new StreamReader(response.GetResponseStream(),Encoding.Default);
    res = reader.ReadToEnd();
  }catch (Exception e){
    res = "{error:'" + e.Message + "'}";
    throw e;
  }
  ```

* 对于DateTime类型，LINQ to Entities 不支持指定的类型成员“Date”。只支持初始值设定项、实体成员和实体导航属性。

* 获取web.config里的appSettings

  ```xml
  <appSettings>
  	<add key="key" value=""></add>
  </appSettings>
  ```

  ```CSharp
  string value = System.Configuration.ConfigurationManager.AppSettings["key"];
  ```

* 在cshtml中使用`@Session["key"]`得到的中文会乱码。对于js，可以先在html中用input的value存储该值，再用js读取input的value，可解决乱码，原因不明

* 在cshtml中，使用Razor表达式从`ViewData，ViewBag，Session`等拿到的中文字符串，直接放进js里时，会被转换成形如"&#28165;"的字符，这是html entity，
  可以通过将其先放进html页面里，再获取来将其转换回来。例如

  ```js
  @{string userCode = (Session["UserCode"] ?? string.Empty).ToString();}
  var userCode = $("<div/>").html("@userCode").text();

  //原生js
  var tmpDiv = document.createElement("div"); 
  tmpDiv.innerHTML = "@userCode"; 
  var userCode = tmpDiv.innerHTML

  ```

  ​


# ADO.NET

###简单介绍ADO.NET

​	简单的讲，**ADO.NET是一组允许.NET开发人员使用标准的，结构化的，甚至无连接的方式与数据交互的技术。**对于ADO.NET来说，可以处理数据源是多样的。可以是应用程序唯一使用的创建在内存中数据，也可以是与应用程序分离，存储在存储区域的数据（如文本文件、XML、关系数据库等）。

​      具体来说，ADO.NET 对 Microsoft SQL Server 和 XML 等数据源以及通过 OLE DB 和 XML 公开的数据源提供一致的访问。数据共享使用者应用程序可以使用 ADO.NET 来连接到这些数据源，并检索、处理和更新所包含的数据。

​      作为.NET框架的重要组成部分，ADO.NET 类封装在 System.Data.dll 中，并且与 System.Xml.dll 中的 XML 类集成。当编译使用 System.Data 命名空间的代码时，需要引用System.Data.dll 和 System.Xml.dll。

​	System.Data命名空间提供了不同的ADO.NET类，它们既分工明确，又相互协作地提供表格数据的访问服务。该类库包含两组重要的类：一组负责处理软件内部的实际数据（**DataSet**），一组负责与外部数据系统通信（**Data Provider**）

​	**DataSet 是 ADO.NET 的非连接（断开）结构的核心组件。**DataSet 的设计目的很明确：为了实现独立于任何数据源的数据访问。因此，ADO.NET结构可以用于多种不同的数据源，用于 XML 数据，或用于管理应用程序本地的数据。DataSet 包含一个或多个 DataTable 对象的集合，这些对象由数据行和数据列以及主键、外键、约束和有关 DataTable 对象中数据的关系信息组成。

ADO.NET 结构的另一个核心元素是 .NET 数据提供程序（**Data Provider**）。具体包括：

- **Connection** 对象提供与数据源的连接。
- **Command** 对象使您能够访问用于返回数据、修改数据、运行存储过程以及发送或检索参数信息的数据库命令。
- **DataReader** 对象从数据源中提供快速的，只读的数据流。
- **DataAdapter** 对象提供连接 DataSet 对象和数据源的桥梁。DataAdapter 使用 Command 对象在数据源中执行 SQL 命令，以便将数据加载到 DataSet 中，并使对 DataSet 中数据的更改与数据源保持一致。

**System.Data**：DataTable,DataSet,DataRow,DataColumn,DataRelation,Constraint

**System.Data.Common（各种数据访问类的基类和接口）**：DataColumnMapping,DataTableMapping

**System.Data.SqlClient（对Sql Server进行操作的数据访问类）**：

1. **SqlConnection**：数据库连接器
2. **SqlCommand**：数据库命名对象
3. **SqlCommandBuilder**：生存SQL命令
4. **SqlDataReader**：数据读取器
5. **SqlDataAdapter**：数据适配器，填充DataSet
6. **SqlParameter**：为存储过程定义参数。定义了命令和存储过程的输入、输出和返回值参数。
7. **SqlTransaction**：数据库事务



### 什么是.NET数据提供程序？

NET Framework数据提供程序**用于连接数据库、执行命令和检索结果。**这些结果将被直接处理，放置在 DataSet 中以便根据需要向用户公开、与多个源中的数据组合，或在层之间进行远程处理。.NET Framework 数据提供程序是轻量的，它在数据源和代码之间创建最小的分层，并在不降低功能性的情况下提高性能。

| NET数据提供程序             | 说明                                       |
| --------------------- | ---------------------------------------- |
| 用于 SQL Server 的数据提供程序 | 提供对 Microsoft SQL Server 7.0 或更高版本中数据的访问。使用 **System.Data.SqlClient** 命名空间。 |
| 用于 OLE DB 的数据提供程序     | 提供对使用 OLE DB 公开的数据源中数据的访问。使用 **System.Data.OleDb** 命名空间。 |
| 用于 ODBC 的数据提供程序       | 提供对使用 ODBC 公开的数据源中数据的访问。使用 **System.Data.Odbc** 命名空间。 |
| 用于 Oracle 的数据提供程序     | 适用于 Oracle 数据源。用于 Oracle 的 .NET Framework 数据提供程序支持 Oracle 客户端软件 8.1.7 和更高版本，并使用 **System.Data.OracleClient** 命名空间。 |
| EntityClient 提供程序     | 提供对实体数据模型 (EDM) 应用程序的数据访问。使用 **System.Data.EntityClient** 命名空间。 |



### 连接字符串

**连接字符串，就是一组被格式化的键值对：它告诉ADO.NET数据源在哪里，需要什么样的数据格式，提供什么样的访问信任级别以及其他任何包括连接的相关信息。**

**连接字符串由一组元素组成，一个元素包含一个键值对，元素之间由“;”分开**

```
key1=value1;key2=value2;key3=value3...
```

* **SQL Server连接字符串**

  1. 标准的安全连接

     ```
     Data Source=myServerAddress;Initial Catalog=myDataBase;
     User Id=myUsername;Password=myPassword;
     ```

     说明：

     **Data Source** ：需要连接的服务器。需要注意的是，如果使用的时Express版本的SQL Server需要在服务器名后加\SQLEXPRESS。例如，连接本地的SQL Server 2008 Express版本的数据库服务器，可以写成Data Source = (local)\SQLEXPRESS或者.\SQLEXPRESS。

     **Initial Catalog** ：默认使用的数据库名称。

     **User ID** ：数据库服务器账号。

     **Password** ：数据库服务器密码。

     或者也可以写成这样：

     ```
     Server=myServerAddress;Database=myDataBase;
     User ID=myUsername;Password=myPassword;Trusted_Connection=False;
     ```

  2. 可信连接

     ```
     Data Source=myServerAddress;Initial Catalog=myDataBase;Integrated Security=SSPI;
     ```

     说明：

     **Integrate Security** ：使用存在的windows安全证书访问数据库。可识别的值为true、false、yes、no以及与true等效的sspi。默认为false

     **SSPI** ：Microsoft安全支持提供器接口（SSPI）是定义得较全面的公用API，用来获得验证、信息完整性、信息隐私等集成安全服务，以及用于所有分布式应用程序协议的安全方面的服务。应用程序协议设计者能够利用该接口获得不同的安全性服务而不必修改协议本身。 

     或者也可以写成这样：

     ```
     Server=myServerAddress;Database=myDataBase;Trusted_Connection=True;
     ```

* #### Access连接字符串

  ```
  Provider=Microsoft.Jet.OLEDB.4.0;Data Source=C:\mydatabase.mdb;User Id=admin;Password=;
  ```

* #### MySQL连接字符串

  ```
  Server=myServerAddress;Database=myDataBase;Uid=myUsername;Pwd=myPassword;
  ```

* #### DB2连接字符串

  ```
  Server=myAddress:myPortNumber;Database=myDataBase;UID=myUsername;PWD=myPassword;
  ```

* #### Oracle连接字符串

  ```
  Data Source=TORCL;User Id=myUsername;Password=myPassword;
  ```



#####连接字符串中可用的选项

**Data Source/Server/Address/Addr/Network Address**：SQL Server实例的名称或网络地址。

Database/Initial Catalog**：数据库的名称。

**User ID**：用来登陆数据库的帐户名。

**Password/Pwd**：与帐户名相对应的密码。

**Application Name**：应用程序的名称。如果没有被指定的话，它的值为.NET SqlClient Data Provider（数据提供程序）。

**AttachDBFileName/Extended Properties（扩展属性）/Initial File Name（初始文件名）**： 可连接数据库的主要文件的名称，包括完整路径名称。数据库名称必须用关键字数据库指定。

**Connect Timeout/Connection Timeout**：连接超时，一个到服务器的连接在终止之前等待的时间长度（以秒计），缺省值为15。

**Connection Lifetime**：连接生存时间，当一个连接被返回到连接池时，它的创建时间会与当前时间进行对比。如果这个时间跨度超过了连接的有效期的话，连接就被取消。其缺省值为0。

**Connection Reset**： 连接重置，表示一个连接在从连接池中被移除时是否被重置。缺省值为真。

**Current Language**： SQL Server语言记录的名称。

**Encrypt**：加密，当值为真时，如果服务器安装了授权证书，SQL Server就会对所有在客户和服务器之间传输的数据使用SSL加密。被接受的值有true、false、yes和no。

**Enlist**：登记，表示连接池程序是否会自动登记创建线程的当前事务语境中的连接，其缺省值为真。

**Integrated Security/Trusted Connection**：表示Windows认证是否被用来连接数据库。它可以被设置成true、false、yes、no或者是和true对等的sspi，其缺省值为false。

**Pooling**：确定是否使用连接池。如果值为真的话，连接就要从适当的连接池中获得，或者，如果需要的话，连接将被创建，然后被加入合适的连接池中。其缺省值为真。

**Max Pool Size**： 连接池允许的连接数的最大值，其缺省值为100。

**Min Pool Size**： 连接池允许的连接数的最小值，其缺省值为0。

**Network Library/Net**：用来建立到一个SQL Server实例的连接的网络库。支持的值包括： dbnmpntw (Named Pipes)、dbmsrpcn (Multiprotocol／RPC)、dbmsvinn(Banyan Vines)、dbmsspxn (IPX／SPX)和dbmssocn (TCP／IP)。协议的动态链接库必须被安装到适当的连接，其缺省值为TCP／IP。

**Packet Size**：数据包大小，用来和数据库通信的网络数据包的大小。其缺省值为8192。

**Persist Security Info**：保持安全信息，用来确定一旦连接建立了以后安全信息是否可用。如果值为真的话，说明像用户名和密码这样对安全性比较敏感的数据可用，而如果值为伪则不可用。重置连接字符串将重新配置包括密码在内的所有连接字符串的值。其缺省值为伪。

**Workstation ID**：连接到SQL Server的工作站的名称。其缺省值为本地计算机的名称。



#####如何构造连接字符串

连接字符串本质上就是一个字符串，可以直接用`string`变量

ADO.NET有专门的类来处理连接字符串：`DbConnectionStringBuilder`，为**强类型连接字符串生成基类**。

```CSharp
//以SQL Server为例
SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder(){
  DataSource = @"(local)\SQLEXPRESS",
  InitialCatalog = "myDataBase",
  IntegratedSecurity = true
}
//新建连接，开启关闭
SqlConnection connection = new SqlConnection(builder.ToString())
connection.Open();
connection.Close();
```



##### 在配置文件中存储连接字符串

在我们实际开发中，我们一般不会把连接字符串直接写在代码中，而是存储在配置文件里。把连接字符串写死在代码中，不便于维护，每次修改字符串时，还得重新编译代码。以ASP.NET应用程序为例，我们一般把连接字符串写在web.config配置文件的<connectionstrings>节点

```xml
<connectionStrings>
    <add connectionString="Data Source=(LocalDb)\MSSQLLocalDB;
                           Initial Catalog=myDataBase;Integrated Security=True" 		
         name="connStr" providerName="System.Data.SqlClient" />
</connectionStrings>
```

我们只需要在程序中添加相应代码来获取配置文件中的值

```CSharp
string connStr = ConfigurationManager.ConnectionStrings["connStr"].ToString(); 
```



### Connection对象

**表示与特定数据源的连接**，不同的数据源，都对应着不同的Connection对象。都继承于DbConnection抽象类



##### 方法

* **Open: **使用 ConnectionString 所指定的设置打开数据库连接。
* **Dispose: **释放由 Component 使用的所有资源。（实际好像是将Connection对象的连接字符串置为null，该连接返回连接池，重新使用时再初始化连接字符串？）
* **Close: **关闭与数据库的连接。 **此方法是关闭任何已打开连接的首选方法。**Close 方法回滚任何挂起的事务。 然后，它将连接释放到连接池，或者在连接池被禁用的情况下关闭连接。



#####属性

* **Database: **在连接打开之后获取当前数据库的名称，或在连接打开之前获取连接字符串中指定的数据库名。
* **DataSource: **获取要连接的数据库服务器的名称。
* **ConnectionTimeOut: **获取在建立连接时终止尝试并生成错误之前所等待的时间。
* **ConnectionString: **获取或设置用于打开连接的字符串。
* **State: **获取描述连接状态的字符串。
  * **Closed: **连接处于关闭状态。
  * **Open: **连接处于打开状态。
  * **Connecting: **连接对象正在与数据源连接。
  * **Executing: **连接对象正在执行命令。
  * **Fetching: **连接对象正在检索数据。
  * **Broken: **与数据源的连接中断。

  ​

##### 注意用完要关闭连接和释放资源

1. 利用try/catch，在finally块中关闭连接和释放资源

2. 利用using语句,**using语句的作用是确保资源使用后，并很快释放它们**

   ```CSharp
   using(SqlConnection conn = new SqlConnection(connStr)){ //todo }
   ```





###连接池

连接池就是一个容器：它存放了一定数量的与数据库服务器的物理连接



##### 连接池工作原理

1. **创建连接池**

   **连接池是具有类别区分的。**也就是说，同一个时刻同一应用程序域可以有多个不同类型的连接池。那么，连接池是如何标识区分的？细致的讲，是由**进程、应用程序域、连接字符串以及windows标识**（在使用集成的安全性时）共同组成签名来标识区分的。但对于同一应用程序域来说，一般只由**连接字符串**来标识区分。当打开一条连接时，如果该条连接的类型签名与现有的连接池类型不匹配，则创建一个新的连接池。反之，则不创建新的连接池。

   即，创建具有相同的**连接字符串**的连接将会共享同一个连接池

2. **分配空闲连接**

   当用户创建连接请求或者说调用Connection对象的Open时，**连接池管理器首先需要根据连接请求的类型签名找到匹配类型的连接池，然后尽力分配一条空闲连接。**具体情况如下：

   - 如果池中有空闲连接可用，返回该连接。
   - 如果池中连接都已用完，创建一个新连接添加到池中。
   - 如果池中连接已达到最大连接数，请求进入等待队列直到有空闲连接可用。

3. **移除无效连接**

   无效连接，即不能正确连接到数据库服务器的连接。对于连接池来说，存储的与数据库服务器的连接的数量是有限的。因此，对于无效连接，如果如不及时移除，将会浪费连接池的空间。其实你不用担心，连接池管理器已经很好的为我们处理了这些问题。如果连接长时间空闲，或检测到与服务器的连接已断开，连接池管理器会将该连接从池中移除。

4. 回收使用完的连接

   **当我们使用完一条连接时，应当及时关闭或释放连接，以便连接可以返回池中重复利用。**我们可以通过Connection对象的Close或Dispose方法，也可以通过C#的using语句来关闭连接。



##### 连接字符串属性

**连接池的行为可以通过连接字符串来控制**，主要包括四个重要的属性：

- **Connection Timeout** ：连接请求等待超时时间。默认为15秒，单位为秒。
- **Max Pool Size** ：连接池中最大连接数。默认为100。
- **Min Pool Size** ：连接池中最小连接数。默认为0。
- **Pooling** ：是否启用连接池。**ADO.NET默认是启用连接池的** ，因此，你需要手动设置Pooling=false来禁用连接池。
- **Connection Lifetime**：连接生存时间，当一个连接被返回到连接池时，它的创建时间会与当前时间进行对比。如果这个时间跨度超过了连接的有效期的话，连接就被取消。其缺省值为0。值0可以保证连接有最大时限
- **Connection Reset**： 连接重置，表示一个连接在从连接池中被移除时是否被重置。缺省值为真。



#####连接池异常与处理方法

当用户打开一个连接而没有正确或者及时的关闭时，经常会引发“连接泄露”问题。泄露的连接，会一直保持打开状态，直到调用Dispose方法，垃圾回收器（GC）才关闭和释放连接。与ADO不同，ADO.NET需要手动的关闭使用完的连接。**一个重要的误区是：当连接对象超出局部作用域范围时，就会关闭连接** 。实际上，当超出作用域时，**释放的只是连接对象而非连接资源。使用完的连接应当尽快的正确的关闭和释放**



##### 高效使用连接池的基本原则

- 在最晚的时刻申请连接，在最早的时候释放连接。
- 关闭连接时先关闭相关用户定义的事务。
- 确保并维持连接池中至少有一个打开的连接。
- 尽力避免池碎片的产生。主要包括集成安全性产生的池碎片以及使用许多数据库产生的池碎片。
  - 池碎片是许多 Web 应用程序中的一个常见问题，应用程序可能会创建大量在进程退出后才会释放的池。 这样，将打开大量的连接，占用许多内存，从而导致性能降低。



###Command对象

**它封装了所有对外部数据源的操作（包括增、删、查、改等SQL语句与存储过程），并在执行完成后返回合适的结果。**都继承于DBCommand抽象类



##### 属性

* **CommandText** ：获取或设置对数据源执行的文本命令。默认值为空字符串。

* **CommandType** ：命令类型，指示或指定如何解释CommandText属性。CommandType属性的值是一个枚举类型，定义结构如下：

  ```CSharp
  public enum CommandType{
    Text = 1,    //SQL 文本命令。（默认。）          
    StoredProcedure = 4,    // 存储过程的名称。          
    TableDirect = 512    //表的名称。  
  }
  ```

  需要特别注意的是，**将CommandType 设置为 StoredProcedure 时，应将 CommandText 属性设置为存储过程的名称。** 当调用 Execute 方法之一时，该命令将执行此存储过程。

* **Connection** ：设置或获取与数据源的连接。

* **Parameters** ：绑定SQL语句或存储过程的参数。参数化查询中不可或缺的对象，非常重要。

* **Transaction** ：获取或设置在其中执行 .NET Framework 数据提供程序的 Command 对象的事务。



##### 方法

* **ExecuteNonQuery** ：执行不返回数据行的操作，并返回一个int类型的数据。

  **注意** ：对于 UPDATE、INSERT 和 DELETE 语句，返回值为该命令所影响的行数。 对于其他所有类型的语句，返回值 为 -1。

* **ExecuteReader** ：执行查询，并返回一个 DataReader 对象。

  注意这个方法的重载ExecuteReader(CommandBehavior)枚举，成员如下：
  * **Default**：此查询可能返回多个结果集。执行查询可能会影响数据库状态。当不设置CommandBehavior标志时默认为Default。
  * **SingleResult**：查询返回个结果集。
  * **SchemaOnly**：查询仅返回列信息。当使用SchemaOnly时，用于SQL Server的.NET Framework数据提供程序将在要执行的语句前加上SET FMTONLY ON。
  * **KeyInfo**：此查询返回列和主键信息。
  * **SingleRow**： 查询应返回一行。
  * **SequentialAccess**：提供一种方法，以便DataReader处理包含带有大量二进制值的列的行。SequentialAccess不是加载整行，而是使DataReader将数据作为流来加载。然后可以使用GetBytes或GetChars方法来指定开始读取操作的字节位置以及正在返回的数据的有限的缓冲区大小。
  * **CloseConnection**：在执行该命令时，如果关闭关联的DataReader对象，则关联的Connection对象也将关闭。

*  **ExecuteScalar** ：执行查询，并返回查询结果集中第一行的第一列（object类型）。如果找不到结果集中第一行的第一列，则返回 null 引用。

* **CreateParameter**：创建SqlParameter实例。 



**ExecuteReader**返回一个**DataReader**对象。

### DataReader对象

**DataReader是一个快速的，轻量级，只读的遍历访问每一行数据的数据流**。使用**DataReader**时，需要注意以下几点：

- **DataReader**一次遍历一行数据，并返回一个包含列名字集合。
- 第一次调用**Read()**方法获取第一行数据，并将游标指向下一行数据。当再次调用该方法时候，将读取下一行数据。
- 当检测到不再有数据行时，**Read()**方法将返回**false**。
- 通过**HasRows**属性，我们知道查询结果中是否有数据行。
- 当我们使用完**DataReader**时，一定要注意关闭。SQL Server默认只允许打开一个**DataReader**。



##### 方法

* **GetOrdinal("colName")**：获取指定列名的列序号(索引号)，使用这个方法可以把经常变动的列进行固定
* **GetName(index)**：  获取列名，参数为指定列名的序列号，返回string
* **IsDBNull**：判断当前读取的数据是否为Null，返回类型为Bool
* **NextResult**：当查询为批处理查询时，使用这个方法去读取下一个结果集，返回值为Bool，如果存在多个结果集，则为 true；否则为 false。
* **Read**：读取数据



##### 属性

* **HasRow**：判断是否包含一行或多行，也就是判断有没有数据，返回类型为Bool。
* **FieldCount**：获取读取的列数，返回类型为Int。
* **IsClosed**：判断读取的数据流是否关闭。



#####性能剖析

读取数据性能总结：

1. DataReader 索引+基于[序列号]->dr[0].ToString()	|Index-based access
  2. DataReader 索引+基于[列名]->dr["Name"].ToString()|性能最差
2. GetString 开头的+基于[序列号]->dr.GetString(0) |type-access
  4. GetSql 开头的+基于[序列号]->dr.GetSqlString(0)|Provider-specific typed accessor 
  5. GetOrdinal()通过列名获取这个列的序列号 |这个方法在提高性能上面有作用

性能（4）-->（3）-->（1）-->（2） 



* 用`reader["列名"]`获取字段很浪费性能，应该在循环外用`int c1 = reader.GetOrdinal("列名")`获取列序号，然后再在循环中用`reader[c1]`获取字段
* **SqlDataReader**是连接相关的，**SqlDataReader**中的查询结果并不是放在程序中，而是放在数据库服务器中，**SqlDataReader**只是相当于一个指针（游标），只能读取当前游标指向的行，连接断开就不能再读取。这样无论查询结果有多少条，对程序占用的内存都几乎没有影响。但**SqlDataReader**对于小数据量的数据来说带来的只有麻烦。 


### 异步执行命令

**异步执行的根本思想是，在执行命令操作时，无需等待命令操作完成，可以并发的处理其他操作。**ADO.NET提供了丰富的方法来处理异步操作，**BeginExecuteNonQuery**和**EndExcuteNonQuery**就是一对典型的为异步操作服务的方法。**BeginExecuteNonQuery**方法返回**System.IAsyncResult**接口对象。我们可以根据**IAsyncResult**的**IsCompleted**属性来轮询（检测）命令是否执行完成。

```CSharp
using (SqlConnection conn = new SqlConnection(connStr.ConnectionString)){
  conn.Open();
  SqlCommand cmd = new SqlCommand(strSQL.ToString(), conn);
  IAsyncResult pending = cmd.BeginExecuteNonQuery();//开始执行异步操作
  double time = 0;
  //检查异步处理状态
  while (pending.IsCompleted == false){
    System.Threading.Thread.Sleep(1);
    time++;
    Console.WriteLine("{0}s", time * 0.001);
  }
  if (pending.IsCompleted == true){
    Console.WriteLine("Data is inserted completely...\nTotal coast {0}s", time * 0.001);
  }
  cmd.EndExecuteNonQuery(pending);//结束异步操作
}
```



### Parameter对象（SqlParameter）

##### 属性

- **DbType:** 获取或设置参数的数据类型。
- **Direction:** 获取或设置一个值，该值指示参数是否只可输入、只可输出、双向还是存储过程返回值参数。
- **IsNullable:** 获取或设置一个值，该值指示参数是否可以为空。
- **ParamteterName:** 获取或设置DbParamter的名称。
- **Size:** 获取或设置列中数据的最大大小。
- **Value:** 获取或设置该参数的值。

##### 命令对象添加参数集合的几种方法

* **AddWithValue**
* **Add**
* **AddRange**

```CSharp
using (SqlConnection connection = new SqlConnection("")){
      SqlCommand command = connection.CreateCommand();
      command.CommandText = "";
 
      //可以使用这种方式添加多个参数，不过方式不够好
      command.Parameters.Add("@name", SqlDbType.NVarChar).Value = "yang"; //第一种方式
      command.Parameters.Add("@age", SqlDbType.Int).Value = 888;
      command.Parameters.Add("@address", SqlDbType.NVarChar, 100).Value = "Jiang Su";
 
      //这种方式直接给定参数名和参数就可以了，可操作性比较差
      command.Parameters.AddWithValue("@name", "yang");
      command.Parameters.AddWithValue("@age", 888).SqlDbType = SqlDbType.Int;
      command.Parameters.AddWithValue("@address", "Jiang su").SqlDbType = SqlDbType.NVarChar;
 
      //直接使用参数集合添加你需要的参数，推荐这种写法
      SqlParameter[] parameters = new SqlParameter[]{
          new SqlParameter("@name",SqlDbType.NVarChar,100){Value = "yang"},
          new SqlParameter("@age",SqlDbType.Int,2){Value = 888},
          new SqlParameter("@address",SqlDbType.NVarChar,20){Value = "Jiang Su"}, 
      };
      command.Parameters.AddRange(parameters);  //参数也可以是一个Array数组，如果采用数组参数代码的可读性和扩展性就不是那么好了
 
      //当我们把参数都添加好之后，会生成一个“SqlParameterCollection”集合类型，相当于参数的集合
      //那么我们就可以对这些参数进行修改和移除了
      //说穿了“SqlParameterCollection”内部其实是一个List<SqlParameter>的集合，只是它里面的复杂度比较高，考虑的很全面
      command.Parameters[0].Value = "hot girl";
      command.Parameters[0].Size = 200;
  } 
```

###DataAdapter

ADO.NET提供了基于非连接的核心组件：DataSet。DataSet组件让我们可以很愉快地在内存中操作以表为中心的数据集合，就好比操作数据库中的表一样。**它为外部数据源与本地DataSet集合架起了一座坚实的桥梁，将从外部数据源检索到的数据合理正确的调配到本地的DataSet集合中**

![img](http://images.cnblogs.com/cnblogs_com/liuhaorain/355410/r_DataAdapter.png)

从上图我们可以清楚的知道，当我查询Customer信息，**DataAdapter**首先将构造一个**SelectCommand**实例（本质就一个Command对象），然后检查是否打开连接，如果没有打开连接则打开连接，紧接着调用**DataReader**接口检索数据，最后根据维护的映射关系，将检索到得数据库填充到本地的**DataSet**或者**DataTable**中。同理，我们需要更新数据源时，**DataAdatper**则将本地修改的数据，跟据映射关系，构造**InsertCommand**，**UpdateCommnad**，**DeleteCommand**对象，然后执行相应的命令。

**DataAdapter**主要有三大功能：

- **数据检索：**尽可能用最简单的方法填充数据源到本地DataSet或者DataTable中。细致的说，DataAdapter用一个DataReader实例来检索数据，因此你必须提供一个Select查询语句以及一个连接字符串。
- **数据更新：**将本地修改的数据返回给外部的数据源相对来说稍微复杂一点。即使，从数据库查询数据时，我们仅仅只需要一条基本的Select语句，而更新数据库则需要区分Insert,Update,Delete语句。
- **表或列名映射：**维护本地DataSet表名和列名与外部数据源表名与列名的映射关系。



##### 重要成员

- **SelectComand属性：**获取或设置用于在数据源选择记录的命令。
- **UpdateCommand属性：**获取或设置用于更新数据源中的记录的命令。
- **DeleteCommand属性：**获取或设置用于从数据源中删除记录的命令。
- **InsertCommand属性：**获取或设置用于将新记录插入数据源中的命令。
- **Fill()：**填充数据集。
- **Update()：**更新数据源。

例子

```CSharp
using (SqlConnection conn = new SqlConnection(connStr)) {
  SqlDataAdapter ada = new SqlDataAdapter(selCmdStr, conn);
  				   //= new SqlDataAdapter(command);
  				   //= new SqlDataAdapter(selCmdStr, connStr);
  DataSet ds = new DataSet();
  ada.Fill(ds);
}
```



### 数据库

* 级联更新和删除：

  ```sql
  在外键上加上这两个约束，当主表更新或删除数据时，副表的数据也会更新
  foreign key (Username) references MyUser(Username) on delete cascade on update cascade
  ```

* sql切换一个属性的值（男变成女，女变成男之类的）

  ```sql
  update Table set sex = (case when sex=1 then 0 else 1 end) where Id = 1;
  ```

* insert/update/delete每次只能作用于一张表






# Entity Framework

 ## Tips

### DbContext.Database.SqlQuery<T>(string)

T中的属性必须与必须与查询出来的语句完全一致，否则会出现异常，除非带有[NotMapped]注解，但是此时该属性即使有查到也不会被赋值

###DbSet

* 在**DbContext**中，可以事先设定各个实体的**DbSet**，也可以通过**DbContext.Set<TEntity>()**动态设置,而且,如果事先已经设有该实体的**DbSet**属性,将会得到该属性而不会创建重复的!

### Linq to Entities的数字与字符串的转换

- 在Linq to Entities，数字与字符串无法直接比较，而`ToString()`方法也无法调用
- 对于**SqlServer**，可以使用`System.Data.Entity.SqlServer` 命名空间中的`SqlFunctions.StringConvert(decimal?/double?).Trim()`，`Trim()`是去除前后空格
- 但是MySql无法使用上述方法，但是也可以用`myInt + ""`来是数字变成字符串。。。。

### EF直接更新的方法

Entry将会强制更新所有属性，性能会比较差

```CSharp
using(var dbContext = new MyDbContext()){
    //更新所有属性
    var user = new User(){ id = 1, name = "John", age = 17};	//根据id来修改的
    dbContext.Entry<User>(user).State = System.Data.Entity.EntityState.Modified;
    
    //更新单个属性
    dbContext.Users.Attach(user);
    dbContext.Entry<User>(user).Property(u => u.age).IsModified = true;
    
    //更新单个属性的另一种方式
    //System.Data.Entity.Infrastructure.IObjectContextAdapter
    var stateEntry =					((IObjectContextAdapter)dbContext).ObjectContext.ObjectStateManager.GetObjectStateEntry(user);
    stateEntry.SetModifiedProperty("name");
    
    db.SaveChanges();
}
```




# 留言板系统

使用asp.net mvc制作了一个留言板系统。注册用户可以进行留言，然后管理员对用户及留言进行回复和管理，留言分为公开和非公开，游客可以看到和搜索公开留言，用户可以删除和修改自己的非公开留言。



## 第一版

仅使用asp.net mvc，数据库连接使用ado.net



### 数据库

使用SQL Server13.0.1601（LocalDB）



#### 用户表MyUser

因为User在SQL Server中是关键字，若要用User表，则要写成`[User]`，这里采用MyUser。

| 列名       | 类型           | 约束                     | 备注   |
| -------- | ------------ | ---------------------- | ---- |
| Id       | INT          | IDENTITY(自增), NOT NULL | 自增Id |
| Username | NVARCHAR(20) | UNIQUE, NOT NULL       | 用户名  |
| Password | NVARCHAR(20) | NOT NULL               | 密码   |
| PhoneNum | NVARCHAR(20) | NOT NULL               | 手机号码 |
| SignDate | DATE         | NOT NULL               | 注册   |
| NewReply | INT          | NOT NULL(0)            | 新回复数 |

T-SQL

```sql
CREATE TABLE [dbo].[MyUser] (
    [Id]       INT 				IDENTITY (1, 1) NOT NULL,
  	/*SQL Server中，中文要使用NVARCAHR(可变长度 Unicode 字符数据)*/
    [Username] NVARCHAR (20) 	NOT NULL,	
    [Password] NVARCHAR (20) 	NOT NULL,
    [PhoneNum] NVARCHAR (20) 	NOT NULL,
    [SignDate] DATE          	NOT NULL,
    [NewReply] INT           	DEFAULT ((0)) NOT NULL,
    PRIMARY KEY CLUSTERED ([Id] ASC),		/*主键*/
    UNIQUE NONCLUSTERED ([Username] ASC)	/*唯一约束*/
);
```



#### 管理员表Admin

| 列名        | 类型           | 约束                     | 备注   |
| --------- | ------------ | ---------------------- | ---- |
| Id        | INT          | IDENTITY(自增), NOT NULL | 自增Id |
| AdminName | NVARCHAR(20) | UNIQUE, NOT NULL       | 管理员名 |
| Password  | NVARCHAR(20) | NOT NULL               | 密码   |

T-SQL

```sql
CREATE TABLE [dbo].[Admin] (
    [Id]        INT 			IDENTITY (1, 1) NOT NULL,
    [AdminName] NVARCHAR (20) 	NOT NULL,
    [Password]  NVARCHAR (20) 	NOT NULL,
    PRIMARY KEY CLUSTERED ([Id] ASC),		/*主键*/
    UNIQUE NONCLUSTERED ([AdminName] ASC)	/*唯一约束*/
);
```



#### 留言表Message

| 列名       | 类型            | 约束                            | 备注   |
| -------- | ------------- | ----------------------------- | ---- |
| Id       | INT           | IDENTITY(自增), NOT NULL        | 自增Id |
| Username | NVARCHAR(20)  | NOT NULL, 外键(MyUser.Username) | 用户名  |
| Title    | NVARCHAR(50)  | NOT NULL                      | 标题   |
| Content  | NVARCHAR(500) | NOT NULL                      | 正文   |
| IsPublic | BIT           | NOT NULL(0)                   | 是否公开 |
| DateTime | DATETIME      | NOT NULL                      | 发表日期 |
| ReplyNum | INT           | NOT NULL(0)                   | 回复数  |
| NewReply | INT           | NOT NULL(0)                   | 新回复数 |

T-SQL

```sql
CREATE TABLE [dbo].[Message] (
    [Id]       INT            IDENTITY (1, 1) NOT NULL,
    [Username] NVARCHAR (20)  NOT NULL,
    [Title]    NVARCHAR (50)  NOT NULL,
    [Content]  NVARCHAR (500) NOT NULL,
    [IsPublic] BIT            DEFAULT ((0)) NOT NULL,
    [DateTime] DATETIME       NOT NULL,
    [ReplyNum] INT            DEFAULT ((0)) NOT NULL,
    [NewReply] INT            DEFAULT ((0)) NOT NULL,
    PRIMARY KEY CLUSTERED ([Id] ASC),	/*主键*/
  	/*外键，级联删除和级联更新*/
    FOREIGN KEY ([Username]) REFERENCES [dbo].[MyUser] ([Username]) ON DELETE CASCADE ON UPDATE CASCADE
);
GO

/*触发器，在Message删除时，在对应的MyUser条目中减去新回复数NewReply*/
CREATE TRIGGER tgr_message_delete
ON Message
AFTER DELETE
AS
BEGIN
UPDATE MyUser SET MyUser.NewReply = MyUser.NewReply - deleted.NewReply
FROM MyUser, deleted 
WHERE MyUser.Username = deleted.Username;
END
```



#### 回复表Reply

| 列名        | 类型            | 约束                            | 备注   |
| --------- | ------------- | ----------------------------- | ---- |
| Id        | INT           | IDENTITY(自增), NOT NULL        | 自增Id |
| MessageId | INT           | NOT NULL, 外键(Message.Id)      | 留言Id |
| AdminName | NVARCHAR(20)  | NOT NULL, 外键(Admin.AdminName) | 管理员名 |
| Content   | NVARCHAR(500) | NOT NULL                      | 回复正文 |
| DateTime  | DATETIME      | NOT NULL                      | 回复日期 |

T-SQL

```sql
CREATE TABLE [dbo].[Reply] (
    [Id]        INT            IDENTITY (1, 1) NOT NULL,
    [MessageId] INT            NOT NULL,
    [AdminName] NVARCHAR (20)  NOT NULL,
    [Content]   NVARCHAR (500) NOT NULL,
    [DateTime]  DATETIME       NOT NULL,
    PRIMARY KEY CLUSTERED ([Id] ASC),	/*主键*/
  	/*外键，级联删除和级联更新*/
    FOREIGN KEY ([MessageId]) REFERENCES [dbo].[Message] ([Id]) ON DELETE CASCADE ON UPDATE CASCADE,
  	/*外键，级联删除和级联更新*/
    FOREIGN KEY ([AdminName]) REFERENCES [dbo].[Admin] ([AdminName]) ON DELETE CASCADE ON UPDATE CASCADE
);
GO

/*触发器，当插入Reply时，增加MyUser和Message的NewReply,Message的ReplyNum*/
CREATE TRIGGER tgr_reply_insert
ON Reply
AFTER INSERT
AS
BEGIN
UPDATE Message SET ReplyNum = ReplyNum + 1, NewReply = NewReply + 1 
FROM Message, inserted 
WHERE Message.Id = inserted.MessageId;

UPDATE MyUser SET NewReply = NewReply + 1
WHERE Username = (SELECT Message.Username FROM Message, inserted WHERE Message.Id = inserted.MessageId)
END
```



#### Tip

* User在SQL Server中是关键字，若要用User表，则要写成`[User]`，最好采用别的用户表名

* `IDENTITY(1,1)`，用于Id，表示从1开始自增，每次自增1

* **VARCHAR(n)与NVARCHAR(n)**

  * **VARCHAR(n)**
    长度为 n 个字节的可变长度且非 Unicode 的字符数据。n 必须是一个介于 1 和 8,000 之间的数值。存储大小为输入数据的字节的实际长度，而不是 n 个字节。

  * **NVARCHAR(n)**

    包含 n 个字符的可变长度 Unicode 字符数据。n 的值必须介于 1 与 4,000 之间。字节的存储大小是所输入字符个数的两倍。

  * **VARCHAR(n)**中，英文字母占1个字节，汉字占2个字节，**NVARCHAR(n)**中则全占2个字节

* SQL Server中，中文要使用NVARCAHR(可变长度 Unicode 字符数据)，自己拼接字符串查询时，要在中文字符串前加上'N'，将字符串转为Unicode字符，如

  ```sql
  SELECT * FROM MyUser WHERE Username LIKE N'%郑%'
  ```

* SQL中的通配符

  * **%**：0到多个字符
  * **_**：一个字符
  * **[charlist]**：字符列表中的任何单一字符
  * **[^charlist]/[!charlist]**：非字符列表中的任何单一字符，SQL Server不支持'!'

* SQL Server中的**DATE/DATETIME**类型都对应C#中的**DateTime**，只是**DATE**的时分秒部分都为0

* SQL Server中的**Bit**类型，对应的是C#中的**bool**，0对应false

* 数据库中，主键和非空的Unique都可以作为别的表的外键

* 级联更新和删除：

  ```sql
  在外键上加上这两个约束，当主表更新或删除数据时，副表的数据也会更新
  foreign key (Username) references MyUser(Username) on delete cascade on update cascade
  ```

* sql切换一个属性的值（男变成女，女变成男之类的）

  ```sql
  update Table set sex = (case when sex=1 then 0 else 1 end) where Id = 1;
  ```

* insert/update/delete每次只能作用于一张表



### ADO.NET

开启一个事务示例：

```CSharp
var conn = GetConnection();
conn.Open();
//开启一个事务
SqlTransaction trans = conn.BeginTransaction();
var cmd = new SqlCommand();
cmd.Connection = conn;
cmd.Transaction = trans;
try{
  cmd.CommandText="...";
  cmd.ExecuteNonQuery();
  ...
  cmd.CommandText="...";
  cmd.ExecuteNonQuery();
  ...
  //提交事务
  trans.Commit();
}catch(Exception){
  //回滚事务
  trans.RollBack();
  throw;
}finally{
  conn.Close()
}
```



## 第二版

在第一版的基础上，持久层使用iBatisNet，IoC框架使用Unity，数据展示使用easyui-datagrid

iBatisNet与Unity结合，将数据库操作分为多层：

* 最底层，iBatis的对应每个Model的配置文件，sql语句就写在其中
* Dao层，通过传入Model对象和ISqlMapper，调用最底层的sql语句
* Service层，调用Dao，被Controller调用

Dao层和Service层都实现于接口，然后使用Unity进行依赖注入

### iBatisNet和Unity需要导入的DLL

* IBatisNet.Common.dll
* IBatisNet.Common.Logging.Log4Net.dll
* IBatisNet.DataMapper.dll


* Microsoft.Practices.EnterpriseLibrary.Common.dll
* Microsoft.Practices.ObjectBuilder2.dll
* Microsoft.Practices.Unity.Configuration.dll
* Microsoft.Practices.Unity.dll
* MvcContrib.Unity.dll



# IBatis

## 配置文件

iBatis含有三种配置文件：

* **providers.config**：指定数据库提供者，.Net版本等信息。名字可以更改
* **xxx.xml**：映射规则。
* **SqlMap.config**：大部分配置一般都在这里，如数据库连接等等。名字不可改



### SqlMap.config

示例

 ```xml
<?xml version="1.0" encoding="utf-8"?>
<sqlMapConfig 
  xmlns="http://ibatis.apache.org/dataMapper" 
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<properties resource="../../../Files/properties.config"/>
  
<settings>
  <!--是否使用Statement命名空间:
        true:引用xml对象，必须加上命名空间
        false:则直接通过方法名引用就行了，但注意保存保证方法名全局唯一。 -->
    <setting useStatementNamespaces="true"/>
    <!--是否启用sqlmap上的缓存机制-->
    <setting cacheModelsEnabled="true" />
    <setting validateSqlMap="false" />
</settings>
  
<providers resource="Setting/ORM/Providers.config"/>
<!-- Database connection information -->
<database>
  <provider name="sqlServer2.0"/>
  <dataSource  name="iBatisInAction"  connectionString="Data Source=(LocalDb)\MSSQLLocalDB;Initial Catalog=MessageBoard;Integrated Security=True" />
</database>

<sqlMaps>
  <sqlMap resource="Setting/ORM/Maps/MyUser.xml" />
  ...
</sqlMaps>

</sqlMapConfig>
 ```

1. **properties节点**

   **properties**节点通常用于引入在外部定义一些键值对配置文件，以方便在后面统一调用，这样修改的时候，只修改就可以了。它的引入方式有3种：

   * **resource**: 通过相对路径来确定文件的位置。
   * **url**：通过绝对路径来确定文件位置。
   * **embedded**:通过嵌入资源方式来确定文件位置。
     * `<sqlMap embedded = "命名空间.文件名.后缀名, 命名空间"/>`

2. **setting节点**

   Settings节点里，可以配置以下5个信息：

   * **useStatementNamespaces**：默认false,是否使用全局完整命名空间。最好启用	

   - **cacheModelsEnabled** :默认true,是否启用缓存。
   - **validateSqlMap**:默认false,使用启用SqlMapConfig.xsd来验证映射XML文件。
   - **useReflectionOptimizer**:默认true,是否使用反射机制访问C#中对象的属性。
   - **useEmbedStatementParams** 是否使用嵌入的方式声明可变参数

3. **provider**

4. **database**

5. **SqlMaps**



### XXX.xml映射文件

示例

```xml
<?xml version="1.0" encoding="utf-8" ?>
<sqlMap namespace="Ibatis" xmlns="http://ibatis.apache.org/mapping" xmlns:xls="http://www.w3.org/2001/XMLSchema-instance">
<alias>
  <!--已在别的映射文件中定义了别名，就不用重复定义，不能与别的命名空间使用相同的别名-->
    <typeAlias alias="Message" type="MessageBoard2.Models.Message,MessageBoard2" />
</alias>
  
<!--缓存模型-->
<cacheModels>
  <cacheModel id="MessageCache" implementation="MEMORY">
    <flushInterval hours="24"/>
    <flushOnExecute statement="Message.AddMessage"/>
    ...
    <property name="Type" value="WEAK"/>
  </cacheModel>
</cacheModels>
  
<!--映射-->
<resultMaps>
  <resultMap id="Message" Class="Message">   <!--id会被statements节点所用，Class实体类所在位置-->
    <result property="Id" column="Id"/>  <!--property实体类的属性名，column对应的列名-->
    <result property="Title" column="Title"/>
    <!--一对多关系，从这个statement中得到回复，column是传递过去的参数，Id-->
    <result property="Replies" column="Id" select="Message.GetReplies"/>
  </resultMap>
</resultMaps>
  
<statements>
  <!--添加-->
  <insert id="AddMessage" parameterClass="Message">
    INSERT INTO Message(Username,Title,Content,DateTime)
    VALUES(#Username#,#Title#,#Content#,#DateTime#)
    <!--插入数据之后，model对象的主属性得到自增主键的新值-->
    <selectKey resultClass="int" type="post" property="Id">
      SELECT @@IDENTITY AS value
    </selectKey>
  </insert>
  
  <!--获取留言，包括对应的回复-->
  <!--id在程序中会被SqlMapper实体类所调用,resultMap就是resultMap节点的id-->
  <select id="GetMessage" parameterClass="Message" resultMap="MessageResult" cacheModel="MessageCache">
    SELECT Message.* FROM Message WHERE Message.Id = #Id# ;
  </select>
  <!--取得留言对应的回复-->
  <select id="GetReplies" parameterClass="int" resultClass="MessageBoard2.Models.Reply" cacheModel="MessageCache">
    SELECT * FROM Reply WHERE MessageId=#value#
  </select>
  
</sqlMap>
```

#### resultMaps

定义了数据库字段名与实体类属性名之间的关系。当然，如果你数据库字段名与实体类属性名完全一样，那么resultMaps部分是可以省略的。**另外要注意一点，ResultMap的列比你查询的列不能少，也不能多。它不会说，ResultMap里映射的列多了，该属性就自动将select返回的列自动置null，而是直接所有列都不映射赋值。也就是说，Person表有Id，Name，Age3列，如果你只想要SELECT两个列(Id,Name)，那么ResultMap里面的3列映射没用了，你必须另外搞一个ResulpMap只映射两列的**



#### statements

用于定义你需要执行的语句，在程序中通过select的id调用。

1. **parameterMap**：参数映射，需结合**parameterMap**节点对映射关系加以定义，对于存储过程之外的statement而言，建议使用**parameterClass**作为参数配置方式，一方面避免了参数映射配置工作，另一方面其性能表现更加出色

2. **parameterClass**：参数类。指定了参数类型的完整类名（包括命名空间），可以用别名，对于简单类型，应用应该使用#value#

3. **resultMap**：结果映射，需结合resultMap节点对映射关系加以定义

4. **resultClass**：结果类。指定了结果类型的完整类名（包括命名空间），可以用别名

5. **cacheModel**：**Statement**对应的Cache模块

6. **extends**：重复使用SQL子句

   ```xml
   <select id="SelectAllCustomers" resultMap="Customer">
      SELECT * FROM Customers
   </select>

   <select id="SelectAllCustomerOrderByCustomerID"
           resultMap="Customer" extends="SelectAllCustomers">
      ORDER BY CustomerID
   </select>
   ```

* **parameterMap的属性**（不推荐）：

  它可以接受三个属性，**id/class/extends**，其中是有**id**是必须的，**class**用于声明使用的实体类名称，可以是别名，也可以是全名
  在它下一级节点中应该包含若干个**parameter**元素，来指定对象属性与当前变量的映射规则，**parameter**有如下常用属性：

  1. **property**：指定类中的一个属性
  2. **column**:定义的参数名称
  3. **direction**:用于声明存储过程的参数方向（input,output,inputoutput）
  4. **dbType**：用于指定property映射到数据库中的数据类型
  5. **type**：用于为参数的对象指定CLR类型
  6. **nullValue**：指定在property为何值时，将会在存储数据时候，替换为null，这是经常会被用到的
  7. **size**:用于指定最大值

* **resultMap**的属性：

  它的属性很多是和**parameterMap**相对应的，但是值得一提的是**它下面可以添加一个constructor元素来匹配一个构造函数**。当然，这个的前提是Customers类中有这样一个构造函数。例如：

  ```xml
  <resultMaps>
    <resultMap id="Customer" class="Customers">
      <constructor>
        <argument argumentName="PersonId" column="PersonID"/>
        <argument argumentName="PersonName" column="PersonName"/>
  　　</constructor>
  　　<result property="PersonId" column="PersonID"/>
  　　<result property="PersonName" column="PersonName"/>
    </resultMap>
  </resultMaps>
  ```

* **存储过程**

  这里有一点区别就是,只可以使用**parameterMap**，而不可以使用**parameterClass** 。还有一点，就是他的参数完全是按照 **parameterMap**中的定义自动匹配的。

  ```xml
  <procedure id="demoProcedure" parameterMap="procedureDemo">
  　　CustOrderHist
  </procedure>
  ```

* **对SQL片段的引用**

  ```xml
  <sql id="order">
  　　ORDER BY PersonID
  </sql>

  <select id="SelectAllCustomerOrderByCustomerID" resultMap="Customer">
  　　SELECT * FROM Person
  　　<include refid="test"/>
  </select>
  ```

* $和#的区别是：\$value\$是直接拼接字符串，#value#是传入值

* 可以一次使用多条sql语句，用begin/end包起来？



### provider.config

注意要将要使用的provider的enable属性改为true





## SqlMapper类

**Ibatis**中，加载、分析配置以及映射文件是在创建**SqlMapper**实例的时候进行的，另外对数据库的操作，也是在**SqlMapper**实例上调用方法来完成。在**IBatis**外部的程序中，创建**SqlMapper**的实例的方式是：

```CSharp
ISqlMapper mapper = Mapper.Instance();
```

在第一次调用`Mapper.Instance()`的时候，由**DomSqlMapBuilder**对象解析**SqlMap.config**(默认路径和命名)文件来创建**SqlMapper**实例，然后会缓存该**mapper**对象，如果程序运行过程中，修改了映射文件，那么再调用`Mapper.Instance()`创建**SqlMapper**实例时，**SqlMapper**会被重新加载创建。相当于一个文件缓存依赖，这个文件缓存依赖由`DomSqlMapBuilder.ConfigureAndWatch()`方法来实现。

如果你不希望修改了配置文件就重新加载，可以通过这样来创建实例

```CSharp
ISqlMapper mapper = builder.Configure();
```

**IBatis.net**的这个东西有个地方不好，默认是使用**HttpContext**作为xxx容器的。

当非**Web**请求线程调用时，如**Timer**调用时会报如下错误:

**ibatis.net：WebSessionStore: Could not obtain reference to HttpContext**

这个问题可以在创建**SQLMapper**的时候指定

```CSharp
SqlMapper.SessionStore = new HybridWebThreadSessionStore(sqlMapper.Id);

public void InitMapper(string sqlMapperPath)
{
　　ConfigureHandler handler = new ConfigureHandler(Configure);	//Configure是一个方法
　　DomSqlMapBuilder builder = new DomSqlMapBuilder();
　　_mapper = builder.ConfigureAndWatch(sqlMapperPath, handler);
　　_mapper.SessionStore = new
    	IBatisNet.DataMapper.SessionStore.HybridWebThreadSessionStore(_mapper.Id);
}
```



在留言板项目中，用了一个类来获取SqlMapper实例，如下：

```CSharp
//实现接口，用来进行依赖注入
public class Mapper : IMapper{
  private static volatile ISqlMapper _mapper = null;
  public ISqlMapper Instance {
    get {
      //如果_mapper为空，实例化
      if(_mapper == null) {
        lock (typeof(SqlMapper)) {
          //double check
          if(_mapper == null) {
            InitMapper("Setting/ORM/SqlMap.config");
          }
        }
      }
      return _mapper;
    }
  }
  
  public void InitMapper(string sqlMapperPath) {
    ConfigureHandler handler = new ConfigureHandler(Configure);
    DomSqlMapBuilder builder = new DomSqlMapBuilder();
    //设置文件缓存依赖，每当sqlMapperPath指向的配置文件被修改，就调用handler，即Configure方法将实例置为null，再重新获取
    _mapper = builder.ConfigureAndWatch(sqlMapperPath, handler);
    _mapper.SessionStore = new 	
      	IBatisNet.DataMapper.SessionStore.HybridWebThreadSessionStore(_mapper.Id);
  }
  
  protected void Configure(object obj) {
    _mapper = null;
  }
}
```

### SqlMapper的数据库操作方法

1. **查询select**

   * **QueryForList：返回List<T>强类型数据集合**

   ```CSharp
   IList<T> QueryForList<T>(string statementName, object parameterObject);
   IList QueryForList(string statementName, object parameterObject);
   void QueryForList<T>(string statementName, object parameterObject, IList<T> resultObject);
   void QueryForList(string statementName, object parameterObject, IList resultObject);
   IList<T> QueryForList<T>(string statementName, object parameterObject, int skipResults,int maxResults);
   IList QueryForList(string statementName, object parameterObject, int skipResults, int maxResults);
   ```

   ​	！参数**skipResults**，表示从结果行掉过**skipResults**行后返回，**maxResults**表示返回的行数。这个在	分页中应该会用到。

   * **QueryForObject：返回一行数据对应程序的实体类实例**

   ```CSharp
   object QueryForObject(string statementName, object parameterObject);
   T QueryForObject<T>(string statementName, object parameterObject);
   T QueryForObject<T>(string statementName, object parameterObject, T instanceObject);
   object QueryForObject(string statementName, object parameterObject, object resultObject)
   ```

   * **QueryWithRowDelegate：通过委托过滤返回的数据**

   ```CSharp
   IList<T> QueryWithRowDelegate<T>(string statementName, object parameterObject, 
                                    RowDelegate<T> rowDelegate);
   IList QueryWithRowDelegate(string statementName, object parameterObject, 
                              RowDelegate rowDelegate);
   ```

   * **QueryForDictionary**
   * **QueryForMap**

2. **insert**

3. **update**

4. **delete**

## SQL语句

### 一对多关系(嵌套类)

```xml
<!--结果映射-->
<resultMap id="MessageResult" Class="Message">
  <result property="Id" column="Id"/>
  ...
  <!--从这个statement中得到回复，column是传递过去的参数，Id-->
  <result property="Replies" column="Id" select="Message.GetReplies"/>
</resultMap>

<!--获取留言，包括对应的回复-->
<select id="GetMessage" parameterClass="Message" resultMap="MessageResult">
      SELECT * FROM Message WHERE Id = #Id# ;
</select>
<!--取得留言对应的回复-->
<select id="GetReplies" parameterClass="int" resultClass="MessageBoard2.Models.Reply">
  SELECT * FROM Reply WHERE MessageId=#value#
</select>
```



### 动态语句

1. **传递单个参数**，直接用基元类型就可以了，调用如：#id#
2. **传递多个参数**：传递多个参数通常使用键值对（如HashTable）或实体类。
   * **parameterClass**是实体类时，如果只用到其中一个属性，调用时可以只传入一个基元类型

#### 动态查询

当满足一定的条件，才拼接某一段SQL代码。如：

```xml
<select id="SelectPersonById" resultMap="Person" parameterClass="Hashtable" >
  SELECT TOP 1 * FROM Person WHERE 1=1
  <dynamic prepend="AND">
    <isLessEqual prepend="AND" property="Id" compareValue="3">  <!--当Id小于3时，才拼接该子句-->
      Id = #Id#
    </isLessEqual>
    <isNotEmpty prepend="AND" property="Name">  <!--当Name不为""或Null时，才拼接该子句-->
      Name = #Name#
    </isNotEmpty>
  </dynamic>
</select>
```

条件如下：

| 属性关键字                    | 含义                 |
| ------------------------ | ------------------ |
| <isEqual>                | 如果参数相等于值则查询条件有效。   |
| <isNotEqual>             | 如果参数不等于值则查询条件有效。   |
| <isGreaterThan>          | 如果参数大于值则查询条件有效。    |
| <isGreaterEqual>         | 如果参数大于等于值则查询条件有效。  |
| <isLessEqual>            | 如果参数小于值则查询条件有效。    |
| <isPropertyAvailable>    | 如果参数中有此属性则查询条件有效。  |
| <isNotPropertyAvailable> | 如果参数中没有此属性则查询条件有效。 |
| <isNull>                 | 如果参数为NULL则查询条件有效。  |
| <isNotNull>              | 如果参数不为NULL则查询条件有效。 |
| <isEmpty>                | 如果参数为空则查询条件有效。     |
| <isNotEmpty>             | 如果参数不为空则查询条件有效。    |
| <isParameterPresent>     | 如果存在参数对象则查询条件有效。   |
| <isNotParameterPresent>  | 如果不存在参数对象则查询条件有效。  |

属性说明：

- **perpend**可被覆盖的SQL语句组成部分，添加在语句的前面，该属性为可选。
- **property** ：是比较的属性，该属性为必选。
- **compareProperty** ：另一个用于和前者比较的属性(必选或选择compareValue属性)
- **compareValue** ：用于比较的值(必选或选择compareProperty属性)



还有一个比较特别的判断条件**iterate**。这个东西用于循环生成多个SQL片段。

```xml
<select id="SelectPersonById" resultMap="Person" parameterClass="Hashtable" >
  SELECT * FROM Person
  <dynamic prepend="WHERE">
    <!--遍历NameList参数，对于每一个生成一个Name in NameList[i]-->
    <isNotNull prepend="And" property="NameList">
          Name in
      <iterate property="NameList" open="(" close=")" conjunction=",">
        #NameList[]#
      </iterate>
    </isNotNull>
  </dynamic>
</select>
```

**Iterate**的属性：

- **prepend**——可被覆盖的SQL语句组成部分，添加在语句的前面，该属性为可选。
- **property**——类型为List的用于遍历的元素属性，该属性为必选。
- **open**——整个遍历内容体开始的字符串，用于定义括号，该属性为可选。
- **close** ——整个遍历内容体结束的字符串，用于定义括号，该属性为可选。
- **conjunction**——每次遍历内容之间的字符串，用于定义AND或OR，该属性为可选。



#### 其它

- **对SQL片段的引用**

  ```xml
  <sql id="order">
  　　ORDER BY PersonID
  </sql>

  <select id="SelectAllCustomerOrderByCustomerID" resultMap="Customer">
  　　SELECT * FROM Person
  　　<include refid="test"/>
  </select>
  ```

- $和#的区别是：\$value\$是直接拼接字符串，#value#是传入值

- 可以一次使用多条sql语句，用begin/end包起来？



## 输出SQL语句

### 输出到控制台

在web.config进行配置

```xml
<configSections>
  <!-- 输出IBatis.net执行的SQL语句到控制台 -->
  <sectionGroup name="iBATIS">
    <section name="logging" 
             type="IBatisNet.Common.Logging.ConfigurationSectionHandler, IBatisNet.Common" />
  </sectionGroup>
</configSections>
<iBATIS>
  <logging>
    <logFactoryAdapter type="IBatisNet.Common.Logging.Impl.TraceLoggerFA, IBatisNet.Common">
      <arg key="showLogName" value="true" />
      <arg key="showDataTime" value="true" />
      <arg key="level" value="ALL" />
      <arg key="dateTimeFormat" value="yyyy/MM/dd HH:mm:ss:SSS" />
    </logFactoryAdapter>
  </logging>
</iBATIS>
```



### 输出到文件

利用log4net，在web.config进行配置

```xml
<configSections>
  <!-- 输出IBatis.net执行的SQL语句 -->
  <sectionGroup name="iBATIS">
    <section name="logging" 
             type="IBatisNet.Common.Logging.ConfigurationSectionHandler, IBatisNet.Common" />
  </sectionGroup>
  <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
</configSections>
<iBATIS>
  <logging>
    <logFactoryAdapter 
      	type="IBatisNet.Common.Logging.Impl.Log4NetLoggerFA, IBatisNet.Common.Logging.Log4Net">
      <arg key="configType" value="inline" />
    </logFactoryAdapter>
  </logging>
</iBATIS>

<log4net>
  <!-- 定义输出的附加信息，大小，输出到的文件等等 -->
  <appender name="RollingLogFileAppender" type="log4net.Appender.RollingFileAppender">
    <param name="File" value="f:\log.txt" />
    <param name="AppendToFile" value="true" />
    <param name="MaxSizeRollBackups" value="2" />
    <param name="MaximumFileSize" value="100KB" />
    <param name="RollingStyle" value="Size" />
    <param name="StaticLogFileName" value="true" />
    <layout type="log4net.Layout.PatternLayout">
      <param name="Header" value="[Header]\r\n" />
      <param name="Footer" value="[Footer]\r\n" />
      <param name="ConversionPattern" value="%d [%t] %-5p %c [%x] - %m%n" />
    </layout>
  </appender>
  <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender">
    <layout type="log4net.Layout.PatternLayout">
      <param name="ConversionPattern" value="%d [%t] %-5p %c [%x] &lt;%X{auth}&gt; - %m%n" />
    </layout>
  </appender>

  <!-- 设置错误的级别以及附加信息 -->
  <root>
    <level value="DEBUG" />
    <appender-ref ref="RollingLogFileAppender" />
    <appender-ref ref="ConsoleAppender" />
  </root>

  <!-- 只是打印错误信息的级别 -->
  <logger name="IBatisNet.DataMapper.Configuration.Cache.CacheModel">
    <level value="DEBUG" />
  </logger>
  <logger name="IBatisNet.DataMapper.Configuration.Statements.PreparedStatementFactory">
    <level value="DEBUG" />
  </logger>
  <logger name="IBatisNet.DataMapper.LazyLoadList">
    <level value="DEBUG" />
  </logger>
  <logger name="IBatisNet.DataAccess.DaoSession">
    <level value="DEBUG" />
  </logger>
  <logger name="IBatisNet.DataMapper.SqlMapSession">
    <level value="DEBUG" />
  </logger>
  <logger name="IBatisNet.Common.Transaction.TransactionScope">
    <level value="DEBUG" />
  </logger>
  <logger name="IBatisNet.DataAccess.Configuration.DaoProxy">
    <level value="DEBUG" />
  </logger>
</log4net>
```



# Unity

Unity.config示例

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="unity" 
             type="Microsoft.Practices.Unity.Configuration.UnityConfigurationSection, 
                   Microsoft.Practices.Unity.Configuration, Version=1.2.0.0, Culture=neutral, 
                   PublicKeyToken=31bf3856ad364e35, processorArchitecture=MSIL"/>
  </configSections>
  <unity>
    <typeAliases>
      <typeAlias alias="IMapper" type="iBatisUnityTest.DataMapper.IMapper, iBatisUnityTest"/>
      <typeAlias alias="Mapper" type="iBatisUnityTest.DataMapper.Mapper, iBatisUnityTest"/>
      <typeAlias alias="ITestUserDao" type="iBatisUnityTest.Dao.ITestUserDao, iBatisUnityTest"/>
      <typeAlias alias="TestUserDao" type="iBatisUnityTest.Dao.TestUserDao, iBatisUnityTest"/>
      <typeAlias alias="ITestUserService" 
                 type="iBatisUnityTest.Service.ITestUserService, iBatisUnityTest"/>
      <typeAlias alias="TestUserService" 
                 type="iBatisUnityTest.Service.TestUserService, iBatisUnityTest"/>
      <!--定义单例模式的别名-->
      <typeAlias alias="singleton" 
                 type="Microsoft.Practices.Unity.ContainerControlledLifetimeManager, 
                       Microsoft.Practices.Unity"/>
    </typeAliases>
    <containers>
      <container name="dt">
        <types>
          <!--Dao-->
          <type type="ITestUserDao" mapTo="TestUserDao">
            <lifetime type="singleton"/>
            <typeConfig 
                   extensionType="Microsoft.Practices.Unity.Configuration.TypeInjectionElement,
               Microsoft.Practices.Unity.Configuration">
            </typeConfig>
          </type>

          <!--Service-->
          <type type="ITestUserService" mapTo="TestUserService">
            <lifetime type="singleton"/>
            <typeConfig 
                   extensionType="Microsoft.Practices.Unity.Configuration.TypeInjectionElement,
               Microsoft.Practices.Unity.Configuration">
              <property name="UserDao" propertyType="TestUserDao">
                <dependency />
              </property>
              <property name="DataMapper" propertyType="Mapper">
                <dependency />
              </property>
            </typeConfig>
          </type>

          <!--Controller-->
          <type type="iBatisUnityTest.Controllers.TestController,iBatisUnityTest">
            <typeConfig 
                   extensionType="Microsoft.Practices.Unity.Configuration.TypeInjectionElement,
               Microsoft.Practices.Unity.Configuration">
              <property name="UserService" propertyType="TestUserService">
                <dependency />
              </property>
            </typeConfig>
          </type>
        </types>

        <instances>
          <add name="sqlMapperPath" type="System.String" value="Setting/ORM/sqlMap.config" />
        </instances>

      </container>
    </containers>
  </unity>
</configuration>
```

在web.config中的<appSettings>添加

```xml
<appSettings>
    <add key="unityConfigPath" value="Setting/IOC/unity.config" />
</appSettings>
```

在Global.asax中添加

```CSharp
//实现IUnityContainerAccessor
public class MvcApplication : System.Web.HttpApplication, IUnityContainerAccessor{
  protected void Application_Start(){
    InitializeContainer();	//实例化容器
    AreaRegistration.RegisterAllAreas();
    FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
  }
  
  private static UnityContainer _container;
  public static IUnityContainer Container{
    get{ return _container; }
  }
  IUnityContainer IUnityContainerAccessor.Container{
    get { return Container; }
  }
  
  protected virtual void InitializeContainer(){
    if(_container == null){
      _container = new UnityContainer();
      // Set the Unity Controller Factory
      ControllerBuilder.Current.SetControllerFactory(typeof(UnityControllerFactory));
      string unityConfigFilePath = AppDomain.CurrentDomain.BaseDirectory +
        ConfigurationManager.AppSettings["unityConfigPath"];
      FileConfigurationSource configExternal = new FileConfigurationSource(unityConfigFilePath);
      UnityConfigurationSection section =
        (UnityConfigurationSection)configExternal.GetSection("unity");
      section.Containers["dt"].Configure(_container);
    }
  }
}
```





# DataTables

基于JQuery的数据表格插件



### 服务器端处理示例

html

```html
<table id="myDataTable" class="table table-striped">
  <thead id="DataTableThread">
    <tr>
      <th>闸次号</th>
      <th>船只数量</th>
      <th>状态</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>
```

js

```js
$("#myDataTable").DataTable({
  scrollX: true,			//横向滚动
  ordering: false,			//排序
  lengthChange: false,		//选择每页条数
  searching: false,			//搜索
  serverSide: true,			//服务器端处理
  processing: true,			//显示处理中
  //data:dataTable传给服务器的值，callback：数据返回后执行的方法，setting：设置对象
  ajax: function(data, callback, settings) {
    $.ajax({
      dataType: "JSON",
      type: "POST",
      data: {
        pageSize: data.length,		//data.length:每页的条数
        page: parseInt((data.start + 1) / data.length) + 1,	//data.start:第一条数据的起始位置(0)
      },
      url: url,
      success: function(r) {
        var returnData = {};
        //封装返回的json变为dataTable要求的标准
        returnData.data = r.result;				//数据
        returnData.recordsTotal = r.total;		//总数据数
        returnData.recordsFiltered = r.total;	//过滤后的数据总数
        returnData.draw = data.draw;			//确保Ajax从服务器返回的是对应的
        callback(returnData);
      },
      error: function(xmlHttpRequest, textStatus, errorThrown) {
        alert("error:" + textStatus);
      }
    });
  },
  columns: [	//设置各列的值，默认为data，即json里对应的对象必须为data，可以通过dataSrc修改
    {	
      data: "queue_up_id",
      //改变某列的显示
      //data:该cell的值，type:当前列的类型，row：当前行的完整数据对象
      //meta:包含 row: 当前行的索引，col: 当前列的索引，settings: 当前DataTables控件的setttings对象
      render: function(data, type, row, meta) {	
        return "<a href='xxx?queueUpId=" + encodeURIComponent(data) + "'>" + data + "</a>";
      }
  	},
    {data: "shipcount"},
    {data: "status"}
  ],
  columnDefs: [
    {	//改变某列的显示
      targets: 2,
      render: function(data, type, row, meta) {
        var text = "";
        switch (data) {
          case 0: text = "结束"; break;
          case 1: text = "开始"; break;
        }
        return text;
      }
    },
    {	//可以指定多列
      targets: [0,1,2],
      render: function (data, type, row, meta) {
          return "<div style='text-align:center;white-space:nowrap'>" + data + "</div>";
      }
    }
  ]

});
```



* `columns.render`和`columnDefs.render`的区别：

  其实这个的区别，归根到底是`columns`和`columndefs`的区别，它们两个的区别就有

  1. `columns`先执行，`columnDefs`后执行
  2. `columnDefs`比`columns`多一个属性 `columnDefs.targets`，有了它可以做很多`columns`做不到的事情
     * **targets**可以有多种写法:
       1. 0或者正整数(可用数组): 表示正向列的索引
       2. 负数(可用数组): 表示反向列的索引
       3. 字符串: 匹配th的class来选择列.
       4. "_all":   所有列,也是默认值.

  **针对第一点**:

  每一次 DataTables 的是重绘或者重载都会后台执行很多回调函数

  `columns.render`是在`createdRow`前执行的 `columnDefs.render`是在`rowCallback`后执行的

  就会导致`columnDefs.render`执行的时候其实`tr`已经全部渲染出来的，大家就可以对全局做一些操作了， 如合并单元格、根据某个`tr`里面`td`改变另一个`tr`里面的`td`的渲染了等等

  **针对第二点**：

  就可以使一个`columnDefs.render`对应多个列了，或者在没有创建`columns`的时候使用，更加灵活，`columns.render`一对一的更加有针对性




### 获取行的原始数据

```js
//在按钮上设置行号
$('#example').dataTable({
	"columns": [{
          "data":"name", 
          "render" : function ( data, type, row, meta) {
        	return  '<button id="btnEdit" data-rowindex="' + meta.row + '">编辑</button>';
          }
      	}]
});

//然后根据元素取出行号，获取数据
var rowIndex = $('#btnEdit').attr('data-rowindex');
$('#example').DataTable().rows(rowIndex).data()[0];
```




# Bootstrap

## datepicker

### 与modal的冲突

当在modal中使用datepicker时，datepicker的显示和隐藏会触发modal的显示隐藏事件，解决方法是阻止其向上冒泡

```CSharp
//阻止datepicker的show/hide事件向上冒泡，因为会触发modal的事件
$document.on("show", ".datepicker", function (e) {
	//e.preventDefault();
	e.stopPropagation();
});
$document.on("hide", ".datepicker", function (e) {
	//e.preventDefault();
	e.stopPropagation();
});
//或者
$(".datepicker").datepicker().show(function (e) {
    //e.preventDefault();
    e.stopPropagation();
}).hide(function (e) {
    //e.preventDefault();
    e.stopPropagation();
});
```





# Tip

* `System.Enum, System.ValueType`本身都是引用类型
* C#嵌套类型可以访问其外层类型的私有成员
* `using`语句提供可确保正确使用`IDisposable`对象的方便语法。


```CSharp
using (Font font1 = new Font("Arial", 10.0f)) 
{
    byte charset = font1.GdiCharSet;
}

//相当于
{
  Font font1 = new Font("Arial", 10.0f);
  try
  {
    byte charset = font1.GdiCharSet;
  }
  finally
  {
    if (font1 != null)
      ((IDisposable)font1).Dispose();
  }
}

//可在using语句中声明一个类型的多个实例
using (Font font3 = new Font("Arial", 10.0f), font4 = new Font("Arial", 10.0f))
{	// Use font3 and font4. }
```

* localDB默认排序规则：SQL_Latin1_General_CP1_CI_AS

* JQuery修改input的value

  ```javascript
  //应该使用
  $('').val(value);
  //而不是以下这样，这样设置后当用户更改input框内的字符，将无法重新设置
  $('').attr("value", value);		
  ```

  ​

* 对一个div，可以使用`style="word-warp:break-word"`来使其内文本自动换行，并使div高度自适应

  * word-wrap:break-word与word-break:break-all共同点是都能把长单词强行断句，不同点是word-wrap:break-word会首先起一个新行来放置长单词，新的行还是放不下这个长单词则会对长单词进行强制断句；而word-break:break-all则不会把长单词放在一个新行里，当这一行放不下的时候就直接强制断句了。

* C#的DateTime通过json传递前端，转换成js的Date

  ```
  var date = eval('new ' + eval('/Date(1335258540000)/').source);
  ```

  js的日期格式转换

  ```js
  //js的日期格式转换
  Date.prototype.format = function (format) {
    var args = {
      "M+": this.getMonth() + 1,
      "d+": this.getDate(),
      "h+": this.getHours(),
      "m+": this.getMinutes(),
      "s+": this.getSeconds(),
      "q+": Math.floor((this.getMonth() + 3) / 3),  //quarter
      "S": this.getMilliseconds()
    };
    if (/(y+)/.test(format))
      format = format.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    for (var i in args) {
      var n = args[i];
      if (new RegExp("(" + i + ")").test(format))
        format = format.replace(RegExp.$1, RegExp.$1.length == 1 ? n : ("00" + n).substr(("" + n).length));
    }
    return format;
  };	

  //调用
  var formatDate = date.format("yyyy/MM/dd");
  ```




* js获取今天和昨天日期

  ```js
  var today = new Date();
  //算出昨天的日期
  var yesterday = new Date();
  yesterday.setTime(today.getTime() - 24 * 60 * 60 * 1000);
  begin = yesterday.getFullYear()+"-"+(yesterday.getMonth()+1)+"-"+yesterday.getDate();
  //今天
  end = today.getFullYear()+"-"+(today.getMonth()+1)+"-"+today.getDate();
  ```

* js获取QueryString

  ```js
  function GetQueryString(name) {
      var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
      var r = window.location.search.substr(1).match(reg);
      if (r != null)
          return decodeURIComponent(r[2]);
      return "";
  }
  ```

  ​



* linq 左连接

  ```CSharp
  //linq to Entity和linq to Object最好不要混用，会报错
  var result = from o in ois
  join s in ships
  on o.enter_id equals s.enter_id.ToString() into grp1
  join e in eehas
  on o.enter_id equals e.enter_id into grp2
  from g1 in grp1.DefaultIfEmpty()
  from g2 in grp2.DefaultIfEmpty()
  select new {
    o,
    carry = grp1.Distinct().Count(),
    //对于linq to Object，对于左连接得来的group的元素，要判断是否为null再使用
    assessment_level = g2 == null ? "" : g2.assessment_level
  };
  ```

  ​

* js手机端自定义tap事件

  ```js
  //自定义tap
  $(document).on("touchstart", function(e) {
    if(!$(e.target).hasClass("disable")) $(e.target).data("isMoved", 0);
  });
  $(document).on("touchmove", function(e) {
    if(!$(e.target).hasClass("disable")) $(e.target).data("isMoved", 1);
  });
  $(document).on("touchend", function(e) {
    if(!$(e.target).hasClass("disable") && $(e.target).data("isMoved") == 0) 		
  		$(e.target).trigger("tap");
  });
  //使用
  $("").on("tap", function(){});
  ```

  ​

* 使a标签不进行跳转

  ```html
  <a href="javascript:void(0);"></a>
  <a href="#"></a>
  ```

  第一种方式利用伪协议：

  > 伪协议不同于因特网上所真实存在的协议，如http://，https://，ftp://，
  >
  > 而是为关联应用程序而使用的.如:tencent://(关联QQ)，data:(用base64编码来在浏览器端输出二进制文件)，还有就是javascript:
  >
  > 我们可以在浏览地址栏里输入"javascript:alert('JS!');"，点转到后会发现，实际上是把javascript:后面的代码当JavaScript来执行，并将结果值返回给当前页面。

  比较推荐第二种方式，但是会跳转到页面顶部，可以在click方法中`return false;`来解决

### JS中Date的怪事

当使用`new Date(str)`来计算日期时，连接日期的字符为“-”的话，月份和日期加不加0得到的时间将是不同的，加了是8点，不加是0点，用“\”来连接则都为0点：

```js
var srtDate = "2018-04-01";	//Sun Apr 01 2018 08:00:00 GMT+0800 (中国标准时间)
new Date(srtDate);	
//2018-12-01 Sat Dec 01 2018 08:00:00 GMT+0800 (中国标准时间)
//2018-4-1, 2018-04-1, 2018-4-01, 2018/04/01	Sun Apr 01 2018 00:00:00 GMT+0800 (中国标准时间) 

//所以比较日期时建议用"/"替换"-"
var strDate1 = strDate.replace(/-/g, "/");
```

推测原因：应该是使用形如“yyyy-MM-dd”的标准格式时间便是8点
