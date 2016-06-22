## 导入头文件
遵循根据类实现的功能之间隔开一行
model类 view类 tool类 other类

## 缩减tableView代码
一、在初始化时注册cell类型 定义可重用标识
    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@“cell”];
二、在初始化cell的方法中 使用可重用缓存池
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@“cell”];
三、系统会自动调用自定义cell中的initWithStyle:reuseIdentifier:方法
在自定义cell中重写initWithStyle:reuseIdentifier:方法就能初始化cell

## if表达式 
#每个if要跟大括号
```objc
**推荐:**
if (!error) {
    return success;
}
**不推荐:**
if (!error)
    return success;
```

#不变值放后面
```objc
**推荐:**
if ([myValue isEqual:@42]) { …

**不推荐:**
if ([@42 isEqual:myValue]) { …
```
#检查空值
```objc
**推荐:**
if (someObject) { …
if (![someObject boolValue]) { …
if (!someObject) { …

**不推荐:**
if (someObject == YES) { … // Wrong
if (myRawValue == YES) { … // Never do this.
if ([someObject boolValue] == NO) { …
```
#if嵌套 将主体代码放在if外
```objc
**推荐:**
- (void)someMethod {
  if (![someOther boolValue]) {
      return;
  }
  //Do something important
}

**不推荐:**
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```
## 复杂的表达式 
当你有一个复杂的 if 子句的时候，你应该把它们提取出来赋给一个 BOOL 变量，这样可以让逻辑更清楚，而且让每个子句的意义体现出来。
```objc
BOOL nameContainsSwift = [sessionName containsString:@“Swift”];
BOOL isCurrentYear = [sessionDateCompontents year] == 2014;
BOOL isSwiftSession = nameContainsSwift && isCurrentYear;

if (isSwiftSession) {
    // Do something
}
```
# 错误处理
当方法返回一个错误参数的引用的时候，检查返回值，而不是错误的变量。
```objc
**推荐:**
NSError *error = nil;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
此外，一些苹果的 API 在成功的情况下会对 error 参数（如果它非 NULL）写入垃圾值，所以如果检查 error 的值可能导致错误。
```
# case语句
每个case下有多条语句用大括号
switch使用枚举变量时，可以不写default

## 枚举类型
当使用enum的时候，建议使用新的固定的基础类型定义，因它有更强大的的类型检查和代码补全。 SDK 现在有一个 宏来鼓励和促进使用固定类型定义 NS_ENUM()
```objc
**例子: **
typedef NS_ENUM(NSUInteger, ZOCMachineState) {
    ZOCMachineStateNone = 1 << 0,
    ZOCMachineStateIdle = 1 << 1,
    ZOCMachineStateRunning = 1 << 2,
    ZOCMachineStatePaused = 1 << 3
};
```
对于多重枚举组合推荐使用位移操作 在做逻辑选择更加方便
ZOCMachineStateNone | ZOCMachineStateIdle

## 命名
通用的约定，推荐使用长的、描述性的方法和变量名（就是话唠~）
```objc
**推荐:**
UIButton *settingsButton;

**不推荐:**
UIButton *setBut;
```
##  常量
常量应该使用驼峰命名法，并且为了清楚，应该用相关的类名作为前缀。
```objc
**推荐:**
static const NSTimeInterval ZOCSignInViewControllerFadeOutAnimationDuration = 0.4;

**不推荐:**
static const NSTimeInterval fadeOutTime = 0.4;

**推荐:**
static NSString * const ZOCCacheControllerDidClearCacheNotification = @“ZOCCacheControllerDidClearCacheNotification”;
static const CGFloat ZOCImageThumbnailHeight = 50.0f;

**不推荐:**
\#define CompanyName @“Apple Inc.”
\#define magicNumber 42
```
常量应该在 interface 文件中这样被声明：
```objc
// Foo.h
extern NSString * const ZOCFooDidBecomeBar
并且应该在实现文件中实现它的定义：
// Foo.m
NSString * const ZOCFooDidBecomeBar = @“ZOCFooDidBecomeBarNotification”;
```
##  方法
 
对于方法签名，在方法类型 (`-`/`+` 符号)后应该要有一个空格。方法段之间也应该有一个空格（来符合 Apple 的规范）。在参数名称之前总是应该有一个描述性的关键词。

使用“and”命名的时候应当更加谨慎。它不应该用作阐明有多个参数，比如下面的`initWithWidth:height:` 例子：
```objc
**推荐:**
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;

**不推荐:**
- (void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## Initializer 和 dealloc 
推荐的代码组织方式：将 `dealloc` 方法放在实现文件的最前面（直接在  `@synthesize` 以及 `@dynamic` 之后），`init` 应该放在 `dealloc`  之后。如果有多个初始化方法， designated initializer 应该放在第一个，secondary initializer 在之后紧随，这样逻辑性更好。
如今有了 ARC，dealloc 方法几乎不需要实现，不过把 init 和 dealloc 放在一起可以从视觉上强调它们是一对的。通常，在 init 方法中做的事情需要在 dealloc 方法中撤销。

### 初始化方法的约定
Objective-C 有 主要 和 次要 初始化方法的观念。
主要 初始化方法是提供所有的参数，次要  初始化方法是一个或多个，并且提供一个或者更多的默认参数来调用 主要 初始化方法的初始化方法。
```objc
@implementation ZOCEvent
//主要初始化方法
- (instancetype)initWithTitle:(NSString *)title
                         date:(NSDate *)date
                     location:(CLLocation *)location
{
    if (self = [super init]) {
        _title    = title;
        _date     = date;
        _location = location;
    }
    return self;
}
//次要初始化方法
- (instancetype)initWithTitle:(NSString *)title
                         date:(NSDate *)date
{
    return [self initWithTitle:title date:date location:nil];
}

- (instancetype)initWithTitle:(NSString *)title
{
    return [self initWithTitle:title date:[NSDate date] location:nil];
}
@end
```
### instancetype 与 id
	实例返回值用instancetype，不用id，便于编译器检查类型。

### 单例
封装好的宏定义文件，直接使用

##  属性
属性应该尽可能描述性地命名，避免缩写，并且是小写字母开头的驼峰命名。我们的工具可以很方便地帮我们自动补全所有东西。所以没理由少打几个字符了，并且最好尽可能在你源码里表达更多东西。
```objc
**例子 :**
NSString *titleText;

**不要这样 :**
NSString* titleText;
NSString * titleText;
```
应该总是使用 setter 和 getter 方法访问属性，除了 `init` 和 `dealloc` 方法。
你总应该用 getter 和 setter ，因为：
- 使用  setter 会遵守定义的内存管理语义(`strong`, `weak`, `copy` etc…) ，这个在 ARC 之前就是相关的内容。举个例子，`copy` 属性定义了每个时候你用 setter 并且传送数据的时候，它会复制数据而不用额外的操作。
- KVO 通知(`willChangeValueForKey`, `didChangeValueForKey`) 会被自动执行。
- 更容易debug：你可以设置一个断点在属性声明上并且断点会在每次 getter / setter 方法调用的时候执行，或者你可以在自己的自定义 setter/getter 设置断点。
- 允许在一个单独的地方为设置值添加额外的逻辑。

应该倾向于用 getter：
- 它是对未来的变化有扩展能力的（比如，属性是自动生成的）。
- 它允许子类化。
- 更简单的debug（比如，允许拿出一个断点在 getter 方法里面，并且看谁访问了特别的 getter
- 它让意图更加清晰和明确：通过访问 ivar `_anIvar` 你可以明确的访问 `self->_anIvar`.这可能导致问题。在 block 里面访问 ivar （你捕捉并且 retain 了 self，即使你没有明确的看到 self 关键词）。
- 它自动产生KVO 通知。
- 在消息发送的时候增加的开销是微不足道的。

## Init 和 Dealloc
有一个例外：你永远不能在 init （以及其他初始化函数）里面用 getter 和 setter 方法，并且你直接访问实例变量。事实上一个子类可以重载 setter 或者 getter 并且尝试调用其他方法，访问属性的或者 ivar 的话，他们可能没有完全初始化。记住一个对象是仅仅在 init 返回的时候，才会被认为是初始化完成到一个状态了。

同样在 dealloc 方法中（在 dealloc 方法中，一个对象可以在一个 不确定的状态中）这是同样需要被注意的。

##合成属性
使用属性的自动同步 (synthesize) 而不是手动的  `@synthesize` 语句，除非你的属性是 protocol 的一部分而不是一个完整的类。

#### 点符号
当使用 setter getter 方法的时候尽量使用点符号。应该总是用点符号来访问以及设置属性。
```objc
**例子:**
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;

**不要这样:**
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```
使用点符号会让表达更加清晰并且帮助区分属性访问和方法调用

### 属性定义
推荐按照下面的格式来定义属性
@property (nonatomic, readwrite, copy) NSString *name;

属性的参数应该按照下面的顺序排列： 原子性，读写 和 内存管理。 这样做的属性更容易修改正确，并且更好阅读。

属性可以存储一个代码块。为了让它存活到定义的块的结束，必须使用 `copy` （block 最早在栈里面创建，使用 `copy`让 block 拷贝到堆里面去）

如果 `BOOL` 属性的名字是描述性的，这个属性可以省略 “is” ，但是特定要在 get 访问器中指定名字，如：
@property (assign, getter=isEditable) BOOL editable;

### 可变对象
任何可以用来用一个可变的对象设置的（(比如 `NSString`,`NSArray`,`NSURLRequest`)）属性的的内存管理类型必须是 `copy` 的。
copy会的底层操作是拷贝旧值—赋给新值—销毁旧值。
这个是用来确保包装，并且在对象不知道的情况下避免改变值。

你应该同时避免暴露在公开的接口中可变的对象，因为这允许你的类的使用者改变你自己的内部表示并且破坏了封装。你可以提供可以只读的属性来返回你对象的不可变的副本。
```objc
/* .h */
@property (nonatomic, readonly) NSArray *elements

/* .m */
- (NSArray *)elements {
  return [self.mutableElements copy];
}
```
## 懒加载（Lazy Loading）
当实例化一个对象可能耗费很多资源的，或者需要只配置一次并且有一些配置方法需要调用，而且你还不想弄乱这些方法。

在这个情况下，我们可以选择使用重载属性的　getter　方法来做　lazy　实例化。通常这种操作的模板像这样：
```objc
- (NSDateFormatter *)dateFormatter {
  if (!_dateFormatter) {
    _dateFormatter = [[NSDateFormatter alloc] init];
        NSLocale *enUSPOSIXLocale = [[NSLocale alloc] initWithLocaleIdentifier:@“en_US_POSIX”];
        [dateFormatter setLocale:enUSPOSIXLocale];
        [dateFormatter setDateFormat:@“yyyy-MM-dd’T’HH:mm:ss.SSSSS”];
  }
  return _dateFormatter;
}
```

#  美化代码
###  空格
* 缩进使用 4 个空格。 确保table键对应4个空格。
* 方法的大括号和其他的大括号(`if`/`else`/`switch`/`while` 等)  总是在同一行开始，在新起一行结束。

```objc
**推荐:**
if (user.isHappy) {
    //Do something
}
else {
    //Do something else
}

**不推荐:**
if (user.isHappy)
{
  //Do something
} else {
  //Do something else
}
冒号对齐:
**推荐:**
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];

**不推荐:**
[UIView animateWithDuration:1.0 animations:^{
    // something 
} completion:^(BOOL finished) {
    // something
}];
```
如果自动对齐让可读性变得糟糕，那么应该在之前把 block 定义为变量，或者重新考虑你的代码签名设计。

### Pragma Mark
`#pragma mark -`  是一个在类内部组织代码并且帮助你分组方法实现的好办法。 我们建议使用  `#pragma mark -` 来分离:
```objc
- 不同功能组的方法 
- protocols 的实现
- 对父类方法的重写
- (void)dealloc { /* … */ }
- (instancetype)init { /* … */ }

#pragma mark - View Lifecycle （View 的生命周期）
- (void)viewDidLoad { /* … */ }
- (void)viewWillAppear:(BOOL)animated { /* … */ }
- (void)didReceiveMemoryWarning { /* … */ }

#pragma mark - Custom getter&&setter （自定义访问器）
- (void)setCustomProperty:(id)value { /* … */ }
- (id)customProperty { /* … */ }

#pragma mark - IBActions  
- (IBAction)submitData:(id)sender { /* … */ }

#pragma mark - Public 
- (void)publicMethod { /* … */ }

#pragma mark - Private
- (void)zoc_privateMethod { /* … */ }

#pragma mark - UITableViewDataSource
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath { /* … */ }

#pragma mark - ZOCSuperclass
// … 重载来自 ZOCSuperclass 的方法

#pragma mark - NSObject
- (NSString *)description { /* … */ }

```
上面的标记能明显分离和组织代码。你还可以用  cmd+Click 来快速跳转到符号定义地方。
但是小心，即使有paragma mark，但是类里面有太多方法说明类做了太多事情，需要考虑重构了。

## 注释
在定义属性和方法时，好的注释可以完美配合XCode的自动补全功能。
```objc
/** 内容正文 */
@property (nonatomic,copy) NSString *body;

/** 日期key取值 */
+ (id)getCacheObjectWithDateKey:(NSString *)key;
```
在调用这些方法时自动补全下面都会显示注释，更方便找对应的方法。

# 对象间的通讯

对象之间需要通信，这也是所有软件的基础。再非凡的软件也需要通过对象通信来完成复杂的目标。本章将深入讨论一些设计概念，以及如何依据这些概念来设计出良好的架构。

## Blocks 
Blocks 是 Objective-C 版本的 lambda 或者 closure（闭包）。
```objc
使用 block 定义异步接口:
- (void)downloadObjectsAtPath:(NSString *)path
                   completion:(void(^)(NSArray *objects, NSError *error))completion;
```
当你定义一个类似上面的接口的时候，尽量使用一个单独的 block 作为接口的最后一个参数。把需要提供的数据和错误信息整合到一个单独 block 中，比分别提供成功和失败的 block 要好。

看上面的方法，完成处理的 block 的参数很常见：第一个参数是调用者希望获取的数据，第二个是错误相关的信息。这里需要遵循以下两点：
* 若 `objects` 不为 nil，则 `error` 必须为 nil
* 若 `objects` 为 nil，则 `error` 必须不为 nil

因为调用者更关心的是实际的数据，就像这样：
```objc
- (void)downloadObjectsAtPath:(NSString *)path
                   completion:(void(^)(NSArray *objects, NSError *error))completion {
    if (objects) {
        // do something with object
    }
    else {
        // handle error
    }
}
```
此外，Apple 提供的一些同步接口在成功状态下向 error 参数（如果非 NULL) 写入了垃圾值，所以检查 error 的值可能出现问题。

### 深入 Blocks
一些关键点：
* block 是在栈上创建的 
* block 可以复制到堆上
* block 有自己的私有的栈变量（以及指针）的常量复制
* 可变的栈上的变量和指针必须用 __block  关键字声明
* 
在block中调用self会增加引用计数，导致循环引用，解决办法就是weak/strong大法。
```objc
**例子:**
__weak __typeof(self) weakSelf = self;
[self executeBlock:^(NSData *data, NSError *error) {
    [weakSelf doSomethingWithData:data];
}];

**不要这样做:**
[self executeBlock:^(NSData *data, NSError *error) {
    [self doSomethingWithData:data];
}];

**多个语句的例子:**
__weak __typeof(self)weakSelf = self;
[self executeBlock:^(NSData *data, NSError *error) {
    __strong __typeof(weakSelf) strongSelf = weakSelf;
    if (strongSelf) {
        [strongSelf doSomethingWithData:data];
        [strongSelf doSomethingWithData:data];
    }
}];

**不要这样做:**
__weak __typeof(self)weakSelf = self;
[self executeBlock:^(NSData *data, NSError *error) {
    [weakSelf doSomethingWithData:data];
    [weakSelf doSomethingWithData:data];
}];
```
可以抽取到宏中或者代码段中调用更方便。
```objc
__weak __typeof(self)weakSelf = self;
__strong __typeof(weakSelf)strongSelf = weakSelf;
```
关于block更深入的使用，我会单独总结一篇出来。


