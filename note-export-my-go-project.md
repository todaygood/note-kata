

# 如何开源我的包让别人import? 



## export 

[Golang开源发布自己的包](https://blog.csdn.net/mrkorbin/article/details/111032300?ops_request_misc=&request_id=&biz_id=102&utm_term=golang%25E5%258F%2591%25E5%25B8%2583%25E8%2587%25AA%25E5%25B7%25B1%25E7%259A%2584%25E5%258C%2585%25E8%25AE%25A9%25E5%2588%25AB%25E4%25BA%25BA%25E4%25BD%25BF%25E7%2594%25A8&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-111032300.first_rank_v2_pc_rank_v29)

测试结果,感觉 建release tag 不知有何用处？

因为我使用的是 go get github.com/todaygood/detect-kernel

 除非指定版本，像这种：

```bash

go get github.com/todaygood/detect-kernel@v0.1.1
```





## import 

```go
package main

import (
  "github.com/todaygood/detect-kernel/config"
)


func main(){

  config.DetectKernelConfig()

}

```









## golang中cannot refer to unexported name问题

今日在golang中编写了个特定包，该包的某个函数试图让外部引用。

结果，在外部引用中，该函数发生错误：cannot refer to unexported name。

比较奇怪的是，其他函数可以被引用。

后来发现一个golang的语法：模块中要导出的函数，必须首字母大写。

PS：1）C语言外部引用的函数，没有这个限制；

​        2）C语言会有extern C或者extern说明，但golang的首字母大写才能导出的语法，显然是golang语言的特性，值得学习 