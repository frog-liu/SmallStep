## 2021-05-04

1.  Redis 实现分布式锁

    导入对应maven依赖

    ```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.3.0</version>
    </dependency>
    ```

    Java实现方式

    ```java
    public class RedisUtils {
        private static final String SUCCESS = "OK";
        /**
        * 获取分布式锁
        **/
        public static boolean tryGetDistributedLock(Jedis jedis, String lockKey, String requestId, long expiretime) {
            String result = jedis.set(lockKey, requestId, getNxSetParams(expiretime));
            return SUCCESS.equals(result);
        }
        
        /**
        * 释放分布式锁
        **/
        public static boolean releaseDistributedLock(Jedis jedis, String lockKey, String requestId) {
            String luaScript = "if redis.call('get', KEY[1]) == ARGV[1] then return redis.call('del', KEY[1]) else return 0 end";
            Object result = jedis.eval(luaScript, Colletions.singletonList(lockKey), Colletions.singletonList(requestId));
            return SUCCESS.equals(result);
        }
        
        private static SetParams getNxSetParams(long expiretime) {
            return new SetParams().nx().px(expiretime);
        }
    }
    ```

    

2.  

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

## 2021-05-06

1.  了解xxl-job排入计划：[xxl-job](https://github.com/xuxueli/xxl-job)

## 2021-05-07

1.  excel 使用

    (1) 两个sheet数据快速比较

    ​	新建sheet，使用IF函数对两个表的数据进行比较

    (2) 字符串操作

    ​	FIND(targetStr, range, index) -- 查找文本

    ​	LEFT(text, charNum) -- 从左边开始截取指定位数字符串

    ​	RIGHT(text, charNum) -- 从右边开始截取指定位数字符串

    ​	MID(text, startNum, charNum) -- 从中间指定位置截取指定位数字符串

    ​	LEN(text) -- 返回字符串长