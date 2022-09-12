```cpp
class Rational {
public:
    Rational(int numerator = 0, int denominator = 1); //构造函数不为 explicit，可以进行 int-to-Rational隐式转换
    int numerator() const;  		//分子
    int denominator() const;		//分母
};
```

若将有理数乘法 operator* 写成 Rational 成员函数：

```cpp
class Rational {
public:
    ...
	const Rational operator* (const Rational& rhs) const;
};
```

这种设计可以很好地满足两个有理数想乘的情况：

```cpp
Rational oneEighth(1, 8);
Rational oneHalf(1, 2);
Rational result = oneHalf * oneEighth;
result = result * oneEighth;
```

但混合式运算时，会发生错误：

```cpp
result = oneHalf * 2;    //OK
result = 2 * oneHalf; 	 //错误！

//等价于
result = oneHalf.operator*(2);  //OK
result = 2.operator*(oneHalf);  //错误！
```

因为 class 中有一个没有 explicit 的“转换构造函数”，所以可以发生隐式类型转换，将 2 直接隐式转换为了一个 Rational 对象。但 2 没法调用 operator*，所以第二条语句无法通过编译。

> 转换构造函数只有一个参数（或其他参数均有默认值），而且该参数又不是 class 的 const reference 。

为了支持混合式算术运算，需要把 operator* 定义成 non-member 函数：

```cpp
class Rational {
    ...
};

const Rational operator*(const Rational& lhs, const Rational& rhs)
{
    return Rational(lhs.numerator() * rhs.numerator(), lhs.denominator() * rhs.denominator());
}
Rational oneFourth(1, 4);
Rational result;
result = oneFourth * 2;
result = 2 * oneFourth; 	//OK
```

要不要把 operator* 实现为一个 Rational class 的 friend 函数？答案是不需要，因为 operator* 函数不需要访问 private 成员

