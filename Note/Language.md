# 编程语言

计算机（Computer）和计算（Compute）的概念

* 在计算机的层次上，CPU执行的是加减乘除的指令代码，以及各种条件判断和跳转指令，所以，汇编语言是最贴近计算机的语言。
* 计算则指数学意义上的计算，越是抽象的计算，离计算机硬件越远。

## 发展

* 汇编语言
* 编译器
* 函数：把长长的代码封装起来，这样就写一次，就可以到处调用了
* 数据结构：把参数组织起来，以后就传递这个数据结构
* Objec：数据和操作给结合起来，形成Objec，以后Object的属性数据不允许直接访问，只能通过这个Object的函数来操作
* Object的方法都是相同的，他们被重复地放在一个个对象当中，Class！把这些重复的方法代码从对象中剥离出来，放到一个公共的Class中！用new 这个关键字： Stack object1 = new Stack(); 
* Stack类的函数定义只有一份，但是Stack类生成的Object有很多份。 人类在写push()函数，pop()函数的时候，要操作Object的数据， 到底操作的是哪一个Object？ 上帝说：“ 要有this !”
* 有继承！把那些类似的、重复代码放到父类当中去，这样子类就可以直接使用，不用重新再写一遍了。
* 要有多态！就是对同一个接口，使用不同的实例而执行不同操作。
* 继承其实是破坏了封装性，父类的很多细节对子类都是可见的，父类的变化可能会极大地影响子类。优先使用组合而不是继承.面向接口编程，而不是实现编程。
* 编程的关键是要抽象啊！你们得把系统需求抽象成高层的概念，然后在概念层次进行编程。

## 类别

* 面向对象
* 命令式
* 函数式
* 程序式

## 分类

* 对应到编程语言，就是越低级的语言，越贴近计算机，抽象程度低，执行效率高，比如C语言；越高级的语言，越贴近计算，抽象程度高，执行效率低，比如Lisp语言。
* 动态语言，即脚本语言，因其易于编写和易于运行的特性，被预测在未来将发展强大。因此，Perl，Python，PHP 和 Ruby 等语言在当时非常流行。今天看来，似乎只有 Python 的发展很强势
* C语言是可以用来编写操作系统的贴近硬件的语言，所以，C语言适合开发那些追求运行速度、充分发挥硬件性能的程序。而Python是用来编写应用程序的高级编程语言。
* 高级编程语言通常都会提供一个比较完善的基础代码库，让你能直接调用

## 编程

编程的核心在于通过各种各样的算法去实现具体的业务逻辑，把繁杂的过程抽象化、可计算化。从纯粹软件的角度讲，甚至可以说：算法 + 数据 == 计算机程序。

## 方法

* 请千万不要用“复制”-“粘贴”把代码从页面粘贴到你自己的电脑上。写程序也讲究一个感觉，你需要一个字母一个字母地把代码自己敲进去，在敲代码的过程中，初学者经常会敲错代码：拼写不对，大小写不对，混用中英文标点，混用空格和Tab键，所以，你需要仔细地检查、对照，才能以最快的速度掌握如何写程序。

## 内容

* 了解编程语言的语法特征
    - 数据类型
    - 运算符
    - 控制语句
    - 编程模式
* 掌握编译或解释的过程，及编译器/解释器性能，调试方法、工具
* 配合算法，实现业务逻辑

## 排名

* 语言
    - go
    - Scala
    - Ruby
    - TypeScript
* 类型
    - 区块链：需求同比增长 517%：精通区块链的工程师通常拥有后端工程师、系统工程师或解决方案架构师等头衔
* 结对编程（一种开发方式，两名程序员在一个工作站上工作）的工作方式能够提高自己在一家公司工作的意愿

## 工具

* [Ruby regular expression editor](https://rubular.com):a Ruby regular expression editor
* [bazelbuild/bazel](https://github.com/bazelbuild/bazel):a fast, scalable, multi-language and extensible build system https://bazel.build

## 语言

* [unisonweb/unison](https://github.com/unisonweb/unison):Next generation programming language, currently in development http://unisonweb.org
* [vlang/v](https://github.com/vlang/v):Simple, fast, safe, compiled language for developing maintainable software. Compiles itself in <1s with zero dependencies. 1.0 release in December 2019. https://vlang.io

## 资源

* [learndesignthehardway](https://www.learndesignthehardway.com)
* [EZLippi/practical-programming-books](https://github.com/EZLippi/practical-programming-books)这里收录比较实用的计算机相关技术书籍，可以在短期之内入门的简单实用教程、一些技术网站以及一些写的比较好的博文
* [freeCodeCamp/freeCodeCamp](https://github.com/freeCodeCamp/freeCodeCamp):The https://freeCodeCamp.org open source codebase and curriculum. Learn to code for free together with millions of people.
* [sdmg15/Best-websites-a-programmer-should-visit](https://github.com/sdmg15/Best-websites-a-programmer-should-visit):🔗 Some useful websites for programmers.
* [wx-chevalier/ProgrammingLanguage-Series](https://github.com/wx-chevalier/ProgrammingLanguage-Series📚 编程语言语法基础与工程实践，JavaScript | Java | Python | Go | Rust | CPP | Swift)
* [KeKe-Li/book](https://github.com/KeKe-Li/book):📚 All programming languages books https://github.com/KeKe-Li/book
