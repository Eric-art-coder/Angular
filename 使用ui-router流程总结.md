
## 详细请看[官网](https://github.com/angular-ui/ui-router)

### 第一步 引入angular/angular-ui-router
```
$ npm install angular
$ npm install angular-ui-router
```

```
<script src = "angular.js"></script>
<script src = "angular-ui-router.js"></script>

```
>  并且引入 `angular.js` 和 `angular-ui-router.js`
  
必须注意先后顺序，必须先引入核心文件



***

### 第二步 引入依赖
- 将 **ui.router** 依赖添加到你的主Angular.js module中
```
     var myApp = angular.module('myApp', ['ui.router']);
    
```

### 路由状态机


- ui-router 引入的是状态机设计模式（也就是State模式）**路由是一种state，URL成了一个状态的简单属性，这个一定要与传统的路由设计模式区分出来**。
- 当你创建ui-sref链接的时候，链接的必须是状态名字而非URL。

> ui-sref的理解：ui代表的是AngularUI的指令，sref包装了html中href的所有属性和状态判断

#####   -. 控制器中使用

 ```
 $scope.redirectToAbout = function(){
    $state.go('about');
 }
 ```
 
#####   -. $stateProvider 替换 $routeProvider

- 当时用ui-router的时候，路由支持（或者说是提供路由，或者是提供状态）的变成了$stateProvider

#####   -. urlRouterProvider

- 作用1：建立默认路由，用于管理未知的URl

```
app.config(['$stateProvider', '$urlRouterProvider', function($stateProvider, $urlRouterProvider) {
    $urlRouterProvider.otherwise('/');
    ...
}]);
```


- 作用2：监听浏览器地址栏URL的变化，重定向到路由定义的状态
 

```
app.config(['$stateProvider', '$urlRouterProvider', function($stateProvider, $urlRouterProvider) {
    $urlRouterProvider
        .when('/legacy-route', '/'});
}]);

```

- 再来一个综合的：

```
app.config(['$stateProvider','$urlRouterProvider',function($stateProvider, $urlRouterProvider){
    $urlRouterProvider
        .when('/content1','/content1/list')
    	.otherwise('/')
}]);
```
- 总之，$urlRouterProvider改动某个url下，可以自动的转到我们想要的url页面
    