title: Xcode断点实用调试技巧
type: iOS
---

> 作者：王振华

# Xcode断点实用调试技巧
断点是iOS开发中最常用的调试技巧，Xcode的断点功能是非常强大的，下面介绍一些常用的技巧。

## 编辑断点
为了测试，我们先写一个简单的demo。
```objectivec
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        for (int i = 0; i < 10; i++) {
            NSLog(@"%i",i);
        }
    }
    return 0;
}
```
然后可以在for循环内加一个断点，并选择Edit Breakpoint。
![1](/img/XcodeDebug/XcodeDebug1.png)

我们可以设置断点执行的condition，这里设置i==5。
![4](/img/XcodeDebug/XcodeDebug4.png)
![3](/img/XcodeDebug/XcodeDebug3.png)
可以看到，当运行到i==5时，断点被触发。
另外，也可以在ignore中选择忽略若干次循环。

action表示触发断点时执行的操作，这里的功能非常强大，除了常见的输出log信息，还可以播放声音，甚至运行脚本。
![7](/img/XcodeDebug/XcodeDebug7.png)

在这里，我们选择忽略前两次循环，并打印i的值。
![2](/img/XcodeDebug/XcodeDebug2.png)

可以看到输出结果。
![5](/img/XcodeDebug/XcodeDebug5.png)

勾选option，可以在断点触发之后继续运行程序。

## 异常捕获断点
当程序crash时，我们希望能准确定位到错误位置。但是Xcode有时并不能准确捕获，而是断在main函数里。这时可以增加一个全局的Exception Breakpoint。大多数异常都可以用这种方法捕获。
![6](/img/XcodeDebug/XcodeDebug6.png)

## 符号断点
Symbolic Breakpoint也是一个全局断点，在断点的描述里可以设置Symbol（方法名）、Module（模块名）、Condition、Action等。例如，想在代码中查找是否有图片为nil，可以这样设置。arg3表示第一个参数，arg4表示第二个参数。
![8](/img/XcodeDebug/XcodeDebug8.png)

当程序运行到有图片被设为nil时，就会触发断点。
![9](/img/XcodeDebug/XcodeDebug9.png)


