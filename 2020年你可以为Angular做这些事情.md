### 原文地址
[How you can help Angular in 2020](https://indepth.dev/how-you-can-help-angular-in-2020/)

### 译文内容
2020年Angular面临的最严峻的挑战是什么？

Ivy在过去几年里占据了Angular团队的大部分时间，2020年仍有很多工作要做。

为了保证Angular团队能够专注于开发Angular10和11的新特性，我们会分析Angular生态系统的走向，并提出你可以帮忙解决的问题。

#### RxJS
2020年我们将会看到RxJS版本7。它不推荐我们继续使用那些将在版本8中废弃的特性，并且为在版本6.x废弃的特性引入了中大改变。2020年我们甚至有可能看到RxJS版本8的出现。

官方的Angular包设置了RxJS的兼容性标准，Angular9的发布大概率会支持RxJS6.5。

这可能会给跟RxJS密切相关的框架带来挑战，我们仍然清楚的记得从RxJS5.x升级到6.0的时候发生了什么。

由于RxJS7.0和8.0会带来比较大的改变，Angular只会在其主要版本中升级RxJS的兼容性。也就是说，我们看到的最早支持RxJS7.0的Angular版本将是版本10。同样的，RxJS8.x的兼容性最早在Angular11中可以见到。

幸运的是，Angular脚手架示意图使得在重大改变下的迁移和升级非常容易。

#### 我能做些什么？
1. 写一些关于RxJS7.x和8.x新特性的文章
2. [为RxJS文档做贡献](https://dzhavat.github.io/2020/01/03/getting-started-with-contributing-to-rxjs-docs.html)
3. 帮助建设RxJS7.0和8.0的`ng update`示意图

#### Bazel
计划将支持Bazel2.1作为Angular9的可选项加入。最终，Bazel将会成为Angular脚手架默认的自动化构建工具。

#### 我能做些什么？
1. 在你的Angular9项目中通过运行以下命令集成Bazel
```
ng add @angular/bazel
```
2. 先参考[Bazel 安装指导](https://docs.bazel.build/versions/2.0.0/install.html)新建一个带有Bazel的Angular9的项目，然后执行以下命令：
```
ngx -p @angular/bazel ng new --collection=@angular/bazel my-angular-workspace
```
3. 为[Angular](https://github.com/angular/angular/issues) 和[Bazel](https://github.com/bazelbuild/bazel/issues) 的GitHub 仓库提交issues

添加`@angular/bazel`转换我们的angular.json文件，以使用`@angular/bazel:build`Angular脚手架生成器来生成启动和配置文件来支持类似RxJS和Protractor等依赖项。

#### More（译者添加）
Bazel是什么？

Protractor是什么？

#### TSLint
[TSLint 已经被废弃了](https://github.com/palantir/tslint/issues/4534#issue-413722441)，2020年底会停止维护。

官方的Angular脚手架lint生成器和Codelyzer的lint规则都是基于TSLint的，Angular开发团队[打算在Angular10中使用ESLint替换TSLint](https://github.com/angular/angular-cli/issues/13732#issuecomment-573149865)

#### 我能做些什么？
1. 参考[Minko Gechev更新的这个issue](https://github.com/angular/angular-cli/issues/13732#issuecomment-575796158)
2. 帮助James Henry创建一个[基于ESLint的Angular脚手架生成器](https://github.com/angular-eslint/angular-eslint/tree/master/packages/builder)，以及[将Codelyzer的规则迁移到ESLint](https://github.com/angular-eslint/angular-eslint#rules-list)