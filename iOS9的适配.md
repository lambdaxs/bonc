iOS9的适配
一、网络请求改为http
NSAppTransportSecurity
NSAllowsArbitraryLoads = YES

二、禁用Bitcode
找到项目配置Build Settings——>Bitcode选项选为NO。

三、新的苹方字体间隙变大
使用sizeToFit方法
计算Label大小向上取整

四、应用间跳转
具体的解决方案也是要在info.plist中设置 LSApplicationQueriesSchemes 类型为数组，下面添加所有用到的scheme。
五、对于cell滑动明显卡顿的地方
用局部刷新代替reloadData
[self.tableView reloadSections:[NSIndexSet indexSetWithIndex:0] withRowAnimation:UITableViewRowAnimationNone];

