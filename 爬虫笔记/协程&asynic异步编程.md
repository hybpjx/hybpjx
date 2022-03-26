# 							协程&asyncio异步编程

为什么要讲？
    --异步非堵塞，asyncio
    --tornado、fastapi，django3.x asgi 、aiohttp 都是异步，提升性能

​    如何讲解
​    --协程。
​    --asyncio模块进行异步编程
​    --实战案例

##     1.协程

​        协程不是计算机提供。计算机只有进程和线程。协程是程序员人为创造
​        
​        协程 也可以称为微线程 是一种用户态内的上下文切换技术，简而言之，其实就是通过一个线程实现
​        代码之间的相互切换执行
​        例如

​      

```python
def fun1():
    print(1)
    print(2)
    ……
    print(3)

def fun2()
    print(4)
    print(5)
    ……
    print(6)

fun1()
fun2()
```

###         1.1 实现协程有这么几个方法

​            1. grenlet 早期模块 gevent（基于grenlet）

​            2. yield 关键字（python生成器）

​            3. asyncio  python3.4之后引入的一个模块 通过装饰器加上yield 也可以实现协程

​            4. async、await 关键字 py3.5 之后可以使用   【推荐】

####         1.1 greenlet 实现协程

​     

```python
from greenlet import greenlet

def fun1():
    print(1)# 第二步 打印 1
    gr2.switch() #第三步 切换到fun2 函数
    print(2) #第六步 打印2
    gr2.switch() #第七步 切换到fun2 函数 从上次执行的地方继续向后执行 而不是从重新执行

def fun2():
    print(3) #第四步 打印3
    gr1.switch() #第五步 切换到fun1 函数 从上次执行的地方继续向后执行 而不是从重新执行
    print(4) #第八步

#生成一个特殊对象

gr1=greenlet(fun1)
gr2=greenlet(fun2)

gr1.switch() #第一步  执行fun1 函数
```

####         1.2  yield 关键字



​         

```python
def func1():
	yield 1
	yield from func2()
	yield 2
def func2():
	yield 3
	yield 4
	f1=func1()
for item in f1:
	print(item)
```

####         1.3  asyncio模块

```python
import asyncio
#通过这个装饰器进行一个装饰器
@asyncio.coroutine #coroutine协程
def func1():
    print(1)
	#网路IO请求：下载一张图片
    yield from asyncio.sleep(2)# 等待两秒 会遇到IO耗时操作， 自动  化切换到tasks中的其他任务
    print(2)
@asyncio.coroutine
def func2():
    print(3)
    #网络IO请求：下载一张图片
    yield from asyncio.sleep(2)# 遇到IO耗时操作， 自动  化切换到tasks中的其他任务
    print(4)
    
#定义一个任务列表
tasks=[
asyncio.ensure_future(func1()),
asyncio.ensure_future(func2())
]
#协程只能通过这个方式去执行
loop = asyncio.get_event_loop()
#由于有两个函数 所以只能创建一个任务来执行两个函数 所以通过内置方法来执行这两个函数 随即执行
loop.run_until_complete(asyncio.wait(tasks))
#遇到io阻塞会自动切换
```

1.4 asynic +await 关键字

​          

```python
import asyncio
    
async def func1():
    print(1)
    #网路IO请求：下载一张图片
    await asyncio.sleep(2)# 等待两秒 会遇到IO耗时操作， 自动  化切换到tasks中的其他任务
    print(2)
async def func2():
    print(3)
    #网络IO请求：下载一张图片
    await asyncio.sleep(2)# 遇到IO耗时操作， 自动  化切换到tasks中的其他任务
    print(4)
#定义一个任务列表
tasks=[
asyncio.ensure_future(func1()),
asyncio.ensure_future(func2())
]
#协程只能通过这个方式去执行
loop = asyncio.get_event_loop()
#由于有两个函数 所以只能创建一个任务来执行两个函数 所以通过内置方法来执行这两个函数 随即执行
loop.run_until_complete(asyncio.wait(tasks))
```




## 2.协程的意义

​        1.在一个线程遇到io等待时间。线程不会傻傻的等，利用空闲时间再去干其他的事情、、
​        案例：下载三张图片   （网络io）

​          

```python
import asyncio,aiohttp,time

async def fetch(session,url):
    print("发送请求",url)
    async with session.get(url,verify_ssl=False) as response:
        content=await response.content.read()
        #以__切割 作为文件名
        filename=url.rsplit("__")[-1]
        with open(filename, "wb") as fp:
        	fp.write(content)

    print("下载完成",url)


#协程函数
async def main():
    async with aiohttp.ClientSession() as session:
        url_list=[
            "https://car2.autoimg.cn/cardfs/product/g28/M01/C4/A3/240x180_0_q95_c42_autohomecar__ChsEnl8_qK-AAUH8ACFaoCew9fQ739.jpg",
            "https://car3.autoimg.cn/cardfs/product/g24/M01/13/9C/240x180_0_q95_c42_autohomecar__ChwFjl8_qK6AVqqDACDoPgqb8x8502.jpg",
            "https://car3.autoimg.cn/cardfs/product/g1/M05/A6/85/240x180_0_q95_c42_autohomecar__ChsEj1-462qAVGCiAArkPN4c4Qo222.jpg"
        ]
        tasks=[asyncio.create_task(fetch(session,url))for url in url_list]

    	await asyncio.wait(tasks)

if __name__ == '__main__':
    #asyncio.run(main=main())
    loop=asyncio.get_event_loop()
    loop.run_until_complete(main())
```



## 3. 事件循环

### 3.1事件循环

​    理解成一个死循环，去检测并执行某些代码

仓库分拣，很多商品，好的分出来怎么做，坏的怎么做，可维修的怎么做

### 3.2快速上手

协程函数定义函数的时候async def 函数名。

协程对象，执行协程函数（）得到的协程对象。



```python
async def func():
    pass

result=func()

```

注意：执行协程函数创建协程对象。函数内部代码不会执行。

如果想要运行协程函数内部代码，必须要将协程对象交给时间循环来处理

```python
import asyncio
async def func():
    print("hello,world")

result=func()

#loop = asyncio.get_event_loop()
#loop.run_until_complete(result)
asyncio.run(result)
```

### 3.3 await

await+可等待对象（协程对象，future，task对象—>io等待）【i是input o是output】

简单说明：

```python
import asyncio

async def func():
    print("hello,world")
    #没有返回值 所以response的值是None
    response=await asyncio.sleep(2)
    print("结束",response)
    
asyncio.run(func())
```

案例

```python
import  asyncio
async def others():
    print("start")
    await asyncio.sleep(2)
    print("end")
    return "返回值"

async def func():
    print("执行写成函数内部代码")
    
    #当遇到io操作挂起当前协程（任务），等操作io完成后再继续往下走，当前协程挂起时，事件协程可以去执行其他的协程（任务）
    #						协程函数+括号=协程对象	
    response=await others()
    print("io请求完成，结果是：",response)

asyncio.run(func())
```

案例2

```python
import  asyncio
async def others():
    print("start")
    await asyncio.sleep(2)
    print("end")
    return "返回值"

async def func():
    print("执行写成函数内部代码")
    
    #当遇到io操作挂起当前协程（任务），等操作io完成后再继续往下走，当前协程挂起时，事件协程可以去执行其他的协程（任务）
    #						协程函数+括号=协程对象	
    response1=await others()
    print("io请求1完成，结果是：",response1)
    
	response2=await others()
	print("io请求2完成，结果是：",response2)

asyncio.run(func())
```



await就是等待对象的值得到结果后	再继续向下执行



### 3.4 Task 对象

``` 
Tasks are used to schedule coroutines *concurrently*.
 When a coroutine is wrapped into a *Task* with functions like `asyncio.create_task()` the coroutine is automatically scheduled to run soon。
```



白话：在事件循环中添加多个任务的。

Tasks用于并发调整协程，通过 asyncio.create_task(协程对象)的方法创建Task对象，这样可以让协程加入事件循环中等待被调度执行，除了使用asyncio.create_task()函数以外，还可以用低层级的loop.creaete_task()或 ensure_future函数，不建议手动实例化Task对象。

注意：asyncio.create_task()函数在python3.7之后被加入，在python3.7之前 可以改用低层级的asyncio.ensure_future()函数



示例1

```python
import asyncio


async def func():
    print(1)
    await asyncio.sleep(2)
    print(2)
    return "返回值"


async def main():
    print("main开始")

    # 创建Task对象，讲当前执行func函数任务添加到事件循环
    task1 = asyncio.create_task(func())

    # 创建Task对象，讲当前执行func函数任务添加到事件循环
    task2 = asyncio.create_task(func())

    print("main结束")

    # 当执行某协程遇到IO操作时，会自动化切换执行其他任务。
    # 此处的await是等待相对应的协程全都执行完毕并获取结果
    ret1 = await task1
    ret2 = await task2
    print(ret1, ret2)


asyncio.run(main())
```

示例2



```python
import asyncio


async def func():
    print(1)
    await asyncio.sleep(2)
    print(2)
    return "返回值"


async def main():
    print("main开始")
    # 创建协程，将协程封装到Task对象中并添加到事件循环的任务列表中，等待事件循环去执行（默认是就绪状态）。
    # 在调用
    task_list = [
    asyncio.create_task(func(),name="n1"),
    asyncio.create_task(func(),name="n2")
    ]


    print("main结束")
    # 等待列表中的任务 会有两个返回值 done是集合     最多等2秒
    # 当执行某协程遇到IO操作时，会自动化切换执行其他任务。
    # 此处的await是等待所有协程执行完毕，并将所有协程的返回值保存到done
    # 如果设置了timeout值，则意味着此处最多等待的秒，完成的协程返回值写入到done中，未完成则写到pending中。
    
    done, pending = await asyncio.wait(task_list, timeout=None)
    print(done)



asyncio.run(main())
```

示例3

```python
import asyncio


async def func():
    print(1)
    await asyncio.sleep(2)
    print(2)
    return "返回值"

# 错误：coroutine_list = [ asyncio.create_task(func()), asyncio.create_task(func()) ]  
# 此处不能直接 asyncio.create_task，因为将Task立即加入到事件循环的任务列表，
# 但此时事件循环还未创建，所以会报错。
task_list = [
	func(),
    func()
    ]

# 使用asyncio.wait将列表封装为一个协程，并调用asyncio.run实现执行两个协程
# asyncio.wait内部会对列表中的每个协程执行ensure_future，封装为Task对象。
asyncio.run(await asyncio.run(task_list))
```



### 3.5 Future 对象

future是task对象的基类

 A `Future`is a special **low-level** awaitable object that represents an **eventual result** of an asynchronous operation.

asyncio中的Future对象是一个相对更偏向底层的可对象，通常我们不会直接用到这个对象，而是直接使用Task对象来完成任务的并和状态的追踪。（ Task 是 Futrue的子类 ）

Future为我们提供了异步编程中的 最终结果 的处理（Task类也具备状态处理的功能）。

示例1

```python
import asyncio
async def main():
    # 获取当前事件循环
    loop = asyncio.get_running_loop()

    # # 创建一个任务（Future对象），这个任务什么都不干。
    fut = loop.create_future()

    # 等待任务最终结果（Future对象），没有结果则会一直等下去。
    await fut

asyncio.run(main())
```

示例2

```python
import asyncio


async def set_after(fut):
    await asyncio.sleep(2)
    fut.set_result("666")


async def main():
    # 获取当前事件循环
    loop = asyncio.get_running_loop()

    # 创建一个任务（Future对象），没绑定任何行为，则这个任务永远不知道什么时候结束。
    fut = loop.create_future()

    # 创建一个任务（Task对象），绑定了set_after函数，函数内部在2s之后，会给fut赋值。
    # 即手动设置future任务的最终结果，那么fut就可以结束了。
    await loop.create_task(set_after(fut))

    # 等待 Future对象获取 最终结果，否则一直等下去
    data = await fut
    print(data)

asyncio.run(main())
```

Future对象本身函数进行绑定，所以想要让事件循环获取Future的结果，则需要手动设置。而Task对象继承了Future对象，其实就对Future进行扩展，他可以实现在对应绑定的函数执行完成之后，自动执行`set_result`，从而实现自动结束。

虽然，平时使用的是Task对象，但对于结果的处理本质是基于Future对象来实现的。

扩展：支持 `await 对象`语 法的对象课成为可等待对象，所以 `协程对象`、`Task对象`、`Future对象` 都可以被成为可等待对象。



### 3.6 concurrent.futures.Future对象

在Python的`concurrent.futures`模块中也有一个Future对象，这个对象是基于线程池和进程池实现异步操作时使用的对象。

```python
import time
from concurrent.futures import Future
from concurrent.futures.thread import ThreadPoolExecutor
from concurrent.futures.process import ProcessPoolExecutor


def func(value):
    time.sleep(1)
    print(value)

#创建一个线程池
pool = ThreadPoolExecutor(max_workers=5)

#创建一个进程池
#pool = ProcessPoolExecutor(max_workers=5)


for i in range(10):
    fut = pool.submit(func, i)
    print(fut)
```

两个Future对象是不同的，他们是为不同的应用场景而设计，例如：`concurrent.futures.Future`不支持await语法 等。

官方提示两对象之间不同：

-  unlike asyncio Futures, `concurrent.futures.Future` instances cannot be awaited.
   
- `asyncio.Future.result()` and `asyncio.Future.exception()` do not accept the *timeout* argument.
  
- `asyncio.Future.result()` and `asyncio.Future.exception()` raise an `InvalidStateError` exception when the Future is not *done*.
- Callbacks registered with `asyncio.Future.add_done_callback()` are not called immediately. They are scheduled with `loop.call_soon()` instead.
- asyncio Future is not compatible with the `concurrent.futures.wait()` and `concurrent.futures.as_completed()` functions.

在Python提供了一个将`futures.Future` 对象包装成`asyncio.Future`对象的函数 `asynic.wrap_future`。

接下里你肯定问：为什么python会提供这种功能？

其实，一般在程序开发中我们要么统一使用 asycio 的协程实现异步操作、要么都使用进程池和线程池实现异步操作。但如果 `协程的异步`和 `进程池/线程池的异步` 混搭时，那么就会用到此功能了。

```python
import time
import asyncio
import concurrent.futures

def func1():
    # 某个耗时操作
    time.sleep(2)
    return "SB"

async def main():
    loop = asyncio.get_running_loop()

    # 1. Run in the default loop's executor ( 默认ThreadPoolExecutor )
    # 第一步：内部会先调用 ThreadPoolExecutor 的 submit 方法去线程池中申请一个线程去执行func1函数，并返回一个concurrent.futures.Future对象
    # 第二步：调用asyncio.wrap_future将concurrent.futures.Future对象包装为asycio.Future对象。
    # 因为concurrent.futures.Future对象不支持await语法，所以需要包装为 asycio.Future对象 才能使用。
    fut = loop.run_in_executor(None, func1)
    result = await fut
    print('default thread pool', result)

    # 2. Run in a custom thread pool:
    # with concurrent.futures.ThreadPoolExecutor() as pool:
    #     result = await loop.run_in_executor(
    #         pool, func1)
    #     print('custom thread pool', result)

    # 3. Run in a custom process pool:
    # with concurrent.futures.ProcessPoolExecutor() as pool:
    #     result = await loop.run_in_executor(
    #         pool, func1)
    #     print('custom process pool', result)

asyncio.run(main())
```

应用场景：当项目以协程式的异步编程开发时，如果要使用一个第三方模块，而第三方模块不支持协程方式异步编程时，就需要用到这个功能，例如：

```python
import asyncio
import requests


async def download_image(url):
    # 发送网络请求，下载图片（遇到网络下载图片的IO请求，自动化切换到其他任务）
    print("开始下载:", url)

    loop = asyncio.get_event_loop()
    # requests模块默认不支持异步操作，所以就使用线程池来配合实现了。
    future = loop.run_in_executor(None, requests.get, url)

    response = await future
    print('下载完成')
    # 图片保存到本地文件
    file_name = url.rsplit('_')[-1]
    with open(file_name, mode='wb') as file_object:
        file_object.write(response.content)


if __name__ == '__main__':
    url_list = [
        'https://www3.autoimg.cn/newsdfs/g26/M02/35/A9/120x90_0_autohomecar__ChsEe12AXQ6AOOH_AAFocMs8nzU621.jpg',
        'https://www2.autoimg.cn/newsdfs/g30/M01/3C/E2/120x90_0_autohomecar__ChcCSV2BBICAUntfAADjJFd6800429.jpg',
        'https://www3.autoimg.cn/newsdfs/g26/M0B/3C/65/120x90_0_autohomecar__ChcCP12BFCmAIO83AAGq7vK0sGY193.jpg'
    ]

    tasks = [download_image(url) for url in url_list]

    loop = asyncio.get_event_loop()
    loop.run_until_complete( asyncio.wait(tasks) )
```



### 3.7 异步迭代器

**什么是异步迭代器**

实现了 `__aiter__()` 和 `__anext__()` 方法的对象。`__anext__` 必须返回一个 [awaitable](https://link.zhihu.com/?target=https%3A//docs.python.org/zh-cn/3.8/glossary.html%23term-awaitable) 对象。`async for` 会处理异步迭代器的 `__anext__()` 方法所返回的可等待对象，直到其引发一个 `StopAsyncIteration` 异常。由 **[PEP 492](https://link.zhihu.com/?target=https%3A//www.python.org/dev/peps/pep-0492)** 引入。

**什么是异步可迭代对象？**

可在 `async for` 语句中被使用的对象。必须通过它的 `__aiter__()` 方法返回一个 [asynchronous iterator](https://link.zhihu.com/?target=https%3A//docs.python.org/zh-cn/3.8/glossary.html%23term-asynchronous-iterator)。由 **[PEP 492](https://link.zhihu.com/?target=https%3A//www.python.org/dev/peps/pep-0492)** 引入。

```python
import asyncio


class Reader(object):
    """ 自定义异步迭代器（同时也是异步可迭代对象） """

    def __init__(self):
        self.count = 0

    async def readline(self):
        # await asyncio.sleep(1)
        self.count += 1
        if self.count == 100:
            return None
        return self.count

    def __aiter__(self):
        return self

    async def __anext__(self):
        val = await self.readline()
        if val == None:
            raise StopAsyncIteration
        return val


async def func():
    # 创建异步可迭代对象
    async_iter = Reader()
    # async for 必须要放在async def函数内，否则语法错误。
    async for item in async_iter:
        print(item)

asyncio.run(func())
```

异步迭代器其实没什么太大的作用，只是支持了async for语法而已。

### 3.8 异步上下文管理器

此种对象通过定义 `__aenter__()` 和 `__aexit__()` 方法来对 `async with` 语句中的环境进行控制。由 **[PEP 492](https://link.zhihu.com/?target=https%3A//www.python.org/dev/peps/pep-0492)** 引入。

```python
import asyncio


class AsyncContextManager:
    def __init__(self):
        self.conn = conn

    async def do_something(self):
        # 异步操作数据库
        return 666

    async def __aenter__(self):
        # 异步链接数据库
        self.conn = await asyncio.sleep(1)
        return self

    async def __aexit__(self, exc_type, exc, tb):
        # 异步关闭数据库链接
        await asyncio.sleep(1)


async def func():
    async with AsyncContextManager() as f:
        result = await f.do_something()
        print(result)


asyncio.run(func())
```

这个异步的上下文管理器还是比较有用的，平时在开发过程中 打开、处理、关闭 操作时，就可以用这种方式来处理。





## 4. uvloop

Python标准库中提供了`asyncio`模块，用于支持基于协程的异步编程。

uvloop是 asyncio 中的事件循环的替代方案，替换后可以使得asyncio性能提高。事实上，uvloop要比nodejs、gevent等其他python异步框架至少要快2倍，性能可以比肩Go语言。

安装uvloop

```text
pip3 install uvloop
```

在项目中想要使用uvloop替换asyncio的事件循环也非常简单，只要在代码中这么做就行。

```python
import asyncio
import uvloop
asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())

# 编写asyncio的代码，与之前写的代码一致。

# 内部的事件循环自动化会变为uvloop
asyncio.run(...)
```

注意：知名的asgi `uvicorn` 内部就是使用的uvloop的事件循环。

## 5.实战案例

为了更好理解，上述所有示例的IO情况都是以 `asyncio.sleep` 为例，而真实的项目开发中会用到很多IO的情况。



当通过python去操作redis时，链接、设置值、获取值 这些都涉及网络IO请求，使用asycio异步的方式可以在IO等待时去做一些其他任务，从而提升性能。

安装Python异步操作redis模块

```text
pip3 install aioredis
```

示例1：异步操作redis。

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import asyncio
import aioredis


async def execute(address, password):
    print("开始执行", address)
    # 网络IO操作：创建redis连接
    redis = await aioredis.create_redis(address, password=password)

    # 网络IO操作：在redis中设置哈希值car，内部在设三个键值对，即： redis = { car:{key1:1,key2:2,key3:3}}
    await redis.hmset_dict('car', key1=1, key2=2, key3=3)

    # 网络IO操作：去redis中获取值
    result = await redis.hgetall('car', encoding='utf-8')
    print(result)

    redis.close()
    # 网络IO操作：关闭redis连接
    await redis.wait_closed()

    print("结束", address)


asyncio.run(execute('redis://47.93.4.198:6379', "root!2345"))
```

示例2：连接多个redis做操作（遇到IO会切换其他任务，提供了性能）。

```python
import asyncio
import aioredis


async def execute(address, password):
    print("开始执行", address)

    # 网络IO操作：先去连接 47.93.4.197:6379，遇到IO则自动切换任务，去连接47.93.4.198:6379
    redis = await aioredis.create_redis_pool(address, password=password)

    # 网络IO操作：遇到IO会自动切换任务
    await redis.hmset_dict('car', key1=1, key2=2, key3=3)

    # 网络IO操作：遇到IO会自动切换任务
    result = await redis.hgetall('car', encoding='utf-8')
    print(result)

    redis.close()
    # 网络IO操作：遇到IO会自动切换任务
    await redis.wait_closed()

    print("结束", address)


task_list = [
    execute('redis://47.93.4.197:6379', "root!2345"),
    execute('redis://47.93.4.198:6379', "root!2345")
]

asyncio.run(asyncio.wait(task_list))
```

### 5.2 异步MySQL

当通过python去操作MySQL时，连接、执行SQL、关闭都涉及网络IO请求，使用asycio异步的方式可以在IO等待时去做一些其他任务，从而提升性能。

安装Python异步操作redis模块

```text
pip3 install aiomysql
```

示例1：

```python
import asyncio
import aiomysql


async def execute():
    # 网络IO操作：连接MySQL
    conn = await aiomysql.connect(host='127.0.0.1', port=3306, user='root', password='123', db='mysql', )

    # 网络IO操作：创建CURSOR
    cur = await conn.cursor()

    # 网络IO操作：执行SQL
    await cur.execute("SELECT Host,User FROM user")

    # 网络IO操作：获取SQL结果
    result = await cur.fetchall()
    print(result)

    # 网络IO操作：关闭链接
    await cur.close()
    conn.close()


asyncio.run(execute())
```

示例2：

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import asyncio
import aiomysql


async def execute(host, password):
    print("开始", host)
    # 网络IO操作：先去连接 47.93.40.197，遇到IO则自动切换任务，去连接47.93.40.198:6379
    conn = await aiomysql.connect(host=host, port=3306, user='root', password=password, db='mysql')

    # 网络IO操作：遇到IO会自动切换任务
    cur = await conn.cursor()

    # 网络IO操作：遇到IO会自动切换任务
    await cur.execute("SELECT Host,User FROM user")

    # 网络IO操作：遇到IO会自动切换任务
    result = await cur.fetchall()
    print(result)

    # 网络IO操作：遇到IO会自动切换任务
    await cur.close()
    conn.close()
    print("结束", host)


task_list = [
    execute('47.93.40.197', "root!2345"),
    execute('47.93.40.197', "root!2345")
]

asyncio.run(asyncio.wait(task_list))
```

### 5.3 FastAPI框架

FastAPI是一款用于构建API的高性能web框架，框架基于Python3.6+的 `type hints`搭建。

接下里的异步示例以`FastAPI`和`uvicorn`来讲解（uvicorn是一个支持异步的asgi）。

安装FastAPI web 框架，

```text
pip3 install fastapi
```

安装uvicorn，本质上为web提供socket server的支持的asgi（一般支持异步称asgi、不支持异步称wsgi）

```text
pip3 install uvicorn
```

示例：

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import asyncio

import uvicorn
import aioredis
from aioredis import Redis
from fastapi import FastAPI

app = FastAPI()

REDIS_POOL = aioredis.ConnectionsPool('redis://47.193.14.198:6379', password="root123", minsize=1, maxsize=10)


@app.get("/")
def index():
    """ 普通操作接口 """
    return {"message": "Hello World"}


@app.get("/red")
async def red():
    """ 异步操作接口 """

    print("请求来了")

    await asyncio.sleep(3)
    # 连接池获取一个连接
    conn = await REDIS_POOL.acquire()
    redis = Redis(conn)

    # 设置值
    await redis.hmset_dict('car', key1=1, key2=2, key3=3)

    # 读取值
    result = await redis.hgetall('car', encoding='utf-8')
    print(result)

    # 连接归还连接池
    REDIS_POOL.release(conn)

    return result


if __name__ == '__main__':
    uvicorn.run("luffy:app", host="127.0.0.1", port=5000, log_level="info")
```

在有多个用户并发请求的情况下，异步方式来编写的接口可以在IO等待过程中去处理其他的请求，提供性能。

例如：同时有两个用户并发来向接口 `http://127.0.0.1:5000/red` 发送请求，服务端只有一个线程，同一时刻只有一个请求被处理。   异步处理可以提供并发是因为：当视图函数在处理第一个请求时，第二个请求此时是等待被处理的状态，当第一个请求遇到IO等待时，会自动切换去接收并处理第二个请求，当遇到IO时自动化切换至其他请求，一旦有请求IO执行完毕，则会再次回到指定请求向下继续执行其功能代码。

### 5.4 爬虫

在编写爬虫应用时，需要通过网络IO去请求目标数据，这种情况适合使用异步编程来提升性能，接下来我们使用支持异步编程的aiohttp模块来实现。

安装aiohttp模块

```text
pip3 install aiohttp
```

示例：

```python
import aiohttp
import asyncio


async def fetch(session, url):
    print("发送请求：", url)
    async with session.get(url, verify_ssl=False) as response:
        text = await response.text()
        print("得到结果：", url, len(text))


async def main():
    async with aiohttp.ClientSession() as session:
        url_list = [
            'https://python.org',
            'https://www.baidu.com',
            'https://www.pythonav.com'
        ]
        tasks = [asyncio.create_task(fetch(session, url)) for url in url_list]

        await asyncio.wait(tasks)


if __name__ == '__main__':
    asyncio.run(main())
```

## 总结

为了提升性能越来越多的框架都在向异步编程靠拢，例如：sanic、tornado、django3.0、django channels组件 等，用更少资源可以做处理更多的事，何乐而不为呢。
