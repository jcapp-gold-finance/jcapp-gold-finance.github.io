title: CAReplicatorLayer创建加载动画
type: iOS
---

> 作者：周思思

* **介绍：**官方文档里写的是：**"A layer that creates a specified number of copies of its sublayers (the source layer), each copy potentially having geometric, temporal, and color transformations applied to it."**
  简单的说它可以拷贝子layer，并和拷贝的子layer拥有一样的功能，比如属性、位置、形变、动画。进行一些不同的属性设置我们可以让它完成一些非常炫酷的动画。


* **代码参考：**<http://code.cocoachina.com/view/133667>
* **效果：**

 ![img](/img/replicatorLayer/replicatorLayer1.gif)

## CAReplicatorLayer的属性

想要了解一个对象最简单的入手点就是了解它的属性和方法

**CAReplicatorLayer** 的属性和方法并不多，主要是：

![MacDown Screenshot](/img/replicatorLayer/replicatorLayer2.jpg)



## 具体实现

### 1.创建一个layer
![MacDown Screenshot](/img/replicatorLayer/replicatorLayer3.jpg)

### 2.给layer添加形变动画
![MacDown Screenshot](/img/replicatorLayer/replicatorLayer4.jpg)

### 3.利用CAReplicatorLayer拷贝多个layer进行动画
![MacDown Screenshot](/img/replicatorLayer/replicatorLayer5.jpg)

### 4.将创建的CAReplicatorLayer添加到所需的layer上
![MacDown Screenshot](/img/replicatorLayer/replicatorLayer6.jpg)

#### 就这样，所需的加载效果的动画就完成了。



## 分享
分享几个讲解CAReplicatorLayer比较详细的blog以及demo，需要的可以再去加深了解，它能完成很多强大的动画

<http://www.ios-animations-by-emails.com/posts/2015-march#tutorial>
<https://zsisme.gitbooks.io/ios-/content/chapter6/careplicatorLayer.html>
<https://github.com/zhoubo2015/CAReplicatorLayerDemo>
