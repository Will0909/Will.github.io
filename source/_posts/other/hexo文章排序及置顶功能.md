---
title: hexo文章排序及置顶功能
categories: 
- 其他
- hexo
---

hexo文章排序按照.md文件的创建时间排序，而不是按照文章中的date排序，可以通过以下方法更改排序方式，另外也可以实现置顶的功能。

#### 方法一

安装**插件 hexo-generator-topindex**

`npm i hexo-generator-topindex -D`

#### 方法二

修改`node_modules/hexo-generator-index/lib/generator.js`

```js
'use strict';
var pagination = require('hexo-pagination');
module.exports = function(locals){
  var config = this.config;
  var posts = locals.posts;
    posts.data = posts.data.sort(function(a, b) {
        if(a.top && b.top) { // 两篇文章top都有定义
            if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
            else return b.top - a.top; // 否则按照top值降序排
        }
        else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面
            return -1;
        }
        else if(!a.top && b.top) {
            return 1;
        }
        else return b.date - a.date; // 都没定义按照文章日期降序排
    });
  var paginationDir = config.pagination_dir || 'page';
  return pagination('', posts, {
    perPage: config.index_generator.per_page,
    layout: ['index', 'archive'],
    format: paginationDir + '/%d/',
    data: {
      __index: true
    }
  });
};
```

#### 设置置顶

按照以上方法操作完成后在需要置顶的文章中加入top参数，如下

```markdown
title: Vue插槽
tags: vue
top: 1
categories: 
- vue
- 基础知识
```

如果存在多个置顶文章，top后的参数越大，越靠前