## 提高angualr性能的几个小习惯

> angular虽出生就重，但是你了解它之后，学会合理使用之后，你会发现原来还可以使 angular 没那么臃肿,来给 angular 减减肥吧！

[本文参考一篇老外的blog](http://www.binpress.com/tutorial/speeding-up-angular-js-with-simple-optimizations/135)

## 用一些简单优化提速AngularJS APP 

AngularJS确实是一个强大的框架，但是并不能解决我们全部的问题。无论是框架有多优秀，我们都会创建迟钝的代码。这就是我们一些很差的使用习惯，没有理解关键的理念造成的问题。接下来的几点，是我深度学习AngularJS得到的一些心得。这些将对你创建一个响应更加快的有很大的帮助！

这些表现底层最为关键的点，是要去考虑减少在angular中的`$$watchers`从而提升`$digest`的循环检查的性能。在你继续用angular工作的时候，这个时刻保持应用的状态变化快捷，快速响应的关键。每一次Model的更新，无论是用户输入到View，或者是听过服务器输入到controller里面。这个时候，Angular将会运行`$digest`循环。

这个内部的循环，只要应用任何一个绑定的数据发生变化，都会导致这种内部执行循环。如果值发生变化，Angular会更新模块里面任何值而返回一个干净的内部值。当我们用AngularJS去创建数据绑定的时候，我们就创建了更多的`$$watchers`和`$scope`对象，这些反过来将会是每一次`$digest`的过程变得更加长。随着我们观察应用，我们需要注意有多少scope和我们创建的绑定，会在每一次`$digest`循环里面去检查。

接下来，让我们看下如何创建一个快速和便捷的angular框架应用。下面也会给出一些代码案例来帮助我们减少`$$watcher`和更好的理解`$digest循环`。

### 一次绑定语法{{::value}}

AngularJS在1.3.0版本之后推送的特色越来越少了,渲染数据的能力已经确定将不会被未来的模板更新迭代。这是一个极好的新闻，对于开发者说。在这个之前，我们基本在用下面来渲染一个DOM：
```
<h1>{{ title }}</h1>
```
然后我们开始使用一次绑定的语法，在value前面添加一个双冒号:
```
<h1>{{ ::title }}</h1>
```
这种一旦Angular渲染了DOM，它独特的性能从`$$watchers`列表中移除出去。这个单次绑定有将会帮助我们提高应用的性能。

一般AngularJS 2000多个绑定的时候就会使得Angular变得缓慢，这个是由于脏检查引起来的。只要我们绑定的越少，我们就可以忽略很多。

使用单次绑定是最容易和最有效果的。这种语法很干净和简洁，这个真正起效果的是减少了`$$watcher`，那么Angular则减少工作量，那么我们的应用响应将相应的变快！


### `$scope.$apply()`和`$scope.$digest()`对比

在你使用angular的历程中，你肯定会被`$scope.$apply()`坑。它很有可能会被滥用，“因为`$scope.$apply()`可能会解决我们使用插件的代码，这可能不是最好的使用方法”，因为这个原因，它很难被理解，并且它也不应该是比较简单的。

`$scope.$apply`是被设计来告诉Angular 数据模型的改变，它仅仅是随着新数据的改变它可以告诉Angular，来更新它自己。这个时候就是使用`$scope.$apply`的最佳时候。但是在过去它确实困惑我很久，时常会抛出一些未获取的错误在js中。你也会抛过一些错误，比如：`$scope.$apply in the “wrong” place, usually too high up the call stack.`

所有人都会在Angular里面使用第三方插件，经常在插件里面，他有自己的的事件系统，在Angular不知情下操作DOM。这就是使用`$scope.$apply`的地方。在数据更新之后，调用`$scope.$apply`触发`$digest`循环，Angular就会拉取数据来从它的根源更新

这里有一段例子：

```
$(elem).myPlugin({
  onchange: function (newValue) {
    // model changes outside of Angular 由于
    $(this).val(newValue);
    // tell Angular values have changed and to update via $digest
    $scope.$apply();
  }
});
```
当`$scope.$apply()`被调用的时候，它会使得全部的应用进行`$digest`循环和将会跑`$rootScope.$digest()`。简而言之，如果是所有的scope都绑定在这个循环中，任何一个改变。但是尽管他整体来看是很快速的，但是整体来看，全部数据的改变，还是会很慢。

相反，使用`$scope.$digest`，它会去跑相对比较准确的`$digest`循环。  
但是他只会$scope树的子代循环，不会往上循环。

这个需要警告的是，如果你依赖的是父scope的数据双向绑定，就不要去使用`$scope.$digest`，这个父节点将不会更新，除非是下一次`$rootScope`全局`$digest`循环。这是因为`$scope.$digest`只会往$scope树下面走而不会往上走。如果你是想更新父节点的值，建议你使用`$scope.$apply()`

### 尽可能避免使用ng-repeat

`ng-repeat`极大提高开发者的效率，但是我们可以通过避免使用这个指令来使Angular，这个指令容易会滥用，因为一个ng-repeat代表的就是一个$scope数组，其会极大地摧毁`$digest`循环的性能。  
比如说，用ng-repeat来制作一个nav，我们可能使用`$interpolate`通过一个对象，将其装变为DOM节点。

所以要在写代码的时候，时刻去提醒自己尽量去避免使用该指令。

### 尽量在directives中操作DOM

在使用Angular的核心指令：ng-show和ng-hide的时候，同样ng-repeat也是一样的。  
ng-repeat会导致`$$watchers`的数量剧增。

如果你写的代码如下，你可能要考虑重构了：
```
<div ng-show=”something”></div>
$scope.something = false;
$scope.someMethod = function () {
  $scope.something = true;
};
```
如果建立指令和一些逻辑不需要依赖在模块上面，这个逻辑会在link的回调里面才会执行；所以绝不要在将操作DOM的逻辑写在Controller里面。虽然Angular提供了ng-show和ng-hide、ng-mouseenter这些指令，但是真正使用的时候你就会发现代价太大了，特别对于`$digest`而言，这样会是的脏检查变得很重！**所以我们要推荐使用原生的addEventListenter或者是jQuery' 的'on'事件**。如下：
```
var menu = $element.find(‘ul’);
menu.hide();
$scope.someMethod = function () {
  menu.show();
};
```

### Limit DOM filters

当在页面内的模型后面增加filter时,这个会造成当前模型在$digest里运行两次,造成不必要的性能浪费.第一次在$$watchers检测任务改变时;第二次发生在模型值修改时,所以尽量少用内联时的过滤器语法,像下面这样的非常影响页面性能

```
{{ filter_expression | filter : expression : comparator }}
```
推荐使用$filter服务来调用某个过滤器函数在后台,这样能明显的提高页面性能,代码如下

```
$filter('filter')(array, expression, comparator);
```

## 总结
上面都是些提高ng项目性能的一些小技巧,虽然ng很强大,但是不规范的代码也会破坏它的性能,所以在写代码之前最好构思下哪些地方是不需要监听器的.



