研究了 lua **在函数里创建函数** 的机制。并得出结论：（1h + 0.5h)

一个函数被创建在一个函数里时：

```lua
local function external()
	local function internal()
        
    end
end
```

如果该内部函数是稳定的结构，则无论外层函数调用多少次，该函数永远只会声明一次；不然则每一次调用外部函数时都会被重新声明一次。

稳定结构指：无论如何，函数内代码的运行结果不会因为外部而变化；

不稳定结构指：函数内代码的运行结果会随着外部而变化；

```lua
--稳定结构----------------------------------------------
local function external()
	local function internal()
    	print("value")
    end
end

local function external()
	local function internal(value)
    	print(value)
    end
end

--不稳定结构----------------------------------------------
local function external(value)
	local function internal()
    	print(value) --引用了外部局部变量
    end
end

--其实我个人认为这应该算稳定结构，但是实验结构表明该内部函数依然会被重复创建。
local function external() 
	local value = 1
    local function internal()
    	print(value)
    end
end
```

来性能上的损耗。如果将两种函数调用同样的次数，后者花费的时间为前者的 6.7 倍左右。

结论：在函数内部声明一个函数，可以提高代码易读性的同时，还可以控制该函数的作用域。但		是内部的函数最好不要引用 **外部局部变量** ，因为如果该外部函数被多次调用，这会带来一定的性能损耗。

