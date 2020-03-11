### 原文地址
[Learn to combine RxJs sequences with super intuitive interactive diagrams](https://indepth.dev/learn-to-combine-rxjs-sequences-with-super-intuitive-interactive-diagrams/)

### 译文内容
![rxjs](https://res.cloudinary.com/indepth-dev/image/fetch/w_1000,f_auto/https://admin.indepth.dev/content/images/2020/02/1_fKySmHgkJLKhg_dNFmF_yQ.png)

在本文中，您将找到最流行的RxJS组合运算符（如mergeMap、forkJoin等）的动态可视化解释。本文还描述了每一种运算符的具体使用方式和终止条件.

当你面对足够复杂的应用程序时,你通常需要面对来自多处数据源的数据. 它可以是多个像Firebase 类的外部数据点,也可以是用户正在使用的几个UI组件.

通过组合序列技术，你可以将来自不同数据流的数据融合成一次进而完成复杂查询。RxJs提供了非常多的运算符来帮你完成上面提到的事情，在这篇文章里我们来看一下最常用的一些。

为了制作动图直观的展示不同操作符处理数据流的方式，我都快成了兼职动画专家了。不过，这些动图都是作为GIF动画嵌入的，加载需要一点时间，请耐心一点等待。

在随附的代码中，我将使用可操作的运算符，所以如果你对他们不熟悉的话可以参考这里（把它称为“管道运算符”）。我将使用自定义流运算符，改运算符会在被订阅时同步交付的第一项数据后保持异步数据发送。

以下是我会在文章中持续使用的图表的图例：

![stream](https://admin.indepth.dev/content/images/2020/02/1_AxPXs_XCzhvBD5Y-OHrrZQ.gif)

#### 简单操作符

##### 同时合并多个序列

首先我们来看以下merge操作符。这个操作符会结合来自不同输入流的若干数据，并将这些所有的数据同时发出。只要有数据从被结合的输入流中产生，这些数据就会出现在最终的结果序列里。这个过程在文档中通常被称为扁平化。

只有所有的输入流都发送数据完成，由merge操作符结合的数据流序列才算完成。只要其中任何一个数据流出错或者没有完成，merge操作就不会完成。

如果你不关心不同数据的产生顺序，只想知道来自不同数据源的全部数据有哪些，那你就可以使用这个操作符，就像他们是来自于同一个数据流一样。

下图展示了merge操作对来自于A和B两个数据流的数据的合并。A和B每个数据流都发出3条数据，他们一经产生就会被合并到结果序列中。

![merge](https://admin.indepth.dev/content/images/2020/02/11.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3, 'partial');
const b = stream('b', 200, 3, 'partial');
merge(a, b).subscribe(fullObserver('merge'));
// can also be used as an instance operator
a.pipe(merge(b)).subscribe(fullObserver('merge'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/combining-sequences-merge)：

![mergeImg](./img/rxjs/merge.jpg)

##### 顺序连接多个序列

下一个组合的方法是concat。它连接所有订阅的流，并依次发出每个流的数据，每次只有一个有效的订阅。一旦当前的流发送数据完成，那就转到下一个数据流，把它传递过来的数据转化为结果序列。

当所有的输入流结束的时候，合并流才完成，如果有输入流报错，合并流就会抛出一个错误。如果某些输入流没有完成，那么合并流也不会完成，因为这意味着有些输入流永远也不会被订阅。

如果数据发出的顺序对你来说是重要的，你想先看到你加入的第一个输入流的数据，那么你就可以使用这个运算符。比如说，你可能有来自缓存和远程服务器的两个来源的数据序列。使用concat连接他们，这样来自缓存中的数据就会被先传递出。

下图中展示了用concat连接来两个不同数据流A和B的数据的过程，每个数据流产生3条数据，可以看到，这些数据先从A然后到B依次进入结果序列中。

![concat](https://admin.indepth.dev/content/images/2020/02/22.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3, 'partial');
const b = stream('b', 200, 3, 'partial');
concat(a, b).subscribe(fullObserver('concat'));
// can also be used as an instance operator
a.pipe(concat(b)).subscribe(fullObserver('concat'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/concat)：

![concatImg](./img/rxjs/concat.jpg)

##### 竞争性序列合并

下一个操作符race介绍了一个很有趣的概念，它不合并每个数据序列，而是用来选择第一个产生数据的可订阅序列。当一个序列开始产生数据的时候，其他的序列就被取消订阅，并且被忽略了。race序列的结果跟随被选中的序列的变化而变化，不论是完成或出错，如果选中序列没有完成，那么race序列永远也不会完成。

当你有多个数据源可以选择的时候，这个操作符就非常有用。比如说，分布在世界各地的服务器由于网络问题，造成的延迟不可预测，而且变化很大。使用这个运算符，你可以把同一个请求发送个多个数据源，然后消费第一个数据源返回的数据。

下图中展示了用race连接来两个不同数据流A和B的数据的过程，每个数据流产生3条数据，从图中可以看到，只有来自A的数据被发出了，因为它是第一个发出数据的流。

![race](https://admin.indepth.dev/content/images/2020/02/33.gif)

这是上面所展示动图的代码示例:

```
const a = intervalProducer('a', 200, 3, 'partial');
const b = intervalProducer('b', 500, 3, 'partial');
race(a, b).subscribe(fullObserver('race'));
// can also be used as an instance operator
a.pipe(race(b)).subscribe(fullObserver('race'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/combining-sequences-race-b-is-ignored)：

![raceImg](./img/rxjs/race.jpg)

#### 利用高阶的可观察对象连接未知数量的序列

上面提到的操作符，不论是作为静态还是实例版本，都只能连接已知数量的输入流序列。但是如果你想连接事先不知道数量，只有在运行时才知道数量的序列该怎么办？实际上，这是在与异步代码打交道的过程中一个非常常见的场景。举例来说，一次网络请求可能导致许多次其他的网络请求，而其他请求的数量由原始的请求结果所决定。

RxJs提供了一种上述操作符的变种，变种的输入是由序列组成的序列，所以被称作高阶可观测对象，也被称为可观测的可观测对象。这些操作符要求从可观测对象出得到的结果是序列（依然是可观测对象），然后运用上面提到的规则处理得到的结果。

只要任何一个被包含的数据流出错，这些操作符就会出错，而且它们只能被作为实例使用。现在，让我们来一个一个地看一下这些操作符。

##### MergeAll

该操作符组合了所有内部发出的流，然后使用普通的merge操作同时每个数据流接收数据。

在下面的动图中你可以看到，高阶的H数据流产生了两个低阶数据流A和B。mergeAll操作符接收了来自两个数据流的数据，然后在他们出现时将它们传递到结果序列中。

![mergeAll](https://admin.indepth.dev/content/images/2020/02/44-1.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3);
const b = stream('b', 200, 3);
const h = interval(100).pipe(take(2), map(i => [a, b][i]));
h.pipe(mergeAll()).subscribe(fullObserver('mergeAll'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/merge-all)：

![mergeAllImg](./img/rxjs/mergeAll.jpg)

##### concatAll

该操作符组合了所有内部发出的流，然后使用普通的concat 操作依次从每个数据流接收数据。

在下面的动图中你可以看到，高阶的H数据流产生了两个低阶数据流A和B。concatAll 操作符先接收来自A的数据，然后接收来自B的数据。然后将它们传递到结果序列中。

![mergeAll](https://admin.indepth.dev/content/images/2020/02/44-1.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3);
const b = stream('b', 200, 3);
const h = interval(100).pipe(take(2), map(i => [a, b][i]));
h.pipe(concatAll()).subscribe(fullObserver('concatAll'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/concat-all)：

![concatAllImg](./img/rxjs/concatAll.jpg)

##### swithcAll

有时候，我们并不想从所有的内层可观测对象中获取数据。在一些场景中，我们之最最新的来自内部序列的值感兴趣。搜索就是一个很好的例子。当用户在搜索框中输入内容时，请求被发送到服务器，因为这是一个异步操作，所以返回值是一个可观测对象。用过用户在返回结果之前更新了搜索框里的内容怎么办呢？第二次请求被发出，所以现在已经有两次查询被发送到了服务器。但是，我们对第一次的搜索并不感兴趣。另外，如果第一次的搜索结果和第二次搜索结果合并在一起了，那么用户会非常惊讶。我们不希望这样，所以这时候就需要用到switchAll运算符。他只订阅并产生从内层数据流传出的最近的值，而忽略其他的数据流。

在下面的动图中你可以看到，高阶的H数据流产生了两个低阶数据流A和B。switchAll操作符先从A中获取数据，一旦B出现，操作符又转向B获取数据，从而放弃A剩下的数据。

![switchAll](https://admin.indepth.dev/content/images/2020/02/66.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3);
const b = stream('b', 200, 3);
const h = interval(100).pipe(take(2), map(i => [a, b][i]));
h.pipe(switchAll()).subscribe(fullObserver('switchAll'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/switch-all)：

![concatAllImg](./img/rxjs/concatAll.jpg)

#### concatMap, mergeMap 和 switchMap

有意思的是，在操作可观测的可观测对象上，concatMap、mergeMap 和switchMap 比concatAll、mergeAll 和switchAll 更常用。但是，如果你仔细考虑一下，它们其实是差不多的东西。所有的Map 类操作符包含两个部分的内容，通过映射和对由高阶数据流产生的内部数据流应用组合逻辑来产生最终的结果数据流。

让我们来看一段熟悉的代码，它描述了mergeAll 是如何工作的：

```
const a = stream('a', 200, 3);
const b = stream('b', 200, 3);
const h = interval(100).pipe(take(2), map(i => [a, b][i]));
h.pipe(mergeAll()).subscribe(fullObserver('mergeAll'));
```

这里面map 提供了一个可观测的数据流，mergeAll 结合了所有来自数据流的对象。而我们使用mergeMap 操作符就可以实现这两个功能：

```
const a = stream('a', 200, 3);
const b = stream('b', 200, 3);
const h = interval(100).pipe(take(2), mergeMap(i => [a, b][i]));

h.subscribe(fullObserver('mergeMap'));
```

结果将会是完全一样，对于concatMap 和switchMap 也成立 —— 自己尝试一下吧。

#### 通过数据配对组合序列

前面的运算符允许我们摊平多个序列，并通过结果数据流来传递这些序列的值，就像他们都来自这个结果序列一样。我们接下来要看的一组操作符仍然以多个数据流为输入，但不同的是，他们对每个序列的值进行配对，以生成输出到结果序列中的单个组合值。

每个操作符都可以将一个可选的映射函数作为最后一个参数，该参数定义了如何组合结果序列中的值。在本文的列子中，我将使用默认的映射函数，该函数仅用逗号作为分割符连接数值。在这章的结尾，我将展示如何使用自定义映射函数。

##### combineLatest
首先,来看一下combineLatest操作符.它允许你从多个输入序列中获取最新的值,并组合成一个值存放到结果序列中.RxJs缓存每个输入序列的最后一个值，一旦所有序列产生至少一个值，它就会使用从缓存中获取最新值的映射函数计算结果值，然后通过结果流发出该计算的输出。

结果流在所有内部流完成时完成，如果任何内部流抛出错误，则将抛出错误。如果任何内部流不完成，它就永远不会完成。另一方面,如果任何流在不发出值的情况下完成了，则结果流将在同一时刻完成而不发出任何内容，因为现在不可能在结果序列中包含来自已完成输入流的值。此外，如果某个输入流不发出任何值并且永远不会完成，那么combineLatest 也将永远不会发出值并且永远不会完成，因为它在等待所有的输入流都产生数据.

如果你想评估某些状态的组合,并且在部分状态更新时获取所有状态的最新值,那么你就可以使用这个操作符.一个简单的例子是一个监控系统.每一个服务都由一个序列表示,该序列返回一个布尔值表示当前服务是否可用.如果所有服务都可用，则监视状态为绿色，因此映射函数应仅执行逻辑与。

在下面的动图中，你可以看到combineLatest 是如何连接A和B两个输入流的。一旦所有流已发射至少一个值，则每个新发出的值都会产生一个组合值，然后被发送到结果序列中。

![combineLatest](https://admin.indepth.dev/content/images/2020/02/111.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3, 'partial');
const b = stream('b', 500, 3, 'partial');
combineLatest(a, b).subscribe(fullObserver('latest'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/combine-latest)：

![concatAllImg](./img/rxjs/combineLatest.jpg)

##### Zip
这个操作符提供了另一种很有意思的合并特性，在某种程度上类似衣服或包上的拉链。它把两个或更多个序列的对应值组合成一个元组（两个输入流是一对）。它会等到所有输入流的对应值被发出，然后通过映射函数把它们转化成一个单独的值然后作为结果发出。在每个输入流发出一个新值的时候该操作符只会触发一次，所以如果一个输入流发出数据的速度比另一个快，那么整体的发送节奏就会由慢的那个数据流拖慢。

当任何内部流完成并且其他流对应的值发出时，结果流就会完成。只有任何一个内部流没有完成，它就不会完成，而任何一个内部流出错也会导致结果流出错。

这个操作方可以很方便地应用于在固定时间间隔内输出一个范围内的值。下面是利用映射函数仅从特定范围内返回数值的一个示例：

```
zip(range(3, 5), interval(500), v => v).subscribe();
```

在下面的动图中，你可以看到Zip 是如何连接A和B两个输入流的。一旦一堆对应的值被发出，结果流就会产生一个结合值。

![zip](https://admin.indepth.dev/content/images/2020/02/222.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3, 'partial');
const b = stream('b', 500, 3, 'partial');
zip(a, b).subscribe(fullObserver('zip'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/zip)：

![concatAllImg](./img/rxjs/zip.jpg)

##### forkJoin

有时候你有一组输入流，但是你只关心每个输入流最后一次发出的值。通常这些输入流只会发出一次值。举例来说，你可能会发出多个网络请求，但是只要所有的请求都返回值的时候你才回采取某项行动。它有点像Promise.all 这个函数的功能。但是，如果输入流中有一项产生了多条数据，那么那你只想保留最后一项。

结果流只在所有内部流完成的时候发出一次值。如果有内部流没完成或者报错了，那么结果流相应地也无法完成或报错。

在下面的动图中，你可以看到forkJoin 是如何连接A和B两个输入流的。一旦一堆对应的值被发出，结果流就会产生一个结合值。一旦一对对应的值产生，结果序列就会产生一个组合值：

![forkJoin](https://admin.indepth.dev/content/images/2020/02/333.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 200, 3, 'partial');
const b = stream('b', 500, 3, 'partial');
forkJoin(a, b).subscribe(fullObserver('forkJoin'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/fork-join)：

![concatAllImg](./img/rxjs/forkJoin.jpg)

##### WithLatestFrom
最后，我们来看一下withLatestFrom 操作符。当你有一个引导流，并且需要来自其他流的最新值时，你就应该使用这个操作符。

与combineLatest 不同的是，combineLatest 在任意一个内部流发出一个新值的时候都会发出一个组合值，而withLatestFrom 只有在引导流发出新值的时候才会发出一次组合值。


在下面的动图中，你可以看到withLatestFrom 是如何连接A和B两个输入流的，其中B是引导流。每次B发出一个新值的时候，结果序列会将该值与A输入流的最新值放到一起联合发出：

![withLatestFrom](https://admin.indepth.dev/content/images/2020/02/444.gif)

这是上面所展示动图的代码示例:

```
const a = stream('a', 3000, 3, 'partial');
const b = stream('b', 500, 3, 'partial');
b.pipe(withLatestFrom(a)).subscribe(fullObserver('latest'));
```

和stackblitz可编辑的[演示](https://stackblitz.com/edit/with-latest-from)：

![concatAllImg](./img/rxjs/forkJoin.jpg)

##### 映射函数

如本文开头所述，用于配对组合值的所有运算符都可以采用投影函数处理最后的结果。通过使用投影函数,你可以选择只发出来自部分输入流的数据,或者对数据进行任意的组合:

```
// return value from the second sequence
zip(s1, s2, s3, (v1, v2, v3) => v2)

// join values using dash as a separator
zip(s1, s2, s3, (v1, v2, v3) => `${v1}-${v2}-${v3}`)

// return single boolean result
zip(s1, s2, s3, (v1, v2, v3) => v1 && v2 && v3)
```
