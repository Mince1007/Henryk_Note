### 1.concat方法

方法用于连接两个或多个数组（arr.concat(arr2);）；返回一个新的数组。

### 2.引用组件

<template>
     <return-back></return-back>
</template>

```javascript
import ReturnBack from 'src/home/mod_common/components/return_back';

components: {
ReturnBack
}
```

### 3.请求数据绑定到表格

```javascript
async loadData () {
let res = await axios.post('/index.php/comment/product_input', {
product: this.product
});
if (res.success && res.data) {
this.$refs.work_count_table && this.$refs.work_count_table.loadData(res.data || []);
}
}
```

