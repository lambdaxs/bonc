# Promise

```java
okHtpp.call(new callback(){
    onFial(err){
    //handle err
    }
    onResponse(data){
    //handle data
    }
})
```

```Swift
[manager GET:@"http://api" parameters:params success:^(NSURLSessionDataTask *task, id responseObject) {
    //handle data
} failure:^(NSURLSessionDataTask *task, NSError *error) {
    //handle error
}];
```

* 几乎所有网络请求都用异步操作完成
* 异步操作意味着我们不知道这个操作什么时候能完成,过程中什么时候会出现错误
* 几乎所有的异步操作都有成功回调和错误处理

##举个栗子🌰

在某个界面中请求网络数据,这些数据是三个接口提供,并且接口3的参数需要获取接口2的结果,接口2的参数需要获取接口1的结果

```oc
NSDictnary *params1 = @{@"name":@"xiaos"}
[manager GET:@"http://api1" parameters:params1 success:^(NSURLSessionDataTask *task, id responseObject) {

    NSDictnary *params2 = @{@"name":responseObject[@"name"]}    
    [manager GET:@"http://api2" parameters:params2 success:^(NSURLSessionDataTask *task,id responseObject) {
    
        NSDictnary *params3 = @{@"name":responseObject[@"author"]}
        [manager GET:@"http://api3" parameters:params3 success:^(NSURLSessionDataTask *task, id responseObject) {

        } failure:^(NSURLSessionDataTask *task, NSError *error) {
        //handle error
        }];
    } failure:^(NSURLSessionDataTask *task, NSError *error) {
        //handle error
    }];

} failure:^(NSURLSessionDataTask *task, NSError *error) {
        //handle error
}];
```

随着嵌套的越多,每次异步处理都要判断错误,
其中再夹杂一些业务逻辑的处理,代码就会变很不易读,
这种结构也让人抓狂.为了解决这种异步回调过多,每步都要进行错误处理的问题.
coder们提出了Promise的解决方案.           
Promise的翻译过来就是承诺.每次异步操作都会有一个结果和一个错误.Promise保证我们能得到想要的结果,也能处理发生的错误.

##then catch
* 一个简单的promise

```oc
//假设封装了一个完整的Promise对象
Promise *fetchData = [Promise new]

//将已有的异步回调模式改为promise模式
- (Promise)get:(NSString *)url params:(id)params {
    Promise *fetchData = [[Promise alloc] initWith:^(sucess,failure){
    //success:^(id) failure:^(NSError *)
        [mgr GET:url parameters:params success:^(NSURLSessionDataTask *task, id responseObject) {
            success(responseObject);
        } failure:^(NSURLSessionDataTask *task, NSError *error) {
            failure(error);
        }];        
    }];
    return fetchData;    
}

//promise化的异步网络请求
[[[http get:@"http://api" params:params] then:^(id result){
    //handle result
}] catch:^(NSError *err){
    //handle err
}]

//将上面一段代码用promise重写
[[[[[http get:@"http://api1" params:p1] then:^(id result){
    p2 = [self handle:result];
    return [http get:@"http://api2" params:p2];    
}] then:^(id result){
    p3 = [self handle:result];
    return [http get:@"http://api3" params:p3];
}] then:^(id result){
    //get the result
}] catch:^(NSError *err){
    //handle the err
}]
```

##使用javaScript书写Promise

```js
//一个ajax请求,类似我们的AFN和okhttp
$.get({
    url:'http://api',
    params:null,
    success:function (data){
    
    },
    failure:function (err){
    
    }
})

//使用Promise封装异步网络请求
var promiseGet = function (request){
    return new Promise(function(s,f){
        $.get({
            url:request.url,
            params:request.params,
            success:function (data){
                s(data)
            },
            failure:function (err){
                f(err)
            }
        })
    })
}

//使用
promiseGet({
    url:'http://api',
    params:null
}).then(function(result){

}).catch(function(err){
    
})

//重写上面的多个关联异步操作
promiseGet({
    url:'http://api1',
    params:p1
}).then(function(rs){
    p2 = rs.name
    return promiseGet({
              url:'http://api2',
              params:p2
            })
}).then(function(rs){
    p3 = rs.age
    return promiseGet({
              url:'http://api3',
              params:p3
            })
}).then(function(rs){
    //handle rs
}).catch(function(err){
    //handle err
})
```

每个Promise都有对应的then方法和catch方法
then(success,failure)
then方法接收两个参数,这个两个参数都是回调函数,也可以视为闭包(在java中没有闭包的概念,但是一般可以用匿名内部类实现类似的功能,比如常用的按钮绑定onClick事件)
success即为上段代码中的\^(id result){}得到上一个promise成功后返回的结果
failure即为上段代码catch中的\^(NSError *err){}得到上一个promise失败后返回的错误信息
then函数有两个参数,但是第二个是可选的.
catch:(failure)就是then(null,failure)的语法糖用于捕捉上面所有promise里发生的错误

##Promise.all
* 应用场景:有时候,需要在所有异步请求全部完成后,接收一个回调做处理.比如:我要接受两个接口返回的数据,然后对里面的某个数据进行相加才能得到结果.

```js
//新建了两个promise任务
var task1 = promiseGet({
    url:'http://api1',
    params:p1
})

var task2 = promiseGet({
    url:'http://api2',
    params:p2
})

Promise.all([task1,task2])
.then(function(rs){
    //rs[0]对应的是task1的请求结果 rs[1]对应的是task2请求结果
}).catch(function(err){
    //handle err
})
```

##Promise.race
* 应用场景:有时候,我们有多个异步任务,但是我只希望得到最先完成任务的回调,而终止其他的异步任务

```js
var task1 = promiseGet({
    url:'http://api1',
    params:p1
})

var task2 = promiseGet({
    url:'http://api2',
    params:p2
})

Promise.race([p1,p2])
.then(function(rs){
    //rs就是最先完成的那个promise的结果
})
```

Promise的应用场景还很广,在iOS中凡是涉及异步回调的任务,全部能用promise改写,从而将分散的代码用then连接起来,业务逻辑更加紧密,代码可读性更好.

举个🌰
有这样一个需求
用户点击一个按钮后,进入imagePicker挑选照片,
然后用get请求获取用户所在地理位置信息,
然后用post请求上传用户照片和用户地理信息到服务器,
最后提示操作成功.
若其中某个操作失败提示操作失败.

这样的业务我们正常处理起来就是监听按钮的onClick事件,
然后调用系统的ImagePicker,实现代理,
在代理方法中处理选取得到的照片或者取消选取.
然后在选取照片成功后,get请求地理信息,
然后将用户的照片和地理信息一起post发送到服务器

若用promise将上述过程包装起来,伪代码如下

```swift
//github上的PromiseKit库,非常适合swift
UIApplication.shared.isNetworkActivityIndicatorVisible = true

firstly {
    when(imagePicker.promise(),URLSession.dataTask(with: "http://getLoctionInfo"))
}.then { imageData,locationInfo  -> Void in
    let url = "http://postImageAndLocation/\(locationInfo.distance)"
    return URLSession.postDataTask(with: url,data:imageData)
}.then { result -> Void in
    //handle result
}.always {
    UIApplication.shared.isNetworkActivityIndicatorVisible = false
}.catch { error in
    UIAlertView(/*…*/).show()
}
```


一些资源:

[java的promise库](http://jdeferred.org/)
[OC/Swift的promise库](https://github.com/mxcl/PromiseKit)
[JavaScript内置了语言级别的promise](http://liubin.org/promises-book/#chapter1-what-is-promise)

