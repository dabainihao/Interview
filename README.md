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

### 7.控制器的生命周期,及处理的操作
1. alloc  创建对象,分配空间
2. init (initWithNibName) 初始化对象,初始化数据
3. loadView 从nib载入视图,通常情况下不需要干涉这一步,除非没有使用xib创建视图
4. viewDidLoad 载入完成,可以进行自定义数据以及动态创建其他控件
5. viewWillAppear 视图将出现在屏幕之前,马上这个视图就会展现在屏幕上
6. viewDidAppear 视图已在屏幕上渲染完成
<p>当一个视图从屏幕上移除并销毁的时候执行的顺序,这个顺序差不多跟上面的相反</p>
1. viewVillDisappear 视图将从屏幕上移除之前执行
2. viewDidDisappear 视图已经被从屏幕上移除,用户看不到这个视图了
3. dealloc 视图被销毁,此处需要对你在init和viewDidLoad中的对象进行释放
<p> 执行的操作 </p>
* -(void)viewDidLoad 
	* 当一个app在载入时会先通过调用loadView方法或者载入IB中创建的初始化界面的方法,将视图载入到内存中.然后在viewDidLoad中进行进一步的设置,通常,我们对于各种初始化数据的载入,初始设定等很多内容都在这个方法中实现,这是个很常用并且很重要的方法,这个方法只会在App刚开始调用的时候调用一次,以后都不会再调动它了,所有只能用来做初始化设置
	* view从xib
* -(void)viewWillAppear:(BOOL)animated;
	* 系统在载入所有数据后,将在屏幕上显示视图,这时会先调用这个方法,通常我们会利用这个方法,对即将显示的视图做进一步的设置,例如我们可以利用这个方法来设置设备不同方向时改如何显示
	* 在app有多个视图时,在试图间切换的时候,并不会再次载入viewDidLoad方法,所以如果在调入视图时需要对数据进行更新,我们只能在这个方法中实现
* -(void)viewDidAppear:(BOOL)animated；
	* 	视图已在屏幕上渲染完毕,当我们在有时候我们不能在viewWillAppear方法里,在视图进行更新,我们可以在这里对视图进行进一步的设置
* -(void)viewWillDisappear:(BOOL)animated；
	* 在视图变换时,当前视图即将被移除,或者被覆盖时,会调用这个方法进行一些善后的处理和设置,需要注意的是当我们被按home键,这个方法不会被执行,app显示的view,仍是挂起时候的view
* -(void)viewDidDisappear:(BOOL)animated	
	* 我们可以重写这个方法,对已经消失,或者被覆盖,或者已经隐藏了的视图做一些其他操作
	
### 8.viewcontroller在从创建到销毁的一个完整生命周期中依次调用了哪些方法
<div>视图的生命周期其实可以理解为Load-Present-Hidden(加载-展现-隐藏)三个阶段，如果从ViewController中方法中执行的顺序来看，顺序应该是这样的:
loadView→viewDidLoad→viewWillAppear→viewDidAppear→viewWillDisappear→viewDidDisappear→dealloc</div>
***
<div>loadView：一般情况下不用用到，除非需要重写设置View；</div>
<div>viewDidLoad/dealloc:视图加载完成之后的设置和视图销毁的时候调用；</div>
<div>viewWillAppear/viewWillDisappear:视图即将呈现和视图即将消失；</div>
<div>viewDidAppear/viewDidDisappear:视图展现在屏幕的时候和视图完全消失在屏幕的时候调用，默认不做任何操作； </div>
<div> 官网给出的示例图</div>
<br>
<img src = "http://img2.tuicool.com/JR7vy2e.png!web" width =400px>	
### 9.什么情况下使用block会造成循环引用
* 在block 中我们通常情况下会用copy修饰,当block被copy的时候,会对block中的用到的对象进行强引用(arc),或者引用计数+1(mrc)
<div>错误的情况 </div>
 <pre>
 @property(nonatomic, readwrite, copy) completionBlock completionBlock;
 self.completionBlock = ^ {
        if (self.success) {
            self.success(self.responseData);
        }
    }
};
 </pre>

* 10.当block为其他对象的一个属性,block又引用了这个对象的其他属性,那么就会对这个变量本身产生强应用，那么变量本身和他自己的Block属性就形成了循环引用,arc下修改成这个样子
<pre> 
@property(nonatomic, readwrite, copy) completionBlock completionBlock;
__weak typeof(self) weakSelf = self;
self.completionBlock = ^ {
    if (weakSelf.success) {
        weakSelf.success(weakSelf.responseData);
    }
};
</pre>
* 注意事项 :也就是生成一个对自身对象的弱引用，如果是倒霉催的项目还需要支持iOS4.3，就用__unsafe_unretained替代__weak。如果是non-ARC环境下就将__weak替换为__block即可。non-ARC情况下，__block变量的含义是在Block中引入一个新的结构体成员变量指向这个__block变量，那么__block typeof(self) weakSelf = self;就表示Block别再对self对象retain啦，这就打破了循环引用。
	
###11.在xib中使用AutoLayou布局UIScrollerView需要注意什么
1. 给scrollerView设置一个view作为contentView,并把添加到scrollerView上的空间添加带contentView上
2. 为scrollerView设置滑动的方向(左右或者上下),并设置contentView的size
3. 添加导航栏的控制器,设置控制器的automaticallyAdjustsScrollViewInsets为NO(默认为yes)

### 12.如何修改一个UITextFiled的Placehoder字体颜色
<p>1. 通过kvc</p>
<pre>
textField.placeholder = @"username is in here!";  //设置placehoder的字
[textField setValue:[UIColor redColor] forKeyPath:@"_placeholderLabel.textColor"]; // 修改颜色  
[textField setValue:[UIFont boldSystemFontOfSize:16] forKeyPath:@"_placeholderLabel.font"];  //修改字体大小
</pre>
* 注意 : 一般修改只读的,或者封装空间里某个子视图,用kvc

### 13.解释一下iOS应用沙盒机制,沙盒路径有几种,分别对应路径下文件的特点是什么
* iOS中的沙盒机制是一种安全体系,每个iOS程序都有一个独立的文件系统(存储空间),只能在自己的文件系统进行操作,这个区域叫做沙盒.所有非代码的文件都应该保存在这个地方,例如plist文件,图像等
* 沙盒的目录结构	<br>
<img src="http://7xrv8n.com1.z0.glb.clouddn.com/62317-e49c692cb5d9ebb3.png" width = "240px" height = "120px">
* 每个文件夹的作用
	* Documents保存应用程序重要的数据文件和用户数据文件,iTunes同步时会备份这个目录
	* /Library/Caches 保存应用程序使用时产生的支持文件和缓存文件，还有日志文件最好也放在这个目录。iTunes 同步时不会备份该目录。
	* /Library/Preferences 保存应用程序的偏好设置文件（使用 NSUserDefaults 类设置时创建，不应该手动创建）
 	* /tmp/ 保存应用运行时所需要的临时数据，iphone 重启时，会清除该目录下所有文件。




 




















	

