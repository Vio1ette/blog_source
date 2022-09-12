考虑一个有理数 class

```cpp
class Rational {
public:
    Rational(int numerator = 0, int denominator = 1);
    ...
private:
    int n,d;  // 分子、分母
    friend const Rational operator* (const Rational& lhs, const Rational& rhs);
};
```

注意 operator* 函数的返回值类型是 const value 类型，这里是否需要返回 reference 以避免临时对象的构造、析构成本呢？

by reference 传递出来的语义是一个已存在的东西的名称，所以如果要返回 reference 对象，那么这个对象必须在 operator* 函数内部创建出来，因为两个形参都不能作为乘积的结果来返回。

1. 在 local stack 上创建对象：

```cpp
const Rational& operator* (const Rational& lhs, const Rational& rhs)
{
    Rational result(lhs.n * rhs.n, lhs.d * rhs.d);
    return result;
}
```

上面这种做法是极其错误的，因为 local 对象在函数结束时已经被销毁了，对这个已死亡对象的 reference 是无意义的

2. 在 heap 上创建对象：

```cpp
const Rational& operator* (const Rational& lhs, const Rational& rhs)
{
    Rational* result = new Rational(lhs.n * rhs.n, lhs.d * rhs.d);
    return result;
}
```

这又会造成不好 delete 的糟糕状况

3. 定义在函数内部的 static Rational 对象

```cpp
const Rational& operator* (const Rational& lhs, const Rational& rhs)
{
    static Rational result;
    result = ...
    return result;
}
```

这种代码在实际中是不可用的，例如：

```cpp
bool operator==(const Rational& lhs, const Rational& rhs);
Rational a,b,c,d;
...
if((a*b) == (c*d)){
}
```

注意，表达式 ((a*b) == (c\*d)) 永远为真，因为 static 对象只有一份

**综上，绝对不要让函数返回 pointer 或 reference 指向一个 local stack 对象，或 heap-allocated 对象。当返回 local static 对象时，要记住这个对象只有一份**



