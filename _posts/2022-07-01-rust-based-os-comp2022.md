---
title: 2022年开源操作系统训练营学习笔记
category: [course, website]
tags: [self-learning, rust]
---

> 之前一直就想完完整整地做一次操作系统的Lab，但值得学习的课程资料实在是太多了，经典的xv6，modern的OSTEP，包括IPADS的ChCore...不过借着清华这样一次开源操作系统训练营的机会，就从rCore开始吧，这篇博客用来记录我在训练营中的笔记

## 7.1 Day1

今天仔细看了本次训练营的大概内容[^schedule]，主要关注workload，目前看来代码量不是特别大，每天做一点的话应该是能完成的，关键在于要多提问题并记录在issue中

- 在学习实践过程记录表上登记自己每日/周学习记录情况的repo网址，并在这个repo上记录每日/周学习记录情况 (成绩分数：20%)
- 在第一阶段学习issues上的提问和回答问题情况，在第一阶段OS学习项目 、 rCore Tutorial v3的详细实验指导内容 上的Pull Request提交情况（代码改进、文档改进、文档错误等） (成绩分数：15%)
- step 0 要求的编程代码的完成情况 (成绩分数：15%)
- step 2 第一阶段OS学习项目的5个实验的完成情况 (成绩分数：50%)

## 7.2 Day2

今天进行了[第零章：实验环境配置](https://learningos.github.io/rust-based-os-comp2022/0setup-devel-env.html)的工作。其实我在之前就配置过 rCore 相关的 Qemu，riscv64-gnu 工具链和 Rust 开发环境了，所以很流畅地就把 hello world 给跑起来了。不过我也尝试了一下 Github classroom 的新功能，感觉还是挺有意思，可以通过 CI 的方式来提交作业。就是可能受到网速和云主机的影响，运行起来没有那么快，这也是 cloud editing 的一些通病吧。

最后决定还是使用本地开发了，明天开始看看 Rust 系统编程的资料，捡一捡 Rust 语言基础。

## 7.3 Day3

今天开始[step0 自学Rust编程](https://github.com/rcore-os/rCore/wiki/study-resource-of-system-programming-in-RUST)。咋一眼看上去觉得 Rust 的教程太多了，比我之前自己整理的`./2022-04-20-Rust-learning-record.md`{: .filepath}多了不知道哪里去了。不过还是不能一口气吃个大胖子，先按照训练营的要求来做吧。

由于我之前已经做过 [rustlings](https://github.com/cascades-sjtu/rustlings) 了， 所以直接开始做 [32 Rust Quizes](https://dtolnay.github.io/rust-quiz/1)，对照着教程复习一些语法的知识，顺便把 writeup 写在下面。这个练习的难度相对来说比 rustlings 要难，需要理解 rust macro，还包括了一些 corner case。所以也不要给自己太大压力了，先求了解吧。

1. Rust 宏定义以及参数传递。Rust 宏和 C 的字符串预处理不同，是带有语义类型的替换
2. 使用 Rustfmt 进行语义分析，闭包，impl trait
3. const 修饰符用于直接替换，即修改的是临时变量。值的命名空间和变量的命名空间不一样
4. `..` 可以代表通配的元素或者是切片中的 RangeFull，u8 类型的 ASCII 值
5. 闭包参数的类型推断
6. 变量覆盖，可以手动借助中间变量推断，赋值语句的值为`()`，是 zero-sized type
7. match arm 和 `enum` 类型的匹配，prelude 提前引入了 `Result::Ok` 和 `Option::Some`
8. `macro_rules!` 中 token 的组织，单个 token 的字符数，例如 `==>` 被解析为了 `== >`
9. opaque exporession token
10. `dyn Trait`
11. 函数指针之间不要相互比较，延迟生命周期绑定
12. `Drop` 的时机和所有者有关
13. ZST 可以同时有多个可变引用，且他们指向同一位置，但不存在解引用的操作
14. `impl Trait` 的作用范围在程序的全局，auto-ref 机制
15. Trait 在作用时的类型推断
16. Rust 没有自增和自减的一元运算符，`--x = -(-x)`
17. 同16，解析的结果为`a-- - --b = a - (-(-(-(-b))))`
18. 调用成员变量的函数指针 VS 调用成员函数
19. s 的所有权发生了转移，但直到程序括号结束后才被移除，从而调用 `Drop`
20. return 表达式需要先被求值再被返回，break-with-value 表达式，`return` 和 `break` 的eager consume 行为不同
21. 判断是 `|| true` 的闭包函数还是 `()`，break-with-value 表达式
22. 判断宏参数包含的 token 个数，默认 `-` 为单独的 token，也可以将整个负数解析为一个 token
23. 默认调用内置方法和不可变引用
24. 宏的变量默认使用局部变量，`const` 代表的不是局部变量，会被覆盖掉
25. 函数将返回值的所有权转移给调用者，但返回值作为类型，没有接受的变量，所以被 `Drop` 了两次
26. `Iterator::map` 中的闭包是跟随实际的迭代器，延迟调用的
27. Supertrait，动态分发和静态分发
28. `let _ = Guard` 后立马调用 `Drop`，因为没有 owner 了
29. 1-tuple 需要显式地表示，但多元 tuple 不需要；intergral literal 默认被推断为 `i32`
30. &ZST 的大小不为0，`&T` 和 `Rc<T>` 都默认实现 Clone trait
31. 函数签名的匹配顺序和 auto-ref 机制的配合
32. match arm 中的 if 语句会作用于尽可能多的 arm
33. `(|| .. .method())() = (|| ..).method()`，随后再调用返回的闭包

完结证明：

![](https://s2.loli.net/2022/07/05/whHlBA5Sj3a4FmR.png)

## 7.4 Day4

今天继续昨天的 32 Rust Quizes，不过训练营的要求又改动了，所以还需要在 Github Classroom 里面额外复习一遍 rustlings。

- 在[学习实践过程记录表](https://github.com/LearningOS/rust-based-os-comp2022/issues/1)上登记自己每日/周学习记录情况的repo网址，并在这个repo上记录每日/周学习记录情况 (成绩分数：20%)
  
  - [学习记录的标杆](https://kiprey.github.io/tags/uCore/)，这是一位本科生的自学ucore for x86的过程记录，是大家学习的榜样，供大家学习参考。

- 在[第一阶段学习issues](https://github.com/LearningOS/rust-based-os-comp2022/issues/)上的提问和回答问题情况，在[第一阶段OS学习项目](https://github.com/LearningOS/rust-based-os-comp2022/) 、 [rCore Tutorial v3的详细实验指导内容](https://rcore-os.github.io/rCore-Tutorial-Book-v3/) 上的Pull Request提交情况（代码改进、文档改进、文档错误等） (成绩分数：15%)

- step 0 要求的[Rust-lang Lab Test based on Rustlings（采用Github Classroom模式的Rustling小练习）](https://classroom.github.com/a/YTNg1dEH) 的完成情况 (成绩分数：15%)

- step 2 [第一阶段OS学习的5个实验](https://github.com/LearningOS/rust-based-os-comp2022#kernel-labs)的完成情况 (成绩分数：50%)，详情看课程网站[^website]

Rust 部分打算跟着[张汉东老师的视频](https://space.bilibili.com/24917186/video)复习吧，当作是增加理解了

## 7.5 Day5

在 codespace 上搭建了 rustlings 的练习环境，跟着张老师的视频。因为之前做过 rustlings 了，所以感觉视频有些无聊，于是便开始自己先按照节奏把前面简单的部分做完了。但张老师的思路是对的，做这样的练习不在于做的多快多正确，而是要理解题目背后的逻辑。之后有时间也可以看看 rustlings 本身的代码。

今天开会后加了老师，助教和一些营员的微信，准备和 [ShiLi](https://shili2017.github.io) 同学组个队。此外，还加了几个飞书的群聊，多接受一些 Rust 资讯吧。

## 7.6 Day6

今天做完了 rustlings 剩下的部分，和第一次做的时候相比来说更加多了些经验，也在不断学习官方的一些写法，重构之前的练习代码，比如 match arm 的妙用。同时，也增加了对于错误处理和类型转换两个主题的理解（之前做的时候可能没仔细看）。本质上说，都是实现 trait，不过是根据 Rust 一些规定的要求来实现 trait，从而能够使用一些内置的函数（比如 `from` 和 `parse`）。在实现 trait 的过程中，我对于编译器报错的阅读能力也增加了。

完结证明（顺便吐槽一下 auto-grading 的问题，只有0/100两种分数）：

![](https://s2.loli.net/2022/07/06/XiYpdvnoQTEAmUR.png)

## 7.7 Day7

今天开始正式做 lab 了。虽然我还没有系统性地看老师给的 RISC-V 的资料，不过还是想着边看边学吧。今天看完了第零章的导读，在我的 MBP 上配置好了环境。由于 Makefile 中有些指令在 MacOS 下的表现不太一样，所以我在云服务器上也配置了一遍环境。读第零章印象最深的三个概念就是：

- rCore 是按照历史发展各个阶段的 OS 形态来展开，从 libOS 到 batch OS，再到现在通用性的 OS
- OS 大致提供了三层的抽象
  1. CPU -> 进程
  2. 物理内存 -> 地址空间
  3. 外设 -> 文件
- 异常控制流的定义有很多种，大概分为中断、陷入、异常。都需要 CPU 切换运行环境，并保存上下文

其他的，对于一些零碎的概念（比如编译目标三元组的表示， RISCV 类型的标记）都更加熟悉了。同时，课程传达的“实践引领原理”的学习顺序也让我产生了一些思考。

从第一章开始，课程开始讲解如何在裸机上编写 Rust 代码，从编译工具链的切换到 `!#[no_std]` 这样的声明，这都和以前在应用程序层编程的体验不同，也是 Rust 作为系统级语言必备的特性。此外，我复习了 Qemu 和计算机启动的工作原理。还做了一部分手动适配 Qemu 的工作，并简单实践了链接器脚本和 `rust-objcopy --strip-all`。

最后，我把只包含一行汇编，经过裁剪后的内核映像在 Qemu 上跑了起来，并通过 gdb 进行了初步的调试。顺便吐槽一下，brew 提供的 `riscv64-unknown-elf-gdb` 需要 python2 的支持，所以我选择直接下载 SiFive 预编译的版本。

## 7.8 Day8



## 课程资料

[^schedule]: https://github.com/LearningOS/rust-based-os-comp2022/blob/main/scheduling.md
[^website]: https://learningos.github.io/rust-based-os-comp2022/