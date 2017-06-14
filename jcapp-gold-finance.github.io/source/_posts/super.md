title: super关键字帮我们做了什么？

type: iOS
---

> 作者：戴奕



本篇文章讲的是super的实际运作原理，如有同学对super与self的区分还有疑惑的，请参考ChenYilong大神的[《招聘一个靠谱的iOS》面试题参考答案（上）](https://github.com/ChenYilong/iOSInterviewQuestions/blob/master/01%E3%80%8A%E6%8B%9B%E8%81%98%E4%B8%80%E4%B8%AA%E9%9D%A0%E8%B0%B1%E7%9A%84iOS%E3%80%8B%E9%9D%A2%E8%AF%95%E9%A2%98%E5%8F%82%E8%80%83%E7%AD%94%E6%A1%88/%E3%80%8A%E6%8B%9B%E8%81%98%E4%B8%80%E4%B8%AA%E9%9D%A0%E8%B0%B1%E7%9A%84iOS%E3%80%8B%E9%9D%A2%E8%AF%95%E9%A2%98%E5%8F%82%E8%80%83%E7%AD%94%E6%A1%88%EF%BC%88%E4%B8%8A%EF%BC%89.md#21-%E4%B8%8B%E9%9D%A2%E7%9A%84%E4%BB%A3%E7%A0%81%E8%BE%93%E5%87%BA%E4%BB%80%E4%B9%88)。

<!-- more -->

## super究竟在干什么？

### 官方提到的super关键字？

打开苹果API文档，搜索`objc_msgSendSuper`。

![super官方解释](http://upload-images.jianshu.io/upload_images/696588-d36d844bdb19ff56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


里面明确提到了使用`super`关键字发送消息会被编译器转化为调用`objc_msgSendSuper`以及相关函数（由返回值决定）。

再让我们看看该函数的定义（这是文档中的定义）：

```objective-c
id objc_msgSendSuper(struct objc_super *super, SEL op, ...);
```

这里的`super`已经不再是我们调用时写的`[super init]`的`super`了，这里指代的是`struct objc_super`结构体指针。文档中明确指出，该结构体需要包含**接收消息的实例**以及**一开始寻找方法实现的父类**：

```objective-c
struct objc_super {
    /// Specifies an instance of a class.
    __unsafe_unretained id receiver;

    /// Specifies the particular superclass of the instance to message. 
    __unsafe_unretained Class super_class;
    /* super_class is the first class to search */
};
```


![objc_super结构体](http://upload-images.jianshu.io/upload_images/696588-fb6f062bae845924.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


既然知道了`super`是如何调用的，那么我们来尝试自己实现一个`super`。



### 手动实现super关键字

让我们先定义两个类：

这是父类：Father类

```objective-c
// Father.h
@interface Father : NSObject
  
- (void)eat;

@end
  
// Father.m
@implementation Father

- (void)eat {
    NSLog(@"Father eat");
}

@end
```

这是子类：Son类

```objective-c
// Son.h
@interface Son : Father

- (void)eat;

@end
  
// Son.m
@implementation Son

- (void)eat {
    [super eat];
}

@end
```

在这里，我们的Son类重写了父类的`eat`方法，里面只做一件事，就是调用父类的`eat`方法。

让我们在main中开始进行测试：

```objective-c
int main(int argc, char * argv[]) {
    Son *son = [Son new];
    [son eat];
}

// 输出：
2017-05-14 22:44:00.208931+0800 TestSuper[7407:3788932] Father eat
```

到这里没毛病，一个Son对象调用了`eat`方法（内部调用父类的`eat`），输出了结果。



#### 1. 下面，我们来自己实现`super`的效果：

改写Son.m：

```objective-c
// Son.m

- (void)eat {
//    [super eat];
    
    struct objc_super superReceiver = {
        self,
        [self superclass]
    };
    objc_msgSendSuper(&superReceiver, _cmd);    
}
```

运行我们的main函数：

```objective-c
//输出
2017-05-14 22:47:00.109379+0800 TestSuper[7417:3790621] Father eat
```

没毛病，我们可是根据官方文档来实现`super`的效果。

难道`super`真的就是如此？

让我们持怀疑的态度看看下面这个例子：

在这里，我们又有个Son的子类出现了：Grandson类

```objective-c
// Grandson.h
@interface Grandson : Son

@end
  
// Grandson.m
@implementation Grandson

@end
```

该类啥什么都没实现，纯粹继承自Son。

然后让我们改写main函数：

```objective-c
int main(int argc, char * argv[]) {
    Grandson *grandson = [Grandson new];
    [grandson eat];
}
```

运行起来，过一会就crash了，如图：

![崩溃提示](http://upload-images.jianshu.io/upload_images/696588-488ad03147e635e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


再看看相关线程中的方法调用：

![crash方法调用](http://upload-images.jianshu.io/upload_images/696588-a5d4df60caaa6334.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是一个死循环，所以系统让该段代码强制停止了。可为什么这里会构成死循环呢？让我们好好分析分析：

1. Grandson中没有实现eat方法，所以main函数中Grandson的实例执行eat方法是这样的：根据类继承关系自下而上寻找，在Grandson的父类Son类中找到了eat方法，进行调用。
2. 在Son的eat方法的实现中，我们构建了一个`superReceiver`结构体，内部包含了`self`以及`[self superclass]`。在调用过程中，self指代的应是Grandson实例，也就是grandson这个变量，那么`[self superclass]`方法返回值也就是Son这个类。
3. 根据第2点的分析，以及我们在文章开头的文档中，苹果指出`superReceiver`中的父类就是开始寻找方法实现的那个父类，我们可以得出，此时的`objc_msgSendSuper(&superReceiver, _cmd)`函数调用的方法实现即是Son类中的`eat`方法的实现。即，构成了递归。

既然这里不能使用`superclass`方法，那么我们要如何自己实现`super`的作用呢？

我们是这段代码的作者，所以，我们可以这样：

```objective-c
// 我们修改了Son.m

- (void)eat {
//    [super eat];
    
    struct objc_super superReceiver = {
        self,
        objc_getClass("Father")
    };
    objc_msgSendSuper(&superReceiver, _cmd);
}

// 输出
2017-05-14 23:16:49.232375+0800 TestSuper[7440:3798009] Father eat
```

我们直接指明`superReceiver`中要寻找方法实现的父类：Father。这里必定有人会问：这样子岂不是每个调用`[super xxxx]`的地方都需要直接指明**父类**？

> “直接指明”的意思是，代码中直接写出这个类，比如直接写：`[Father class]`或者`objc_getClass("Father")`，这里面的Father与"Father"就是我们在代码里写死的。

先不谈这个疑问，我们来分析这段代码：

1. Grandson中没有实现eat方法，所以main函数中Grandson的实例执行eat方法是这样的：根据类继承关系自下而上寻找，在Grandson的父类Son类中找到了eat方法，进行调用。
2. 在Son的eat方法的实现中，我们构建了一个`superReceiver`结构体，内部包含了`self`以及`Father`这个类。
3. `objc_msgSendSuper`函数直接去Father类中寻找`eat`方法的实现，并执行（输出）。

现在这段代码是以正常逻辑执行的。



#### 2. `[super xxxx]`真的要直接指明父类？ 

我们使用clang的rewrite指令重写Son.m：

```shell
clang -rewrite-objc Son.m 
```

生成的Son.cpp文件：

```c++
static void _I_Son_eat(Son * self, SEL _cmd) {
    ((void (*)(__rw_objc_super *, SEL))(void *)objc_msgSendSuper)((__rw_objc_super){(id)self, (id)class_getSuperclass(objc_getClass("Son"))}, sel_registerName("eat"));
}
```

这一行到底的代码可读性太差，让我们稍稍分解下（由于语法问题我们作了少量语法修改以通过编译，实际作用与原cpp中一致）：

```objective-c
static void _I_Son_eat(Son * self, SEL _cmd) {
    __rw_objc_super superReceiver = (__rw_objc_super){
        (__bridge struct objc_object *)(id)self,
        (__bridge struct objc_object *)(id)class_getSuperclass(objc_getClass("Son"))};
    
    typedef void *Func(__rw_objc_super *, SEL);
    Func *func = (void *)objc_msgSendSuper;
    
    func(&superReceiver, sel_registerName("eat"));
}
```

先修改Son.m运行起来：

```objective-c
// Son.m

- (void)eat {
//    [super eat];
    
  //_I_Son_eat即为重写的函数
    _I_Son_eat(self, _cmd);
}

// 输出
2017-05-15 00:08:37.782519+0800 TestSuper[7460:3810248] Father eat
```

没有毛病。

重写的代码里构建了一个`__rw_objc_super`的结构体，定义如下：

```objective-c
struct __rw_objc_super { 
	struct objc_object *object; 
	struct objc_object *superClass; 
    // cpp里的语法，忽略即可
	__rw_objc_super(struct objc_object *o, struct objc_object *s) : object(o), superClass(s) {} 
};
```

该结构体与`struct objc_super`一致。之后我们将`objc_msgSendSuper`函数转换为指定参数的函数`func`进行调用。这里请注意`__rw_objc_super superReceiver`中的**第二个值**：`class_getSuperclass(objc_getClass("Son"))`。

该代码直接指明的类是本类：Son类。但是`__rw_objc_super`结构体中的`superClass`并不是本类，而是**通过runtime查找出的父类**。这与我们自己实现的 “直接指明Father为`objc_super`结构体的`super_class`值” 最后达到的效果是一样的。

所以，**`[super xxxx]`肯定要通过指明一个类，可以是父类，也可以是本类，来达到正确调用父类方法的目的！**只不过“直接指明”这件事，编译器会帮我们搞定，我们只管写`super`即可。



## clang rewrite不可靠

### 为何clang不可靠

clang的rewrite功能所提供的重写后的代码并非编译器（LLVM）转换后的代码，如今的编译器在Xcode开启bitcode功能后会生成一种中间代码：LLVM  Intermediate Representation（LLVM IR）。该代码向上可统一大部分高级语言，向下可支持多种不同架构的CPU，具体可查看[LLVM文档](http://llvm.org/docs/LangRef.html)。所以我们的目标是从IR代码求证`super`究竟在做什么事！



### 查看IR代码

终端里cd到Son.m文件所在目录，执行：

```shell
clang -emit-llvm Son.m -S -o son.ll
```

生成的IR代码比较多，我们挑重点进行查看：

```c
%0 = type opaque

// Son的eat方法
define internal void @"\01-[Son eat]"(%0*, i8*) #0 {
  %3 = alloca %0*, align 8    // 分配一个指针的内存，8字节对齐（声明一个指针变量）
  %4 = alloca i8*, align 8    // 分配一个char *的内存（声明一个char *指针变量）
  %5 = alloca %struct._objc_super, align 8    // 给_objc_super分配内存（声明一个struct._objc_super变量）
  store %0* %0, %0** %3, align 8    // 将第一个参数，id self 写入%3分配的内存中去
  store i8* %1, i8** %4, align 8    // 将_cmd写入%4分配的内存中区
  %6 = load %0*, %0** %3, align 8   // 读出%3内存中的数据到%6这个临时变量（%3中存的是self）
  %7 = bitcast %0* %6 to i8*        // 将%6变量的类型转换为char *指针类型，指向的还是self
  %8 = getelementptr inbounds %struct._objc_super, %struct._objc_super* %5, i32 0, i32 0    // 取struct._objc_super变量（%5）中的第0个元素，声明为%8
  store i8* %7, i8** %8, align 8    // 将%7存入%8这个变量中，即把i8* 类型的 self存入了结构体第0个元素中
  %9 = load %struct._class_t*, %struct._class_t** @"OBJC_CLASSLIST_SUP_REFS_$_", align 8    // 声明%9临时变量为struct._class_t*类型，内容为@"OBJC_CLASSLIST_SUP_REFS_$_"
  %10 = bitcast %struct._class_t* %9 to i8*   // 将%9的变量强转为char *类型
  %11 = getelementptr inbounds %struct._objc_super, %struct._objc_super* %5, i32 0, i32 1   // 取struct._objc_super变量（%5）中的第1个元素，声明为%11
  store i8* %10, i8** %11, align 8    // 将%9的变量，即@"OBJC_CLASSLIST_SUP_REFS_$_"存入结构体第1个元素中
  %12 = load i8*, i8** @OBJC_SELECTOR_REFERENCES_, align 8, !invariant.load !7    // 将@selector(eat)的引用放入char *类型的%12变量中

  // 函数调用，传入参数为上述生成的struct._objc_super结构体和 @selector(eat)，调用函数objc_msgSendSuper2
  call void bitcast (i8* (%struct._objc_super*, i8*, ...)* @objc_msgSendSuper2 to void (%struct._objc_super*, i8*)*)(%struct._objc_super* %5, i8* %12)
  ret void
}


@"OBJC_CLASS_$_Son" = global %struct._class_t { 
                                                %struct._class_t* @"OBJC_METACLASS_$_Son",
                                                %struct._class_t* @"OBJC_CLASS_$_Father", 
                                                %struct._objc_cache* @_objc_empty_cache, 
                                                i8* (i8*, i8*)** null,
                                                %struct._class_ro_t* @"\01l_OBJC_CLASS_RO_$_Son" 
                                              }, section "__DATA, __objc_data", align 8

// 直接存放进入struct._objc_super的变量， 内容为@"OBJC_CLASS_$_Son"
@"OBJC_CLASSLIST_SUP_REFS_$_" = private global %struct._class_t* @"OBJC_CLASS_$_Son", section "__DATA, __objc_superrefs, regular, no_dead_strip", align 8
```

IR的语法其实不难记，还是比较好懂的。这里我们只要对照着看即可：

* %1，%2，@xxx之类的都是指代变量，理解为变量名就可以了
* i8指8位的int类型，即1个字节的char类型。i8*就是指char *指针
* alloca指分配内存，理解为声明一个变量即可，如alloca i8*即为一个char *的变量
* %0在开头的代码里说明了是一个不透明的类型，所以%0*就指代一个万能指针，理解为id即可
* store为写入内存
* load为从内存中读取出来
* bitcast为类型转换
* getelementptr inbounds取指定内存偏移

代码中既有汇编的赶脚，又有高级语言的味道。基本上注释都补全了，代码中的逻辑和上文中**我们自己实现的/clang重写的**代码基本相似。但是这里注意`@"OBJC_CLASSLIST_SUP_REFS_$_"`这个变量。

`@"OBJC_CLASSLIST_SUP_REFS_$_"`其实就是对应到`struct objc_super`结构中的第二个元素：`super_class`。在IR代码的%11以及后面那一行就是体现。

而`@"OBJC_CLASSLIST_SUP_REFS_$_"`的定义就是`@"OBJC_CLASS_$_Son"`这个全局变量。`@"OBJC_CLASS_$_Son"`全局变量就是Son这个类对象，里面包含了元类：`@"OBJC_METACLASS_$_Son"`，以及父类：`@"OBJC_CLASS_$_Father"`，以及其他的一些数据。然而，看到这里，我们发现这和我们自己实现的`super`，以及clang重写的`super`都不一样：这里是直接将`[Son class]`作为`struct objc_super`的`super_class`，但是并没有任何调用`class_getSuperclass`的地方...



### 查看汇编源码

但是，这里唯一的一个函数`@objc_msgSendSuper2`貌似与众不同，与我们之前看到的`objc_msgSendSuper`相比多了个2，难道是这个函数在作鬼？那就让我们到官方的objc4-709源码里查询下这个函数（位于`objc-msg-arm64.s`文件中）：

```c
ENTRY _objc_msgSendSuper2
UNWIND _objc_msgSendSuper2, NoFrame
MESSENGER_START

ldp	x0, x16, [x0]		// x0 = real receiver, x16 = class
ldr	x16, [x16, #SUPERCLASS]	// x16 = class->superclass
CacheLookup NORMAL

END_ENTRY _objc_msgSendSuper2
```

这是一段汇编代码，没错，苹果为了提高运行效率，发送消息相关的函数是直接用汇编实现的。

这里我们来简单分析下这个函数：

1. `ldp x0, x16, [x0]`：从x0出读取两个字数据到x0与x16中，根据注释，读取的数据应该是对应的`self`与`[Son class]`。
2. `ldr x16, [x16, #SUPERCLASS]`：将x16的数值+SUPERCLASS值的偏移作为地址，取出该地址的数值保存在x16中。这里的`SUPERCLASS`定义是`#define SUPERCLASS 8`，也就是偏移8位，那么取到的应该就是`@"OBJC_CLASS_$_Father"`这个父类`[Father class]`到x16中。
3. 执行`CacheLookup`函数，参数为NORMAL。

让我们看看`CacheLookup`的定义：

```c
/********************************************************************
 *
 * CacheLookup NORMAL|GETIMP|LOOKUP
 * 
 * Locate the implementation for a selector in a class method cache.
 *
 * Takes:
 *	 x1 = selector
 *	 x16 = class to be searched
 *
 * Kills:
 * 	 x9,x10,x11,x12, x17
 *
 * On exit: (found) calls or returns IMP
 *                  with x16 = class, x17 = IMP
 *          (not found) jumps to LCacheMiss
 *
 ********************************************************************/

#define NORMAL 0
#define GETIMP 1
#define LOOKUP 2

.macro CacheLookup
	// x1 = SEL, x16 = isa
	ldp	x10, x11, [x16, #CACHE]	// x10 = buckets, x11 = occupied|mask
	and	w12, w1, w11		// x12 = _cmd & mask
	add	x12, x10, x12, LSL #4	// x12 = buckets + ((_cmd & mask)<<4)

	ldp	x9, x17, [x12]		// {x9, x17} = *bucket
1:	cmp	x9, x1			// if (bucket->sel != _cmd)
	b.ne	2f			//     scan more
	CacheHit $0			// call or return imp
	
2:	// not hit: x12 = not-hit bucket
	CheckMiss $0			// miss if bucket->sel == 0
	cmp	x12, x10		// wrap if bucket == buckets
	b.eq	3f
	ldp	x9, x17, [x12, #-16]!	// {x9, x17} = *--bucket
	b	1b			// loop

3:	// wrap: x12 = first bucket, w11 = mask
	add	x12, x12, w11, UXTW #4	// x12 = buckets+(mask<<4)

	// Clone scanning loop to miss instead of hang when cache is corrupt.
	// The slow path may detect any corruption and halt later.

	ldp	x9, x17, [x12]		// {x9, x17} = *bucket
1:	cmp	x9, x1			// if (bucket->sel != _cmd)
	b.ne	2f			//     scan more
	CacheHit $0			// call or return imp
	
2:	// not hit: x12 = not-hit bucket
	CheckMiss $0			// miss if bucket->sel == 0
	cmp	x12, x10		// wrap if bucket == buckets
	b.eq	3f
	ldp	x9, x17, [x12, #-16]!	// {x9, x17} = *--bucket
	b	1b			// loop

3:	// double wrap
	JumpMiss $0
	
.endmacro
```

具体的`CacheLookup`我们这里就不再展开了，我们只关心这里是从哪里查找方法的。在注释中，明确说到这是一个“去类的方法缓存中寻找方法实现”的函数，参入的参数是x1中的selector，x16中的class**(class to be searched 就是说从这个类中开始查找)**，而这时候的x16，恰恰是我们刚才在`_objc_msgSendSuper2`存入的父类`[Father class]`，因此，**方法会从这个类中开始查找**。



### 整体调用流程

从手动实现->查看clang重写->查看IR码->查看汇编源码这几个过程分析下来，我们总算是把这条真实的`super`调用链路搞搞清楚了：

1. 编译器指定一个` struct._objc_super`结构体， 结构体中`self`为接收对象，**直接指明**自身的类为结构体第二个class类型的值。
2. 调用`_objc_msgSendSuper2`函数，传入上述` struct._objc_super`结构体。
3. 在`_objc_msgSendSuper2`函数中直接通过偏移量**直接查找**父类。
4. 调用`CacheLookup`函数去父类中查找指定方法。



## 结论

所以，从真实的IR代码中，**`super`关键字其实是直接指明本类Son，再结合`_objc_msgSendSuper2`函数直接获取父类去查找方法的，而并非像clang重写的那样，指明本类，再通过runtime查找父类。**

其实先指明本类，再通过runtime查找父类，也是没有问题的，这还可以避免一些运行时“更改父类”的情况。但是LLVM的做法应该是有他的道理的，可能是出于性能考虑？