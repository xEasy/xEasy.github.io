---
layout: post
title: Rails源码加载方式
catagory:
- rails
- ruby
permalink: Rails源码加载方式
meta_description: Rails源码加载方式
browser_title: Rails源码加载方式
comments: true
---

在Rails里有三种加载源码的方式：

 - 使用 `require` ，这个是ruby的方式。
 - 基于 `AcitiveSupport` 的 `const_missing` 机制。
   如果找不到某个常量（类 或者 模块），就会根据其名称，去加载对应的文件，同时添加到一个已经加载的常量的列表里。
   比如在代码里使用了 `Abc::Def`，那么 Rails 就会尝试加载 `abc/def.rb` 这个文件。
   文件的搜索路径在`ActiveSupport::Dependencies.autoload_paths`
   中，可以在 `application.rb` 中用`config.autoload_paths` 中添加。
   Rails-3 默认没有 'lib' 目录哦。所以默认情况下，使用这种方式加载不了。
 - 使用 require_dependency 加载。

使用第一种方式，应用启动后，只加载一次。

使用后两种，在development 模式下，每次处理新的请求，都会重新加载。

但是要注意，后两种加载的时候，会把源码所在的文件展开为绝对路径，再去 require 。
所以，使用Ruby-1.8.7 的时候要注意了：对于同一个文件，使用不同路径 require 文件，Ruby-1.8.7 会加载多次。

所以项目中应该统一使用一种方式加载。


另外：如果想让 `ActiveSupport::Dependencies.autoload_paths`
中的某个目录的子目录只加载一次，可以添加到 `config.autoload_once_paths` 中。
