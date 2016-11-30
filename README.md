# vue-pagination
## 无依赖，使用简单，智能分布

## 样式可以方便自定义只需要传入4个prop：
``` bash
total（总页数） 
current（当前页）
handler（翻页处理函数）
between（当前页前后最多留的页码数）
```

## 使用方法示例
``` bash
import LjyPagination from 'path/ljy-pagination.vue'
```
``` html
<ljy-pagination
:total="total"
:current="page"
:between="2"
@change-page="changePage"
></ljy-pagination>
```