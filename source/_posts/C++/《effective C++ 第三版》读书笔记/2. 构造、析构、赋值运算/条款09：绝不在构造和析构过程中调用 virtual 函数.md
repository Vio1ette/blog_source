

为什么不要在构造函数中调用 virtual 函数？

在 base class 构造期间，virtual 函数退化为 non-virtual 函数，因为在 base 类构造的时候，derived 类还没有被构造出来，此时是不可能调用到 derived 类的函数的

在 derived class 对象的 base class 构造期间，对象的类型被编译器视为 base class，而不是 derived class。而且此时 dynamic_cast 和 typeid 都会将该对象的类型识别为 base class

对于析构函数，有类似的解释：

由于是先调用 derived class 的析构函数，再调用其 base class 的析构函数，此时对象的 derived 部分已经被销毁了，所以再调用虚函数也没有意义了

