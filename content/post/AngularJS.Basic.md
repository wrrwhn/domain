+++
date = "2016-12-14T22:07:46+08:00"
title = "AngularJS.Basic"
draft = false
tags = ["整理","AngularJS"]
share = true
+++

## 导言和准备
- [地址](http://www.angularjs.cn/A00g)
- [源码](git://github.com/angular/angular-phonecat.git)
- [图解Git](https://marklodato.github.io/visual-git-guide/index-zh-cn.html)

## 引导程序
### 参考
    - 详细：http://angularjs.cn/A003
    - 源码：https://github.com/angular/angular-phonecat
    - 环境：https://github.com/nodejs/node-gyp
    - 安装异常：http://stackoverflow.com/questions/21365714/nodejs-error-installing-with-npm
    - 安装异常：http://blog.sina.com.cn/s/blog_9b624c5d0102vgwl.html   
### 启动服务
- `npm install`
- `npm start`
    - 需要行先安装 python，仅目前只支持 2.*.*，不支持 3.*.*
    - 并将 git 设置为环境变量，即将 D:\Program Files (x86)\Git\bin\;D:\Program Files (x86)\Git\libexec\git-core; 加入环境变量中
       - 注：有时需要重启才能生效
       - 更多注意事项请看 https://github.com/nodejs/node-gyp
### 测试服务
- `npm test`
### 切换服务
- `git checkout -f step-0`
    - 其中各阶段主要重点均在 https://github.com/angular/angular-phonecat
### 应用界面
- http://localhost:8000/app/index.html
### 核心代码解析
```
    <!-- np-app 指令，用于标记 AngularJS 脚本的作用域 -->
    <html lang="en" ng-app>   
    <!-- 加载 AngularJS 脚本 -->
    <script src="bower_components/angular/angular.js"></script>   
    <!-- {{'yet' + '!'}} 表示绑定功能，用于将运算结果插入 DOM 中 -->
    <p>Nothing here {{'yet' + '!'}}</p><br/>
```

## AngularJS模板
### 地址
- 详细：http://angularjs.cn/A005
- ngController：https://docs.angularjs.org/api/ng/directive/ngController
- ngRepeat：https://docs.angularjs.org/api/ng/directive/ngRepeat
### 特点
- 在AngularJS中，一个视图是模型通过HTML模板渲染之后的映射。
- 这意味着，不论模型什么时候发生变化，AngularJS会实时更新结合点，随之更新视图。
```
<!-- 为视图范围添加控制器，可为其添加别名 -->
<body ng-controller="PhoneListCtrl as ctrl">
<!-- 迭代器 -->
<li ng-repeat="phone in phones">
注：
    <!-- 临时创建数据并迭代 -->
    <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7, 8 , 9, 10]">
    <!-- 迭代对象 -->
    <tr ng-repeat="(name, age) in {'adam':10, 'amalie':12}">
    <!-- 可使用  ng-repeat-start 和 ng-repeat-end 来实现跨域重复 -->
    <header ng-repeat-start="item in items">Header {{ item }}</header>
    <div class="body">Body {{ item }}</div>
    <footer ng-repeat-end>Footer {{ item }}</footer>
// 在控制器中初始化数据，其中 scope 指代作用域
// 方法一
    var phonecatApp = angular.module('phonecatApp', []);
    phonecatApp.controller('PhoneListCtrl', function($scope) {
      $scope.hello="hello, world";
      $scope.phones = [
        {'name': 'Nexus S',
         'snippet': 'Fast just got faster with Nexus S.'}
      ];
    });

// 方法二
    angular.module('controllerAsExample', [])
      .controller('SettingsController1', SettingsController1);
    function SettingsController1() {
      this.name = "John Smith";
      this.contacts = [
        {type: 'phone', value: '408 555 1212'},
        {type: 'email', value: 'john.smith@example.org'} ];
    }
    // 为控制器提供方法
    SettingsController1.prototype.greet = function() {
      alert(this.name);
    };
```

### 迭代器
- 地址
    + 详细：http://angularjs.cn/A006
- 示例
```
<!-- repeat - key,val 形式 -->
<option ng-repeat="(key, val) in {name:'name', age:'age'}" value="{{key}}">{{val}}</option>
<!-- 以 Select 的结果作为列表的排序条件 -->
index.html
    <select ng-model="orderProp">
    <li ng-repeat="phone in phones | filter:query | orderBy:orderProp">
<!-- 设置同一域内的 orderProp 初始值，再次体现双向绑定 -->
controllers.js
    $scope.orderProp = 'name';
```

### XHR和依赖注入
- 地址
    - 详细：http://angularjs.cn/A008
$Http：https://code.angularjs.org/1.1.0/docs/api/ng.$http
    - 关于 Angular.js 你需要知道的知识： http://codethoughts.info/angular.js/2015/05/15/things-i-wish-i-were-told-about-angular-js/
    - AngularJS 单元测试： http://codethoughts.info/javascript/2015/09/06/angularjs-apps-unit-testing/
- 依赖注入
    - 异步响应，且 Angular 会自动检测应答并解析
    ```
    function PhoneListCtrl($scope, $http) {
      $http.get('phones/phones.json').success(function(data) {
        $scope.phones = data;
      });
      $scope.orderProp = 'age';
    }
    ```
    - 避免压缩造成系统无法识别参数
    ```
    var PhoneListCtrl = ['$scope', '$http', function($scope, $http) { /* constructor body */ }];
    ```
### 建议
- `$` 前缀为 AngularJS 内部使用前缀，不建议使用

### 测试
- 注入指定的函数，
    - $httpBackend 为 XMLHttpRequest 或 JSONP 的兼容 HTTP 后端，使用时使用高层次抽象的 $http 等 ，测试时用于模拟 HTTP 服务器实现
    - inject(function(_$httpBackend_, $rootScope, $controller) {}
- 为每个执行创建新作用域
    - $rootScope.$new();
- 将新作用域传递给 PhoneListCtrl
    - $controller(PhoneListCtrl, {$scope: scope});
- 通知等待 HTTP 请求并确认响应方式
    - $httpBackend.expectGET('phones/phones.json').respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
- 清空请求队列，使用请求实际发出并得到响应
    - $httpBackend.flush()

## 链接与图片模板
### 地址
- 详细：http://angularjs.cn/A009
- ngSrc：https://code.angularjs.org/1.1.0/docs/api/ng.directive:ngSrc
### 数据
- "imageUrl": "img/phones/motorola-defy-with-motoblur.0.jpg"
### 链接
- ng-src 同 src，但使用 img 时浏览器一遇到就会向未编译的 URL 发送一个请求，但 AngularJS 只在页面载入完成后开始编译从而得到正确的 URL 地址
    - `<img ng-src="{{phone.imageUrl}}">`

## 依赖注入
### 地址
- 详细：http://angularjs.cn/A00z

### 依赖实现
- 它的依赖是能被创建的，一般用new操作符就行。
- 能够通过全局变量查找依赖。
- 依赖能在需要时被导入。

### 注入器服务
```
angular.module('myModule', []).
  // 创建'greeter'时需依赖'$window'
  factory('greeter', function($window) {
    // 创建'greet'的工厂方法
    return {
      greet: function(text) {
        $window.alert(text);
      }
    };
  }).
// 由 module 中创建注入器，正常由 Angular bootstrap 自动实现
var injector = angular.injector('myModule');
// 向注入器请求依赖
var greeter = injector.get('greeter');
```

### 分离控制器和注入器
```
<div ng-controller="MyController">
  <button ng-click="sayHello()">Hello</button>
</div>
function MyController($scope, greeter) {
  $scope.sayHello = function() {
    greeter('Hello World');
  };
}
// The 'ng-controller' directive does this behind the scenes
injector.instantiate(MyController);
```

### 依赖标记
```
Angular 提供等效依赖方式
推荐依赖
    function MyController($scope, greeter) {
      // $scope, greeter 均需注入到函数中的依赖
      // 这种方法无法使用压缩混淆，因为参数名的改变而导致其无效
    }
$inject 标记
    var MyController = function(renamed$scope, renamedGreeter) {...}
    MyController.$inject = ['$scope', 'greeter'];
行内标记
    someModule.factory('greeter', ['$window', function(renamed$window) {...;}]);
    等价于
        var greeterFactory = function(renamed$window) {...;};
        greeterFactory.$inject = ['$window'];
        someModule.factory('greeter', greeterFactory);
```

### 工厂方法
```
angualar.module('myModule', []).
  config(['depProvider', function(depProvider){
    ...
  }]).
  factory('serviceId', ['depService', function(depService) {
    ...
  }]).
  directive('directiveName', ['depService', function(depService) {
    ...
  }]).
  filter('filterName', ['depService', function(depService) {
    ...
  }]).
  run(['depService', function(depService) {
    ...
  }]);
```

## 路由与多视图
### 地址
- 详细：http://angularjs.cn/A00a

### 目录结构
```
/app/index.html
/app/partials/phone-list.html
/app/partials/phone-detail.html
```
### index.html
```
<html lang="en" ng-app="phonecatApp">
// 模板点位符，配合 $route 使用
<div ng-view></div>
```
### app.js
```
// 引入 phonecatApp 模块并配置路由、控制器
var phonecatApp = angular.module('phonecatApp', ['ngRoute', 'phonecatControllers']);
// 路由配置= 指定路径-> 跳转界面+ 控制器
phonecatApp.config(['$routeProvider',
  function($routeProvider) {
    $routeProvider.
      when('/phones', {templateUrl: 'partials/phone-list.html',controller: 'PhoneListCtrl'}).
      when('/phones/:phoneId', {templateUrl: 'partials/phone-detail.html',controller: 'PhoneDetailCtrl'}).        // :phoneId 表示输入参数，将自动被提取至 $routeParams 对象
      otherwise({redirectTo: '/phones'});
  }]);
```
### controllers.js
```
// 获取参数值并填充至全局对象中。支持压缩写法
phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams',
  function($scope, $routeParams) {
    $scope.phoneId = $routeParams.phoneId;
  }]);
```

### 参数解析
```
phonecatApp：DI 实现模块化对象
phonecatControllers：ID 实现控制器模块化对象
PhoneDetailCtrl：控制器实例
```

## 过滤器
### 地址：
- 详细：http://angularjs.cn/A00c
### 流程
+ 界面引用 filter.js
+ ngApp 中 moudle phonecatFilters，如 angular.module('phonecat', ['phonecatFilters'])
+ 指定字段调用 {{ expression | filter }}
### 定义
```
angular.module('phonecatFilters', []).filter('checkmark', function() {
  return function(input) {
    return input ? '\u2713' : '\u2718';
  };
});
```

### 内置过滤器
```
    currency：格式化为货币
        {{num | currency : '￥'}}
    date
        {{1304375948024 | date}}
        {{1304375948024 | date:"MM/dd//yyyy @ h:mma"}}
    filter：过滤文字是否包含指定内容
    json：转化为 JSON 格式
    limitTo
    lowercase/ uppercase
    number
    orderBy
```

## 事件处理器
### 地址
- 详细：http://angularjs.cn/A00d

### 定义全局方法
```
function PhoneDetailCtrl($scope, $routeParams, $http) {
 $scope.setImage = function(imageUrl) {
    $scope.mainImageUrl = imageUrl;
  }
}
```

### 事件绑定
```
// 参数若引用全局值，不需要再加 {{}}
    <img ng-src="{{img}}" ng-click="setImage(img)">
    AngularJS入门教程11：REST和定制服务
        http://angularjs.cn/A00e
            $resource： https://code.angularjs.org/1.1.0/docs/api/ngResource.$resource
步骤
    app/js/services.js
        定义 REST 服务
    app/js/app.js
        注册时依赖模块
    app/js/controllers.js
        控制器
    app/index.html
        界面引用 ngResource 模块

实现
    定义 REST 服务
    // 定义工厂服务 Phone
        // 服务格式：$resource(url[, paramDefaults][, actions]);
        // 默认包含
            // {
            //     'get':    {method:'GET'},
            //     'save':   {method:'POST'},
            //     'query':  {method:'GET', isArray:true},
            //     'remove': {method:'DELETE'},
            //     'delete': {method:'DELETE'}
            // };
        angular.module('phonecatServices', ['ngResource']).
            factory('Phone', function($resource){
                return $resource(
                      'phones/:phoneId.json',
                      {},
                      { query: {method:'GET', params:{phoneId:'phones'}, isArray:true} }
                  );
            });
    添加服务依赖：
        angular.module('phonecat', ['phonecatFilters', 'phonecatServices'])
    数据查询
        phonecatControllers.controller('PhoneListCtrl', ['$scope', 'Phone',
          function($scope, Phone) {
            $scope.phones = Phone.query();    // 调用 Service 的指定服务 query
          }]);
        phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', 'Phone',
          function($scope, $routeParams, Phone) {
            $scope.phone = Phone.get({phoneId: $routeParams.phoneId}, function(phone) {
              $scope.mainImageUrl = phone.images[0];
            });
          }]);
```
- 注意：
    - 调用 Phone 服务时未传递任何回调函数，但非同步返回。
    - 被同步返回的为 Future 对象，当 XHR 相应返回时将填充进数据

## AngularJS补充
### 介绍
- 为动态WEB应用设计的结构框架，利用 数据绑定 和 依赖注入 补充 HTML 的不足
- AngularJS 的 HTML 编译器能让浏览器识别新的 HTML 语法，从而关联到 HTML 元素或属性上 -> 指令 - 特定领域语言（Domain Specific Language）

### 标识符实现
- 使用双大括号{{}}语法进行数据绑定；
- 使用DOM控制结构来实现迭代或者隐藏DOM片段；
- 支持表单和表单的验证；
- 能将逻辑代码关联到相关的DOM元素上；
- 能将HTML分组成可重用的组件。

### 适用
- 构建 CRUD 工程，
- 支持数据绑定
- 基本模板标识符
- 表单验证
- 路由
- 深度链接
- 组件重用
- 依赖注入

### 编译
- 编译
    + 遍历 DOM 并收集所有指令，生成一个链接函数
- 链接
    + 给指令绑定作用域，生成动态视图。作用域模型的任何改变都会反映到视图上。

### 指令
- ng-app 自动初始化
```
载入和指令内容相关的模块
创建一个应用的注入器（Injector）
拥有 ngApp 的标签将指定为 AngularJS 应用
注：手动初始化
    代码
        <script>
        angular.element(document).ready(function() {
            angular.bootstrap(document);
        });
    </script>
遵守顺序
    等页面和所有脚本加载完成后指定要成为 AngularJS 应用的节点
    调用 api/angular.bootstrap 将模板编译成可执行的、数据双向绑定的应用程序
```
