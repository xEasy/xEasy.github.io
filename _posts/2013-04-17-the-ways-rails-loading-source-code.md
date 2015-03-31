---
layout: post
title: Angular
category:
- angular
- javascript
- html
permalink: angular-1
meta_description: Angular
browser_title: Angular
comments: true
---

###Angular

  * 一个前端MV*框架
  * 解决了JS与HTML交互的障碍
  * 直接使用HTML作为模板语言，扩展HTML语法
  * 让APP构建清晰简洁
  * 数据绑定，依赖注入

####优势

  * 操作HTML DOM
  * Data <=> UI 数据交互
  * 内建Services

###开始

  * ng-app ng的启动指令，通常应该在根节点
  * ng-model 对username进行数据绑定
  * {{ 数据。。。 }}  模板显示username这个属性

####Angular $scope

  * 数据模型、js对象
  * VC里面都可以访问
  * 层次结构

###Data binding & Ajax

  * Data Binding in Classical Template Systems
  ![classify data binding](/assets/images/ng-binding.png)

  * Data Binding in Angular Templates
  ![ng data binding](/assets/images/ng-binding.png)

  * Ajax ($http)

###Directives & Expressions

  * ngBind, ngModel, ngClass
  * 绑定在DOM元素上的函数，它可以调用方法、定义行为、绑定controller及$scope对象、操作DOM
  * NG的HTML compile 会通过 Directice 为DOM定义一定特定的行为或者改变它或者它的子节点

```
<div ng-controller="Controller">
  Hello <input ng-model='name'> <hr/>
  <span ng-bind="name"></span> <br/>
  <span ng:bind="name"></span> <br/>
  <span ng_bind="name"></span> <br/>
  <span data-ng-bind="name"></span> <br/>
  <span x-ng-bind="name"></span> <br/>
</div>
```

  * 自定义directive
  * Expressions  `{{ username }}`
    * 所有表达式都在本地$scope的context中执行
    * 其中的错误不会被抛出
    * 没有函数控制流程
    * 接受多个过个过滤器 `{{ username | uppercase }} -> WORLD`

###Services

  * 单例
  * 存在于application的整个生命周期
  * 保存数据，controller 之间共享数据

###Routing

  * $routeProvider
