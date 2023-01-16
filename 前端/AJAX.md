# Ajax

[TOC]

## 基本概念

Asynchronous JavaScript And XML

### 作用

与服务器进行数据交换：通过AJAX可以给服务器发送请求，并获取服务器响应的数据

异步交互：可以在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术





## Axios异步框架

使用前要引入axios的js

```javascript
<script src="js/axios-0.18.0.js"></script>
```

```javascript
axios({
    method:"get",
    url:"...."
}).then(function(resp){
    alert(resp.data)
});
```

