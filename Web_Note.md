### 浏览器页面渲染过程

大概分成几个步骤：

1. 解析HTML文本并构建DOM tree
2. 解析CSS样式表并构建CSSOM tree
3. 根据DOM tree 和 CSSOM tree 构建 Render tree
4. 根据Render tree信息进行布局处理（Layout）
5. 对页面元素进行绘制（Painting）

![](E:\Github-Henryk\Henryk_Note\pic\webkitflow.png)

