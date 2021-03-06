# js basics



<style type="text/css">
@import "http://258i.com/static/bower_components/snippets/css/mp/style.css";
</style>
<script src="http://258i.com/static/bower_components/snippets/js/mp/fly.js"></script>


## typeof

`typeof`输出是`字符串`类型，输出为以下`7个`值之一：

* number
* string
* boolean
* object
* function
* undefined
* symbol

<div id="test_10" class="test">
<div class="test-console"></div>
<div class="test-container">

    @[data-script="javascript editable"](function(){

        var s = fly.createShow('#test_10');
        var items = [
            '1'
            , '"Hello, World!"'
            , 'true'
            , 'null'
            , 'undefined'
            , '[ 1, 2, 3]'
            , 'Array'
            , '{ name: "Michael" }'
            , 'new Object()'
            , 'function(){}'
            , 'new Number(1)'
            , 'new String("Hello")'
            , 'NaN'
            , 'Infinity'
        ];
        var str;
        s.show('typeofs: ');
        for(var i=0; i<items.length; i++){
            str = 'typeof ' + items[i]; 
            s.append_show(
                str
                , eval(str) 
            );
        }

    })();

</div>
<div class="test-panel">
</div>
</div>





## 一般等式

    null == undefined
    0 == ''
    false == 0
    false == ''
    true == 1
    true != 100
    NaN != NaN

`注意`：`NaN`不能做比较，需要判断一个数是否为NaN，使用`isNaN()`


<div id="test_20" class="test">
<div class="test-console"></div>
<div class="test-container">

    @[data-script="javascript editable"](function(){

        var s = fly.createShow('#test_20');
        var items = [
                [ 'null', 'undefined' ]
                , [ '0', '""' ]
                , [ 'false', '0' ]
                , [ 'false', '""' ]
                , [ '1', 'true' ]
                , [ '100', 'true' ]
                , [ 'NaN', 'NaN' ]
            ]
            , expr
            ;
        s.show('common equaltions test: \n');
        for(var i=0; i<items.length; i++){
            expr = items[i][0] + ' == ' + items[i][1]; 
            s.append_show(
                expr
                , eval(expr)
            );
        }

    })();

</div>
<div class="test-panel">
</div>
</div>




## 绝对等式

    typeof null === 'object'
    void 0 === undefined

数字判断：

    num === +num

IE9以下，`hasEnumBug`

<div id="test_25" class="test">
<div class="test-console"></div>
<div class="test-container">

    @[data-script="javascript editable"](function(){

        var s = fly.createShow('#test_25');
        var items = [
                [ 'typeof null', '"object"' ]
                , [ 'void 0', 'undefined' ]
                , [ '5', '+5' ]
                , [ '1', 'true' ]
                , [ 'Infinity', 'Infinity' ]
            ]
            , expr
            ;
        s.show('absolute equaltions test: \n');
        for(var i=0; i<items.length; i++){
            expr = items[i][0] + ' === ' + items[i][1]; 
            s.append_show(
                expr
                , eval(expr)
            );
        }

    })();

</div>
<div class="test-panel">
</div>
</div>




## TRUE表达式

    !null
    !void 0
    !undefined
    !0
    !''
    !NaN
    !!Infinity


## 位运算符

`~a`相当于

    var b = -a;
    b = b -1;

<div id="test_30" class="test">
<div class="test-console"></div>
<div class="test-container">

    @[data-script="javascript editable"](function(){

        var s = fly.createShow('#test_30');
        s.show('bit-wise operations: ');

        s.append_show(
            '~5'
            , ~5    
        );

        s.append_show(
            '~0'
            , ~0    
        );

        s.append_show(
            '~-1'
            , ~-1    
        );

        s.append_show(
            '( 3.1415 | 0 )'
            , 3.1415 | 0 
        );

    })();

</div>
<div class="test-panel">
</div>
</div>


## Object.prototype.toString.call(obj)

注意`不是Object.toString`，该toString来自`Function.prototype.toString`

    function(){}    [object Function]
    []              [object Array]
    10              [object Number]



## arguments是Array-like的

    (function(){
        console.log(arguments);
    })();

`output`:

    []


## 正则表达式之转义序列

1. `\xxx`: 八进制数xxx规定的字符
2. `\xdd`: 十六进制数dd规定的字符
3. `\uxxxx`:十六进制数xxxx规定的Unicode字符



## CSSOM部分相关扩展 


> <http://www.w3.org/TR/cssom-view-1/>


### 相关概念

* padding edge / padding area
* border edge / border area
* scrolling area
* css pixels / device pixels
* layout box: css layout box / svg layout box


### window扩展

 <img src="./img/cssom-window.png" height="400">

1. [非CSSOM扩展] window.getComputedStyle(element)
2. [viewport] window.innerWidth
3. [viewport] window.innerHeight
4. [viewport scrolling] window.scrollX 
5. [viewport scrolling] window.scrollY 
6. [viewport scrolling] window.pageXOffset 
7. [viewport scrolling] window.pageYOffset 
11. [client] window.screenX
12. [client] window.screenY
13. [client] window.outerWidth
14. [client] window.outerHeight
15. [client] window.devicePixelRatio
8. [viewport scrolling] window.scroll() 
9. [viewport scrolling] window.scrollTo() 
10. [viewport scrolling] window.scrollBy() 



### element扩展

1. element.getClientRects()，返回`DOMRect`，包含`x`, `y`, `width`, `height`, `top`, `right`, `bottom`, `left`字段，与`clientArea`相关
2. element.getBoundingClientRect()，与`clientArea`相关
3. element.scrollIntoView()
7. element.scrollTop
8. element.scrollLeft
9. [readonly] element.scrollWidth
10. [readonly] element.scrollHeight，不包含border
11. [readonly] element.clientTop，`border-top-width`加上border-top和top padding edge之间可能存在的`scroll bar`的height。<http://www.w3.org/TR/cssom-view-1/#dom-element-clienttop>
12. [readonly] element.clientLeft
13. [readonly] element.clientWidth
14. [readonly] element.clientHeight，不包含border
4. element.scroll()
5. element.scrollTo()
6. element.scrollBy()


### HTMLElement扩展

1. [readonly] offsetParent
2. [readonly] offsetTop，与`offertParent`有关
3. [readonly] offsetLeft
4. [readonly] offsetWidth
5. [readonly] offsetHeight， `border edge height`，包含border


<div id="test_60" class="test" style="position:relative; border: 1px solid red;">
<div class="test-panel" style="
    height: 100px; 
    border: 1px solid green; 
    background-color: #17becf;
    text-align: center;
    font-size: 80px;
    line-height: 100px;
    color: #fff;
    ">.test-panel</div>
<div class="test-console"></div>
<div class="test-container">

    @[data-script="javascript editable"](function(){

        setTimeout(function(){

            var s = fly.createShow('#test_60')
                , $element = $('#test_60 .test-panel')
                , element = $element[0]
                , st = window.getComputedStyle(element)
                , list
                , element
                ;
            s.show('getComputedStyle() with .test-panel: ');
            list = [
                'display'
                , 'box-sizing'
                , 'width'
                , 'height'
                , 'padding-left'
                , 'margin-left'
                , 'position'
                , 'left'
                , 'color'
                , 'background'
                , 'font'
            ];
            list.forEach(function(item){
                s.append_show('st["' + item + '"]', st[item]); 
            });


            s.append_show('\n.test-panel getClientRects() & getBoundingClientRect(): ');
            s.append_show(
                objectParse(element.getClientRects())
            );
            s.append_show(
                objectParse(element.getBoundingClientRect())
            );


            list = [
                'innerWidth'
                , 'innerHeight'
                , 'scrollX'
                , 'scrollY'
                , 'pageXOffset'
                , 'pageYOffset'
                , 'screenX'
                , 'screenY'
                , 'outerWidth'
                , 'outerHeight'
                , 'devicePixelRatio'
            ];

            s.append_show('\nwindow extensions: ');
            list.forEach(function(item){
                s.append_show('window.' + item, window[item]); 
            });

            list = [
                'scrollTop'
                , 'scrollLeft'
                , 'scrollWidth'
                , 'scrollHeight'
                , 'clientTop'
                , 'clientLeft'
                , 'clientWidth'
                , 'clientHeight'
            ];

            s.append_show('\n.test-panel element extensions: ');
            list.forEach(function(item){
                s.append_show('element.' + item, element[item]); 
            });

            list = [
                'offsetTop'
                , 'offsetLeft'
                , 'offsetWidth'
                , 'offsetHeight'
            ];

            s.append_show('\n.test-panel htmlelement extensions: ');
            list.forEach(function(item){
                s.append_show('element.' + item, element[item]); 
            });


            s.append_show('\n.test-panel jQuery offset() and position()');
            s.append_show('offset()', $element.offset());
            s.append_show('position()', $element.position());


            function objectParse(obj){
                var ret = {};
                if(typeof obj == 'object'){
                    for(var i in obj){
                        ret[i] = objectParse(obj[i]);
                    }
                }
                else {
                    ret = obj;
                }
                return ret;
            }

        }, 1000);

    })();

</div>
</div>





## JSON.stringify

    JSON.stringigy(document.body.getBoundingClientRect())

`Safari`能输出`ClientRect`类型对象的内部内容，而`Chrome`只输出空对象`"{}"`。
这也是上方`objectParse()`方法存在的原因，能保证`Chrome`能输出其内容。




## hasOwnProperty() 与 in 操作符

> <http://www.ecma-international.org/ecma-262/6.0/#sec-own-property>


### hasOwnProperty()

* `own property`: property that is directly contained by its object
* `inherited property`: property of an object that is not an own property but is a property (either own or inherited) of the object’s prototype

### in operator 

The production RelationalExpression : RelationalExpression in ShiftExpression is evaluated as follows:
1. Let `lref` be the result of evaluating RelationalExpression.
2. Let `lval` be GetValue(lref).
3. Let `rref` be the result of evaluating ShiftExpression.
4. Let `rval` be GetValue(rref).
5. If Type(rval) is not Object, throw a TypeError exception.
6. Return the result of calling the `[[HasProperty]]` `internal` method of rval with argument ToString(lval).

`in`使用的是内部方法`hasProperty()`，包含继承而来的属性。

<div id="test_90" class="test">
<div class="test-console"></div>
<div class="test-container">

    @[data-script="javascript editable"](function(){

        var s = fly.createShow('#test_90')
            , obj1 = {name: 'hudamin'}
            ;

        s.show('hasOwnProperty(): ');
        s.append_show(obj1.hasOwnProperty('name'));

        function Person(name, age, sex){
            this.name = name;
            this.age = age;
            this.sex = sex;
        }

        Person.prototype.sayHello = function(){
            s.append_show(this.name, this.age, this.sex);
        }

        function Student(name, age, sex, score){
            Person.call(this, name, age, sex);
            this.score = score;
        }

        Student.prototype = new Person();
        Student.prototype.constructor = Student;
        Student.prototype.sayHello = function(){
            Person.prototype.sayHello.apply(this);
            s.append_show(this.score);
        }


        var obj2 = new Student('hudamin', 20, 'male', 100)
            , list = ['name', 'sayHello', 'sayYes']
            , wrapper = {Person: Person, Student: Student}
            ;

        s.append_show('\nsayHello:');
        obj2.sayHello();
        obj2.sayYes = function(){
            s.append_show('YES!');
        };

        s.append_show('\n');
        list.forEach(function(key){
            s.append_show(
                'obj2.hasOwnProperty("' + key + '")'
                , obj2.hasOwnProperty(key)
            );
        });

        s.append_show('\nin operator:');

        var props = []
            , ownProps = [];
        for(var i in obj2){
            props.push(i); 
            if(obj2.hasOwnProperty(i)){
                ownProps.push(i);
            }
        }
        s.append_show('property', props);
        s.append_show('own property', ownProps);

        s.append_show('\ninstanceof operator');
        list = ['Student', 'Person'];
        list.forEach(function(item){
            s.append_show(
                'obj2 instanceof ' + item
                , obj2 instanceof wrapper[item]
            );
        });

    })();

</div>
<div class="test-panel">
</div>
</div>


## Fundamental Objects

<http://www.ecma-international.org/ecma-262/6.0/#sec-fundamental-objects>

    Object.assign(target, ...sources)
    Object.create(O [, Properties ])
    Object.defineProperties(O, Properties)
    Object.defineProperty(), P, Attributes)
    Object.freeze(O)
    Object.getOwnPropertyDescriptor(O, P)
    Object.getOwnPropertyNames(O)
    Object.getOwnPropertySymbols(O)
    Object.getPrototypeOf(O)
    Object.is(value1, value2)
    ...






## blob

## Float32Array



