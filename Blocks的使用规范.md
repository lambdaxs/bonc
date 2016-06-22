## Blocks 

Blocks 是 Objective-C 版本的 lambda 或者 closure（闭包）。
使用 block 定义异步接口:
- (void)downloadObjectsAtPath:(NSString *)path
                   completion:(void(^)(NSArray *objects, NSError *error))completion;

当你定义一个类似上面的接口的时候，尽量使用一个单独的 block 作为接口的最后一个参数。把需要提供的数据和错误信息整合到一个单独 block 中，比分别提供成功和失败的 block 要好。

这样做的原因：
* 通常这成功处理和失败处理会共享一些代码（比如让一个进度条或者提示消失）；
* Apple 也是这样做的，与平台一致能够带来一些潜在的好处；
* block 通常会有多行代码，如果不是在最后一个参数的话会打破调用点；
* 使用多个 block 作为参数可能会让调用看起来显得很笨拙，并且增加了复杂性。

看上面的方法，完成处理的 block 的参数很常见：第一个参数是调用者希望获取的数据，第二个是错误相关的信息。这里需要遵循以下两点：
* 若 `objects` 不为 nil，则 `error` 必须为 nil
* 若 `objects` 为 nil，则 `error` 必须不为 nil

因为调用者更关心的是实际的数据，就像这样：
- (void)downloadObjectsAtPath:(NSString *)path
                   completion:(void(^)(NSArray *objects, NSError *error))completion {
    if (objects) {
        // do something with the data
    }
    else {
        // handle error
    }
}

此外，Apple 提供的一些同步接口在成功状态下向 error 参数（如果非 NULL) 写入了垃圾值，所以检查 error 的值可能出现问题。

### 深入 Blocks
一些关键点：
* block 是在栈上创建的 
* block 可以复制到堆上
* block 有自己的私有的栈变量（以及指针）的常量复制
* 可变的栈上的变量和指针必须用 __block  关键字声明

最重要的事情是 `__block` 声明的变量和指针在 block 里面是作为显示操作真实值/对象的结构来对待的。
__block用来修饰的变量，在block里面就是真实的变量，会根block外的变量保持同步。
例如：
float price = 1.99;  
float (^finalPrice)(int) = ^(int quantity) {  
    return quantity * price;
};
int orderQuantity = 10;  
NSLog(orderQuantity, finalPrice(orderQuantity));  
打印值是10 19.9

但是在block内部更改外部值
float price = 1.99;  
float (^finalPrice)(int) = ^(int quantity) {  
    price = 0.99;
    return quantity * price;
};
int orderQuantity = 10;  
NSLog(orderQuantity, finalPrice(orderQuantity));
编译器就会报错，原因就是在block中的外部变量是只读的。

这时候用__block修饰变量就能在block中读写这个变量保持同步。
__block float price = 1.99;  
float (^finalPrice)(int) = ^(int quantity) {  
    return quantity * price;
};
int orderQuantity = 10;  
price = .99;

如果在定义之后但是 block 没有被调用前，对象被释放了，那么 block 的执行会导致 Crash。 `__block`  变量不会在 block 中被持有，最后… 指针、引用、解引用以及引用计数变得一团糟。


###  self 的循环引用
当使用代码块和异步分发的时候，要注意避免引用循环。 总是使用 `weak` 引用会导致引用循环。 此外，把持有 blocks 的属性设置为 nil (比如 `self.completionBlock = nil`) 是一个好的实践。它会打破 blocks 捕获的作用域带来的引用循环。

* **1**: 只能在 block 不是作为一个 property 的时候使用，否则会导致 retain cycle。
直接在 block 里面使用关键词 self

* **2**:  当 block 被声明为一个 property 的时候使用。
在 block 外定义一个 `__weak` 的 引用到 self，并且在 block 里面使用这个弱引用

* **3**: 和并发执行有关。当涉及异步的服务的时候，block 可以在之后被执行，并且不会发生关于 self 是否存在的问题。
在 block 外定义一个 `__weak` 的 引用到 self，并在在 block 内部通过这个弱引用定义一个 `__strong`  的引用。

