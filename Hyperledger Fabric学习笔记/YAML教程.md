YAML 的语法和其他高级语言类似，并且可以简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和YAML非常接近）。

> YAML 是专门用来写配置文件的语言，非常简洁和强大，远比 JSON 格式方便。
>
> 在线Demo验证[链接](http://nodeca.github.io/js-yaml/)

### 基本语法

- 大小写敏感
- 使用缩进表示层级关系
- 缩进<u>不允许使用tab</u>，只允许空格
- 缩进的**空格数不重要**，只要相同层级的元素**左对齐**即可
- '#'表示注释

### 数据类型

YAML 支持以下几种数据类型：

- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
- 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
- 纯量（scalars）：单个的、不可再分的值

### YAML 对象

对象键值对使用冒号结构表示 **key: value**，冒号后面<u>要加一个空格</u>。

也可以使用 **key:{key1: value1, key2: value2, ...}**。

还可以使用<u>缩进表示层级关系</u>；

```yaml
key: 
    child-key: value
    child-key2: value2
```

较为复杂的对象格式，可以使用问号加一个空格代表一个复杂的 key，配合一个冒号加一个空格代表一个 value：

```
?  
    - complexkey1
    - complexkey2
:
    - complexvalue1
    - complexvalue2
```

意思即对象的属性是一个数组 [complexkey1,complexkey2]，对应的值也是一个数组 [complexvalue1,complexvalue2]

### YAML 数组

一组连词线`-`开头的行，构成一个数组。

```
- A
- B
- C
```

YAML 支持多维数组，可以使用行内表示：

```yaml
key: [value1, value2, ...]
```

数据结构的子成员是一个数组，则可以在该项下面缩进一个（多个空格也行，只要保证子成员左对齐）空格。

```yaml
-
 - A
 - B
 - C
```

一个相对复杂的例子：d

```yaml
companies:
    -
        id: 1
        name: company1
        price: 200W
    -
        id: 2
        name: company2
        price: 500W
```

意思是 companies 属性是<u>一个数组</u>，每一个数组元素又是由 id、name、price 三个属性构成。

数组也可以使用流式(flow)的方式表示：

```yaml
companies: [{id: 1,name: company1,price: 200W},{id: 2,name: company2,price: 500W}]
```

### *引用

**&** 锚点和 ***** 别名，可以用来引用:

> 注意：
>
> YAML锚点在整个文档中必须是**唯一**的
>
> 未能指定唯一锚点将导致`yaml.load()`上的错误

```yaml
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults
  
##### 相当于 #####
defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost
```

> **&** 用来建立锚点（defaults），**<<** 表示将**键值对**<u>合并到当前数据</u>，***** 用来引用锚点。

下面是另一个例子，在server对redis的访问配置中，针对不同的db可能会写成如下配置:

```YAML
user:
    host: 127.0.0.1
    db: 8

book:
    host: 127.0.0.1
    db: 9

comment:
    host: 127.0.0.1
    db: 10
# 此处host其实配置都是一样的，只有db不一样，通过锚点和引用的功能，可以写成如下:
localhost: &localhost1  # 指将localhost所对应的值设为一个锚点
    host: 127.0.0.1
user:
    <<: *localhost1
    db: 8
book:
    <<: *localhost1
    db: 9
comment:
    <<: *localhost1
    db: 10
```

其中`&`表示将localhost1作为localhost的**别名**，标识取别名localhost1对应的value，`<<`表示将localhost1代表的map**合并入**当前map数据。



### 参考链接

[YAML 入门教程-菜鸟教程](https://www.runoob.com/w3cnote/yaml-intro.html)

[YAML 语言教程](http://www.ruanyifeng.com/blog/2016/07/yaml.html)