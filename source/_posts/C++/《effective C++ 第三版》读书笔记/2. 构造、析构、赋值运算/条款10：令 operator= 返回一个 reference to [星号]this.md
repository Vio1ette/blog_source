为了实现“连锁赋值”，赋值操作符（或所有赋值相关运算）必须返回一个 reference 指向操作符的左侧实参

```cpp
class Widget {
public:
    ...
	Widget& operator=(cosnt Widget& rhs)
    {
        ...
	   return *this;
    }
    ...
}
```

​	