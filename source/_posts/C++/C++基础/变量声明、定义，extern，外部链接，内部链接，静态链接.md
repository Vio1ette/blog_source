### 1. 声明和定义的关系

声明使得<u>名字</u>为程序所知，规定了变量的类型和名字，一个文件如果想使用别处定义的名字则必须包含对那个名字的声明

定义负责<u>创建</u>与名字关联的<u>实体</u>，声明规定了变量的类型和名字，在这一点上定义与之相同。但除此之外，<u>定义还负责申请存储空间</u>，也可能会为变量赋一个初始值。

如果想<u>单独声明</u>一个变量，而<u>不定义</u>它，就在变量名前添加关键字<u>extern</u>，而且<u>不要显示地初始化</u>变量

```cpp
extern int i;  //声明i而非定义i
int j;  	   //声明并定义j，此处虽然没有显示初始化，但已经为j分配内存了
```

定义有两种写法，第一是不加extern，第二是显示初始化，任何包含了显示初始化的声明即成为定义，即使有extern存在

```cpp
extern double pi = 3.14;   //定义
```

> <u>在函数体内部，如果试图初始化一个由extern关键字标记的变量，将引发错误</u>，extern定义的变量必须是全局的，这样才可能在其他文件中使用，所以不能再语句块里定义

### 2. extern

如果要在多个文件中使用外部（全局）变量，只需要在一个文件中包含该变量的定义（单定义规则），但在**使用该变量的其他所有文件中**，都必须使用关键字`extern`声明它：

```cpp
//file01.cpp
extern int cats = 20; //定义
int dogs = 22; 		  //定义
int fleas;		  	  //还是定义，默认初始化为0
```

```cpp
// file02.cpp
// use cats and dogs from file01.cpp
//非定义，仅声明
extern int cats;
extern int dogs;
```

```cpp
//file98.cpp
//use cats, dogs, and fleas from file01.cpp
extern int cats;
extern int dogs;
extern int fleas;
```

> `file01.cpp` 中 `cats` 的`extern`是可以省略的，效果一样

### 2. 不能在函数体内部初始化一个`extern`变量

![extern](D:\Hugo\Sites\Puppy's\static\images\extern-大型项目工程中使用全局变量\image-20211101202636295.png)

原因：[c++ - Why does initializing an extern variable inside a function give an error? - Stack Overflow](https://stackoverflow.com/questions/17090354/why-does-initializing-an-extern-variable-inside-a-function-give-an-error)

`extern`变量告诉编译器，整个项目中的所有同名`symbol`，都指的是同一个`externally defined i`，所以如果允许在函数内初始化（定义）一个被`extern`标记的变量，就会有==多重定义==的风险

例如：

```cpp
#include <iostream>
using namespace std;

extern int i; // 同一个 externally defined i
int i = 10;
void test()
{
    std::cout << "Hi" << i << std::endl;
}

int main()
{
    extern int i; // 这个 extern 会影响到其作用域内的所有 i，但是会被局部 i 屏蔽
    i++;
    test();
}
```

