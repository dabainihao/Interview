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

###14.说一下类和类别,类扩展的的区别
1. 类
	* 对象是对客观事物的抽象，类是对对象的抽象。类是一种抽象的数据类型,它们的关系是，对象是类的实例，类是对象的模板
	* <p> 下图是关系</p>
	 <img src = "http://d.hiphotos.baidu.com/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=a23a37d36b061d95694b3f6a1a9d61b4/e4dde71190ef76c6acd4b5c09816fdfaae5167d2.jpg" width = 400px , height = 300px>
2. 类扩展(Extension)
	*  能为某个类附加额外的属性，成员变量，方法声明
	* 一般的类扩展写到.m文件中
	* 一般的私有属性写到类扩展
<p> 具体的使用</p>
<pre>
	@interface Mitchell()
	//属性
	//方法
	@end
</pre>	 

<p style = "text-indent: 2em""> 3 .分类(Category) </p>
	* 将类的实现分散到多个不同文件或多个不同框架中。
	* 创建对私有方法的前向引用。
	* 向对象添加非正式协议。
* <p> 注意事项 </p>
 	* 分类只能增加方法（包括类方法和对象方法），不能增加成员变量
 	* 在分类方法的实现中可以访问原来类中的成员变量；
	* 分类中可以重新实现原来类中的方法，但是会覆盖掉原来的方法，导致原来的方法无法再使用（警告）；
	* 方法调用的优先级：分类->原来的类->父类，若包含有多个分类，则最后参与编译的分类优先；
	* 在很多的情况下，往往是给系统自带的类添加分类，如NSObject和NSString，因为有的时候，系统类可能并不能满足我们的要求。
	* 在大规模的应用中，通常把相应的功能写成一个分类，可以有无限个分类，对原有类进行扩充，一般分模块写，一个模块一个分类。
	
###15. 现在大部分应用都采用ARC,那么是否就意味着我们不用再去管理内存了,如果需要的话,举几个简答你的例子
* 在使用block的时候如果block内部用到了一些当前控制器的属性, 要用__weak修饰一下,放置循环引用
* 在调用一些C语言库的时候,需要用其对应的释放啊,例如绘制图片
* 我们需要在控制器的的dealloc方法中移除动画,
* 子线程中没有办法访问主线程的autoreleasepool,需要给子线程添加autoreleasepool,保证在子线程中初始化的变量及时释放

###16. 提供几种方式去实现线程1,线程2,线程3的执行顺序
* NSOperation和NSOperationQueue 结合使用, 在NSOperation子类创建对象添加依赖
* 使用GCD中dispatch_barrier_asyn(dispatch_queue_t queue,block) 这个函数的
* 使用GCD中线程组,dispatch_group_notify 这个函数

###17. 请说明一下修饰符的作用
* static C中static用在局部变量，局部变量在代码块结束后将不会回收，下次使用保持上次使用后的值；static用在方法与全局变量，表示该方法与全局变量只在本文件中有效，外部无法使用extern引用该全局变量或方法。
* __block 在block中修改外部变量的值
* __bridge 桥接oc c
* const const修饰的数据类型是指常类型,常类型的变量或对象的值是不能被更新的


 ###18. 请简述UIView与CALayer有什么不同
* UIView是iOS系统中界面元素的基础，所有的界面元素都继承自它。它本身完全是由CoreAnimation来实现的（Mac下似乎不是这样）。它真正的绘图部分，是由一个叫CALayer（Core Animation Layer）的类来管理。UIView本身，更像是一个CALayer的管理器，访问它的跟绘图和跟坐标有关的属性，例如frame，bounds等等，实际上内部都是在访问它所包含的CALayer的相关属性。
* 首先UIView可以响应事件，Layer不可以.UIView集成于UIResponder,在UIResponder中定义了处理各种事件和事件传递的接口, 而CALayer直接继承 NSObject，并没有相应的处理事件的接口。 
* 3.UIView主要是对显示内容的管理而 CALayer 主要侧重显示内容的绘制。
 <br>
	[参考链接](http://www.cocoachina.com/ios/20150828/13244.html)
	
### 19 .内存的分区
* 栈区(stack) 由编译器自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
* 堆区（heap） — 一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收 。（ios中alloc都是存放在堆中）
* 全局区（静态区）（static）—，全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。 - 程序结束后由系统释放。注意：全局区又可分为未初始化全局区：.bss段和初始化全局区：data段。
* 常量区—常量字符串就是放在这里的。 程序结束后由系统释放
* 代码区—存放函数体的二进制代码。
编码注意：当一个app启动后，代码区，常量区，全局区大小已固定，因此指向这些区的指针不会产生崩溃性的错误。而堆区和栈区是时时刻刻变化的（堆的创建销毁，栈的弹入弹出），所以当使用一个指针指向这两个区里面的内存时，一定要注意内存是否已经被释放，否则会产生程序崩溃（编程中很常见）。

### 20.术语
* 密钥：密钥是一种参数，它是在明文转换为密文或将密文转换为明文的算法中输入的参数。密钥分为对称密钥与* 非对称密钥（也可以根据用途来分为加密密钥和解密密钥）
* 明文：没有进行加密，能够直接代表原文含义的信息
* 密文：经过加密处理处理之后，隐藏原文含义的信息
* 加密：将明文转换成密文的实施过程
* 解密：将密文转换成明文的实施过程

### 21.哈希算法
* 概念: 哈希算法将任意长度的二进制值映射为较短的固定长度的二进制值，这个小的二进制值称为哈希值。

* 哈希值是一段数据唯一且极其紧凑的数值表示形式。数据的哈希值可以检验数据的完整性。一般用于快速查找和加密算法。

* 典型的的哈希算法有：MD2、MD4、MD5 和 SHA-1等。

### 22.MD5

MD5：Message Digest Algorithm MD5（中文名为消息摘要算法第五版）为计算机安全领域广泛使用的一种散列函数，用以提供消息的完整性保护。

MD5算法具有以下特点：
1、 压缩性：任意长度的数据，算出的MD5值长度都是固定的（16进制，32位）。
2、容易计算：从原数据计算出MD5值很容易。
3、抗修改性：对原数据进行任何改动，哪怕只修改1个字节，所得到的MD5值都有很大区别。
4、强抗碰撞：已知原数据和其MD5值，想找到一个具有相同MD5值的数据（即伪造数据）是非常困难的


















	
