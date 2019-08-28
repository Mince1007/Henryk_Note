### 浏览器页面渲染过程

大概分成几个步骤：
1. 解析HTML文本并构建DOM tree
2. 解析CSS样式表并构建CSSOM tree
3. 根据DOM tree 和 CSSOM tree 构建 Render tree
4. 根据Render tree信息进行&布局处理（Layout）
5. 对页面元素进行绘制（Painting）

![avatar](./pic/webkitflow.png)

### Prototype原型
Prototype是JavaScript中的对象的一个特殊的内置属性，就是对于其它对象的引用。几乎所有的对象在创建时[[Prototype]]都会被赋予一个非空的值。  



### React Diff算法
广度优先
节点跨层移动
![avatar](./pic/diff-dommov-e.png)

### 问题定位技巧
1.调用栈
2.断点监听
3.更多的是自己看代码不要太偏执，都没看完或者看懂整体逻辑，就不应该去提问，很容易造成依赖；起码看不懂的话先问看不懂的，再去看整体逻辑和业务分析，再定位问题！这很关键！