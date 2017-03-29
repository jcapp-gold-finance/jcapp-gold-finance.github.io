# IOS 代码规范（一）

## IOS常用规范

1.在类的头文件中尽量少引入其他头文件

> 要点 
> + 除非确有必要，否则不要引入头文件。一般来说，应在某个类的头文件中使用向前声明来提及别的类，并在实现文件中引用那些类的头文件==。（@class）
> + 协议尽量移动至“class-continuation 分类”（==.m 文件中的intreface==）

2.多用字面量语法，少用消息语法

> ```objective-c
> @1
> ```
>
>  代替 
>
> ```objective-c
>  [NSNumber numberWithInt:1];
> ```
>
> 等等
>
> + 应该使用字面量语法来创建字符串、数值、数组、字典。与创建此类对象的常规方法相比，这么做更加简明扼要。
> + 应该通过取下标的方式来访问数组，或者Key类访问字典的元素

3.多用类型常量，少用#define预处理指令

>```objective-c
>#define ANIMATION_DURATION 0.3
>```
>
>用
>
>```objective-c
>static const NSTimeInterval kAnimationDuration = 0.3
>```
>
>如果是全局的话用最新的
>
>.h
>
>```objective-c
>FOUNDATION_EXPORT  NSString* const kMyConstantString;
>```
>
>.m
>
>```objective-c
>NSString*const kMyConstantString = @"Hello,Wenjie!"
>```

><font color=red>全局符号表 extern</font>
>
>+ 不要用预处理器定义常量 原则上除非 预定义，不用用定义
>+ static const  和 extern

4.用枚举表示状态、选项、状态码

> 比较需要关注的是组合枚举
>
> + 例子
>
>   ```objective-c
>   enum UIViewAutoresizing{
>     	UIViewAutoresizingNone 					= 0,
>      	UIViewAutoresizingFlexibleLeftMargin 	= 1 << 0,
>     	UIViewAutoresizingFlexibleWidth      	= 1 << 1,
>     ....
>   }
>   ```
>
>   <font color=red>如果枚举确定 在使用 switch 时最后没有default: 之后枚举添加，会有警告</font>
>
>   NS_ENUM  NS_OPTIONS 用法要注意