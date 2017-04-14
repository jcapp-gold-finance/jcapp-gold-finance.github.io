title: 链式编程简单的使用
type: iOS
---

> 作者：邵雄华

  链式编程－顾名思义，链式，连贯性为其主要特征，放在编程领域来讲，说简单点就是把一系列的代码执行动作串联起来，不用单独一个一个的执行
在使用 Masonry 和 SDAutoLayout 框架实现自动布局的时候，进行了对比

#### Masonry:
```
[View mas_makeConstraints:^(MASConstraintMaker *make) {
      make.top.equalTo(anotherView);
      make.left.equalTo(anotherView);
      make.width.mas_equalTo(@60);
      make.height.mas_equalTo(@60);
}];
```

#### SDAutoLayout:
```
View.sd_layout
.leftSpaceToView(self.view, 10)
.topSpaceToView(self.view, 80)
.heightIs(130)
.widthRatioToView(self.view, 0.4);  
```
相比较两种实现方法来看，SDAutoLayout相对来说更简单，代码量更少，也更容易理解和使用！

这里说一下我对这两个框架的一点理解
Masonry 和 SDAutoLayout 实现思路：链式编程思想。

#### 链式编程
* 链式编程思想：是将多个操作（多行代码）通过点号(.)链接在一起成为一句代码,使代码可读性好。a(1).b(2).c(3)

* 链式编程特点：方法的返回值是block,block必须有返回值（本身对象），block参数（需要操作的值）

* 代表：Masonry框架、SDAutoLayout框架

### 链式编程另一个关键点－－block：

##### Block的定义格式

返回值类型(^block变量名)(形参列表) = ^(形参列表) {
};
调用Block保存的代码
block变量名(实参);

默认情况下,Block内部不能修改外面的局部变量
Block内部可以修改使用__block修饰的局部变量

##### Block的模式

1.无参数无返回值的Block
2.有参数无返回值的Block
3.有参数有返回值的Block


Block简单用法举例

无参数无返回值的Block

```
/**
 *  无参数无返回值的Block
 */
- (void)func1 {
    /**
     *  void ：就是无返回值
     *  emptyBlock：就是该block的名字
     *  ()：这里相当于放参数。由于这里是无参数，所以就什么都不写
     */
    void (^emptyBlock)() = ^(){
        NSLog(@"无参数,无返回值的Block");
    };
    emptyBlock();
}
有参数无返回值的Block
```
```
/**
     *  调用这个block进行两个参数相加
     *
     *  @param int 参数A
     *  @param int 参数B
     *
     *  @return 无返回值
     */
    void (^sumBlock)(int ,int ) = ^(int a,int b){
        NSLog(@"%d + %d = %d",a,b,a+b);
    };
    /**
     *  调用这个sumBlock的Block，得到的结果是20
     */
    sumBlock(10,10);
有参数有返回值的Block
```
```
/**
     *  有参数有返回值
     *
     *  @param NSString 字符串1
     *  @param NSString 字符串2
     *
     *  @return 返回拼接好的字符串3
     */    
    NSString* (^logBlock)(NSString *,NSString *) = ^(NSString * str1,NSString *str2){
        return [NSString stringWithFormat:@"%@%@",str1,str2];
    };
    //调用logBlock,输出的是 我是Block
    NSLog(@"%@", logBlock(@"我是",@"Block"));
```

##### Block结合typedef使用

自己定义一个Block类型，用定义的类型去创建Block，更加简单便捷。

这里举一个例子 UIButton 的链式编程实现

###### CHButton.h

```
//
//  CHButton.h
//  testDemo
//
//  Created by 邵雄华 on 2017/4/13.
//  Copyright © 2017年 sxh. All rights reserved.
//

#import <UIKit/UIKit.h>

@class CHButton;

//typedef一个返回值为CHButton* 参数为NSString* 的名字为ChainButtonStringBlock的block,方便以后快速定义该类型block

typedef CHButton *(^ChainButtonStringBlock)(NSString *aName);

typedef CHButton *(^ChainButtonIntegerBlcok)(NSUInteger aNumber);

typedef CHButton *(^ChainButtonColorBlock)(UIColor *aColor);

typedef CHButton *(^ChainButtonFrameBlock)(CGRect aframe);

@interface CHButton : UIButton


- (ChainButtonStringBlock)imageName;

- (ChainButtonStringBlock)title;

- (ChainButtonIntegerBlcok)titleFont;

- (ChainButtonColorBlock)textColor;

- (ChainButtonFrameBlock)btnframe;


//该方法为工厂方法,能够快速创建一个ChainButton,在一个参数为block的方法中一次性设置好你需要的ChainButton
+ (CHButton *)makeJJButton:(void (^)(CHButton *))block;


@end

```

###### CHButton.m

```
//
//  CHButton.m
//  testDemo
//
//  Created by 邵雄华 on 2017/4/13.
//  Copyright © 2017年 sxh. All rights reserved.
//

#import "CHButton.h"

@implementation CHButton

- (ChainButtonStringBlock)imageName{
    return ^CHButton *(NSString *aName){
        [self setImage:[UIImage imageNamed:aName] forState:UIControlStateNormal];
        NSLog(@"imageName");
        return self;
    };
}

- (ChainButtonStringBlock)title{
    return ^CHButton *(NSString *aName){
        [self setTitle:aName forState:UIControlStateNormal];
        NSLog(@"title");
        return self;
    };
}

- (ChainButtonIntegerBlcok)titleFont{
    return ^CHButton *(NSUInteger aNumber){
        self.titleLabel.font = [UIFont systemFontOfSize:aNumber];
        NSLog(@"titleFont");
        return self;
    };
}

- (ChainButtonColorBlock)textColor{
    return ^CHButton *(UIColor *aColor){
        [self setTitleColor:aColor forState:UIControlStateNormal];
        NSLog(@"textColor = %@",aColor);
        return self;
    };
}

- (ChainButtonFrameBlock)btnframe{
    return ^CHButton *(CGRect btnframe){
        [self setFrame:btnframe];
        return self;
    };
}

+ (CHButton *)makeJJButton:(void (^)(CHButton *))block{
    CHButton * button = [[CHButton alloc] init];
    block(button);
    return button;
}


@end
```

##### 使用方法:利用工厂模式创建一个CHButton
```
[CHButton makeJJButton:^(CHButton *button) {
        button.title(@"xixixi").imageName(@"abc").titleFont(20).textColor([UIColor orangeColor]).btnframe(CGRectMake(100, 100, 100, 100));
        button.frame = CGRectMake(100, 250, 100, 100);
        [self.view addSubview:button];
        [button addTarget:self action:@selector(didClick:) forControlEvents:UIControlEventTouchUpInside];
    }];
```


### 总结
链式编程要返回自身，每一个链式方法都必须返回明确的类型才能一直点下去，使用protocol或继承都不行，必须在每一个方法的声明时明确返回对象的类型。所以链式文件中不但有该类的方法声明，也有父类的方法声明。

后续会对更多的类进行链式的一种封装，提高开发的效率。
