const是一个语义约束，由编译器强制实现

#### 1. 指针与const

涉及到指针时，注意区分const是修饰指针本身还是指针所指之物

```cpp
char* p = greeting;
const char* p =greeting;    // 不可修改 *p
char* const p = greeting;   // 不可修改 p
```

#### 2. STL迭代器与const

声明一个迭代器为`const`就像声明一个指针为`const（T* const）`，表示迭代器不能指向不同的东西，但它所指之物本身可以修改，而`const_iterator`约束了所指之物本身不能被修改。

```cpp
const std::vector<int>::iterator iter = vec.begin();
std::vector<int>::const_iterator cIter = vec.begin();
```

#### 3. const成员函数

在成员函数声明语句末端添加`const`关键字，意味着该函数不能修改对象内任何`non-static`成员变量（可以修改static）。const对象不能被修改， 所以const对象只能调用const成员函数。

```cpp
class T{
  void fun1() const;   //const成员函数
};
```

> 两个成员函数如果只是常量性不同，可以被重载

##### 3.1 bitwise constness 和 logical constness

`bitwise`常量性是编译器规定的，表示对象内部的任何一个字节都不被修改，但这种常量性无法保证成员指针指向的data不被修改。

`logical`常量性比较宽松，主张一个const成员函数可以修改它所处理的对象内的某些bits，但只有在客户端侦测不出的情况下才行。具体实现要用`mutable`关键字，指出那些无关紧要的变量，这些变量即使在const成员函数内也可以被修改。

```cpp
class CTextBlock {
public:
    ...
	std::size_t length() const;
private:
    char* pText;
    mutable std::size_t textLength; // 可被const函数修改
    mutable bool lengthIsValid;		// 可被const函数修改
};

std::size_t CTextBlock::length() const
{
    // 可以对 textLength 和 lengthIsValid 进行修改
}
```

##### 3.2 用const成员函数实现non-const成员函数

为了避免代码重复，有时需要这么做，方法是使用转型（casting）

```cpp
class TextBlock {
public:
    ...
	const char& operator[](std::size_t position) const 
    {
        return text[position];
    }
    
    char& operator[](std::size_t position)
    {
    	return 
            const_cast<char&>(
            static_cast<const TextBlock&>(*this)[position]
                                );
    }
    
private:
    char* pText;
};
```

这里有两次转型，第一次用来为 *this 添加 const，所以调用 operator[] 时可以调用其 const 版本，然后第二次转型从 const operator[] 的返回值中移除 const

