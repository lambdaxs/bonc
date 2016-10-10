# Swift 3.0
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




