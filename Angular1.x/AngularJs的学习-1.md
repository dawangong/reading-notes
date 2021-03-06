#### 1. 作用域层级

- ng-controller指令会调用scope的$new()方法创造一个scope的实例$scope（作用域实例）。
- Angular拥有$rootScope，它是其他所有作用域的父级作用域，将会在应用启动（加载模块）时自动创建。
- 变量会沿着作用域向下继承。（也就是说子作用域可以访问到父作用域的变量等）
- $scope会拥有$parent属性，这个属性会指向父级作用域。
- 每个作用域实例（$scope）都有$on方法，用来注册作用域事件的处理器。

#### 2. 路由

- 原理：
    - 哈希#
    - HTML5中新的history API
- 作用：
    - 防止浏览器向后台发起请求
    - 为angular提供识别标识
    - angular会获取到当前url并且将#后面的字段截取到与when()中参数进行比较。
    
>感觉路由很像ajax刷新局部页面，经过试验在network里果然发现了xhr请求

#### 3. angular模块

- angular自身提供了模块化功能
- 在angular中一切都是从模块开始，通过注入依赖将很多个模块链接起来，形成一个大的模块。
- 如果有多个控制器，而每个控制器负责不同的功能但是却有部分相同的代码逻辑需求怎么办？
    - 通过angular中的服务解决。
    - 可以把服务当做一个单独模块，然后给需要的控制器们注入依赖，就可以在它们里面使用注册过的服务了。
- 官方推荐的模块切分：如下目录结构（个人理解）

![image](http://upload.ouliu.net/i/20171214102535dal2d.jpeg)

>官方版如下：

```
.                     app
                       |
    -----------------------------------------------
    |             |            |         |        |
controllers   directives   services   routes   filters
```


#### 4. angular中的bind和表达式

1. 功能：都是将视图和数据进行双向的绑定。
2. 区别：表达式有一个缺点：当网速比较慢或者刷新比较快时是能够看到表达式本身的。
3. 解决方案：
    - 表达式对应的添加类名'ng-cloak'。
    - 在主页面用ng-bind其他的模板页面（片段html）用表达式。
    

#### 5. 指令

- 指令执行的三个阶段 & compile和link：
    - 加载阶段：加载angular.js，找到ng-app指令，确定应用的边界
    - 编译阶段：
        - 遍历DOM，找到所有指令；
        - 根据指令代码中的template、replace、transclue转换DOM结构
        - 如果存在compile函数则调用
    - 链接阶段：
        - 对每一条指令运行link函数
        - link函数一般用来操作DOM，绑定事件监听器
    - 关于compile和link：、
        - compile函数用来对模板自身进行转换，而link函数负责在模型和视图之间进行动态关联；
        - 作用域在链接阶段才会被绑定到编译之后的link函数上
        - compile函数仅仅在编译阶段运行一次，而对于指令的每个实例，link函数都会执行一次
        - compile可以返回preLink和postLink函数，而link函数只会返回postLink函数
        - 如果需要修改DOM结构，应该在postLink中来做这件事情，而如果在preLink中做这件事情会导致错误
        - 大多数时候我们只要编写link函数即可


- link相关
    - 解释：当directive被angular 编译后，执行该方法。
    - link后跟随的方法中的参数解释：
        - scope:angular中的scope对象
        - element:当前触发函数的元素
        - attrs:attrs是个map，内容是你这个directive上的所有属性
    - 当directive中只有一个link函数可以直接返回匿名function

>创建controller和directive的时候，会自动创建自己的私有scope对象，私有scope从rootScope继承.  

>在默认的情况下，directive不会创建他们自己的scope.他们会用他们父对象的scope作为自己的scope.  但是 angularjs 允许改变这种默认行为。

>注释做自定义指令的注意点

1. 自定义中小驼峰命名的要在注释中改为用'-'连接的方式。
2. 通过注释执行命令时要注意前面带有derective：字样，效果如下：

>还有一点，经过实验不能用大驼峰命名指令

```javascript
<div ng-app="apps">
     <!--directive:my-de -->
</div>
```


```javascript
let apps=angular.module('apps',[]);
    apps.directive('myDe',() => {
        return{
            restrict:'M',
            replace:true,
            template:'<h1>dgfrhfgdh</h1>'
        }
    })
```

  
#### 8. derective下的scope对象

1. false：会使用父对象的scope对象（默认）
2. true：会创造一个新scope对象继承父对象的scope对象（像js里父类子类继承），也就是拥有了和父级一样却不会相互影响的scope对象。
3. {}：创造一个全新隔离的scope空对象，指令间互不影响。（如果只想用父级scope中某些属性方法，可以用下面的传值）
4. 有三种方法把值从parent 对象中传递到 directive 中。
    1. 通过 @ 传值  （字符串绑定，one way binding(单向绑定）,就是传递字符串到directive 进行显示)，在调用directive 的时候，需要对 attribute 使用 {{}} 进行传值。
    2. 通过  = 传值   (模型 绑定， two way bingding（双向绑定）） 在调用directive 的时候，需要对 attribute 使用  模型名称  进行传值。
    3. 通过 &　传值 （方法绑定）
    
>实际上给我感觉都是用属性去接收。
    
#### 7. ng-include和ng-view

1. 功能：都可以插入html片段
2. 区别：
    1. ng-include用于重用重复的html片段，比如header区。
    2. ng-view则和路由搭配，方便视图控制。
    3. ng-include用法示例：
    

```javascript
<div ng-include="'header.html'">
```

#### 8. survice的特性

1. Service都是单例的
2. Service由$injector负责实例化
3. Service在整个应用的生命周期中存在，可以用来共享数据
4. 在需要使用的地方利用依赖注入机制注入Service
5. 自定义的Service需要写在内置的Service后面
6. 内置Service的命名以$符号开头，自定义Service应该避免

>Service，Factory，Provider本质上都是Provider

#### 9.templateUrl和template:$templateCache

- 两者都可以复用同一套模板，那么实际应用中有什么区别呢？
    - angular的模板获取：AngularJS的模板tpl通过XHR下载
    - 在实际中，如果使用templateUrl会默认需要时从规定的路径去取。实际上一个模板如果被重复需要，就会重复请求获取。而使用template:$templateCache可以允许$http 服务缓存经过XHR的模板请求，这样它们就只会被请求一次。当一个模板被取到了，它的内容就会存储在 $templateCache 中。
    
    
#### 10.directive中template下的replace和transclude的用法

1. replace：是否替换自定义指令元素中所写的内容（true/false）
2. transclude：按要求合并，看示例：


```javascript
template: "<div>Hello everyone!<div ng-transclude></div></div>"
//自定义指令元素内部写的内容替换到ng-transclude里面去
```

#### 11. ui.router

1. 方法
- go(to,params,options)
    - to：(string)，即将跳转的状态。
    - params:(object)，跳转所带的参数。
    - options：(object)，可选配置对象。有location(是否更新地址栏的url，或以什么字符串替换url)，inherit(是否继承当前url的参数)，relative(url相对路径：如"^",指代相对的父级，"."指代相对的子级)，notify(是否广播$stateChangeStart和$stateChangeSuccess事件)，reload(是否重新载入)。
    
>go方法表现是重新走路由配置（根据state）

```javascript
$state.go('xxx');
```

- reload(state):重新载入当前状态的方法。
    - state：一个状态名称或者状态对象。
    
>reload方法表现是在原有view中重载指定路由的请求


```javascript
$state.reload('xxx');
```

- get(stateOrName,context)：返回任何指定的状态或所有状态的配置对象。

    - stateOrName：如果提供此参数，返回的是指定状态的配置对象；如果不提供此参数，则返回全部状态的配置对象。
    - context：当context是一个相对的参考状态，状态会在相关上下文中检索。

2. 属性
- current
- params

3. 事件
- $stateChangeError:路由状态变化发生错误时触发的事件.
- $stateChangeStart:路由状态变化发生前触发的事件.
- $stateChangeSuccess:路由状态变化正确时触发的事件.
- $stateNotFound:路由状态没找到的时候触发的事件.

>在$rootScope.$on()中监听事件变化


```javascript
$rootScope.$on('xxx事件',function(){
     your code ...
})
```
4. 其他
    1. $urlRouterProvider：负责监听$location.当$location变化的时候，$urlRouterProvider开始在一个规则的列表中一个个的查找，直到找到匹配的值。$urlRouterProvider用于在后端指定url的状态配置。所有的url被编译成UrlMatcher对象。
    2. state(name,stateConfig)：注册一个状态，并给定其配置。
    3. otherwise();定义一个当请求的路径是无效路径时跳转的路径（可用来呈现默认路由对应的视图）。
    4. $stateProvider：处理路由状态的服务，路由的状态反映了该项在应用程序中的位置，描述了在当前状态下UI是应该怎么样的，并且该做什么。

#### 12. 事件广播

>一般共享的方法放在服务中通过注入使用...

1. 发送消息： $scope.$emit(name, data) 或者 $scope.$broadcast(name, data);
2. 接收消息： $scope.on(name,function(event,data){ });
3. 区别： $emit 广播给父controller   $broadcast 广播给子controller
4. 在$on的方法中的event事件参数，其对象的属性和方法如下：

事件属性    |	目的
--- | ---
event.targetScope   |	发出或者传播原始事件的作用域
event.currentScope	|目前正在处理的事件的作用域
event.name|	事件名称
event.stopPropagation()	|一个防止事件进一步传播(冒泡/捕获)的函数(这只适用于使用`$emit`发出的事件)
event.preventDefault()	|这个方法实际上不会做什么事，但是会设置`defaultPrevented`为true。直到事件监听器的实现者采取行动之前它才会检查`defaultPrevented`的值。
event.defaultPrevented	|如果调用了`preventDefault`则为true