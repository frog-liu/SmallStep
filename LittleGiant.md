## 2021-05-04

1.  Redis 常用命令

    ```json
    EXISTS key...  -- key单个或者多个，返回存在key的个数
    HSET hash key... value... -- 指定hash存放多个key-value，返回添加成功的个数；如果指定的hash已存在指定的key，则对应的value会被覆盖
    PEXPIRE key expireTime -- 指定key的过期时间，单位毫秒
    HEXISTS hash key -- 指定hash是否存在指定key，存在返回1，否则返回0
    HINCRBY hash key number -- 指定hash的指定key对应的value增加number，返回增加后的值
    PTTL key -- 返回指定key的剩余有效时间，如果不存在key则返回-2，如果key没有关联过期时间则返回-1
    PUBLISH -- 发布消息，返回接收到消息的redis客户端数量
    ```

2.  Redis 实现分布式锁

    导入对应maven依赖

    ```xml
    <!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
    <dependency>
        <groupId>org.redisson</groupId>
        <artifactId>redisson</artifactId>
        <version>3.15.0</version>
    </dependency>
    ```

    Java实现主要部分代码

    ```java
    public class XXXClass {
        @Resource
        private RedissionClient redissionClient;
       	
        public void xxxMethod() {
            Rlock lock = redissionClient.getLock(lockName);
            try {
                lock.lock(expireTime, timeUnit);
                do something...
            } catch (Exception e) {
                handleException(e);
            } finally {
                lock.unlock();
            }
        }
    }
    ```

    加锁Lua脚本

    ```lua
    -- 是否存在对应的lockName
    if (redis.call('exists', KEYS[1]) == 0) then
        -- 如果不存在，生成名为lockName的hash,并设置key为ARGV[2]，value为1
        redis.call('hset', KEYS[1], ARGV[2], 1);
        -- 设置过期时间为ARGV[1]
        redis.call('pexpire', KEYS[1], ARGV[1]);
        -- 返回nil结束
        return nil;
        end;
    	-- 如果存在，则判断名为lockName的hash里面是否存在指定key(ARGV[2])	
    if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then
        -- 如果存在，则锁重入:ARGV[2]对应的value增加1，重置过期时间
        redis.call('hincrby', KEYS[1], ARGV[2], 1);
        redis.call('pexpire', KEYS[1], ARGV[1]);
        -- 返回nil结束
        return nil;
        end;
    -- 如果存在lockName，不存在指定Key，则返回lockName的过期时间
    return redis.call('pttl', KEYS[1]);
    ```
    
    解锁Lua脚本
    
    ```lua
    -- 如果KEYS[1] hash不存在对应Key ARGV[3]
    if (redis.call('hexists', KEYS[1], ARGV[3]) == 0) then
        -- 返回nil，结束
        return nil;
        end;
    -- KEYS[1] hash对应ARGV[3] Key的value减一
    local counter = redis.call('hincrby', KEYS[1], ARGV[3], -1);
    if (counter > 0) then
        -- 如果减一操作后，value大于0，则重置过期时间，返回0结束
        redis.call('pexpire', KEY[1], ARGV[2]);
        return 0;
        else
        -- 如果减一操作后，value小于等于0，则删除hash
        redis.call('del', KEYS[1]);
        -- 发布无所的消息，返回1结束
        redis.call('publish', KEYS[2], ARGV[1]);
        return 1;
        end;
    return nil;
    ```
    
    暴力解锁Lua脚本
    
    ```Lua
    -- 删除指定hash KEYS[1], 成功发布无效消息并返回1，否则返回0
    if (redis.call('del', KEYS[1]) == 1) then
        redis.call('publish', KEYS[2], ARGV[1]);
        return 1
        else
        return 0
        end
    ```
    
    

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

## 2021-05-11

1.  Java注解

    (1)  元注解

    ```apl
    @Target: -- 注解作用域
    {
    	ElementType.TYPE            -- 类或接口
    	ElementType.FIELD           -- 属性
    	ElementType.METHOD          -- 方法
    	ElementType.PARAMETER       -- 参数
    	ElementType.CONSTRUCTOR     -- 构造方法
    	ElementType.LOCAL_VARIABLE  -- 本地变量
    	ElementType.ANNOTATION_TYPE -- 注解
    	ElementType.PACKAGE         -- 包
    	ElementType.TYPE_PARAMETER  -- 参数类型
    	ElementType.TYPE_USE        -- 构造方法
    }
    ```

    ```apl
    @Retention: -- 注解生命周期
    {
    	RetentionPolicy.SOURCE  -- 编译时去除
    	RetentionPolicy.CLASS   -- 编译后保留在class文件,JVM运行时去除
    	RetentionPolicy.RUNTIME -- 编译至运行时一直保留
    }
    ```

    ```apl
    @Inherited: -- 该注解可被子类继承
    ```

    ```apl
    @Documented: -- 生成javadoc时会包含注解
    ```

## 2021-05-13

1.  AST

    **名词解释：** Abstract Syntax Tree，简称AST，抽象语法树。以树状结构展示代码结构。

    **作       用：**动态修改代码，例如lombok

2.  java源文件编译成class文件过程

    ```xml
    (1) 将所有的源文件解析成语法树，输入到编译器的符号表
    	a. 词法分析：将源码的字符流转换成token流
    		token主要分为三种类型：java关键字、运算符&逻辑运算符、自定义变量
    	b. 语法分析：TreeMaker依据token流构建语法树
    		语法树的每一个节点代表程序代码，如：包、类型、修饰符、运算符、类、数据类型、方法、返回值等
    	c. 将java类中的符号输入到符号表中
    (2) 注解处理器的注解处理过程
    (3) 分析语法树并生成字节码
    ```