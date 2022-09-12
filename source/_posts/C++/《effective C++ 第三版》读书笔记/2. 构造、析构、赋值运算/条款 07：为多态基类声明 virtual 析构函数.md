```cpp
class Base{};
class Derived : Base {};
Base* b1 = new Derived();
delete b1;
```

当一个`Base`类指针指向一个`derived`类对象，那么要求`Base`类中的析构函数一定要被声明为`virtual`。否则会发生未定义行为，通常是对象的 derived 部分没有被销毁（只调用了基类的析构函数，而基类的`non-virtual`析构函数只负责销毁基类的成员）

析构函数的运作方式是：最深层派生（most derived）类的析构函数会最先被调用，然后其每一个 base class 的析构函数会被调用。<u>编译器会在 derived class 的析构函数中创建一个对其直接基类的析构函数的调用动作。</u>



