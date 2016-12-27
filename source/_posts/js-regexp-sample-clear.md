title: JS RegExp 正则表达式-简明使用指南
date: 2016-12-27 15:23:00
author: [mac.meng]
tags: [regexp,js,code]
---

JavaScript Regular Expressions 

### 正则学习的难点,违反直觉的地方
1. 大量的缩写, 你可以写一堆,但也可以写一个  例如`\w`=`[a-zA-Z0-9_]`
2. 几个核心概念 anchors,meta,quantifiers,group
3. 反义词不是 `!`开始 例如[\d]单字必须是数字 [^\d] 单子不是数字和 [\D]是一个功能
4. group的嵌套关系 \$0, \$1, \$2, \$n \$0是整体(如果命中的话), \$n 是你定义的第几个`()group`
5. js 的 RegExp 支持的比较有限, 很多正则的高级用法并不支持

<!-- more -->


### js中如何使用
* regex.exec // 最强大功能最多
* regex.test // 返回true/false注意他会改变 reg.lastIndex 
* string.match // 返回值是 group的数组, 利用regexp做字符提取
* string.replace // 返回 string 利用regexp做字符替换

VSCode, WebStorm 中都可以用正则来搜索事半功倍

### 正则的几个核心概念

* anchors: 锚点
* meta sequences: 匹配字符串
* quantifiers: 度量  `?` `*` `+` `{n,m}`
* group: 组 (a|b) 
* flags/modifiers 扫面方式 /g 全局 /m 逐行


#### anchors

* `^` 以什么字符开始
* `$` 以什么字符结束
* `\b` 单词边界 = `(^\w|\w$|\W\w|\w\W)` (^)表示不已xx开头
    * `/d\b/g` 找d而且d是单词的结尾,例如word, add中的最后一个d[link](https://regex101.com/r/wVAFiN/7)
* `\B` 不是单词的结尾
    * 可以是开头或者中间   

#### meta sequences

* `.` 任意单字符
* `\s` 任意空格,tab或者换行 \S 相反
* `\d` 任意数字 \D 相反
* `\w` 任意大小写字母和数字 \W 相反
* 还有一些hex和转义字符 例如 \/\/ 包含 '//' 双斜线

#### quantifiers
度量一定都跟在 meta 后面

* `?` 0 - 1
* `*` 0 - non-limit 
* `+` 1 - non-limit
* `{n}` n个 不能多也不能少
* `{n,m}` n - m 个, n必填,可以是0, m可以不写,默认 non-limit


#### group
你可以的定义很多组, 但是可以捕获的组最多9个 \$1 - \$9. \$0是个默认的全部字符变量的组

**string.match 例子**

```javascript
var str = 'For more information, see Chapter 3.4.5.1';
var re = /see (chapter \d+(\.\d)*)/i;
var found = str.match(re);
// found is a array [$0, $1, $2]
```

**string.replace 例子**

```javascript
var str = `800-456-7890
555-456-7890
4564567890`;

var regex = /(\d{3})[\s-]?(\d{3})[\s-]?(\d{4})/g;
var str = str.replace(regex, "area code: $1");
console.log(str);
```

#### flags
表达式最后 /g, /m, /gi, /gu 可以组合使用
* `g` global,全局会搜索多行文本
* `m` multiline 每一行单独执行正则匹配
* `i` case insensitive 忽略大小写
* `y` sticky 用的不多
* `u` 支持unicode字符 例: `/𝌆/u`

### 参考网站

这个[网站](https://regex101.com)有4种语言的正则写法, 有正则语法高亮显示, 有group的显示, 和一些常用用法还有library

一些练习示例 
https://regex101.com/r/wVAFiN/3
https://regex101.com/r/wVAFiN/2



