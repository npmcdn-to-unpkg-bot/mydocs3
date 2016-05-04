# YAML Memo

> Yet Another Markup Language


YAML是`另一种标记语言`的外语缩写；但为了强调这种语言以数据做为中心，而不是以置标语言为重点而用返璞词重新命名。
它是一种直观的能够被电脑识别的数据序列化格式，是一个可读性高并且容易被人类阅读，容易和脚本语言交互，用来表达资料序列的编程语言。
它是类似于标准通用标记语言的子集XML的数据描述语言，语法比XML简单很多。


YAML参考了其他多种语言，包括：XML、C语言、Python、Perl以及电子邮件格式RFC2822。


## 格式模式

### 多行缩进

数据结构可以用类似大纲的缩排方式呈现，结构通过缩进来表示，连续的项目通过减号“-”来表示，map结构里面的key/value对用冒号“:”来分隔。样例如下

    house:
      family:
        name: Doe
        parents:
          - John
          - Jane
        children:
          - Paul
          - Mark
          - Simone
      address:
        number: 34
        street: Main Street
        city: Nowheretown
        zipcode: 12345


`注意`：

1. 字串不一定要用双引号标识；
1. 在缩排中空白字符的数目并不是非常重要，只要相同阶层的元素左侧对齐就可以了（不过不能使用TAB字符）；
1. 允许在文件中加入选择性的空行，以增加可读性；
1. 在一个档案中，可同时包含多个文件，并用“——”分隔；
1. 选择性的符号“...”可以用来表示档案结尾（在利用串流的通讯中，这非常有用，可以在不关闭串流的情况下，发送结束讯号）。


### 单行缩写

YAML也有用来描述好几行相同结构的数据的缩写语法，数组用'[]'包括起来，hash用'{}'来包括。因此，上面的这个YAML能够缩写成这样:


    house:
      family: { name: Doe, parents: [John, Jane], children: [Paul, Mark, Simone] }
      address: { number: 34, street: Main Street, city: Nowheretown, zipcode: 12345 }





## 适用场景 编辑

### 脚本语言

由于实现简单，解析成本很低，YAML特别适合在脚本语言中使用。列一下现有的语言实现：Ruby，Java，Perl，Python，PHP，OCaml，JavaScript，除了Java，其他都是脚本语言。

### 序列化

YAML比较适合做`序列化`。因为它是宿主语言数据类型直转的。

### 配置文件

YAML做配置文件也不错。写YAML要比写XML快得多(无需关注标签或引号)，并且比ini文档功能更强。
比如Ruby on Rails的配置就选用的YAML。对ROR而言，这很自然，也很省事.
由于兼容性问题，不同语言间的数据流转建议不要用YAML.



## 语言比较

### JSON

JSON的语法是YAML1.2版的子集，同时非常接近YAML1.0与1.1版的子集，因此大部分的JSON文件都可以被YAML的剖析器剖析。这是因为JSON的语法结构和YAML的内置格式相同。虽然大范围的分层也可以使用类似JSON的内置格式，不过这并YAML标准并不建议这样使用，除非这样编写能让文件可读性增加。YAML的许多扩展在JSON是找不到的，如：进阶资料形态、关系锚点、字串不需要双引号、映射资料形态会储存键值的顺序。


### 缩排划界

由于YAML的运作主要依赖大纲式的缩排来决定结构，这有效解决了界定符冲突（Delimiter collision）的问题。YAML的资料形态不依赖引号之特点，使的YAML文件可以利用区块，轻易的插入各种其他类型文件，如：XML、SDL、JSON，甚至插入另一篇YAML。

### 安全性

YAML是纯粹用来表达资料的语言，所以内部不会存代码注入的可执行命令。这代表剖析器会相当（至少）安全的解析文件，而不用担心潜在与执行命令相关的安全漏洞。举例来说，JSON是JavaScript的子集，使用JavaScript本身的剖析器是相当诱人的，不过也造成许多代码注入的漏洞。虽然在所有资料序列语言中，安全解析本质上是可能的，但可执行性却正是这样一个恶名昭彰的缺陷；而YAML缺乏相关的命令语言，可能相对安全。