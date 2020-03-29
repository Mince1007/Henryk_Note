### webpack.config.js

学习地址：https://www.jianshu.com/p/080e18fcf0e3

阮一峰webpack github ：https://github.com/ruanyf/webpack-demos

```javascript
/*
entry是入口，output是输出生成的bundle文件对应关系。
运行: cd demo01，然后npm run dev即可
*/
//demo01
module.exports = {
    entry: 'index.js', //入口文件
    output： {
    	filename:'./bundle.js' //打包输出文件
	}
}
```

