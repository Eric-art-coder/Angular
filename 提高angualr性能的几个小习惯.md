## 提高angualr性能的几个小习惯

> angular虽出生就重，但是你了解它之后，学会合理使用之后，你会发现原来还可以使 angular 没那么臃肿,来给 angular 减减肥吧！

[本文参考一篇老外的blog](http://www.binpress.com/tutorial/speeding-up-angular-js-with-simple-optimizations/135)

## 用一些简单优化提速Angularjs APP 

AngularJS确实是一个强大的框架，但是并不能解决我们全部的问题。无论是框架有多优秀，我们都会创建迟钝的代码。这就是我们一些很差的使用习惯，没有理解关键的理念造成的问题。接下来的几点，是我深度学习AngularJS得到的一些心得。这些将对你创建一个响应更加快的有很大的帮助！

这些表现底层最为关键的点，是要去考虑减少在angular中的`$$watchers`从而提升`$digest`的循环检查的性能。在你继续用angular工作的时候，这个时刻保持应用的状态变化快捷，快速响应的关键。每一次Model的更新，无论是用户输入到View，或者是听过服务器输入到controller里面。这个时候，Angular将会运行`$digest`循环。

这个内部的循环，只要应用任何一个绑定的数据发生变化，都会导致这种内部执行循环。如果值发生变化，Angular会更新模块里面任何值而返回一个干净的内部值。当我们用AngularJS去创建数据绑定的时候，我们就创建了更多的`$$watchers`和`$scope`对象，这些反过来将会是每一次`$digest`的过程变得更加长。随着我们观察应用，我们需要注意有多少scope和我们创建的绑定，会在每一次`$digest`循环里面去检查。

接下来，让我们看下如何创建一个快速和便捷的angular框架应用。下面也会给出一些代码案例来帮助我们减少`$$watcher`和更好的理解`$digest循环`。


