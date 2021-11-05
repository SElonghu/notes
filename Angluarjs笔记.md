## Angluarjs笔记

### 1.angular.min.js引用

js引入到head标签中，ng-app="模块名"写到body标签内。

<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
    <title>angular.min.js引用</title>
    <script type="text/javascript" src="angular.min.js"></script>
</head>
<body ng-app="myAPP">
</body>
</html>   


### 2.模块和controller定义

```javascript
#模块定义并赋值给myapp，myApp模块名，[]内填其他模块
var myApp = angular.module('myApp',[]);
#定义控制器的两种形式，并在控制器的函数中引入$scope
myApp.controller('myAppController',function($scope){
    $scope.x = 0;
})
或者
angular.module('myApp').controller('myAppController',function($scope){
    $scope.x = 0;
})

```

### 3.controller里的函数定义

```javascript
#这是一个加法函数定义
$scope.add = function(){
    return x+y;
}
```

### 4.angular事件

#### 4.1点击事件ng-click

```html
<input type="button" ng-click="add()" value="求和">
```

#### 4.2循环操作ng-repeat

```javascript

```

### 5.路由ngRoute

#index.html中

```html
<script type="text/javascript" src="angular-route.min.js"></script>
<body>
    <ul>
        <li><a href="#showname">姓名列表显示</a></li>
        <li><a href="#showinfo">信息列表显示</a></li>
    </ul>
    <div style="width:300px; height:300px;border:1px solid black;" ng-view> 
    </div>
</body>
```

#app.route.js中

```javascript
var app = angular.module('app',['ngRoute']);
app.config(['$routeProvider',function($routeProvider){
    $routeProvider
    .when('/showname',{
        templateUrl:'showname.html',
        controller:'showNmeController'
    })
    .when('/showinfo',{
        templateUrl:'showinfo.html'
        controller:'showInfoController'
    })
    .otherwise({
       redirectTo:'/showname' 
    });
}]);
```

### 6.$rootScope服务

解决controller之间的通信问题，$rootScope可以创建全局变量供所有控制器及对应的html使用。

#controller.js中

```javascript
app.controller('appController',['$scope','$rootScope',function($scope,$rootScope){
    $scope.x = 1;
    $rootScope.y = x;
}]);
```

### 7.$http服务

```json
#res/test.json
{"id":"1","name":"lh"}
```



```javascript
#GET方法
$http({
        method: 'GET',
        url: 'res/test.json'
    }).success(function(respond){
        console.log(respond.id);
    }).error(function(err){
        console.log(err);
    });
```

html中应当用ng-src、ng-href指令代替src、href属性。

### 8.$filter服务

自带的4种过滤器：

货币过滤器

```html
#货币过滤器（参数写啥，x前面就是啥）
{{x | currency:'货币符号'}}
{{x | currency}}或{{x | currency:'$'}}

```

filter过滤器

```html
#filter过滤器
*** | filter:子集数组
<input type="text" ng-model='filter_input'>
<ul>
    <li ng-repeat='pname in pnames | filter:filter_input'>{{pname}}</li>
</ul>
```

```javascript
#controller中
$scope.filter_input = '';
$scope.pnames = ['lily','lisa','libai','lilei','lixiaolong','lihua'];
```

uppercase, lowercase过滤器

```html
{{*** | uppercase}}
```

orderBy过滤器

```html
//用于根据表达式的值对显示的内容进行排序
//一般用于ng-repeat结构中，且ng-repeat不是简单数据结构
<ul>
    <li ng-repeat='info in infos | orderBy:"score"'>{{info.stuName+','+info.score}}</li>
</ul>
```

```javascript
$scope.infos = [{stuName:'frank',score:86},
                {stuName:'lily',score:100},
                {stuName:'lucy',score:42},
                {stuName:'lilei',score:43}
];
```

