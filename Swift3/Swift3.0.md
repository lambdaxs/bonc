# Swift 3.0
###iOS10 访问权限

```js
//在info.plist文件中添加键值对，输入Privacy 就可以找到各种权限的键 值为描述字符
Privacy - Photo Library Usage Description : 亲，可以打开你的相册吗？
//用到自带地图、钥匙链、后台、HomeKit、VPN等网络配置权限的应用，需要到工程配置的Capabilities中打开对应开关
```



###使用[Carthage](https://github.com/Carthage/Carthage)管理第三方库
```shell
touch Cartfile
#list github source
carthage update
```
###Cartfile content 

```shell
github "Alamofire/Alamofire" ~> 4.0.1
github "SnapKit/SnapKit"  == 3.0.2
github "SwiftyJSON/SwiftyJSON" >= 2.1.2
github "rs/SDWebImage" ~> 3
github "mxcl/PromiseKit" ~> 4.0
github "uacaps/PageMenu"
```
```
>= 1.0 for “at least version 1.0”
~> 1.0 for “compatible with version 1.0”
== 1.0 for “exactly version 1.0”
```
###常用第三方库
* [Alamofire](https://github.com/Alamofire/Alamofire) （类AFN）
* [SwiftyJSON](https://github.com/lingoer/SwiftyJSON) （类MJExtension）
* [SDWebImage](https://github.com/rs/SDWebImage) （同）
* [SnapKit](https://github.com/SnapKit/SnapKit)   （类Masorny）
* [SwiftLint](https://github.com/realm/SwiftLint) (代码风格检查器)
* [PromiseKit](https://github.com/mxcl/PromiseKit) (异步编程框架)
* [SVProgressHUD](https://github.com/SVProgressHUD/SVProgressHUD) (HUD)


##第三方库的基本使用
###SVProgressHUD的使用
####SVProgressHUD的静态方法
Code | 效果
--- | ---
show() | 显示无限循环进度条
dismiss() | 关闭HUD
showProgress(0.1, status: "正在下载") | 显示有进度的HUD
showInfo(withStatus: "请输入密码") | 显示信息
showSuccess(withStatus: "下载完成") | 显示成功信息
showError(withStatus: "密码错误") | 显示错误信息
show(UIImage(named:"icon"), status: "展示图片") | 显示自定义图片
###Alamofire
* 常用的GET请求

```swift
Alamofire.request("https://jsonapi.com").responseJSON { (rs) in
            switch rs.result {
            case .success(let v):
                print(v)
            case .failure(let e):
                print(e)
            }
        }
```

* 常用的POST请求

```swift
let parameters: Parameters = [
            "name": "xiaos",
            "age": 22,]   
Alamofire.request("https://jsonapi.com",method:.post,parameters:parameters).responseJSON { (rs) in
//rs.request?.url == "https://jsonapi.com"
//rs.request?.httpBody == name=xiaos age=22
        }
            
Alamofire.request("https://jsonapi.com",method:.post,parameters:parameters,encoding:URLEncoding.queryString).responseJSON { (rs) in
//rs.request?.url == "https://jsonapi.com?name=xiaos&age=22"
        }
```

* 完整的GET请求

```swift
Alamofire.request("https://jsonapi.com", method: .get, parameters: ["name":"xiaos"], encoding: URLEncoding.methodDependent, headers: ["key1":"value1"]).responseJSON { (rs) in
            print("the request is \(rs.request)")//the request is https://jsonapi.com?name=xiaos
            print("the request header is \(rs.request?.allHTTPHeaderFields)")//the request header is ["key1": "value1"]
            
            switch rs.result {
            case .success(let v):
                print("this is com req \(v)")
            case .failure(let e):
                print("error is \(e)")
            }
        }
```

####参数编码的使用
#####URLEncoding.methodDependent
* 在GET HEAD DELETE中，将参数添加到url后面，拼接成query键值对
* 在POST中，将参数添加到请求体中

#####URLEncoding.queryString
* 在GET POST HEAD DELETE中，都是将参数添加到url后面，拼接成query键值对

#####URLEncoding.httpBody
* 在GET HEAD DELETE中无效
* 在POST中，将参数添加到请求体中

默认使用的.methodDependent适合大部分场景

###SwiftyJSON的使用

####datas.json

```json
{
    "list":[{
            "id":"495450",
            "type_id":"4",
            "title":"你养我长大，我陪你变老……",
            "link":"http://zhangjunliang.baijia.baidu.com/article/649503",
            "summary":"时间都去哪儿了王铮亮 - 听得到的时间当我们还在感叹着时间都去哪儿了时间已经悄悄染白了父母...",
            "addtime":"1476013530",
            "arr":[1,2,3],
            "dict":{"name":"xiao"}
            },
            {
            "id":"495451",
            "type_id":"4",
            "title":"你养我长大，我陪你变老……",
            "link":"http://zhangjunliang.baijia.baidu.com/article/649503",
            "summary":"时间都去哪儿了王铮亮 - 听得到的时间当我们还在感叹着时间都去哪儿了时间已经悄悄染白了父母...",
            "addtime":"1476013530",
            "arr":[4,5,6],
            "dict":{"name":"xiao1"}
            },
            {
            "id":"495452",
            "type_id":"4",
            "title":"你养我长大，我陪你变老……",
            "link":"http://zhangjunliang.baijia.baidu.com/article/649503",
            "summary":"时间都去哪儿了王铮亮 - 听得到的时间当我们还在感叹着时间都去哪儿了时间已经悄悄染白了父母...",
            "addtime":"1476013530",
            "arr":[7,8,9],
            "dict":{"name":"xiao2"}
            }
            ]
}
```

####model

```swift
import Foundation
import SwiftyJSON

class NewsModel {
    var id:String?
    var type_id:String?
    var title:String?
    var link:String?
    var summary:String?
    var addtime:String?
    var arr:[Any]?
    var dict:[String:Any]?
    
    init(_ json:JSON) {
        self.id = json["id"].string
        self.type_id = json["type_id"].string
        self.link = json["title"].string
        self.summary = json["link"].string
        self.addtime = json["addtime"].string
        self.arr = json["arr"].array
        self.dict = json["dict"].dictionary
    }
}
```
####转换json数据为对象

```swift
let filePath = Bundle.main.path(forResource: "datas", ofType: "json")
if let jsonPath = filePath {
  do {
      let data = try Data(contentsOf: URL(fileURLWithPath: jsonPath))
      let dict = try JSONSerialization.jsonObject(with: data as Data, options: JSONSerialization.ReadingOptions.mutableLeaves)
      if let list = JSON(dict)["list"].array {
          //let newsModels:[NewsModel] = list.map(v in return NewsModel(v))
          let newsModels:[NewsModel] = list.map({NewsModel($0)})
          print(newsModels)//此处已经将json中的list数组转换为newsModel对象数组
      }
  }
  catch{
      print(error)
  }
}
```

####转换json的data

```swift
let jsonStr = "{\"name\":\"xiao\"}"
if let jsonData = jsonStr.data(using: .utf8, allowLossyConversion: false) {
  let json = JSON(data: jsonData)
  print(json["name"].string)//xiao?
}
```

###PromiseKit与Alamofire的配合使用

```swift
//model
class Person {
    var name:String?    
    init(_ json:JSON) {
        self.name = json["name"].string
    }
}

//handle data
//json1.json {"name":"xiaos1"}
firstly {
  Alamofire.request("https://www.xsdota.com/json1.json").responseJSON()
}.then(on:DispatchQueue.global()) { (json) -> Promise<Person> in
  let json = JSON(json)
  return Promise(value:Person(json))
}.then { (person) -> Void in
  print("person name is \(person.name)")
}.catch { (e) in
  print(e)
}

func login(username:String,pwd:String) -> Promise<Person> {
    let q = DispatchQueue.global()
    UIApplication.shared.isNetworkActivityIndicatorVisible = true
       
    let params = ["username":username,"pwd":pwd]
       
    return
      firstly {
          Alamofire.request("https://www.xsdota.com/login.json",parameters:params).responseJSON()
      }
      .then(on: q) { json in
          Person(JSON(json))
      }
      .always {
          UIApplication.shared.isNetworkActivityIndicatorVisible = false
      }
}

firstly {
  login(username: "xiaos", pwd: "123456")
}
.then { (person) -> Void in
  print(person.name)
}
.catch { (e) in
  switch e {
      case Alamofire.AFError.responseSerializationFailed:
          self.bn_showInfo(msg: "json解析失败")
      default:break
  }
}

//when 多个异步任务全部处理完成后
let oneTask = Alamofire.request("https://www.xsdota.com/json3.json").responseJSON()
let twoTask = Alamofire.request("https://www.xsdota.com/json2.json").responseJSON()
   
when(fulfilled: [oneTask,twoTask])
.then { (rs) -> Void in
  for result in rs {
      print(result)
  }
}.catch { (e) in
  print(e)
}

//race 龟兔赛跑 谁先完成获得谁的结果
race(oneTask,twoTask).then { (rs) -> Void in
  print("龟兔赛跑 冠军是 \(JSON(rs)["name"])")
}.catch { (e) in
  print(e)
}
```

###Promise与UIKit的配合

####UIAlertController
```swift
let alert = PMKAlertController.init(title: "this is title", message: "this is msg", preferredStyle: .alert)
let one = alert.addActionWithTitle(title: "one")
let two = alert.addActionWithTitle(title: "two")
promise(alert)
  .then { (action) -> Promise<Int> in
      switch action {
          case one:
              return Promise(value:10)
          case two:
              return Promise(value:20)
          default: throw BNError.msg
      }
  }
  .then {num ->Void in
      print("num is \(num)")
  }.catch { (e) in
      print(e)
}
```

