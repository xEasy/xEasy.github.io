---
layout: post
title: Rails Activerecord::Relation中count、size、length的不同之处
category:
- rails
- activerecord
permalink: rails-activerecord-relation-count-size-length-different
meta_description: Rails Activerecord::Relation中count、size、length的不同之处
browser_title: Rails Activerecord::Relation中count、size、length的不同之处
comments: true
---

- `#count` 总是会生成Sql语句访问数据库。
- `#length` 会加载所有记录，并且计算结果Array中的长度。
- `#size` 会聪明地选择是否访问数据库，根据我们是否已经加载了数据。

### 结论

- 尽量使用`#size`, 除非你需要立即获得记录的总数
- 因为`#size`已经做了聪明的选择，所以尽量避免使用`#count`
