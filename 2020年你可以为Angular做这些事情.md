### 原文地址
[How you can help Angular in 2020](https://indepth.dev/how-you-can-help-angular-in-2020/)

### 译文内容
2020年Angular 面临的最严峻的挑战是什么？

Ivy在过去几年里占据了Angular 团队的大部分时间，2020年仍有很多工作要做。

为了保证Angular 团队能够专注于开发Angular 10和11的新特性，我们会分析Angular 生态系统的走向，并提出你可以帮忙解决的问题。

#### RxJS
2020年我们将会看到RxJS 版本7。它不推荐我们继续使用那些将在版本8中废弃的特性，并且为在版本6.x废弃的特性引入了中大改变。2020年我们甚至有可能看到RxJS版本8的出现。

官方的Angular 包设置了RxJS 的兼容性标准，Angular 9的发布大概率会支持RxJS6.5。

这可能会给跟RxJS 密切相关的框架带来挑战，我们仍然清楚的记得从RxJS 5.x升级到6.0的时候发生了什么。

由于RxJS 7.0和8.0会带来比较大的改变，Angular 只会在其主要版本中升级RxJS 的兼容性。也就是说，我们看到的最早支持RxJS 7.0的Angular 版本将是版本10。同样的，RxJS 8.x的兼容性最早在Angular 11中可以见到。

幸运的是，Angular 脚手架示意图使得在重大改变下的迁移和升级非常容易。

#### 我能做些什么？
1. 写一些关于RxJS 7.x和8.x新特性的文章
2. [为RxJS文档做贡献](https://dzhavat.github.io/2020/01/03/getting-started-with-contributing-to-rxjs-docs.html)
3. 帮助建设RxJS7.0和8.0的`ng update`示意图

#### Bazel
计划将支持Bazel 2.1作为Angular 9的可选项加入。最终，Bazel 将会成为Angular 脚手架默认的自动化构建工具。

#### 我能做些什么？
1. 在你的Angular 9项目中通过运行以下命令集成Bazel
```
ng add @angular/bazel
```
2. 先参考[Bazel 安装指导](https://docs.bazel.build/versions/2.0.0/install.html)新建一个带有Bazel 的Angular 9的项目，然后执行以下命令：
```
ngx -p @angular/bazel ng new --collection=@angular/bazel my-angular-workspace
```
3. 为[Angular](https://github.com/angular/angular/issues) 和[Bazel](https://github.com/bazelbuild/bazel/issues) 的GitHub 仓库提交issues

添加`@angular/bazel`转换我们的angular.json 文件，以使用`@angular/bazel:build` Angular 脚手架生成器来生成启动和配置文件来支持类似RxJS和Protractor等依赖项。

#### More（译者添加）
Bazel 是什么？

Protractor 是什么？

#### TSLint
[TSLint 已经被废弃了](https://github.com/palantir/tslint/issues/4534#issue-413722441)，2020年底会停止维护。

官方的Angular 脚手架lint 生成器和Codelyzer 的lint 规则都是基于TSLint 的，Angular 开发团队[打算在Angular10中使用ESLint替换TSLint](https://github.com/angular/angular-cli/issues/13732#issuecomment-573149865)

#### 我能做些什么？
1. 参考[Minko Gechev更新的这个issue](https://github.com/angular/angular-cli/issues/13732#issuecomment-575796158)；
2. 帮助James Henry 创建一个[基于ESLint的Angular脚手架生成器](https://github.com/angular-eslint/angular-eslint/tree/master/packages/builder)，以及[将Codelyzer的规则迁移到ESLint](https://github.com/angular-eslint/angular-eslint#rules-list)。

#### Protractor
作为伴随Angular 开箱即用的端到端测试框架，Protractor 的情况不容乐观。尽管Protractor 2019年的issue 的数量一直在增长（2019年一年就有超过200个打开的issue），也为它包含的Selenium WebDriver APIs 带来了巨大的改变，但是它基本上没有被使用过。

Protractor 最近的一个稳定版本（5.4.2）是在2018年12月发布的。2019年3月在NPM发布了一个[非官方版本6](https://github.com/angular/protractor/issues/5290#issuecomment-521320499)，但是至今都没有被打上latest的标签。

首先，由于Selenium WebDriver 的改变，我们不得不将自己所有的测试用例从同步的方式改为使用`async-await` 的异步方式。其次，一些特性被破坏了，并且文档和类型部分丢失或过时了。

[Angular 开发团队已经接手了Protractor](https://github.com/angular/protractor/issues/5209#issuecomment-523182031)，但是处理Ivy 就够他们忙的了，他们可能没有时间来处理Protractor 和Selenium.

#### 我能做些什么？
1. Protractor 需要`ng update` 示意图，分别用于将测试用例从Protractor 5.x升级到6.0和在浏览器中查询DOM的时候使用`async-await` 方法；
2. Protractor 需要来自`elenium-webdriver` 全量的更新类型；
3. Protractor 需要更新它的文档以体现出两方面的信息：一是API 的变更，二是一般的测试流程；
4. `webdriver-manager` (Protractor 的一部分) 需要解决所有的bug 并且更新到它的文档中。

#### Augury
Augury 是由Rangle.io 维护的官方浏览器开发调试工具。尽管它一直在修复问题，但是自从最初版本出来之后没有看到任何值得留意的新增特性。

2020年Augury 面临的挑战是它[没有任何支持Ivy 的计划](https://github.com/rangle/augury#supported-version)，所以对于新的使用Ivy 维护的项目来说，它将不再适用。

Ivy 移除了`ng.probe`，但是引入了一组新的用于运行时调试的API。

#### 我能做些什么？
1. 问一下Angular 开发团队你能为[新的调试API 的文档建设](https://github.com/angular/angular/issues/30737#issuecomment-498284237)做些什么；
2. [针对Augury](https://github.com/rangle/augury)，迁移到Ivy 新的调试API 并提交pull request；
3. 为新的使用场景像[Augury GitHub仓库](https://github.com/rangle/augury/issues) 提供新的issue。
