自我赋值：

```cpp
class Bitmap {...};
class Widget {
    ...
private:
    Bitmap* pb;
};
Widget w;
...
w = w;
```

下面是一个在自我赋值时不安全的 operator= 实现代码

```cpp
Widget& Widget::operator=(const Widget& rhs)
{
    delete pb; 					//停止使用当前的 bitmap
    pb = new Bitmap(*rhs.pb);	 //使用 rhs's bitmap 的副本
    return *this;
}
```

operator= 函数的参数 rhs 和 *this 有可能是同一个对象，如果真是这样，那么 delete pb; 就会将这个对象销毁！后面的拷贝将是无意义的

传统优化手段是加一个 if 判断：

```cpp
if (this == &rhs) return *this;  //证同测试
```

但是这段代码还不具备异常安全性，假如`new Bitmap`导致了异常，Widget 最终会持有一个指针指向被删除的 Bitmap

让 operator 具备异常安全性往往自动获得自我赋值安全性：

```cpp
Widget& Widget::operator=(const Widget& rhs)
{
    Bitmap* pOrig = pb;
    pb = new Bitmap(*rhs.pb);	 //使用 rhs's bitmap 的副本
    delete pOrig;
    return *this;
}
```

现在这段代码在`new Bitmap`爬出异常时仍能正常工作，同时也可兼容处理自我赋值（虽然效率不高）

copy and swap 技术

```cpp
class Widget{
  	...
	void swap(Widget& rhs);		// 交换*this和rhs的数据 
    ...
};
Widget& Widget::operator=(const Widget& rhs)
{
    Widget temp(rhs); //不用 new 了，而是用一个栈上的局部对象
    swap(temp);		 // 自我赋值时仍能工作
    return *this;    // 局部变量 temp 自动销毁，
}  
```



