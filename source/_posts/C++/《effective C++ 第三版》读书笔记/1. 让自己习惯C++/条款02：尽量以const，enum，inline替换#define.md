尽量用编译器，而不用预处理器

#### 1. #define 、const 定义符号常量

```cpp
#define ASPECT_RATIO 1.653
```

`#define`在<u>预处理阶段</u>进行文本展开，编译器看不到`ASPECT_RATIO`符号名，如果涉及到`1.653`的代码在编译时报错，就会很难Debug，所以用`const`会比较好

```cpp
const double ASPECT_RATIO = 1.653;
```

- `#define`在预处理阶段进行的，而`const`是一个运行时概念
- `#define`定义的常量没有类型，没有类型安全检查 ，`const`的常量带类型，要在编译时进行类型检查
- `#define`是直接替换，没有分配内存，存储于程序代码段中。而`const`需要进行内存分配，存储于程序数据段中
- `const`可以将常量限制在作用域内，例如class专属常量，而`#define`不重视作用域，不能提供封装性

#### 2. #define、inline 定义函数

- #define宏函数容易导致歧义，会发生不可预计的行为，要用inline内联函数代替宏函数，就可以实现可预计的行为和类型安全（template inline），还可以设定作用域和访问规则
- inline只是对内联的建议，编译器不一定真的实现内联
