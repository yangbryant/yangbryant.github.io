---
layout: post
title: YAML 语法入门
date: 2017-03-17 16:20:42 +08:00
author: Srefan
catalog: true
tags:
    - 入门
    - YAML
---

***

## 一.概述

**YAML** 是一种通用的数据串行化格式, 专门用来写配置文件的语言, 目标就是 **[方便人类读写]**, 非常简洁和强大, 远比 **JSON** 格式方便 . 本文参考 [阮一峰博客][Link_1] 和 [YAML Spec][Link_2] 的帮助文档.

***

## 二.规则

* 大小写敏感.
* 使用缩进表示层级关系.
* 缩进不允许使用 **Tab** 键, 只允许用 **空格**.
* 缩进的空格数目不重要, 只要相同层级的元素 **左侧对齐** 即可.
* **#** 注释,从此字符到行尾, 会被解析器忽略.

***

## 三.数据结构

* 对象: 键值对的集合, 映射(mapping),哈希(hashes),字典(dictionary)
* 数组: 一组按次序排列的值, 序列(sequence),列表(list)
* 纯量: 单个,不可再分的值, scalars

### 1.对象

* 对象的一组键值对,使用冒号结构表示.

```
animal: pets 
```

转换格式后:

```javascript
{animal: 'pets'}
```

* Yaml也允许另一种写法,所有的键值对写成一个行内对象:

```
hash: { name: Steve, foo: bar }
```

转换格式后:

```javascript
{ hash: { name: 'Steve', foo: 'bar' } }
```

### 2.数组

* 一组 **连词线** 开头的行, 构成数组.

```
- Cat
- Dog
- Goldfish
```

转换格式后:

```javascript
[ 'Cat', 'Dog', 'Goldfish' ]
```

* 数组也可以使用行内表示法:

```
animal: [Cat, Dog]
```

转换格式后:

```javascript
{ animal: [ 'Cat', 'Dog' ] }
```



### 3.复合结构

* 对象和数组可以结合使用,形成复合结构.

```
languages:
 - Ruby
 - Perl
 - Python 

websites:
 YAML: yaml.org 
 Ruby: ruby-lang.org 
 Python: python.org 
 Perl: use.perl.org 
```

转换格式后:

```javascript
{ languages: 
   [ 'Ruby', 'Perl', 'Python' ],
   
  websites: 
   { YAML: 'yaml.org',
     Ruby: 'ruby-lang.org',
     Python: 'python.org',
     Perl: 'use.perl.org'
    }
}
```

### 4.纯量

* 纯量是基本的数据类型:

> 1. 字符串
> 2. 布尔值
> 3. 整数
> 4. 浮点数
> 5. Null
> 6. 时间
> 7. 日期

* 数组直接用字面量表示.

```
number: 10.25
```

格式转换后:

```javascript
{ number: 12.30 }
```

* Bool值用 **true** 和 **false** 表示.

```
isSet: true
```

格式转换后:

```javascript
{ isSet: true }
```

* Null用~表示.

```
parent: ~
```

格式转换后:

```javascript
{ parent: null }
```

* 时间采用 **ISO8601** 格式表示.

```
iso8601: 2001-12-14t21:59:43.10-05:00 
```

转换格式后:

```javascript
{ iso8601: new Date('2001-12-14t21:59:43.10-05:00') }
```

* 日期采用复合 **ISO8601** 格式的年、月、日表示.

```
date: 1976-07-31
```

转换格式后:

```javascript
{ date: new Date('1976-07-31') }
```

* YAML 允许使用两个感叹号, **强制转换** 数据类型.

```
e: !!str 123
f: !!str true
```

转换格式后:

```javascript
{ e: '123', f: 'true' }
```

### 5.字符串

* 字符串是最常见,也是最复杂的一种数据类型.

* 字符串默认不使用引号表示.

```
str: this is the string
```

转换格式后:

```javascript
{ str: 'this is the string' }
```

* 如果字符串之中包含空格或特殊字符,需要放在引号之中.

```
str: 'key： value'
```

转换格式后:

```javascript
{ str: 'key: value' }
```

* 单引号和双引号都可以使用,双引号不会对特殊字符转义.

```
s1: 'key\nvalue'
s2: "key\nvalue"
```

转换格式后:

```javascript
{ s1: 'key\\nvalue', s2: 'key\nvalue' }
```

* 单引号之中如果还有单引号,必须连续使用两个单引号转义.

```
str: 'labor''s day'
```

转换格式后:

```javascript
{ str: 'labor\'s day' }
```

* 字符串可以写成多行,从第二行开始,必须有一个单空格缩进.换行符会被转为空格.

```
str: this
  is
  the
  string
```

转换格式后:

```javascript
{ str: 'this is the string' }
```

* 多行字符串可以使用 **\|** 保留换行符,也可以使用 **>** 折叠换行.

```
this: |
  Foo
  Bar
that: >
  Foo
  Bar
```

转换格式后:

```javascript
{ this: 'Foo\nBar\n', that: 'Foo Bar\n' }
```

* **+** 表示添加文字块末尾的换行, **-** 表示删除字符串末尾的换行.

```
s1: |
  Foo

s2: |+
  Foo

s3: |-
  Foo
```

转换格式后:

```javascript
{ s1: 'Foo\n', s2: 'Foo\n\n', s3: 'Foo' }
```

* 字符串之中可以插入 **HTML** 标记.

```
message: |

  <p style="color: red">
    section
  </p>
```

转换格式后:

```javascript
{ message: '\n<p style="color: red">\n  section\n</p>\n' }
```

### 6.引用

* 锚点**&**和别名__*__,可以用来引用.

```
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults
```

等同于:

```
defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost

test:
  database: myapp_test
  adapter:  postgres
  host:     localhost
```

> **&** 用来建立锚点(**defaults**), **<<** 表示合并到当前数据, __*__ 用来引用锚点.

```
- &showell Steve 
- Clark 
- Brian 
- Oren 
- *showell 
```

转换格式后:

```javascript
[ 'Steve', 'Clark', 'Brian', 'Oren', 'Steve' ]
```

### 7.函数和正则表达式的转换

* 这是 **JS-YAML** 库特有的功能,可以把函数和正则表达式转为字符串.

```
# example.yml
fn: function () { return 1 }
reg: /test/
```

解析上面的 yml 文件的代码如下:

```javascript
var yaml = require('js-yaml');
var fs   = require('fs');

try {
  var doc = yaml.load(
    fs.readFileSync('./example.yml', 'utf8')
  );
  console.log(doc);
} catch (e) {
  console.log(e);
}
```

从 JavaScript 对象还原到 yaml 文件的代码如下:

```javascript
var yaml = require('js-yaml');
var fs   = require('fs');

var obj = {
  fn: function () { return 1 },
  reg: /test/
};

try {
  fs.writeFileSync(
    './example.yml',
    yaml.dump(obj),
    'utf8'
  );
} catch (e) {
  console.log(e);
}
```

## 四.参考链接

此文参考于 [阮一峰的博客][Link_1],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

其他参考文档如下:
* [YAML 1.2 规格][Link_2]
* [YAML from Wikipedia][Link_3]
* [YAML for Ruby][Link_4]

***

[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[Link_1]: http://www.ruanyifeng.com/blog/2016/07/yaml.html
[Link_2]: http://www.yaml.org/spec/1.2/spec.html
[Link_3]: https://en.wikipedia.org/wiki/YAML
[Link_4]: http://yaml.org/YAML_for_ruby.html