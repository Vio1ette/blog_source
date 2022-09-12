以 by value 方式传参会有额外的构造函数和析构函数的调用开销，以 by reference 传参可以回避这些开销，const 可以防止函数内部修改 reference 参数

by value 传参还会造成 slicing（对象切割）问题，例如形参类型为 base class，当传入一个 derived class 对象时，此对象会被切割至仅剩 base class 部分。而当以 reference 类型传参时，将一个 derived class 对象赋给 base 引用/指针是合法的，不会被切割。