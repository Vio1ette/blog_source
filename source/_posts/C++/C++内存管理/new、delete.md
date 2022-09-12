new操作符的执行过程：

1. 调用operator new分配内存（底层调用malloc()函数）
2. 调用构造函数
3. 返回相应类型的指针 

#### delete

操作符的执行过程：

1. 调用析构函数
2. 调用operator delete释放对象内存，底层调用free()函数

调用析构函数只是释放对象管理的资源，而 operator delete 释放对象本身的资源。例如 vector 可能的内存布局如下：

![image-20220519104008938](https://raw.githubusercontent.com/Vio1ette/blog-img/main/image-20220519104008938.png)

栈中就是对象本身，而堆中就是 vector 对象管理的资源，析构函数释放的是堆资源，operator delete 释放的是栈资源

​	

delete this

delete this如果发生在析构函数中，delete会调用析构函数，析构函数中有会执行delete，则会导致无限循环，使栈内存溢出

delete this如果在普通函数中，则delete this之后不能再使用到成员资源（比如某个成员变量或者虚函数），否则就会报错

