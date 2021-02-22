

# todo1

sealos 高可用通过ipvs实现的localLB，占用资源少，稳定可靠，类似kube-proxy的实现



# go module

`Go1.1.1`版本发布(2018-08-24发布)已经过去几天，从官方的博客中看到，有两个比较突出的特色，一个就是今天讲的`module`，模块概念。目前该功能还在试验阶段，有些地方还需要不断的进行完善。在官方正式宣布之前，打算不断修正这种支持。**到时候就可以移除对`GOPATH`和`go get`命令的支持。**
如果你想现在想就试试这个新功能`module`，需要你将你的代码仓库放到`GOPATH/src`目录之外。然后在那个目录下创建一个`go.mod`文件，从文件树中运行`go`命令。

 之前Golang 主要依靠vendor和GOPATH来管理依赖库，vendor相对主流，**但现在官方更提倡go mod。**



在使用模块的时候， `GOPATH` 是无意义的，不过它还是会把下载的依赖储存在 `GOPATH/src/mod` 中，也会把 `go install` 的结果放在 `GOPATH/bin`（如果 `GOBIN` 不存在的话）

- `go mod download` 下载模块到本地缓存，缓存路径是 `$GOPATH/pkg/mod/cache`
- `go mod edit` 是提供了命令版编辑 `go.mod` 的功能，例如 `go mod edit -fmt go.mod` 会格式化 `go.mod`
- `go mod graph` 把模块之间的依赖图显示出来



## RefLink

[Go module的介绍及使用](https://blog.csdn.net/benben_2015/article/details/82227338?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.control)

[三分钟掌握Go mod常用与高级操作](https://zhuanlan.zhihu.com/p/103534192)



## GO111MODULE环境变量

运行go env -w GO111MODULE=on

运行go env -w GOPROXY=https://goproxy.cn,direct //使用代理

注意 https://goproxy.io 也可替换成阿里云的 https://mirrors.aliyun.com/goproxy

备注：

配置环境变量 GO111MODULE：有三个可选值： off、 on、 auto，默认值是 auto。

1.GO111MODULE=off，无模块支持，go会从 GOPATH 和 vendor 文件夹寻找包。

2.GO111MODULE=on，模块支持，go会忽略 GOPATH 和 vendor 文件夹，只根据 go.mod下载依赖。

3.GO111MODULE=auto，在 $GOPATH/src外面且根目录有 go.mod文件时，开启模块支持

- 如果`GO111MODULE=off`，那么`go`命令行将不会使用新的`module`功能，相反的，它将会在`vendor`目录下和`GOPATH`目录中查找依赖包。也把这种模式叫`GOPATH模式`。
- 如果`GO111MODULE=on`，那么`go`命令行就会使用`modules`功能，而不会访问`GOPATH`。也把这种模式称作`module-aware`模式，这种模式下，`GOPATH`不再在`build`时扮演导入的角色，但是尽管如此，它还是承担着存储下载依赖包的角色。它会将依赖包放在`GOPATH/pkg/mod`目录下。
- 如果`GO111MODULE=auto`，这种模式是默认的模式，也就是说在你不设置的情况下，就是`auto`。这种情况下，`go`命令行会根据当前目录来决定是否启用`module`功能。只有当当前目录在`GOPATH/src`目录之外而且当前目录包含`go.mod`文件或者其子目录包含`go.mod`文件才会启用。

## go.mod文件




如果想获取更多的有关go.mod文件的介绍，可以使用命令go help go.mod

go.mod文件用//注释，而不用/**/。文件的每行都有一条指令，由一个动作加上参数组成



## go build -mod

当执行`go build -mod=vendor`的时候，会在生成可执行文件的同时将项目的依赖包放到主模块的`vendo`r目录下

`go get -m [packages]`会将下载的依赖包放到`GOPATH/pkg/mod`目录下，并且将依赖写入到`go.mod`文件。

go get -u=patch`会更新主模块下的所有依赖包。



有一个专门的命令`go mod tidy`，用来查看和添加缺失的`module`需求声明以及移除不必要的。



`go.mod`文件是可读，也是可编辑的。`go`命令行会自动更新`go.mod`文件来维持一个标准格式以及精确的引入声明。



go.mod`文件用`//`注释，而不用`/**/



## go mod 

`Go mod`提供了一系列操作`modules`的命令，记住，所有的`go`命令中现在已经内置了对`module`的支持，而不仅仅是`go mod`命令。例如使用`go get`时，会经常自动在后台添加、移除、升级、降级依赖包版本。





**go mod tidy**
默认情况下，Go不会移除`go.mod`文件中的无用依赖。所以当你的依赖中有些使用不到了，可以使用`go mod tidy`命令来清除它。
用法：`go mod tidy [-v]`它会添加缺失的模块以及移除不需要的模块。执行后会生成`go.sum`文件(模块下载条目)。添加参数`-v`，例如`go mod tidy -v`可以将执行的信息，即移除的模块打印到标准输出。
**go mod vendor**
用法：`go mod vendor [-v]`，此命令会将`build`阶段需要的所有依赖包放到主模块所在的`vendor`目录中，并且测试所有主模块的包。同理`go mod vendor -v`会将添加到`vendor`中的模块打印到标准输出。
**go mod verify**
用法：`go mod verify`。此命令会检查当前模块的依赖是否已经存储在本地下载的源代码缓存中，以及检查自从下载下来是否有修改。如果所有的模块都没有修改，那么会打印`all modules verified`，否则会打印变化的内容。



## 如何下载依赖包？ 

单个依赖包， go get XXX  , 或者加参数-u  , 更新一下已经下载的依赖包

或者

go mod download -json 



### 升级依赖

升级 major 版本: `go get -u github.com/xxx/xxx`
升级全部依赖的 minor 或 patch 版本: `go get -u`
升级全部依赖的 patch 版本: `go get -u=patch`

### 如何自动下载所有依赖包？

大部分情况下大家下载 Go 项目都是使用`go get`命令，它除了会下载指定的项目代码，还会去下载这个项目所依赖的所有项目。

但是有的时候我们的项目由于各种原因并不是通过`go get`下载的，是通过`git clone`下载的，这样代码下下来就没有依赖包了，没办法编译通过的。这样的话怎么办呢？

> ```
> go get -d -v ./...
> ```

- `-d`标志只下载代码包，不执行安装命令；
- `-v`打印详细日志和调试日志。这里加上这个标志会把每个下载的包都打印出来；
- `./...`这个表示路径，代表当前目录下所有的文件。







# 写一个go程序如何build,同时把依赖放到包里面？

1. 新建一个项目目录， 编写代码



```
[root@node1 detect-kernel-config]# ls
detect-kernel-config.go
[root@node1 detect-kernel-config]# vim detect-kernel-config.go
```



2. go mod init 初始化

```
[root@node1 detect-kernel-config]# go mod init
go: cannot determine module path for source directory /opt/detect-kernel-config (outside GOPATH, module path must be specified)

Example usage:
        'go mod init example.com/m' to initialize a v0 or v1 module
        'go mod init example.com/m/v2' to initialize a v2 module

Run 'go help mod init' for more information.
[root@node1 detect-kernel-config]# go mod init detect-kernel
go: creating new go.mod: module detect-kernel
[root@node1 detect-kernel-config]# ls
detect-kernel-config.go  go.mod
[root@node1 detect-kernel-config]# vim go.mod
[root@node1 detect-kernel-config]# cat go.mod
module detect-kernel

go 1.15
```



3.  go mod tidy  将go.mod中添加依赖

```
[root@node1 detect-kernel-config]# go mod tidy
go: finding module for package golang.org/x/sys/unix
go: downloading golang.org/x/sys v0.0.0-20201231184435-2d18734c6014
go: found golang.org/x/sys/unix in golang.org/x/sys v0.0.0-20201231184435-2d18734c6014

[root@node1 detect-kernel-config]#
```



4.  准备build, 将依赖的源码放到该项目目录树之中

   执行命令`go mod vendor`生成vendor文件夹，该文件夹下将会放置你`go.mod`文件描述的依赖包，文件夹下同时还有一个文件`modules.txt`，它是你整个工程的所有模块。在执行这条命令之前，如果你工程之前有vendor目录，应该先进行删除。同理`go mod vendor -v`会将添加到vendor中的模块打印出来；

```
[root@node1 detect-kernel-config]# go mod vendor -v

# golang.org/x/sys v0.0.0-20201231184435-2d18734c6014

## explicit

golang.org/x/sys/internal/unsafeheader
golang.org/x/sys/unix
[root@node1 detect-kernel-config]# ls
detect-kernel-config.go  go.mod  go.sum  vendor
```

5. 开始build

```
[root@node1 detect-kernel-config]# ls
detect-kernel-config.go  go.mod  go.sum  vendor
[root@node1 detect-kernel-config]# go build
[root@node1 detect-kernel-config]# ls
detect-kernel  detect-kernel-config.go  go.mod  go.sum  vendor
[root@node1 detect-kernel-config]# ./detect-kernel
release:5.9.14-1.el7.elrepo.x86_64
kernel config has been found
```

生成的二进制文件名，是开头go mod init命令参数module_name



6. 修改代码时检查依赖是否有修改

```
[root@node1 detect-kernel-config]# go mod verify
all modules verified

```

执行命令`go mod verify`来检查当前模块的依赖是否全部下载下来，是否下载下来被修改过。如果所有的模块都没有被修改过，那么执行这条命令之后，会打印`all modules verified`





# go项目中如何包含私有包

go mod初始化：在$GOPATH外建一个文件夹，把个人代码放进去，我的测试代码路径：https://github.com/kevinhao8/go-mod-example。

首先main入口代码所在文件夹创建mod

创建语句 go mod init [module name]

比如我的测试代码 redisTest.go，创建语句就是 go mod init redisTest，成功创建时返回 go: creating new go.mod: module redisTest

此时文件夹下出现 go.mod文件，打开发现只有2行如下，并没有记录依赖库。

module redisTest

go 1.12

此时需要输入go test语句，根据需要的依赖自动生成require，也就是依赖包，此时go.mod多了如下内容（红字是我写的注释，文件里面没有）

require (
   github.com/gin-contrib/sse v0.0.0-20190301062529-5545eab6dad3 // indirect（有indirect注释的代表间接依赖，没有的代表直接依赖）
   github.com/gin-gonic/gin v1.3.0 
   github.com/golang/protobuf v1.3.1 // indirect
   github.com/mattn/go-isatty v0.0.7 // indirect
   github.com/ugorji/go/codec v0.0.0-20190316192920-e2bddce071ad // indirect（这里是版本号+时间戳+hash）
   gopkg.in/go-playground/validator.v8 v8.18.2 // indirect
   gopkg.in/yaml.v2 v2.2.2 // indirect
)

此时会有如下失败提示，此处为我踩得第一个坑！！！花了挺长时间才解决

build redisTest: cannot load dbredis: cannot find module providing package dbredis

dbredis是我写的私有包，代码是没有问题的，为什么找不到呢？从网上查了一圈，**发现私有包如果不想发布到网上，需要手动添加require ，然后replace 进行替换，将私有包指向本地module所在的绝对或相对路径。一般用相对路径更通用。**

此时手动将go.mod改为如下，红字为新加

require (
   **dbredis v0.0.0**
   github.com/gin-contrib/sse v0.0.0-20190301062529-5545eab6dad3 // indirect
   github.com/gin-gonic/gin v1.3.0
   github.com/golang/protobuf v1.3.1 // indirect
   github.com/mattn/go-isatty v0.0.7 // indirect
   github.com/ugorji/go/codec v0.0.0-20190316192920-e2bddce071ad // indirect
   gopkg.in/go-playground/validator.v8 v8.18.2 // indirect
   gopkg.in/yaml.v2 v2.2.2 // indirect
)

replace dbredis v0.0.0 => ./dbredis

再度运行go test 命令，发现仍有失败提示如下，找不到dbredis文件夹下的go.mod文件。

go: parsing dbredis\go.mod: open E:\code\go-mod-example\dbredis\go.mod: The system cannot find the file specified.
go: error loading module requirements

从网上查资料发现，这种情况下需要给私有包也生成mod，这样整个工程的依赖才能完整。故运行如下命令：

cd dbredis

go mod init dbredis（此处我写的mod名跟package名一致，不知道不一致行不行）

go test

三条命令依次运行通过，dbredis文件夹下的go.mod文件如下：

module dbredis

go 1.12

require github.com/go-redis/redis v6.15.2+incompatible

此时再运行如下命令：

cd ..（回到上层文件夹）

go test

运行通过，不再有报错。运行命令 go build redisTest.go 也能够正常生成redisTest.exe

至此通过mod来管理依赖包基本实现，我们的程序能基本脱离$GOPATH编译。



















