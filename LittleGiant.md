## 2021-05-05

1.  JavaScript 缓存数据

    **localStorage**和**sessionStorage**缓存数据

    **localStorage**是HTML5客户端永久化数据存储方案，同一个域名范围内有效；**sessionStorage**在重启浏览器、关闭页面或者重新打开页面时失效。

    常见用法：

    ```javascript
    setItem(key, value) -- 指定某个key，缓存数据
    getItem(key) -- 获取指定key对应的数据
    removeItem(key) -- 删除指定key对应的数据
    key(index) -- 指定index获取对应的key
    clear() -- 删除所有的数据
    ```

    **特别说明**：storage只能缓存字符串，可以通过`JSON.stringify()`来转换Array、Map等，再通过`JSON.parse(data)`将Array、Map来转换成我们需要的格式，同时缓存的数据也有大小限制。

2.  MySql 多个字段排序

    格式如下：

    ```mysql
    order by field1 desc, field2 asc... -- 如果未指定desc或asc，默认按升序方式排列
    ```

3.  JQuery重置form表单

    ```js
    $('#id').trigger('reset')
    ```

4.  

