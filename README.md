# Name

lua-resty-rediswrap - Lua redis client for the ngx_lua based on openresty/lua-resty-redis;


# 使用方法

`redis`操作方法和`官方redis库`基本保持一致, 只是省去了设置超时时间、创建连接、设置`keepalive`连接池、关闭连接等步骤, 大大的方便我们的操作.

## `redis`常规命令用法

```lua
local redis = require "redis_wrap"

-- redis配置参数列表, 如果不想开启keepalive, 需要去掉下表中的keepalive两个元素,
-- 然后新加一个：keepalive = "off" 即可.
local redis_opts = {
    db_index = 1,
    timeout = 2000, -- 2 seconds.
    keepalive_max_idle_timeout = 10000, -- 10 seconds.
    keepalive_pool_size = 20,
}

-- 获取redis对象
local red = redis:new(redis_opts)

-- set 操作
local ok, err = red:set("dog", "an animal")
if not ok then
    ngx.say("failed to set dog: ", err)
    return
end

-- get 操作
local res, err = red:get("dog")
if not res then
    ngx.say("failed to get dog: ", err)
    return
end
```

## `pipeline requests`用法

```lua
local redis = require "redis_wrap"

local redis_opts = {
    db_index = 1,
    timeout = 2000, -- 2 seconds.
    keepalive_max_idle_timeout = 10000, -- 10 seconds.
    keepalive_pool_size = 20,
}

local red = redis:new(redis_opts)

red:init_pipeline(4) -- 参数可以省略, 加参数是在已知命令条目数量的情况下, 为了高效处理.

red:set("dog", "111")
red:set("cat", "222")
red:get("cat")
red:get("dog")

local results, err = red:commit_pipeline()
if not results then
    ngx.say("failed to commit the pipelined requests: ", err)
    return
end
```


# 参考资料

https://github.com/openresty/lua-resty-redis

https://moonbingbing.gitbooks.io/openresty-best-practices/content/redis/out_package.html
