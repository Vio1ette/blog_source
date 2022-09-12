#### 1. ref 和 out

ref关键字通过引用传递变量，在声明方法和调用方法时，都要加ref关键字，变量传递给方法之前必须先做初始化

使用out关键字可以实现让方法返回多个值

```c#
public static bool TryParse(string s, out int result);
```

TryParse的返回值有一个bool值，指明解析是否成功，如果解析成功，还会有第二个返回值，即解析的结果result

result在传递给方法之前不需要预先初始化

和ref类似，在声明方法和调用方法时都要加out

> 这里的out int result也是引用传递，但是ref和out的应用场景是不一样的，**ref** 用在需要传引用时，**out** 用在需要返回多个值时

