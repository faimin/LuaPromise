# LuaPromise

`lua`版本的`promise`实现

+ 支持链式调用

### Usage

```lua
-- 测试代码
local _class = {}

function _class:new()
    local class = {}
    class["key"] = "value"
    setmetatable(class, self)
    return self
end

local promise1 = Promise:new():async(function(fulfill, reject)
    fulfill(100)
--    reject("❌")
end):thenNext(function(value)
    return value * 2      -- value = 100
end):catch(function(error)
    print(error)         -- errorxxxx
end):thenNext(function(value)
    print(value)          -- 200
    return value / 100     -- 2
end)

local promise2 = Promise:new():async(function(fulfill, reject)
    fulfill(2300)
end)

local promise3 = Promise:new():async(function(fulfill, reject)
--    fulfill("你好")
    reject("❌")
end)

Promise:new():all({ promise1, promise2, promise3 }):thenNext(function(value)
    return value
end):catch(function(error)
    print(error)
end):thenNext(function(value)
    for i, v in ipairs(value) do
        print(i, v)
        --print result is:
        --1    2
        --2    2300
        --3    你好
    end
end)

Promise():finish({ promise1, promise2, promise3 }):thenNext(function(value)
    print("第一个next")
    return value
end):catch(function(error)
    print("都错了", error)
end):thenNext(function(value)
    print("来结果了。。。")
    for i, v in ipairs(value) do
        print(i, v)
    end
end)

--支持内部返回promise的处理
Promise():async(function(fulfill, reject)
    local p = Promise():async(function(_fulfill, _reject)
        _fulfill("1")
        --_reject("-1")
    end)
    fulfill(p)
end):thenNext(function(v)
    local p = Promise():async(function(_fulfill, _reject)
        _fulfill(v .. " 10000000")
        --_reject("内部错误")
    end)
    return p
end):catch(function(e)
    local errortext = "catch结果 = " .. e
    return errortext
end):thenNext(function(v)
    print("thenNext结果 = ", v)
end):catch(function(e)
    print("最后的error", e)
end)

return _class
```
