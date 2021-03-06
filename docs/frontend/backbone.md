# Backbone解析

2014-09-29

## AMD、CMD支持

    (function(root, factory){
        // AMD first
        if(typeof define === 'function' && define.amd){
            define(['underscore', 'jquery', 'exports'], function(_, $, exports){
                root.Backbone = factory(root, exports, _, $);
            });
        } 
        // then Node.js or CommonJS
        else if(typeof exports !== 'undefined'){
            var _ = require('underscore');
            factory(root, exports, _);
        }
        // last global Backbone
        else {
            root.Backbone = factory(root
                , {}, root._
                // $ can be jQuery, Zepto, ender
                , ( root.jQuery || root.Zepto || root.ender || root.$ )
            );
        }
    })(this, function(root, Backbone, _, $){
        ...
    });

## Events，可以mixin到任何一个对象，提供自定义事件的功能。

1. `on/bind`: function(name, callback, context)
2. `once`: function(name, callback, context)
3. `off/unbind`: function(name, callback, context)
4. `trigger`: function(name)
5. `stopListening`: function(obj, name, callback)
6. `listenTo`: function(obj, name, callback)
7. `listenToOnce`: function(obj, name, callback)

## Model，通常表示数据表的一行

### 1. 说明

1. `validate`对attributes进行验证，返回值error，如果`!error == true`，则通过验证；
    否则，属性不会变化，也不会触发change事件。

    仅当`options.validate标志被设置且model.validate方法存在`，才进行属性验证。

    只设置options.validate标志或者model.validate方法存在，都不足以促使进行属性验证。


2. 事件按序触发
3. `unset`不存在的属性，不会触发`change`事件
4. `this.id`代表Model的唯一服务端标识，它的值由以下代码决定：
    
        if(this.idAttribute in attrs) this.id = attrs[this.idAttribute];

    也就是说，即使idAttribute并不是`id`，而是比如说`_id`，也会生成`this.id`。

        model.unset('_id')
    
    将会重置`this.id`为`undefined`，使得其称为一个New Model，即`model.isNew()`为true
5. `model.sync`函数，其error事件在封装的`options.error`方法中触发
6. `save, fetch, destroy`都会调用`model.sync`和服务器进行同步
7. `patch`方式，若`options.attrs不存在`，会在options下添加`attrs`属性
8. 事件触发：

    * `request`：每次发起服务端请求时都会触发，参数为`model, xhr, options`
    * `sync`：请求成功返回以后触发，参数`model, resp, options`
    * `error`：请求失败以后触发，参数`model, resp, options`，同时会设置`model.validationError`
    * `change`：模型属性发生变化触发，参数`model, options`
    * `change:name`：模型属性发生变化触发，参数`model, attr, options`
    * `invalid`：模型验证失败时触发，参数`model, error, options`
    * `destroy`：发送同步请求前触发，或者同步请求成功返回后触发，参数`model, model.collection, options`

9. `success`与`error`函数参数：`model, resp, options`
10. `model.isNew() == true`情况下，destroy无需与服务器进行同步
11. 默认情况下，不会进行属性验证，除非传入`{validate: true}`。model.validate函数接收参数`attrs, options`
12. 嵌套change事件`不会改变model._previousAttributes`
13. `change:name`事件在所有属性都变化以后才触发
14. set同样的值，`不会触发change和change:name事件`
15. unset一个undefined的属性，不会触发change和change:name事件
16. `{wait: true}`的save方法，如果服务器请求失败，本地属性不会更新
17. `Utils.results(model, 'url')`必须有返回值，否则无法进行`model.sync`操作，程序异常
18. `{wait: true}`的save方法，即使服务器请求失败，本地属性也会进行validate
11. `save`与`fetch`都会设置`options.parse`标志位
1. 嵌套change事件只触发一次

        var times = 0;
        model.on('change:x', function(){
            times++;
            model.set({x: true});
        });
        model.set({x: true});

    以上代码，`times == 1`
1. `new Model(attrs, options)`，其中的attrs和options会`原封不动`的传入initialize方法，即使它们是`undefined`

### 2. 代码结构

1. constructor:
    1. cid: _.uniqueId('c')
    2. attributes: {...}
    3. collection: Backbone.Collection
    4. changed: {...}

2. prototype:
    1. changed: null
    2. validationError: null
    3. idAttribute: 'id'
    4. initialize: function(){}
    5. toJSON: funciton(options)
    6. sync: function(method, model, options)
    7. get: function(attr)
    8. escape: function(attr)
    9. has: function(attr)
    10. set: function(key, val, options)
        * trigger('change:KEYNAME', this, value, options)
    11. unset: function(attr, options)
    12. clear: function(options)
    13. hasChanged: function(attr)
    14. changedAttributes: function(diff)
    15. previous: function(attr)
    16. previousAttributes: function()
    17. fetch: function(options)
        * trigger('read', this, options)
        * trigger('sync', model, resp, options)
    18. save: function(key, val, options)
        * trigger('sync', model, resp, options)
    19. destroy: function(options)
    20. url: function()
    21. parse: function(resp, options)
    22. clone: function()
    23. isNew: function()
    24. isValid: function(options)
    25. _validate: function(attrs, options)
    26. keys: function() 
    27. values: function()
    28. pairs: function()
    29. invert: function()
    30. pick: function()
    31. omit: function()
    32. chain: function()
    33. isEmpty: function()

## Collection: rows 


## View


### 1. 说明

1. `view.delegateEvents(events)`，会略过未定义的method
2. `options.el`指定为一个不存在的元素，会导致`model.el`未定义
3. `构造参数：`
    
    * `View`: `options`，包含以下预定义选项：

            var viewOptions = [
                    'model'
                    , 'collection'
                    , 'el'
                    , 'id'
                    , 'attributes'
                    , 'className'
                    , 'tagName'
                    , 'events'
                ];            

    * `BaseView`: `options, parent` 
    * `PageView`: `options, action, router`，action为字符串，router为路由对象。PageView由Router创建，创建后PageView._router保存了对Router的引用。
    * `SubView`: 同BaseView
    * `SubPageView`: 同BaseView
    * `GlobalView`：`options, router`，router为路由对象


### 2. 代码结构

1. constructor
    1. cid: _.unique('view')
    2. attrs maybe:
        * model
        * collection
        * el
        * id
        * attributes
        * className
        * tagName
        * events
    2. this._ensureElement()
    3. this.initialize.apply(this, arguments)

2. vars
        var delegateEventSplitter = /^(\S+)\s*(.*)$/;

2. prototype
    1. tagName: 'div'
    2. $: function(selector)
    3. initialize: function()
    4. render: function()
    5. remove: function()
    6. _removeElement: function()
    7. setElement: function(element)
    8. _setElement: function(el)
    9. delegateEvents: function(events)
    10. delegate: funciton(eventName, selector, listener) 
    11. undelegateEvents: function()
    12. undelegate: funciton(eventName, selector, listener)
    13. _createElement: function(tagName)
    14. _ensureElement: function()
    15. _setAttributes: function(attributes)


## Router

### 1. 说明

1. `getHash`，不包含`#`。比如：
        http://path#hash
    返回值为：
        hash
2. 路由参数：

        命名参数：      /[^/?]+/ 
        splat参数：     /[^?]*?/
        可选参数：      /(?:([^/?]+))?/
        查询参数：      /(?:\?([\s\S]*))?$/

    `说明：`
    1. 命名参数`不包含/`，splat参数可以`包含/`，可选参数实际上是可选命名参数
    2. 命名参数`至少包含一个`字符，不能为空；splat参数`可以为空`，本身具有可选的特性 
    3. 查询参数在route尾部，`以?开头`
    4. 查询参数解析时，`不进行decode`

3. route事件：参数`name, args` 或 `route:name, args`

        routes: {
            /* router               route */
            'index/:type':          'index'
        }

    `route`对应`name参数`。

4. 不管路由callback是否存在，`总会触发route事件`
5. `router.execute`方法如果返回`false`，则不会触发route事件
6. `支持空路由模式`：

        routes: {
            /* router               route */
            '':                     'index'
        }

7. `支持默认路由处理器`，该处理器自动获取`action`，以及将对应的`命名参数转换成JSON`，并传给`Router.doAction`:

        routes: {
            "index":                      "_defaultHandler:index",
            "index/:type":                "_defaultHandler:index",
            "index/:type/p:page":         "_defaultHandler:index"
        }
    
    `_defaultHandler`是默认实现的方法，`冒号(:)`后面的非空单词为`实际action`
        


### 2. 代码结构

1. constructor

    1. this.routes = options.routes
    2. this._bindRoutes()
    3. this.initialize.apply(this, arguments)

2. vars:

        optionalParam = /\((.*?)\)/g 
        namedParam = /(\(\?)?:\w+/g 
        splatParam = /\*\w+/g 
        escapeRegExp = /[\-{}\[\]+?.,\\\^$|#\s]/g 

2. prototype

    1. initialize: function() 
    2. route: funciton(route, name, callback)
    3. execute: function(callback, args, name)
    4. navigate: function(fragment, options)

        如果是hash，fragment为hash值（不包含#），如果是pushState，fragment为path值（除去root部分）

    5. _bindRoutes: function()
    6. _routeToRegExp: function(route)
    7. _extractParameters: function(route, fragment)







## History

1. constructor

    1. this.handlers = []
    2. _.bindAll(this, 'checkUrl')
    3. this.location = window.location
    4. this.history = window.history


2. vars

        routeStripper = /^[#\/]|\s+$/g
        rootStripper = /^\/+|\/+$/g
        pathStripper = /#.*$/

3. static
    1. History.started = false

4. prototype

    1. interval: 50
    2. atRoot: function()
    3. getSearch: function()
    4. getHash: function(window)
    5. getPath: function()
    6. getFragment: function(fragment)
    7. start: function(options)
        * this.options
        * this.root
        * this._wantsHashChange
        * this._hasHashChange
        * this._wantsPushState
        * this._hasPushState
        * this.fragment
    8. stop: function()
    9. route: function(route, callback)
    10. checkUrl: function(e)
    11. loadUrl: function(fragment)
    12. navigate: function(fragment, options)
    13. _updateHash: function(location, fragment, replace)


## vars

    var previousBackbone = root.Backbone
    var slice = array.slice
    var urlError = function()
    var wrapError = function(model, options)

比较特殊的`extend`方法，生成一个子类：

子类可以通过`__super__.xxx`调用父类原型链的对应方法。

    var extend = function(protoProps, staticProps){
        var parent = this;
        var child;

        // The constructor function for the new subclass is either defined by you
        // (the "constructor" property in your `extend` definition), or defaulted
        // by us to simply call the parent's constructor.
        if (protoProps && _.has(protoProps, 'constructor')) {
          child = protoProps.constructor;
        } else {
          child = function(){ return parent.apply(this, arguments); };
        }

        // Add static properties to the constructor function, if supplied.
        _.extend(child, parent, staticProps);

        // Set the prototype chain to inherit from `parent`, without calling
        // `parent`'s constructor function.
        var Surrogate = function(){ this.constructor = child; };
        Surrogate.prototype = parent.prototype;
        child.prototype = new Surrogate;

        // Add prototype properties (instance properties) to the subclass,
        // if supplied.
        if (protoProps) _.extend(child.prototype, protoProps);

        // Set a convenience property in case the parent's prototype is needed
        // later.
        child.__super__ = parent.prototype;

        return child;
    };

## static attrs and methods

1. `Backbone.sync`: function(method, model, options)
2. `Backbone.ajax`: function()
3. `Backbone.history` = new History
4. `Backbone.VERSION` = '1.1.2';
5. `Backbone.$` = $;
6. `Backbone.noConflict` = function()
7. `Backbone.emulateHTTP` = false
8. `Backbone.emulateJSON` = false

