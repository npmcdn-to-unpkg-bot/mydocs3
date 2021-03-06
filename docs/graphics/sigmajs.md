# sigmajs


> 致力于在网页中绘制`网状图形`，提供交互接口。图形技术支持Canvas、WebGL和SVG。


## 一、初识

<http://sigmajs.org>

<https://github.com/jacomyal/sigma.js/wiki>





<script src="http://258i.com/static/build/sigma/sigma.js"></script>
<script src="http://258i.com/static/bower_components/snippets/js/mp/fly.js"></script>
<style type="text/css">
@import "http://258i.com/static/bower_components/snippets/css/mp/style.css";
</style>






## 二、快速启动

以下代码提供`sigma`实例的生成器，根据`实例ID`在上下文中只保持一个实例，即使`多次调用`也是如此。

    @[data-script="javascript"]
    function getUniqueSigmaInstance(instId, config){

        var instances = (
                arguments.callee.__instances
                    || ( arguments.callee.__instances = [] )
            )
            ;

        if(!instances[instId]) {
            if(!config) {
                instances[instId] = new sigma();
            }
            else {
                instances[instId] = new sigma(
                    $.extend(
                        {}
                        , config
                    ) 
                );
            }
        }

        return instances[instId];
    }

    function getRandomGraph(numOfNodes, numOfEdges, isFixSize){

        var i
            , s
            , N = numOfNodes
            , E = numOfEdges
            , g = { nodes: [], edges: [] }
            ;

        for(i=0; i<N; i++){
            g.nodes.push({
                id: 'n' + i
                , label: '' + i
                , x: Math.random()
                , y: Math.random()
                , size: isFixSize ? 0.05 : Math.random()
                , color: fly.randomColor() 
            });
        }

        for(i=0; i<E; i++){
            g.edges.push({
                id: 'e' + i
                , source: 'n' + (Math.random() * N | 0) 
                , target: 'n' + (Math.random() * N | 0) 
                , size: isFixSize ? 0.01 : Math.random()
                // , type: 'curve'
                // , color: fly.randomColor() 
                , color: '#ccc'
                , hover_color: '#f00'
            });
        }

        return g;
    }


再提供一个`脉搏`模拟器`TickEaseIn`：

    @[data-script="javascript"]function TickEaseIn(begin, end, steps, type){
        var me = this;

        me.begin = begin;
        me.end = end;
        me.steps = steps;

        // type: 'quadratic|cubic'
        me.type = type || 'quadratic';
        me._currentStep = 1;
        me._currentValue = me.begin;
        me.initial = false;
    }

    TickEaseIn.prototype.step = function(){
        var me = this
            , delta
            , ret
            , begin = me.begin
            , end = me.end
            , _curStep = me._currentStep
            , steps = me.steps
            ; 

        if(!me.initial){
            me.initial = true;
            return me.begin;
        }

        if(me.isFinished()) {
            ret = null;
        }
        else {
            if('cubic' == me.type){
                delta = ( end - begin )
                    * ( 3 * Math.pow(_curStep, 2) - 3 * _curStep + 1 )
                    / Math.pow(steps, 3)
                    ;
            }
            else {
                delta = ( end - begin ) 
                    * ( 2 * _curStep - 1 ) 
                    / Math.pow(steps, 2);
            }
            if(me._currentStep == me.steps){
                me._currentValue = me.end;
            }
            else {
                me._currentValue += delta;
            }
            ret = me._currentValue;
        }
        me._currentStep++;
        return ret;
    };

    TickEaseIn.prototype.isFinished = function(){
        var me = this;
        if(me._currentStep > me.steps) {
            return true;
        }
        return false;
    };  



## 三、从例子开始

两个节点一条边。

<div id="test_10" class="test">
<div class="test-container">
<div id="test_10_graph" class="test-graph"></div>

    @[data-script="javascript editable"]
    (function(){

        var s = fly.createShow('#test_10');
        
        var sm = getUniqueSigmaInstance(
                    'test_10'
                    , {
                        renderers: [
                            {
                                container: 'test_10_graph'
                                , type: 'canvas'
                            }
                        ]
                    }
                ); 

        sm.graph
            .clear()
            .addNode({
                id: 'n0'
                , label: 'Hello'
                , x: 0.5
                , y: 0.5
                , size: 1
                , color: '#f00'
            }) 
            .addNode({
                id: 'n1'
                , label: 'World!'
                , x: 0.8
                , y: 0.8
                , size: 1
                , color: '#00f'
            }) 
            .addEdge({
                id: 'e0'
                , source: 'n0' 
                , target: 'n1'
                // , type: 'curve'
                , color: '#ccc'
            }) 
            ;

        s.show(1, sm.graph.nodes());
        s.append_show(2, sm.graph.edges());

        sm.refresh();

    })();

</div>
<div class="test-console"></div>
<div class="test-panel"></div>
</div>





@[style="text-align:center"]<img src="./img/sigma-graph-view-camera-renderer.png" width="560">





## 四、Renderers


目前支持Canvas、WebGL、SVG。

canvas绘制，`5`个`layer`：node、edge、labels、捕获鼠标事件层以及显示hover对象层。


### 4.1 自定义renderer插件

自定义节点渲染插件，比如`sigma.canvas.nodes.squares：`

    sigma.canvas.nodes.square = function(node, context, settings) {
        var prefix = settings('prefix') || '',
            size = node[prefix + 'size'];

        context.fillStyle = node.color || settings('defaultNodeColor');
        context.beginPath();
        context.rect(
            node[prefix + 'x'] - size,
            node[prefix + 'y'] - size,
            size * 2,
            size * 2
        );

        context.closePath();
        context.fill();
    };


### 4.2 sigma实例的创建

不带任何参数，只创建graph，不绑定renderer：

    var sm = new sigma();

如果只提供container，会绑定renderer：

    var sm = new sigma(containerId);
    var sm = new sigma(containerDom);





## 五、Settings

非常类似javascript的`prototype chain`。

`三个`层次的settings，组件本身的配置、sigma实例的配置以及全局默认配置，也按照这个顺序逐次往上获取。

三条`规则`：

1. It must be possible to have two running instances of sigma with different parameters.
2. It must be possible to have two running renderers of the same sigma instance with different parameters.
3. The settings of each renderer, of each instance, and the global settings must be modifiable at any time.









<div id="test_20" class="test">
<div class="test-container">
<div id="test_20_graph" class="test-graph"></div>
<div class="test-console"></div>

    @[data-script="javascript editable"]
    (function(){

        var s = fly.createShow('#test_20');
        var g = getRandomGraph(10, 20, true);
        var containerId = 'test_20_graph';

        var sm = getUniqueSigmaInstance(
                    'test_20'
                    , {
                        settings: { 
                            // rescale settings 
                            sideMargin: 0.1 

                            // instance global settings
                            , enableEdgeHovering: true
                            , edgeHoverPrecision: 5
                        }
                        , renderers: [
                            {
                                type: 'canvas' 
                                , container: containerId 
                                , settings: {
                                    // captors settings
                                    doubleClickEnabled: true
                                    , mouseWheelEnabled: false

                                    // rescale settings
                                    , minEdgeSize: 0.5
                                    , maxEdgeSize: 1
                                    , minNodeSize: 1 
                                    , maxNodeSize: 5

                                    // renderer settings
                                    , edgeHoverColor: fly.randomColor() 
                                    , edgeHoverSizeRatio: 1
                                    , edgeHoverExtremities: true
                                }
                            }
                        ]
                    }
                ); 

        sm
            .graph
            .clear()
            .read(g)
            ;

        sm.refresh();

    })();

</div>
<div class="test-panel"></div>
</div>




## 六、事件绑定




<div id="test_30" class="test">
<div class="test-container">
<div id="test_30_graph" class="test-graph"></div>
<div class="test-console"></div>

    @[data-script="javascript editable"]
    (function(){

        var s = fly.createShow('#test_30');
        var g = getRandomGraph(3, 15, true);
        var containerId = 'test_30_graph';

        var sm = getUniqueSigmaInstance(
                    'test_30'
                    , {
                        settings: { 
                            sideMargin: 0.05 

                            // instance global settings
                            , enableEdgeHovering: true
                            , edgeHoverPrecision: 5
                        }
                        , renderers: [
                            {
                                type: 'canvas' 
                                , container: containerId 
                                , settings: {
                                    // captors settings
                                    doubleClickEnabled: true
                                    , mouseWheelEnabled: false

                                    // rescale settings
                                    , minEdgeSize: 0.5
                                    , maxEdgeSize: 1
                                    , minNodeSize: 1 
                                    , maxNodeSize: 5

                                    // renderer settings
                                    , edgeHoverColor: fly.randomColor() 
                                    , edgeHoverSizeRatio: 1
                                    , edgeHoverExtremities: true
                                }
                            }
                        ]
                    }
                ); 

        sm
            .graph
            .clear()
            .read(g)
            ;

        var m = 1
            , isSimpleMode = $('#test_30_checkbox').is(':checked')
            ;

        s.show('starting ...');
        sm
            .unbind()
            .bind(
                'click doubleClick'
                , function(e) {
                    s.append_show(m++, e.type, e.data.captor);
                }
            )
            .bind(
                'overNode outNode clickNode doubleClickNode rightClickNode'
                , function(e) {
                    if(isSimpleMode){
                        s.append_show(m++, e.type);
                    }
                    else {
                        s.append_show(m++, e.type, e.data.node.label, e.data.captor);
                    }
                }
            )
            .bind(
                'overEdge outEdge clickEdge doubleClickEdge rightClickEdge'
                , function(e) {
                    if(isSimpleMode){
                        s.append_show(m++, e.type);
                    }
                    else {
                        s.append_show(m++, e.type, e.data.edge, e.data.captor);
                    }
                }
            )
            .bind(
                'clickStage doubleClickStage rightClickStage'
                , function(e) {
                    if(isSimpleMode){
                        s.append_show(m++, e.type);
                    }
                    else {
                        s.append_show(m++, e.type, e.data.captor);
                    }
                }
            )
            .bind(
                'clickNodes clickEdges doubleClickNodes doubleClickEdges rightClickNodes rightClickEdges overNodes overEdges outNodes outEdges'
                , function(e) {
                    if(isSimpleMode){
                        s.append_show(m++, e.type);
                    }
                    else {
                        s.append_show(m++, e.type, e.data.edges || e.data.nodes, e.data.captor);
                    }
                }
            )
            .bind(
                'render'
                , function(e) {
                    s.append_show(m++, e.type);
                }
            )
            ;

        // s.show(sm.renderers);
        // s.show('sm.camaras', sm.cameras);

        sm.refresh();

    })();

</div>
<div class="test-panel container">
<div class="checkbox"><label><input type="checkbox" checked id="test_30_checkbox">精简模式</label></div>
</div>
</div>




## 七、Cameras


可以看作往`screen`投射图形的`摄像机`，决定`视角`、`比例`、`投射中心`等投射参数。需要考虑以下坐标空间：

* `graph`坐标空间
* `camera`坐标空间
* `screen`坐标空间，`renderer`的`width`与`height`属性，表示的就是`screen`坐标的尺寸

1. 可以认为在`ratio`为`1`的情况下，三者的`比例`是`1:1:1`，关系为：

        camera.x = graph.x * ratio

2. 将图形居中，可先获得`graph``坐标空间`中其图形中点的位置，记为`graph.x`, `graph.y`，再调用`camera.goTo()`

        cam1.goTo({
            x: graph.x
            , y: graph.y
        });

    `不受`当前`ratio`以及`angle`的`影响`。

3. `全局居中`展示。理论上可行，实践中发现，`仅使用`缩放来达到`居中`展示效果的方式`存在`一些问题。
    比如，它总是对`所有元素`都做`比例`缩放，这样就`无法`满足特定需求，比如希望在`全局`展示情况下，
    节点`尺寸`保持`固定`大小。`靠谱`方案还是得使用`rescale`，直接调整`坐标`。

        ratio = graph.w / screen.w; 
        cam1.goTo({
            x: graph.x
            , y: graph.y
            , ratio: ratio
        });


坐标系窥视：在下方代码编辑区中的`cam1.goTo(...)`中填入`合适`的`(x, y)`坐标，使得四个彩色球的图形在画布`居中`显示。

<div id="test_39" class="test">
<div class="test-container">
<div id="test_39_graph" class="test-graph"></div>
<div class="test-console"></div>

    @[data-script="javascript editable"]
    (function(){

        var s = fly.createShow('#test_39');
        var g = getRandomGraph(3, 15, true);
        var color = fly.randomColor();
        var g = {
            nodes: [
                {id: 'n2', x: 1000, y: -1000, size: 10, label: '(1000, -1000)', color: color}
                , {id: 'n0', x: 0, y: 0, size: 10, label: '(0, 0)', color: color}
                , {id: 'n1', x: 1000, y: 1000, size: 10, label: '(1000, 1000)', color: color}
                , {id: 'n3', x: 2000, y: 0, size: 10, label: '(2000, 0)', color: color}
                , {id: 'n4', x: 1000, y: 0, size: 20, label: '(1000, 0)', color: color}
            ]
            , edges: []
        };
        var g2 = {
            nodes: [
                {id: 'n0', x: 0, y: -100, size: 1, label: '(0, -100)', color: '#666'}
                , {id: 'n1', x: 100, y: 0, size: 1, label: '(100, 0)', color: '#666'}
                , {id: 'n2', x: 0, y: 100, size: 1, label: '(0, 100)', color: '#666'}
                , {id: 'n3', x: -100, y: 0, size: 1, label: '(-100, 0)', color: '#666'}
            ]
            , edges: [
                {id: 'e0', source: 'n0', target: 'n2', color: '#666'}
                , {id: 'e1', source: 'n1', target: 'n3', color: '#666'}
            ]
        };
        var containerId = 'test_39_graph';

        var sm = getUniqueSigmaInstance(
                'test_39'
                , {
                    settings: {
                        autoRescale: 0
                    }
                }
            )
            , sm2 = getUniqueSigmaInstance(
                'test_39_2'
                , {
                    graph: g2
                    , settings: {
                        autoRescale: 0
                    }
                    , renderers: [
                        {
                            type: 'canvas'
                            , container: containerId
                            , settings: {
                                mouseWheelEnabled: false
                            }
                        }    
                    ]
                }
            )
            , renderer1
            , cam1
            ; 

        if(!sm.rendererAdded){
            cam1 = sm.addCamera('cam1');

            renderer1 = sm.addRenderer({
                id: 'renderer1'
                , type: 'canvas' 
                , container: containerId
                , camera: cam1
                , settings: {
                    autoRescale: 0
                    , mouseWheelEnabled: false
                }
            });

            sm.rendererAdded = 1;
        }
        else {
            cam1 = sm.cameras['cam1'];
            renderer1 = sm.renderers['renderer1'];
        }

        sm
            .graph
            .clear()
            .read(g)
            ;

        sm.refresh();
        sm2.refresh();

        s.show(
            'camera.x', cam1.x
            , 'camera.y', cam1.y
            , 'camera.ratio', cam1.ratio
            , 'camera.angle', cam1.angle
        );
        s.append_show(
            'renderer.width', renderer1.width
            , 'renderer.height', renderer1.height
        );

        cam1.goTo({x:0, y:0, ratio:1, angle: Math.PI/4});

        s.append_show(
            'camera.x', cam1.x
            , 'camera.y', cam1.y
            , 'camera.ratio', cam1.ratio
            , 'camera.angle', cam1.angle
        );
        s.append_show(
            'renderer.width', renderer1.width
            , 'renderer.height', renderer1.height
        );

        var node = {x: 0, y:0}; 
        var pos = cam1.cameraPosition(node.x, node.y);
        s.append_show(
            '\ngraph space: (' + node.x + ', ' + node.y + ')'
        );
        s.append_show(
            'camera space: (' + pos.x + ', ' + pos.y + ')\n'
        );
        node = {x: 10, y:10}; 
        pos = cam1.cameraPosition(node.x, node.y);
        s.append_show(
            'graph space: (' + node.x + ', ' + node.y + ')'
        );
        s.append_show(
            'camera space: (' + pos.x + ', ' + pos.y + ')\n'
        );
        var rect = {w: 10, h: 10};
        s.append_show(
            'screen (w,h) = (' + rect.w + ', ' + rect.h + ')'
            , 'camera rect'
            , cam1.getRectangle(rect.w, rect.h)
        );

        function animate(){
            var fps = 20
                , xTick = new TickEaseIn(0, 1000, fps * 1, 'cubic')
                , ratioTick = new TickEaseIn(1, 10, fps * 1, 'cubic')
                , angleTick = new TickEaseIn(Math.PI / 4, Math.PI, fps * 1, 'cubic')
                ;

            _animate();

            function _animate(){
                if(!xTick.isFinished()){
                    cam1.goTo({
                        x: xTick.step()
                        , y: 0
                        , ratio: ratioTick.step() 
                        , angle: angleTick.step()
                    });
                    setTimeout(_animate, 1000 / fps);
                }
            }
        }

        setTimeout(function(){
            animate();
        }, 1000);

    })();

</div>
<div class="test-panel">
<button id="test_10_clear">sm.clear()</button>
</div>
</div>


Camera与Renderer的对应关系：

<style type="text/css">
#test_40 .test-graph {
    width: 25%;
    float: left;
}
</style>

<div id="test_40" class="test">
<div class="test-container">
<div id="test_40_graph_1" class="test-graph"></div>
<div id="test_40_graph_2" class="test-graph" style="width:75%;"></div>
<div class="test-console"></div>

    @[data-script="javascript editable"]
    (function(){

        var s = fly.createShow('#test_40');
        var g = getRandomGraph(3, 15, true);
        var containerId1 = 'test_40_graph_1';
        var containerId2 = 'test_40_graph_2';

        var sm = getUniqueSigmaInstance(
                'test_40'
            )
            , cam1
            , cam2
            ; 

        if(!sm.rendererAdded){
            cam1 = sm.addCamera('cam1');
            cam2 = sm.addCamera('cam2');

            sm.addRenderer({
                type: 'canvas' 
                , container: containerId1
                , camera: cam1
            });

            sm.addRenderer({
                type: 'canvas' 
                , container: containerId2 
                , camera: cam2
            });
            sm.rendererAdded = 1;
        }
        else {
            cam1 = sm.cameras['cam1'];
            cam2 = sm.cameras['cam2'];
        }

        sm
            .graph
            .clear()
            .read(g)
            ;

        var m = 1
            , isSimpleMode = $('#test_40_checkbox').is(':checked')
            ;

        s.show('camera1', cam1.x, cam1.y, cam1.ratio, cam1.angle);
        s.append_show('camera2', cam2.x, cam2.y, cam2.ratio, cam2.angle);

        var node = sm.graph.nodes()[0]; 
        var pos = cam1.cameraPosition(node.x, node.y);
        s.append_show(
            'cam1'
            , 'graph space: (' + node.x + ', ' + node.y + ')'
            , 'camera space: (' + pos.x + ', ' + pos.y + ')'
        );
        pos = cam2.cameraPosition(node.x, node.y);
        s.append_show(
            'cam2'
            , 'graph space: (' + node.x + ', ' + node.y + ')'
            , 'camera space: (' + pos.x + ', ' + pos.y + ')'
        );
        s.append_show(
            'screen (w,h) = (100, 100)'
            , 'cam1 rect'
            , cam1.getRectangle(100, 100)
        );
        s.append_show(
            'screen (w,h) = (100, 100)'
            , 'cam2 rect'
            , cam2.getRectangle(100, 100)
        );


        cam1.goTo({
            x: Math.random()
            , y: Math.random()
            , ratio: 1.5 
            // , angle: Math.PI / 2
            , angle: Math.PI 
        });

        cam2.goTo({
            x: 0
            , y: 0
            , ratio: 1
            , angle: 0
        });
        s.append_show('\nafter goTo() invoked...');
        s.append_show('camera1', cam1.x, cam1.y, cam1.ratio, cam1.angle);
        s.append_show('camera2', cam2.x, cam2.y, cam2.ratio, cam2.angle);

        $('#test_10_clear')
            .off()
            .on('click', function(){
                sm.graph.clear();
                sm.refresh();
            });

        sm.refresh();

        node = sm.graph.nodes()[0]; 
        pos = cam1.cameraPosition(node.x, node.y);
        s.append_show(
            'cam1'
            , 'graph space: (' + node.x + ', ' + node.y + ')'
            , 'camera space: (' + pos.x + ', ' + pos.y + ')'
        );
        pos = cam2.cameraPosition(node.x, node.y);
        s.append_show(
            'cam2'
            , 'graph space: (' + node.x + ', ' + node.y + ')'
            , 'camera space: (' + pos.x + ', ' + pos.y + ')'
        );

    })();

</div>
<div class="test-panel">
<button id="test_10_clear">sm.clear()</button>
</div>
</div>


## 八、滑块控制缩放

todo











