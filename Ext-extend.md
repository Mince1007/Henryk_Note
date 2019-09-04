### Ext基础组件介绍


由图可见，我们要理解业务中最常用的组件，就需要了解它们所继承的几个父类；

**Component -> BoxComponent -> Container -> Panel**

Ext-API

#### 继承

```javascript
var BaseClass = function (config) {
    this.name = 'xxx';
    Ext.apply(this, config); //通过配置项来设置其属性,注意和applyIf进行区分
};

BaseClass.prototype = {
    say : function(){
    	alert('i am parent');
    }
}

/*
var p1 = new BaseClass({
	name: 'lzj'
})

p1.name // 'lzj'

*/

var ChildClass = function (config) {
	BaseClass.apply(this, arguments);
}

var F = function() {};

F.prototype = BaseClass.prototype;

ChildClass.prototype = new F(); // 原型指向空函数的实例，实际只继承了BaseClass的prototype; 

ChildClass.prototype.constructor = ChildClass; // 原型构造函数指向自己

ChildClass.superclass = BaseClass.prototype; //保存父类的prototype


var c1 = new ChildClass();
c1.name = 'xxx';
```

这里使用空函数的作用是实现了，公有属性和私有属性的分开继承，同时也避免了调用两次BaseClass；



改装
上面只是一个组件之间的继承，但是继承的目的只是方便我们二次开发，也就是我们还需要自己改装这个组件

通常有两种方式：

在子类继承时进行操作

```javascript
ChildClass.prototype.say = function () {
	alert('i am child');
}
c1.say();// 'i am child';

//在实例化的时候进行操作
var c1 = new ChildClass({
    say : function(){
    	alert('i am child');
    }
});
c1.say();// 'i am child';
```

但是这些都太麻烦了，Ext.extend函数中利用Ext.override帮我们做好了，我们只需要自己添加配置即可；不过只会覆盖prototype上的属性

```javascript
var ChildClass = Ext.extend(BaseClass,{
    say : function(){
    	alert('i am child');
    }
})
var c1 = new ChildClass();
c1.say();// 'i am child';
```

*注意，在实际开发中我们经常需要改写父类组件的某个函数功能，但可能只是需要加一点逻辑并不是完全重写，所以superclass的作用就体现出来了；

```javascript
var ChildClass = Ext.extend(BaseClass,{
    say : function(){
        alert('insert');
        ChildClass.superclass.say.apply(this,arguments);
    }
})
var c1 = new ChildClass();
c1.say();// 'insert i am parent';
```

#### Ext.util.MixedCollection

表示一组键和值对的集合。MixedCollection中的每个键都必须是唯一的，同一个键不能存在两次。此集合已排序，可以通过索引或键访问集合中的项。新添加的项将添加到集合的结尾。

```javascript
var coll = new Ext.util.MixedCollection();
coll.add('key1', 'val1');
coll.add('key2', 'val2');
coll.add('key3', 'val3');

console.log(coll.get('key1')); // prints 'val1'
console.log(coll.indexOfKey('key3')); // prints 2
```


Ext.ComponentMgr就用到了它：注册、创建组件，插件

```javascript
Ext.ComponentMgr = function(){
var all = new Ext.util.MixedCollection();
var types = {};
var ptypes = {};

return {
all : all,

register : function(c){
    all.add(c);
},

registerType : function(xtype, cls){
    types[xtype] = cls;
    cls.xtype = xtype;
},

create : function(config, defaultType){
    return config.render ? config : new types[config.xtype || defaultType](config);
},

registerPlugin : function(ptype, cls){
    ptypes[ptype] = cls;
    cls.ptype = ptype;
},
....
};
}();

Ext.reg = Ext.ComponentMgr.registerType; 

Ext.preg = Ext.ComponentMgr.registerPlugin;

Ext.create = Ext.ComponentMgr.create;
```

#### Ext.util.Observable

Base class that provides a common interface for publishing events. Subclasses are expected to to have a property "events" with all the events defined, and, optionally, a property "listeners" with configured listeners defined.

简单理解为，继承此属性的子类将会有事件监听和发布的功能

Component组件的事件机制就是继承于它

```javascript
Employee = Ext.extend(Ext.util.Observable, {
    constructor: function(config){
        this.name = config.name;
        this.addEvents({
            "fired" : true,
            "quit" : true
    	});

        // Copy configured listeners into *this* object so that the base class's
        // constructor will add them.
        this.listeners = config.listeners;

        // Call our superclass constructor to complete construction process.
        Employee.superclass.constructor.call(this, config);
        this.fireEvent('quit');
    }
});

var newEmployee = new Employee({
    name:'lzj',
    listeners: {
        quit: function() {
        // By default, "this" will be the object that fired the event.
        alert(this.name + " has quit!");
        }
    }
});

//'lzj has quit'
```



#### Component 

**主要流程：**

继承Ext.util.Observable ->

注册组件 -> 

initComponent(空函数，子组件继承时，代码初始化逻辑入口) ->

render ->

onRender ->

afterRender(空函数，子组件继承时，布局逻辑入口)



**核心函数**

\> render：设置组件的container属性，即组件在页面上挂载的真实dom；
\>
\> onRender：生成组件包裹节点this.el，并将需要渲染的dom结构添加到el内部，最后把this.el挂载到container上；



```javascript
javascript
this.render(this.renderTo);
this.onRender(this.container, position || null);

var cmp = new Ext.Component({
    renderTo : 'component-demo', //页面真实的dom节点id或者dom节点
    autoEl : {
        tag : 'p', //el的html标签
        html : '<span>1</span>' //组件内部渲染内容
    }
})

<div id="component-demo"></div>

/*页面dom结构
<div id="component-demo">
    <p id="ext-comp-1001">
    <span>1</span>
    </p>
</div>
*/

cmp.container //Ext.Element {dom: div#component-demo, id: "component-demo"}
cmp.el //Ext.Element {dom: p#ext-comp-1001, id: "ext-comp-1001"}
//Ext.Element是Ext将dom节点进行了一次封装，使得节点拥有了一些功能函数，例如addClass; 类比jq
```



#### BoxComponent

BoxComponent继承了Component，并改写了afterRender函数，使得BoxComponent支持设置宽高以及定位

```javascript
javascript
var cmp = new Ext.BoxComponent({
    autoEl : {
        tag : 'div',
        html : '<p style="height:400px;">1</p>'
    },
    renderTo : 'component-demo',
    width : 300,
    height : 200,
    boxMaxHeight : 400,
    autoScroll : true
});
```

#### Container

Container继承BoxComponent，可以将此组件理解为容器，因为增加了__items__属性，方便我们可以往此组件中添加其他组件，由于可以包含多个子组件，所以就有了对子 组件__布局__的概念；

