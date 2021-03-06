# 深圳市神州路路通网络科技有限公司Objective-C编码规范

此文档描述了深圳市神州路路通网络科技有限公司iOS开发团队的编程规范。



## 参考文档

此编码规范在制定的时候参考和借鉴了以下这些优秀的Objective-C编码风格指南：

* [Coding Guidelines for Cocoa](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Objective-C Style Guide](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [Daniel's Objective-C Coding Style Guidelines](http://www.cocoachina.com/bbs/read.php?tid=36545&keyword=%B1%E0%C2%EB%B9%E6%B7%B6)
* [raywenderlich.com Objective-C style guide](https://github.com/raywenderlich/objective-c-style-guide)

## 目录

* [代码结构](#代码结构)
* [代码格式化](#代码格式化)
* [注释](#注释)
* [命名规范](#命名规范)
	* [类名/类别名/协议](#类名类别名协议) 
	* [方法](#方法)
	* [函数](#函数)
	* [常量](#常量)
	* [变量](#变量)
	* [通知和异常](#通知和异常)
* [布尔值](#布尔值)
* [条件语句](#条件语句)
* [初始化方法](#初始化方法)
* [CGRect函数](#CGRect函数)
* [Xcode工程结构](#Xcode工程结构)


## 代码结构

实现文件中的代码结构，提倡以下约定：

* 用`#pragma mark -`将函数或方法按功能进行分组。
* dealloc方法放到实现文件的最顶部。
	> 这样是为了时刻提醒你要记得释放相关资源。

* delgate或协议相关方法放到一般内容之后。

	```objc
	#pragma mark - Lifecycle
	
	- (void)dealloc {}
	- (instancetype)init {}
	- (void)viewDidLoad {}
	- (void)viewWillAppear:(BOOL)animated {}
	- (void)didReceiveMemoryWarning {}
	
	#pragma mark - Custom Accessors
	
	- (void)setCustomProperty:(id)value {}
	- (id)customProperty {}
	
	#pragma mark - Protocol conformance
	#pragma mark - UITextFieldDelegate
	#pragma mark - UITableViewDataSource
	#pragma mark - UITableViewDelegate
	
	#pragma mark - NSCopying
	
	- (id)copyWithZone:(NSZone *)zone {}
	
	#pragma mark - NSObject
	
	- (NSString *)description {}
	```

## 代码格式化

* 只用空格缩进，1个TAB = 4个空格字符
	> 在XCode->Preferences->Text Editing->Indentation中进行如下设置：
	>
	> 1. Prefer indent using: 选择 Spaces
	> 2. Tab key：选择 Intents in leading whitespace
	> 3. 所有需要填写空格数目的地方都设置成4个
	> 
	> ps. 设置成4个，是因为Xcode的默认缩进是4个空格。大量遗留代码也都是采用的缩进4个空格。

* 建议：每行代码的长度最多不超过100个字符
	> 为了防止代码过长，也为了兼顾Macbook上的排版效果，将每行长度限制成100个字符。
	>
	>勾选XCode->Preferences->Text Editing->Editing，并将长度设置成100个字符来打开行宽指示。
	> 
	> ps. Google倡导的每行80个字符有点少，会带来更频繁的换行，因此增加到100个字符。

* 建议：尝试将单个函数或方法的实现代码控制在30行内
	> 如果某个函数或方法的实现代码过长，可以考量下是否可以将代码拆分成几个小的拥有单一功能的方法。
	>
	> ps. 30行是在13寸macbook上XCode用14号字体时，恰好可以让一个函数的代码做到整屏完全显示的行数。

* 建议：将单个实现文件里的代码行数控制在500～600行内
	> 为了简洁和便于阅读，建议将单个实现文件的代码行数控制在500～600行以内最好。
	> 
	> 当接近或超过800行时，就应当开始考虑分割实现文件了。
	>
	> 最好不要出现代码超过1000行的实现文件。
	>
	> 我们一般倾向于认为单个文件代码行数越长，代码结构就越不好。而且，翻代码翻的手软啊。
	> 
	> 可以使用Objective-C的Category特性将实现文件归类分割成几个相对轻量级的实现文件。
	> 
	> 可以勾选上XCode->Preferences->Text Editing->Editing中的Line numbers，开启行号提示。

* 实现文件中，函数实现或方法实现之间必须至少有一行空行
	> 没有空行，代码过长后，全粘在一起，很影响阅读。
	
	```objc
	//禁止的
	- (void)loadView
	{
		//load view...
	}
	- (void)viewDidLoad
	{
		[super viewDidLoad];
		
		//Do Something...
	}
	
	
	//正确的
	- (void)loadView
	{
		//load view...
	}
	
	- (void)viewDidLoad
	{
		[super viewDidLoad];
		
		//Do Something...
	}
	```
	
* 重载父类方法时，遇到必须调用父类方法时。调用super的代码和重载的代码之间留一行空行。
	> 这样做是为了便于区分出对super的调用。
	> 通常在iOS SDK中，有许多方法在重载的时候，都要求调用super。有时候忘记调用super就会出现行为怪异的bug。
	> 因此这里要求将调用super的代码区隔开来，方便阅读，也方便查找是否忘记了对super的调用。

	```objc
	- (void)viewWillDisappear:(BOOL)animated
	{
	    [super viewWillDisappear:animated];
	    
	    //空一行，将super方法的调用和重载代码区隔开来。
	    [NSObject cancelPreviousPerformRequestsWithTarget:self];
	}
	```

* 实现文件中，函数体的左花括号另起一行，不和函数名同行
	> 此条是为了和XCode模板生成的文件的代码风格保持一致。

	```objc
	//赞成的
	- (void)didReceiveMemoryWarning 
	{
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	
	//不赞成的
	- (void)didReceiveMemoryWarning {
	
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	```

* 其他地方(`if`/`else`/`while`/`switch`等)，左花括号不单独另起一行。左花括号后面紧接着的代码块超过5行后，代码块和括号之间要有一行空行；代码块小于5行可以不空行
	> 此条是为了和XCode自动代码补全生成的代码风格保持一致。


	```objc
	//赞成的
	- (void)didReceiveMemoryWarning 
	{
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	    
	    if (somethCondtion) {
	    
	        //DO Something. 
	    }
	}
	
	//不赞成的
	- (void)didReceiveMemoryWarning 
	{
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	    
	    if (somethCondtion) 
	    {
	        //DO Something
	    }
	}
	```

* 建议: if/else中，else与上一条分支语句的右括号之间需要换行
	> 此条是为了防止else和上一个分支的代码块挨在一起，影响阅读，所以建议要换行。
	> 
	> 换行后，也便于快速定位到else分支。


	```objc
	//赞成的
	if (a > 0) {
	
		//Do Something
	} 
	else {
	
		//Do Something
	}
	
	//不赞成的
	if (a > 0) {
	
		//Do Something
	} else {
	
		//Do Something
	}
	```
	
* 如果需要手动使用`@synthesize` 或 `@dynamic`，每行只能定义一个属性

* 方法调用中，如果block参数需要换行时，block结尾的花括弧要和声明block那一行的第一个字符对齐

	```objc
	[operation setCompletionBlock:^{
	    [self.delegate newDataAvailable];
	}];
	```

* 如果方法调用中部分的代码过长，造成内嵌的block代码缩进过长，可以适当的增加手动换行，以减少代码缩进

	```objc
	//如以下代码在loadWindowWithCompletionBlock前加了手动换行，是被提倡的：
	[[SessionService sharedService]
	    loadWindowWithCompletionBlock:^(SessionWindow *window) {
	    
	        if (window) {
	            [self windowDidLoad:window];
	        } 
	        else {
	            [self errorLoadingWindow];
	        }
	}];
	```


## 注释

* 注释应该尽量保持简洁，代码应该尽量达到能自我解释的程度
	> 当然用于生成文档的注释除外，用于生成文档的注释要尽量详细，特别是你的接口可能有副作用的时候，要注释清楚。

* 注释必须和代码保持同步。不要出现代码修改了，注释不更新的情况

* 对函数或API接口的注释，都采用[Javadoc](http://en.wikipedia.org/wiki/Javadoc)风格规范
	> 因为XCode5支持直接将Javadoc风格的注释生成文档。
	> 
	> 也有用于添加Javadoc风格注释的XCode插件：[VVDocumenter-Xcode](https://github.com/onevcat/VVDocumenter-Xcode)

## 命名规范

无论什么情况下，都要尽量坚持苹果的命名规范，特别是涉及到[内存管理规则](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html)时
> 这里的"内存管理规则"，强调的是底层Core Foundation框架中，名字带Create或Copy的函数，返回的对象，你要负责它的释放。

#### 类名/类别名/协议

* 类名、类别名字及协议名字，都采用大驼峰式命名规则

* 文件名要能反映出它所包含的类的名称
	> 如：NSString.h 和 NSString.m 包含了NSString类的定义和实现

* Category的文件名要包含它所扩展的那个类的名称，并且类别名称要尽量能够描述它的功能
	> UIImage+Resize.h 或 UIImage+TintColor.h

* 在面向特定应用的代码中，类名尽量避免使用前缀，每个类都使用相同的前缀会影响可读性
	> 面向特定应用的代码，指那些只会在一个项目中使用的代码，不会被用于其他项目中的代码。

* 在面向多应用的代码中，类名要使用前缀，防止命名冲突
	> 面向多应用的代码，指那些会被多个项目共同使用的代码。
	> 
	> 比如CRKit这个类库中，使用了CR前缀。

* 协议声明或定义中，类型标识符、协议名称、尖括号之间不留空格

	```objc
	@interface MyProtocoledClass : NSObject<NSWindowDelegate> 
	{
	    @private
	    id<MyFancyDelegate> _delegate;
	}
	
	- (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
	@end
	```

#### 方法

* 方法名和参数名都采用小驼峰式命名规则。
	> 如：- (BOOL)isFileExistedAtPath:(NSString *)filePath;

* 方法声明中，-/+和返回值类型之间要空1个空格，方法名和参数类型之间以及参数类型和参数名之间不留空格

	```objc
	- (void)invokeWithTarget:(id)target; //正确
	- (void)invokeWithTarget: (id)target; //错误
	- (void)invokeWithTarget:(id) target; //错误
	- (void)invokeWithTarget: (id) target; //错误
	```

* 方法声明中，参数过多超过一行时，可以增加手动换行，使每个参数占用一行，以冒号对齐

	```objc
	- (void)doSomethingWith:(GTMFoo *)theFoo
	                   rect:(NSRect)theRect	               interval:(float)theInterval;
	```

* 方法名第一段比其他部分短时，每个参数占用一行，每行至少缩进4个空格，尽量保持参数以冒号对齐
	> 同时选中多行代码，用快捷键"command+["或"command+]"可以减少或增加缩进。

	```objc
	- (void)short:(GTMFoo *)theFoo
	              longKeyword:(NSRect)theRect
	        evenLongerKeyword:(float)theInterval
	                    error:(NSError **)theError; 
	```

* 方法名和参数名应该尽量读起来像一句话。具体参见[苹果的方法名命名规范](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html)
	> 如：convertPoint:fromRect: 或者 replaceCharactersInRange:withString:

* 当各个参数是接收者的某个属性时，方法名中不要用"and"来连接

	```objc
	//赞成的
	- (int)runModalForDirectory:(NSString *)path file:(NSString *) name types:(NSArray *)fileTypes;
	
	//不赞成的
	- (int)runModalForDirectory:(NSString *)path andFile:(NSString *)name andTypes:(NSArray *)fileTypes;
	```

* 如果方法名描述了两种不同的动作，要使用"and"来连接

	```objc
	- (BOOL)openFile:(NSString *)fullPath withApplication:(NSString *)appName andDeactivate:(BOOL)flag;
	```

* getter方法的方法名应该和变量名字相同，不允许使用"get"前缀
	> 本规则仅适用于Objective-C，C++使用C++的相关规范

	```objc
	- (id)delegate;		// 正确
	- (id)getDelegate;	//禁止
	```

* 类私有方法以下划线开头
	> 如：```- (void)_startDownloadFiles;```
	> 
	> Objective-C里面没有真正严格意义上私有方法。这里所说的"私有方法"指那些不需要公开的、只会在实现文件中使用的方法。
	> 
	> 这样做的好处是，可以直观的快速区别实现文件中的私有方法和公有方法。
	>
	> 这样做会很便于重构。如果某个方法废弃了，需要移除的时候，发现它是以下划线开头的，那么就可以确定这个方法是私有的，只会在这个实现文件中被用到。那么直接在该实现文件搜索这个方法的名字，然后清理掉搜索到的地方就可以了。不必再在整个项目中查找是否没有清理干净。
	> 
	> 根据苹果的建议，这种做法可能覆盖掉父类的私有方法。
	> 但是目前还没有遇到过这种情况，而且我们认为此条约定带来的好处远远大于它潜在的危险，因此仍然推行这条约定。

#### 函数

函数指纯C函数，这里提倡与苹果风格类似的约定。

* 函数名采用大驼峰式命名方式，参数名采用小驼峰式命名方式

* 如果函数和某个特定类型相关，那么函数名前缀要和类型前缀一样
	> 如```CGRectMake()```、```CGContextCreate()```等

#### 常量

* 创建NSString, NSDictionary, NSArray, 以及NSNumber等常量时，使用Literals语法

	```objc
	//例如：
	NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
	NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"}; 
	NSNumber *shouldUseLiterals = @YES; 
	NSNumber *buildingZIPCode = @10018; 
	
	//而不是：
	NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil]; 
	NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil]; 
	NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES]; 
	NSNumber *ZIPCode = [NSNumber numberWithInteger:10018];
	```

* 定义枚举常量时，使用NS_ENUM或NS_OPTIONS
	> NS_ENUM和NS_OPTIONS都提供了类型检查

	```objc
	//例如：
	typedef NS_ENUM(NSUInteger, PPNavBarButtonColor) {
	    PPNavBarButtonColorBlack,
	    PPNavBarButtonColorGreen,
	    PPNavBarButtonColorDefault = PPNavBarButtonColorBlack
	};
	
	typedef NS_OPTIONS(NSUInteger, PSTCollectionViewScrollPosition) {
	    PSTCollectionViewScrollPositionNone                 = 0,
		
	    PSTCollectionViewScrollPositionTop                  = 1 << 0,
	    PSTCollectionViewScrollPositionCenteredVertically   = 1 << 1,
	    PSTCollectionViewScrollPositionBottom               = 1 << 2,
		
	    PSTCollectionViewScrollPositionLeft                 = 1 << 3,
	    PSTCollectionViewScrollPositionCenteredHorizontally = 1 << 4,
	    PSTCollectionViewScrollPositionRight                = 1 << 5
	};
	```

* 定义常量时，除非明确的需要将常量当成宏使用，否则优先使用`const`，而非`#define`

* 只在某一个特定文件里面使用的常量，用static
	> static关键字保证变量只有文件作用域，可以避免变量名重名造成的链接错误问题。
	> 
	> 比如：
	> 
	> static CGFloat const RWImageThumbnailHeight = 50.0;

* 常量名以小写k开头，采用首字母大写的方式来分割单词

	```objc
	//例如：
	const int kNumberOfFiles = 12;
	NSString *const kUserKey = @"kUserKey";
	enum DisplayTinge {
		kDisplayTingeGreen = 1,
		kDisplayTingeBlue = 2
	};
	```

* 和特定类型相关的枚举常量使用类名作为前缀，而不用小写k开头。

	```objc
	typedef NS_OPTIONS(NSUInteger, UICollectionViewScrollPosition) {
	    UICollectionViewScrollPositionNone                 = 0,
	    UICollectionViewScrollPositionTop                  = 1 << 0,
	    UICollectionViewScrollPositionCenteredVertically   = 1 << 1,
	    UICollectionViewScrollPositionBottom               = 1 << 2,
	    UICollectionViewScrollPositionLeft                 = 1 << 3,
	    UICollectionViewScrollPositionCenteredHorizontally = 1 << 4,
	    UICollectionViewScrollPositionRight                = 1 << 5
	};
	```

#### 变量

* 属性名和变量名都采用小驼峰式命名规则

* 实例变量名以下划线开头，局部变量不能以下划线开头

* 禁止使用匈牙利标记法或含糊不清的缩写单词来命名变量
	> for循环中的i、j、k这种情况例外。
	>
	> Objective-C中，变量名应该尽量清楚的描述它的用途。这样可以使别人立即明白代码的意思，不要担心这样会导致代码过长。

	```objc
	//以下这些都是错误的命名规范
	int w;
	int nerr;
	int nCompConns;
	tix = [[NSMutableArray alloc] init];
	obj = [someObject object];
	p = [network port];
	
	//以下这些才是赞成的命名规范
	int numErrors;
	int numCompletedConnections;
	tickets = [[NSMutableArray alloc] init];
	userInfo = [someObject object];
	port = [network port];
	```

* 指针符号 "*" 靠近变量名字。(常量定义除外)

	```objc
	NSString *varName; //赞成的
	
	NSString* varName; //不赞成的
	```

* 使用property时，优先使用点语法
	> 使用点语法会让代码简洁。但对于其他情况，都应该使用方括号语法。

	```objc
	//赞成的
	NSInteger arrayCount = [self.array count];
	view.backgroundColor = [UIColor orangeColor];
	[UIApplication sharedApplication].delegate;
	
	//不赞成的
	NSInteger arrayCount = self.array.count;
	[view setBackgroundColor:[UIColor orangeColor]];
	UIApplication.sharedApplication.delegate;
	```

#### 通知和异常

* 通知名字的命名规则：[相关联的类名字] + [Did | Will] + [独一无二的一段名称] + Notification
	> 如：UIApplicationDidBecomeActiveNotification

* 异常名字的命名规则：[前缀] + [独一无二的一段名称] + Exception
	> 如：NSColorListIOException


## 布尔值

* Objective-C的布尔值只使用`YES`和`NO`

* `true`和`false`只能用于CoreFoundation，C或C++的代码中

* 禁止将某个值或表达式的结果与`YES`进行比较
	> 因为BOOL被定义成signed char。这意味着除了YES(1)和NO(0)以外，它还可能是其他值。
	> 
	> **因此C或C++中的非0为真并不一定就是YES**

	```objc
	//以下都是被禁止的
	- (BOOL)isBold 
	{
	    return [self fontTraits] & NSFontBoldTrait;
	}
	
	- (BOOL)isValid 
	{
	    return [self stringValue];
	}
	
	if ([self isBold] == YES) {
		//...
	}
	
	
	//以下才是赞成的方式
	- (BOOL)isBold 
	{
	    return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
	}
	
	- (BOOL)isValid 
	{
	    return [self stringValue] != nil;
	}
	
	- (BOOL)isEnabled 
	{
	    return [self isValid] && [self isBold];
	}
	
	if ([self isBold]) {
		//...
	}
	```

* 虽然`nil`会被直接解释成`NO`，但还是建议在条件判断时保持与nil的比较，因为这样代码更直观。

	```objc
	//比如，更直观的代码
	if (someObject != nil) {
		//...
	}
	
	//没那么直观的代码
	if (!someObject) {
		//...
	}
	```

* 在C或C++代码中，要注意NULL指针的检测。
	> 向一个nil的Objective-C对象发送消息不会导致崩溃。但由于Objective-C运行时不会处理给NULL指针的情况，所以为了避免崩溃，需要自行处理对于C/C++的NULL指针的检测。

* 如果某个`BOOL`类型的property的名字是一个形容词，建议为getter方法加上一个"is"开头的别名。

	```objc
	@property (assign, getter = isEditable) BOOL editable;
	```

## 条件语句

* 条件语句的语句体，即便只有一行，也不能省略花括弧
	> 这样可以减少失误。比如你在if语句体中增加第二行语句的时候，就可能会因为没有花括号而导致新增的第二行语句没有被包含在if语句体中。另外，[这里](http://programmers.stackexchange.com/a/16530)还提到了其他的一些危险情况。 

	```objc
	//赞成的
	if (error == nil) {
	  return success;
	}
	
	//不赞成的
	if (error == nil)
	  return success;
	  
	//或
	if (error == nil) return success;
	```

* 多层嵌套的条件语句，优先考虑条件不成立可以立即跳出的情况
	> Objective-C的代码普遍比较长，如果再加上多层嵌套的条件语句，代码缩进会增多，代码会变得更长，会影响可读性。比如，下面这种情况，换成优先考虑可以跳出的情况，可以有效的减少代码缩进长度：

	```objc
	//一般流程
	if (a) {
	
	    if (b) {
	    
	        if (c) {
	        
	        	//do something
	       	} 
	    } 
	
	} 
	
	
	//优先考虑可以跳出的流程
	if (!a) {
		return;
	}
	
	if (!b) {
		return;
	}
	
	if (!c) {
		return;
	}
	
	//do something
	```

* 三目运算只有在能增加代码清晰度和整洁度的时候才推荐使用
	> 三目运算符(?:)，如果不能增加代码整洁度和清晰度，使用时就要谨慎。特别是，嵌套使用多个三目运算，这种要尽量避免。因为它会使代码更难阅读。
	> 
	> 另外，三目运算符中的条件判断是一个语句，最好用小括号括起来。如果直接是一个布尔值则无需括号。例如：

	```objc
	//赞成的
	NSInteger value = 5;
	result = (value != 0) ? x : y;
	
	BOOL isHorizontal = YES;
	result = isHorizontal ? x : y;
	
	//不赞成的
	result = a > b ? x = c > d ? c : d : y;
	```

## 初始化方法

* 初始化方法的返回类型用```instancetype```

	> 关于instancetype的介绍参见[NSHipster.com](http://nshipster.com/instancetype/)。

## CGRect函数

* 访问CGRect中的x、y、width或height元素时，不直接访问而是使用[CGGeometry](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html)相关函数
	> CGGeometry里面的函数，会对CGRect参数进行隐式的标准化处理，然后再计算结果。因此，你应该避免直接读取或重写CGRect数据结构里面的值，而要使用这些函数来进行相关操作。
	> 
	> 什么叫标准化处理，参见[CGGeometry Reference](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html)的Overview章节。

	```objc
	//赞成的
	CGRect frame = self.view.frame;
	
	CGFloat x = CGRectGetMinX(frame);
	CGFloat y = CGRectGetMinY(frame);
	CGFloat width = CGRectGetWidth(frame);
	CGFloat height = CGRectGetHeight(frame);
	CGRect frame = CGRectMake(0.0, 0.0, width, height);
	
	//不赞成的
	CGRect frame = self.view.frame;
	
	CGFloat x = frame.origin.x;
	CGFloat y = frame.origin.y;
	CGFloat width = frame.size.width;
	CGFloat height = frame.size.height;
	CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
	```


## Xcode工程结构

* 实体文件应该和XCode工程文件保持同步，防止出现文件不一致
	> 任何手动创建的XCode Group都应该在文件系统有一个对应的文件夹。代码不仅要根据类型组织，更要以更加清晰的特征来区分归类。

* 建议：在可能的情况下，始终要勾选在Build设置选项中”Treat Warnings as Errors（将告警视为错误）“选项。同时尽可能多的暴露更多的additional warnings(附加告警)。如果要忽略某类特定Warning（告警），请使用[Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas)。
	> 此条不做强制要求，但是"将警告视为错误"是你应当要有的态度。


## Just for fun
最后贴张图娱乐一下，虽然说Objective-C中长名是美德，但是什么东西还是要有个度。[有人](http://weibo.com/xiaoleiwang)写了个[脚本](https://github.com/Quotation/LongestCocoa)统计Cocoa Framework中各种最长的命名，结果发现低估了苹果程序员的造句能力。Mac平台最长的常量名96个字符，最长的方法名150个字符，C函数名都能到68个字符！ -_-# 泥煤，自从学会了Objective-C，妈妈再也不用担心我的造句能力了。

![](http://ww1.sinaimg.cn/bmiddle/6b0f120dgw1ebvbkxggxij20dk0i4aeh.jpg)