# interview exams


2016-08-06,
2016-07-29,
2015


> 面试要严谨，对结果的`客观性`负责




<style type="text/css">
@import "http://258i.com/static/bower_components/snippets/css/mp/style.css";
</style>
<script src="http://258i.com/static/bower_components/snippets/js/mp/fly.js"></script>



## 考察代码能力


### 实现trim函数

`要求`：
1. 编写trim函数: `function trim( s ) { ... }`
2. 返回值类型为`string`。
3. 有一定的错误处理功能。

`分析`：
1. 有缜密思考的，表明编写代码有较强的健壮性，质量可以保证。比如对非字符串的判断
2. 使用正则方式还是只会使用基于索引查找再做字符串方法调用 
3. 知道对`&ensp;`, `&emsp;`以及`全角空格`也能过滤，分别是`&#8194;`, `&#8195;`和`&#12288;`

`简单例子`：

<div id="test_js_trim" class="test">
<div class="test-container">

    @[data-script="javascript"](function(){

        var s = fly.createShow('#test_js_trim');

        function trim ( s ) {
            if ( 'string' == typeof s
                || s instanceof String ) {
                return s.replace(/^\s*|\s*$/g, '');
            }
            return '';
        }


        var testCases = [
            ' abc '
            , new String('    def     ')
            , {a:1, b:2}
            , ' + '
            , '　+　'
        ];
        s.show('Start testing ...');
        for ( var i = 0; i < testCases.length; i++ ) {
            s.append_show(
                testCases[i]
                , '_' + trim(testCases[i]) + '_'
            );
        }

    })();

</div>
<div class="test-console"></div>
<div class="test-panel">
</div>
</div>





## 考察基础知识


### 正则表达式

1. `/\b/`与`/[\b]/`的区别: `单词边界` & `退格符`
2. 正则表达式的修饰符：`g` & `i`
3. 贪婪匹配与非贪婪匹配，默认是哪种模式？
4. 反向引用 



### 数组操作

1. 数组操作`splice`，a = [1, 2, 3]，通过调用splice方法，a = [1, 1.5, 2, 3] 

        a.splice(1, 0, 1.5);

2. sort

3. slice, concat



### 类型判断

#### isXXX

实现`isString`, `isDate`, `isArray`, `isFunction`, `isNumber`的通用方式
        
    function isXXX(s) { return Object.prototype.toString.call(s) === '[object XXX]'; }

分析：

1. `isString`，需要区别字面量和通过new String创建的两种变量的判断都要满足。
    会使用通用方式，表明有一定代码积累；但是需要会使用`typeof`结合`instanceof`的方式，
    如果不知道，表明基础还是有一定问题。

2. 让应试者编写`工厂`方案，需要知道`闭包`的使用方式。可以使用非闭包方案来让应试者发现问题。


<div id="test_isXXX" class="test">
<div class="test-container">

    @[data-script="javascript"](function(){

        function isXXX() {
            var types = [ 'String', 'Date', 'Array', 'Function', 'Number' ]
                , obj = {}
                ;

            for ( var i = 0; i < types.length; i++ ) {
                ( function () {
                    var type = types[ i ];
                    obj[ 'is' + type ] = function( inst ) {
                        return Object.prototype.toString.call( inst )
                            === '[object ' + type + ']'; 
                    }; 
                } )();
            }

            return obj;
        }

        var T = isXXX();

        var s = fly.createShow('#test_isXXX');
        var items = [
                new String( 'abc' )
                , new Date()
                , new Array()
                , function() {}
                , 12345
                , 'Hello, World!'
            ];
        s.show('start testing ...');
        for ( var i = 0; i < items.length; i++ ) {
            s.append_show(
                T.isString( items[ i ] )
                , T.isDate( items[ i ] )
                , T.isArray( items[ i ] )
                , T.isFunction( items[ i ] )
                , T.isNumber( items[ i ] )
            );
        }

    })();

</div>
<div class="test-console"></div>
<div class="test-panel">
</div>
</div>





### js类与继承


2. 用js实现类的定义、继承、包含类属性、实例属性、类方法、实例方法、公共属性、私有属性

3. 用js定义一个`Person`类，包含属性name和age，方法sayHello，输出name和age；再定义一个子类Student，包含属性grade，方法sayHello，除了输出父类相关属性外，再输出grade。
    
        function Person(name, age) {
            this.name = name;
            this.age = age;
        }

        Person.prototype.sayHello = function(){
            console.log(this.name, this.age);
        };

        function Student(name, age, grade) {
            // 体现复用思想
            Person.call(this, name, age);
            this.grade = grade;
        }

        Student.prototype = new Person();

        // 体现对constructor属性的理解 
        Student.prototype.constructor = Student;

        Student.prototype.sayHello = function(){
            // 体现复用思想
            Person.prototype.sayHello.apply(this, arguments);
            console.log(this.grade);
        };


4. 调用与父类同名方法的方式有多种，如果希望用`this._super()`方式调用，应该怎么实现

    参考：<a href="../frontend/class_extend.md.html">js类扩展方式</a>


7. `typeof x`有几种结果：number, string, boolean, function, object, undefined 

    知道es6中增加了`symbol`类型，可以加分。

8. typeof null == 'object'
9. typeof undefined == 'object'

10. Promise, Generator函数 

11. `use strict`

12. ES6新特性

13. Koa/Express

14. new关键字的作用

        new func1(); // 创建对象，并设置原型链
        func1();     // 执行函数

15. 在浏览器的地址栏输入URL到网页展现完毕，这期间都发生了什么？
    
    * DNS解析
    * 浏览器缓存
    * HTML文档返回
    * 解析link、style、script，阻塞特性
    * 服务端发生什么


16. 橡皮擦效果，关键使用了哪个关键的Canvas属性？

        context.globalCompositeOperation = 'destination-out';

17. AMD与CMD的区别？

18. Function之apply与call的区别？

19. 编写UMD方案







## 智力题

1. 一根不均匀的绳子，烧完1个小时，如何计算半小时
2. 以你现有的资源和能力，估算一下中国有多少私家车




## 综合知识

1. 编译原理：lex/yacc, flex/bison
2. 前端性能优化






## shell编程

1. 删除当前目录下（包含子目录）所有以`.png`为后缀的文件

        find . -type f -iregex '.*\.png$' -exec rm -rf {} \;




## css面试

1. 垂直居中 
2. 二列布局
3. flex-box



## 架构能力





