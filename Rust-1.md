
## 为啥要使用Rust这个编程语言？ Rust有啥优势？


https://www.zhihu.com/question/456820006


微软windows、Linux 每年修复的漏洞中有70%与 内存安全有关， Rust的特别优势就是：内存安全。 

Linux驱动程序可能率先Rust化，Rust的性能与C不相上下。 

[2022 年，Rust 将成为 Linux 内核第二官方语言？](https://www.infoq.cn/article/uyhdleo68ycy5jntwxkv)


## 为什么 Rust 如此受欢迎？
 

Rust 致力于成为优雅解决高并发和高安全性系统问题的编程语言，适用于大型场景，即创造维护能够保持大型系统完整的边界。这就导致了它强调安全，内存布局控制和并发的特点。
标准 Rust 性能与标准 C++性能不相上下。


## Rust特点

[Rust口碑那么好，为何学的人那么少？](https://www.51cto.com/article/713598.html)

高效、安全但难学

[为什么 Rust 是编程的未来？](https://zhuanlan.zhihu.com/p/436382446)


## 何时选择Rust?

[何时使用 Rust 和何时使用 Golang？](https://www.51cto.com/article/629271.html)


开发人员体验

首先，让我们看一下每种语言的学习曲线。Golang 在设计时考虑了简单性。开发人员经常将其称为“无聊”语言，也就是说，其有限的内置功能集使 Golang 易于学习、使用。

此外，Golang 提供了比 C++更简单的替代方案，隐藏了诸如内存安全性和内存分配等方面的内容。Rust 采用了另一种方法，迫使您考虑诸如内存安全性的概念。所有权的概念和传递指针的能力使 Rust 失去了学习的吸引力。当您不断考虑内存安全性时，您的工作效率就会降低，并且您的代码注定会变得更加复杂。

与 Golang 相比，Rust 的学习曲线非常陡峭。值得一提的是，与 Python 和 JavaScript 等动态语言相比，Golang 的学习曲线较为陡峭。

## 何时使用 Golang

Go 在各种用例中都能很好地工作，使其成为创建 Web API 的 Node.js 的绝佳替代品。正如Loris Cro[12]指出的那样，“ Go 的并发模型非常适合必须处理多个独立请求的服务器端应用程序”。这正是 Golang 提供 goroutines 的原因。

此外，Golang 内置了对 HTTP Web 协议的支持。您可以使用内置的 HTTP 支持快速设计一个小型 API，并将其作为微服务运行。因此，Golang 非常适合微服务架构并满足 API 开发人员的需求。

简而言之，如果您重视开发速度并且更喜欢语法简单而不是性能，那么 Golang 是一个很好的选择。最重要的是，Golang 提供了更好的代码可读性，这对于大型开发团队而言是一个重要标准。

Golang 最初是由 Google 的工程师设计的，于 2009 年向公众推出。它的创建是为 C++提供替代方案，该替代方案易于学习和编码，并且经过优化可在多核 CPU 上运行。

从那时起，Golang 对于希望利用该语言提供的并发性的开发人员来说非常有用。该语言提供了 goroutine，使您可以将函数作为 goroutine 运行。

Golang 的一大优势是您可以轻松使用 goroutines。只需将go添加到函数前即可使其作为 goroutine 运行。Golang 的并发模型允许您跨多个 CPU 内核部署工作负载，从而使其成为一种非常有效的语言。

### glang的优势在于goroutine和并发。

如上所述，Golang 支持并发。例如，假设您正在运行一个处理 API 请求的 Web 服务器。您可以使用 Golang 的 goroutine 程序将每个请求作为 goroutine 运行，通过将任务分发到所有可用的 CPU 内核来最大程度地提高效率。

Goroutines 是 Golang 内置功能的一部分，而 Rust 仅支持本地 async/await 语法来支持并发。因此，在并发方面，Golang 的开发人员有经验优势。但是，Rust 在保证内存安全方面要好得多。


## Rust 开发IDE 

https://www.jetbrains.com/zh-cn/rust/

https://www.zhihu.com/question/40914986

截至到目前，最佳选择是Vscode+Rust Analyzer或者Clion(Liux下调试好用，Windows下需要GNU编译链）

就目前的使用体验来说，IntelliJ Rust 在编码时的体验是比 VS Code + RLS 要好不少的，功能全面。相比之下 rls-vscode 插件还差得比较远，而且经常引入新 BUG ，谁用谁知道



