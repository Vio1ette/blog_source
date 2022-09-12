why？为什么要发明lambda函数？

简化仿函数

#### 1. 语法

```cpp
[capture](parameters) mutable -> return_type{statement} 
```

其中：

- [capture]：lambda函数引出符

  > [var]：以值传递的方式捕捉变量var
  >
  > [=]：以值传递的方式捕捉所有父作用域的变量（包括this）
  >
  > [&var]：以引用传递的方式捕捉var
  >
  > [&]：以引用传递的方式捕捉所有父作用域的变量（包括this）
  >
  > 注意：1. 以上方式可以组合（[=, &a, &b]），但不允许重复捕获，例如[=, a]会导致编译错误

- (parameters)：参数列表，不需要时可连同括号()一起省略

- mutable：mutable修饰符。默认情况下lambda函数总是一个const函数，mutable可以取消其常量性。在使用该修饰符时，参数列表不可省略（即使参数为空）

- ->return_type：返回类型，没有返回值时，可连同->一起省略

- {statement}：函数体

#### 2. lambda底层实现 & 仿函数

##### 2.1 仿函数

###### 仿函数定义

在一个类中重载()运算符，可以将该类对象像函数一样使用

```cpp
class _functor {
public:
    int operator()(int a, int b){
        return a + b;
    }
};

int main()
{
    int girls = 3, boys = 4;  
    _functor totalChild;
    cout << totalChild(5, 6) << endl;
    return 0;
}
```

###### 仿函数的初始状态

仿函数可以有初始状态，这是它与普通函数的重要区别。<u>class私有成员的状态构成了仿函数的初始状态</u>，可以通过构造函数在声明对象时初始化状态

> 仿函数其可以拥有多个不同的初始状态（多个具有不同初始值的对象）

```cpp
class Tax {
private:
    float rate;
    int base;
public:
    Tax(float r, int b):rate(r),base(b){}
    float operator()(float money){return (money - base) * rate;}
};

int main()
{
    //调用构造函数
    Tax high(0.4, 30000);  			// 设定初始状态（rate，base）
    Tax middle(0.25, 20000);  		// 设定初始状态（rate，base）
    cout <<"tax over 3w:"<<high(37500)<< endl;
    cout << "tax over 2w:" << middle(27500) << endl;
    return 0;
}
```

##### 2.2 lambda函数与仿函数的比较

<u>lambda捕捉父作用域（用户）的变量作为初始状态</u>，仿函数通过构造函数来初始化状态（构造函数的参数由用户传递）。事实上，仿函数是编译器<u>实现</u>lambda的一种方式，现阶段，通常<u>编译器</u>都会把lambda函数转化为一个仿函数

> lambda函数是仿函数的“语法糖”

![image-20220401162858026](https://raw.githubusercontent.com/Vio1ette/blog-img/main/image-20220401162858026.png)

——图源：《深入理解C++11》

#### lambda捕获的参数存在内存中的什么位置？

面试时遇到这个题，当时脑子懵了，没回答上来，其实挺简单的，猜都能猜到

1. 值传递

```cpp
int main()
{
    int a=1;
    cout<<"父作用域的a的内存位置："<<&a<<endl;
    int girls =3,boys=4; 
    auto totalChild = [a](int x,int y)->int{
    	cout<<"lambda作用域中a的内存位置："<<&a<<endl;
        return x+y+a;
    };
    cout<<totalChild(girls,boys)<<endl;
    return 0;
}
```

对于父作用域的局部变量a，它存在栈中，lambda值捕获的a也在栈中，其内存地址在boys后面分配，就像是新定义的局部变量

2. 引用传递

引用传递地址肯定是不变的，父作用域什么地址，lambda表达式中也是一样的地址

#### 3. 与函数指针的区别是什么？



#### lambda表达式内部无法修改<u>值传递</u>的值，因为lambda表达式内部仿函数实现时，()重载函数默认是`const`的

```cpp
int main()
{
    int a = 1;
    cout << "父作用域的a的内存位置：" << &a << endl;
    int girls = 3, boys = 4;
    auto totalChild = [a](int x, int y)->int {
        a = 2; //编译器报错，表达式不是可修改的左值
        cout << "lambda作用域中a的内存位置：" << &a << endl;
        return x + y + a;
    };
    cout << totalChild(girls, boys) << endl;
    return 0;
}
```

如果要修改，需要加`mutable`取消()重载函数的常量性

```cpp
auto totalChild = [a](int x, int y)mutable->int {
	...
}
```

#### lambda表达式是不能赋值的

```cpp
auto a = [] { cout << "A" << endl; };
auto b = [] { cout << "B" << endl; };
a = b;   // 非法，lambda无法赋值
auto c = a;   // 合法，生成一个副本
```

a、b的类型都是`lambda []void()->void`，但是不能赋值，因为lambda表达式内部实现禁用了赋值运算符（=delete）。但是没有禁用拷贝构造函数，所以可以用一个lambda表达式去初始化另一个lambda表达式

