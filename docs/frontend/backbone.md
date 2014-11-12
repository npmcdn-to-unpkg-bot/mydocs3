# Backbone解析

2014-09-29

## AMD、CMD支持

    (function(root, factory){
        if(typeof define === 'function' && define.amd){
            define(['underscore', 'jquery', 'exports'], function(_, $, exports){
                root.Backbone = factory(root, exports, _, $);
            });
        } 
        else if(typeof exports !== 'undefined'){
            var _ = require('underscore');
            factory(root, exports, _);
        }
        else {
            root.Backbone = factory(root
                , {}, root._
                , ( root.jQuery || root.Zepto || root.ender || root.$ )
            );
        }
    })(this, function(root, Backbone, _, $){
        ...
    });

## Events，可以mixin到任何一个对象，提供自定义事件的功能。

1. on/bind: function(name, callback, context)
2. once: function(name, callback, context)
3. off/unbind: function(name, callback, context)
4. trigger: function(name)
5. stopListening: function(obj, name, callback)
6. listenTo: function(obj, name, callback)
7. listenToOnce: function(obj, name, callback)

## Model，通常表示数据表的一行

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
    6. sync: function()
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

        如果是hash，fragment为hash值（不包含#），如果是pushState，fragment为path值（出去root部分）

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

1. Backbone.sync: function(method, model, options)
2. Backbone.ajax: function()
3. Backbone.history = new History
4. Backbone.VERSION = '1.1.2';
5. Backbone.$ = $;
6. Backbone.noConflict = function()
7. Backbone.emulateHTTP = false
8. Backbone.emulateJSON = false
