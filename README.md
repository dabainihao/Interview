### 1. #import和#include的区别,@class代表什么
* "#import"和"#include"都是头文件包含,"#import"能保证只包含一次,不会发生重复包含,
 "#include"是c语言中的包含头文件为了保证不被重复包含,一般加上条件编译

 ```c
    #ifndef _FS                         
    #define _FS
    #include"header.h"
    #endf
 ```
 
* @class 类前向申明,就是相当于告诉编辑器有这个类,但类的定义在后面提供,放置循环引入

### 2.谈谈Obj-c的内存管理方式及过程
* obj-c内存管理采用引用计数机制, 在MRC下手动管理,在ARC下,我们不需要手动的添加retain与
release, 但内存管理法则是一样的
* 内存管理的必要性: 因为移动设备的内存极其有限,所以每个APP所占用的内存也是有限制的,
当某个app所占内存过多时,系统就会发出内存警告,这时需要回收一些不再使用的内存空间,比如一些不再
使用的对象与变量
* 管理范围 : 任何集成与NSObject的对象,对基本数据类型管理无效.因为对象与其他数据
类型在系统的存储的空间不一样,局部变量储存在栈中,对象存在堆区,当代码块结束时候这个代码
块中所涉及的局部变量被回收,指向对象的指针也被回收,此时对象没有了指针指向, 单依然存在
内存中, 造成内存泄露
* 内存管理的原则 : (谁创建谁释放)
    * 只要有人在使用这个对象,这个对象就不会被回收;如果你想使用这个对象,你就应该让给这个
对象引用计数+1,如果你不想使用这个对象,引用计数就应该-1
    *  谁创建谁release,如果你通过alloc,new,copy来创建了一个对象,那么你就必须调用release或者autorelease方法
,不是你创建的就不用你去负责
    * 谁retain 谁release,只要你调用了这个对象,不管这个对象什么时候生成,都应该调用release
* 内存管理的过程
    * 在MRC下，对于需要手动释放的对象的内存管理，我们通过release使对象引用计数-1，若其引用计数变成0，则对象会被立刻释放掉。对于autorelease交给自动释放池管理的对象，每个runloop循环结束就会去自动释放池中使所有autorelease类型对象的引用计数减一，若变成0，则释放之。
    * 在ARC下，我们没有不能直接调用retain/release来管理释放，都是交给自动释放池来管理的。因此，若创建临时变量，想要使用完就释放之，需要在临时变量放到新创建的自动释放池里，这样就可以使用完后就到达了自动释放池的一个循环，就会去使对象引用计数减一，变成0后释放之。
最后：对于交给自动释放池管理的对象，是在每个run loop事件循环结束时才会去使对象引用计数减一，此时引用计数为0的才会得到释放。

### 3. Obj-c 有私有方法吗,私有变量呢
* objective-c – 类里面的方法只有两种, 静态方法和实例方法
* 没有实实存在的私有方法。通常所谓的私有方法就是放在.m文件中声明和实现，外部不能直接看到而已，但是若我们知道有这么一个API，我们是可以调用的。比如，在苹果上架会因为使用了苹果的所谓的私有API而被拒，而这个所谓的私有API就是指苹果没有公司出来，但是我们通过其它方式可以看到苹果的内部有这样一个API可以实现某些不公开的功能。
通过前向引用就可以调用
* 私有变量是有的
```c
    @interface Person: NSObject {
        @private NSString *_privateName;
    }
```
默认是受保护的,外部也不能给这个变量赋值
```c
@interface Person: NSObject {
         NSString *_privateName;
    }
``` 

### 4.Objective-C有多继承吗？没有的话用什么代替？Cocoa中所有的类都是NSObject的子类？
* oc 中没有多继承, 用代理来替代
* 官网关于Cocoa框架有如下介绍：
Cocoa supplies two root classes: NSObject and NSProxy.
* 根类是NSOjbect，它元类的isa指针指向的是NSObject
<img src="http://www.henishuo.com/wp-content/uploads/2015/12/inherit.png" width="400px" height="400px">
* NSProxy 使用来做消息转发

### 5.浅拷贝与深拷贝的区别是什么
* 用最简单的话说：浅拷贝就是指针拷贝（指向原有内存空间），而深拷贝是内容拷贝（有新的内存空间）。
或者说：浅复制并不拷贝对象本身而仅仅是拷贝指向对象的指针；深复制是直接拷贝整个对象内存到另一块内存中。
* 更详细地可以阅读这篇文章：[iOS深拷贝与浅拷贝](http://www.henishuo.com/ios-shadowcopy-deepcopy/)
* 注意区分可变拷贝与不可变拷贝

### 6.属性readwrite、readonly、assign、retain、copy、nonatomic各是什么作用，在哪种情况下用？

<p>作用分别为: </p>

* readwrite：代表可读可写，会生成getter和setter方法
* readonly：代表只读，只生成getter方法，不会生成setter方法
* assign：代表普通赋值，通常用于非对象类型
* retain：MRC下才能手动使用，与ARC下的strong一样，指定强引用，引用计数加1
* copy：代表拷贝，也是强引用，引用计数加1，进行指针拷贝
* nonatomic：代表非原子操作，非线程安全，但可提高性能 

<p> 使用场景: </p>

* readwrite：默认就是，通过不用显示指定，需要生成setter和getter时使用
* readonly：当不希望生成setter时使用
* assign：通常是非对象类型使用
* retain：MRC下才能使用，表示对象强引用
* copy：生成不可变对象、需要拷贝时使用
* nonatomic：不要求线程安全时使用，可提高性能，通常都会使用
