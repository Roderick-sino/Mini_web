#Python拓展知识

## 进程

### 1、多进程的使用

#### 1.1 导入进程包

```python
#导入进程包
import multiprocessing
```

#### 1.2 Process进程类的说明

Process（[group[,target[,name[,args[,kwargs]]]]])

- group:指定进程组，目前只能使用None
- target:执行的目标任务名
- name:进程名字
- args:以元组的方式给执行任务传参
- kwargs:以字典的方式给执行任务传参



Process创建的实力对象的常用方法：

- start():启动子进程实力（创建子进程）
- join():等待子进程执行结束
- terminate():不管任务是否完成，立即终止子程序

Process创建的实力对象的常用属性：

name：当前进程的别名，默认为Process-N，N从1开始递增的整数

| 属性名或方法名  | 功能                                                         |
| --------------- | ------------------------------------------------------------ |
| run()           | 第 2 种创建进程的方式需要用到，继承类中需要对方法进行重写，该方法中包含的是新进程要执行的代码。 |
| start()         | 和启动子线程一样，新创建的进程也需要手动启动，该方法的功能就是启动新创建的线程。 |
| join([timeout]) | 和 thread 类 join() 方法的用法类似，其功能是在多进程执行过程，其他进程必须等到调用 join() 方法的进程执行完毕（或者执行规定的 timeout 时间）后，才能继续执行； |
| is_alive()      | 判断当前进程是否还活着。                                     |
| terminate()     | 中断该进程。                                                 |
| name属性        | 可以为该进程重命名，也可以获得该进程的名称。                 |
| daemon          | 和守护线程类似，通过设置该属性为 True，可将新建进程设置为“守护进程”。 |
| pid             | 返回进程的 ID 号。大多数操作系统都会为每个进程配备唯一的 ID 号。 |

####1.3 代码实例
```python
# 1.导入进程
import multiprocessing

# 2.创建子进程
import time


# 跳舞任务(子进程)
def dance():
    for i in range(3):
        print('跳舞中...')
        time.sleep(0.2)


# 唱歌任务(主进程)
def sing():
    for i in range(3):
        print('唱歌中...')
        time.sleep(0.2)


# 1.group进程中目前只能使用None，一级不需要设置
# 2。target:进程执行的目标任务
# 3. name:进程名，如果不设置，默认是Process-1，....
dance_process = multiprocessing.Process(target=dance)

# 3.启动进程执行对应的任务
dance_process.start()

# 主进程执行唱歌任务
sing()
"""
唱歌中...
跳舞中...
唱歌中...
跳舞中...
唱歌中...
跳舞中...
"""
#主进程先执行。如果创建了2个子进程，子进程的执行顺序是无序的
```

### 2、获取进程编号

#### 2.1 获取进程编号的目的

获取进程编号的目的是验证主进程和子进程的关系，可以得知子进程是由哪个主进程创建出来的

- 获取当前进程编号
- 获取当前父进程编号

#### 2.2 获取当前的进程编号

os.getpid()表示获取当前进程编号

代码

```python
# 1.导入进程
import multiprocessing

# 2.创建子进程
import os

import time


# 跳舞任务
def dance():
    # 获取当前进程的编号
    dance_process_id = os.getpid()
    print("dance_process_id:", dance_process_id, multiprocessing.current_process())
    # 获取父进程编号
    dance_process_parent_id = os.getppid()
    print("dance_process的父进程编号是：", dance_process_parent_id)
    for i in range(3):
        print('跳舞中...')
        time.sleep(0.2)


# 唱歌任务
def sing():
    sing_process_id = os.getpid()
    print("sing_process_id:", sing_process_id, multiprocessing.current_process())
    sing_process_parent_id = os.getppid()
    print("sing_process的父进程编号是：", sing_process_parent_id)
    for i in range(3):
        print('唱歌中...')
        time.sleep(0.2)
        # 扩展：根据进程编号杀死指定进程,9-强制杀死
        os.kill(sing_process_id, 9)


# 1.group进程中目前只能使用None，一级不需要设置
# 2。target:进程执行的目标任务
# 3. name:进程名，如果不设置，默认是Process-1，....
if __name__ == '__main__':
    # 获取当前进程编号（主进程）
    main_process_id = os.getpid()
    # 获取当前进程对象，查看当前代码是由哪个进程执行:multiprocessing.current_process()
    # main_process_id: 7192, <_MainProcess(MainProcess, started)>
    print("main_process_id:", main_process_id, multiprocessing.current_process())
    dance_process = multiprocessing.Process(target=dance)
    sing_process = multiprocessing.Process(target=sing)

    print("dance_process:", dance_process)
    print("sing_process:", sing_process)
    # 3.启动进程执行对应的任务
    dance_process.start()
    sing_process.start()

```

输出

```python
main_process_id: 8936 <_MainProcess(MainProcess, started)>
dance_process: <Process(Process-1, initial)>
sing_process: <Process(Process-2, initial)>
dance_process_id: 19232 <Process(Process-1, started)>
dance_process的父进程编号是： 8936
跳舞中...
sing_process_id: 16552 <Process(Process-2, started)>
sing_process的父进程编号是： 8936
唱歌中...
跳舞中...
跳舞中...
```

### 3、进程执行带有参数的任务

- args元组
- kwargs字典

```python
import multiprocessing


# 显示信息的任务
def show_info(name, age):
    print(name, age)


if __name__ == '__main__':
    # # 创建子进程
    # sub_process = multiprocessing.Process(target=show_info, args=("李四", 20))
    # # 启动程序
    # sub_process.start()
    sub_process = multiprocessing.Process(target=show_info, kwargs={"age":20,"name":'王五'})
    # 启动程序
    sub_process.start()
```

## 线程



### 1、多线程的使用

线程之间执行是无序的，共享全局变量

#### 1.1导入线程模块

```python
#导入线程模块
import threading
```

####1.2 线程类的参数说明

Thread（[group[,target[,name[,args[,kwargs]]]]])

- group:指定线程组，目前只能使用None
- target:执行的目标任务名
- name:线程名字
- args:以元组的方式给执行任务传参
- kwargs:以字典的方式给执行任务传参

#### 1.3 常用的方法

- threading.currentThread(): 返回当前的线程变量。
- threading.enumerate(): 返回一个包含正在运行的线程的list。正在运行指线程启动后、结束前，不包括启动前和终止后的线程。
- threading.activeCount(): 返回正在运行的线程数量，与len(threading.enumerate())有相同的结果。

除了使用方法外，线程模块同样提供了Thread类来处理线程，Thread类提供了以下方法:

- **run():** 用以表示线程活动的方法。

- start():

  启动线程活动。

  

- **join([time]):** 等待至线程中止。这阻塞调用线程直至线程的join() 方法被调用中止-正常退出或者抛出未处理的异常-或者是可选的超时发生。

- **isAlive():** 返回线程是否活动的。

- **getName():** 返回线程名。

- **setName():** 设置线程名

#### 1.4多线程的使用

代码

```python
# 导入线程模块
import threading
import time


def sing():
    # 获取当前线程
    current_thread = threading.current_thread()
    print("sing的current_thread:", current_thread)
    for i in range(3):
        print('唱歌中。。。')
        time.sleep(0.3)


def dance():
    current_thread = threading.current_thread()
    print("dance的current_thread:", current_thread)
    for i in range(3):
        print('跳舞中。。。')
        time.sleep(0.3)


if __name__ == '__main__':
    # 获取当前线程
    main_thread = threading.current_thread()
    print("main_thread:", main_thread)
    # 创建子线程
    sing_thread = threading.Thread(target=sing, name="sing_thread")
    dance_thread = threading.Thread(target=dance, name="dance_thread")
    # 启动子线程执行对应的任务
    sing_thread.start()
    dance_thread.start()

```

结果

```python
main_thread: <_MainThread(MainThread, started 7188)>
sing的current_thread: <Thread(sing_thread, started 9444)>
唱歌中。。。
dance的current_thread: <Thread(dance_thread, started 10068)>
跳舞中。。。
唱歌中。。。
跳舞中。。。
跳舞中。。。
唱歌中。。。

```

默认3个进程

### 2、线程执行带有参数的任务

```python
import threading


def show_info(name, age):
    print("name: %s,age: %d" % (name, age))


if __name__ == '__main__':
    # 以元组方式传参
    # sub_thread = threading.Thread(target=show_info, args=('李四', 20))
    # sub_thread.start()
    # 已字典的方式传参
    sub_thread = threading.Thread(target=show_info, kwargs={'name': '李四', 'age': 20})
    sub_thread.start()

```

注：

1. 线程之间执行是无序的
2. 主线程会等待所有的子线程执行结束再结束
3. 线程之间共享全局变量
4. 线程之间共享全局变量数据出现错误

### 3、线程同步

将多任务改成单任务保证线程的安全

- 线程等待（join）

  ```python
  import threading
  
  # 全局变量
  g_num = 0
  
  
  def task1():
      for i in range(100000):
          global g_num  # 表示要申明修改全局变量的内地址
          g_num += 1
      print("task1", g_num)
  
  
  def task2():
      for i in range(100000):
          global g_num  # 表示要申明修改全局变量的内地址
          g_num += 1
      print("task1", g_num)
  
  
  if __name__ == '__main__':
      # 创建2个子进程
      first_thread = threading.Thread(target=task1)
      second_thread = threading.Thread(target=task2)
  
      first_thread.start()
      first_thread.join()
      second_thread.start()
  
  ```

  

- 互斥锁（lock）

####3.1 互斥锁的使用

互斥锁的使用步骤

```python
#创建锁
mutex=threading.Lock()
#上锁
mutex.acquire()
#释放锁
mutex.release()
```

```python
import threading

# 全局变量
g_num = 0
# 创建互斥锁
mutex = threading.Lock()


def task1():
    #上锁
    mutex.acquire()
    for i in range(100000):
        global g_num  # 表示要申明修改全局变量的内地址
        g_num += 1
    print("task1", g_num)
    #解锁
    mutex.release()


def task2():
    # 上锁
    mutex.acquire()
    for i in range(100000):
        global g_num  # 表示要申明修改全局变量的内地址
        g_num += 1
    print("task1", g_num)
    #解锁
    mutex.release()

if __name__ == '__main__':
    # 创建2个子进程
    first_thread = threading.Thread(target=task1)
    second_thread = threading.Thread(target=task2)

    first_thread.start()

    second_thread.start()

```



### 4、死锁

##网络编程

IP、端口和端口号、TCP、socket(套接字)

网络通信的流程

1. 通过ip地址找到网络中的设备

2. 通过端口号找到对应的进程的端口

3. 传输数据还需要使用传输协议TCP，保证数据的可靠性

4. socket完成进程之间的网络数据传输

   

步骤

1. 创建服务端端套接字对象
2. 绑定端口号
3. 设置监听
4. 等待客户端的连接请求
5. 接收数据
6. 发送数据
7. 关闭套接字

### 1、TCP客户端程序开发

#### 1.1 开发tcp客户端程序开发步骤回顾



#### 1.2 socket 类的介绍

导入socket

```python
import socket
```

创建客户端socket 对象

```
socket.socket([family[, type[, proto]]])
```

参数说明：

- family: 套接字家族可以是 AF_UNIX 或者 AF_INET
- type: 套接字类型可以根据是面向连接的还是非连接分为`SOCK_STREAM`或`SOCK_DGRAM`
- protocol: 一般不填默认为0.

Socket的方法

| 函数                                 | 描述                                                         |
| :----------------------------------- | ------------------------------------------------------------ |
| 服务器端套接字                       |                                                              |
| ==s.bind()==                         | 绑定地址（host,port）到套接字， 在AF_INET下,以元组（host,port）的形式表示地址。 |
| ==s.listen()==                       | 开始TCP监听。backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数量。该值至少为1，大部分应用程序设为5就可以了。 |
| ==s.accept()==                       | 被动接受TCP客户端连接,(阻塞式)等待连接的到来                 |
| 客户端套接字                         |                                                              |
| ==s.connect()==                      | 主动初始化TCP服务器连接，。一般address的格式为元组（hostname,port），如果连接出错，返回socket.error错误。 |
| s.connect_ex()                       | connect()函数的扩展版本,出错时返回出错码,而不是抛出异常      |
| 公共用途的套接字函数                 |                                                              |
| ==s.recv()==                         | 接收TCP数据，数据以字符串形式返回，bufsize指定要接收的最大数据量。flag提供有关消息的其他信息，通常可以忽略。 |
| ==s.send(da'ta）== data是2进制       | 发送TCP数据，将string中的数据发送到连接的套接字。返回值是要发送的字节数量，该数量可能小于string的字节大小。 |
| s.sendall()                          | 完整发送TCP数据，完整发送TCP数据。将string中的数据发送到连接的套接字，但在返回之前会尝试发送所有数据。成功返回None，失败则抛出异常。 |
| s.recvfrom()                         | 接收UDP数据，与recv()类似，但返回值是（data,address）。其中data是包含接收数据的字符串，address是发送数据的套接字地址。 |
| s.sendto()                           | 发送UDP数据，将数据发送到套接字，address是形式为（ipaddr，port）的元组，指定远程地址。返回值是发送的字节数。 |
| ==s.close()==                        | 关闭套接字                                                   |
| s.getpeername()                      | 返回连接套接字的远程地址。返回值通常是元组（ipaddr,port）。  |
| s.getsockname()                      | 返回套接字自己的地址。通常是一个元组(ipaddr,port)            |
| s.setsockopt(level,optname,value)    | 设置给定套接字选项的值。                                     |
| s.getsockopt(level,optname[.buflen]) | 返回套接字选项的值。                                         |
| s.settimeout(timeout)                | 设置套接字操作的超时期，timeout是一个浮点数，单位是秒。值为None表示没有超时期。一般，超时期应该在刚创建套接字时设置，因为它们可能用于连接的操作（如connect()） |
| s.gettimeout()                       | 返回当前超时期的值，单位是秒，如果没有设置超时期，则返回None。 |
| s.fileno()                           | 返回套接字的文件描述符。                                     |
| s.setblocking(flag)                  | 如果 flag 为 False，则将套接字设为非阻塞模式，否则将套接字设为阻塞模式（默认值）。非阻塞模式下，如果调用 recv() 没有发现任何数据，或 send() 调用无法立即发送数据，那么将引起 socket.error 异常。 |
| s.makefile()                         | 创建一个与该套接字相关连的文件                               |

tcp客户端步骤

1. 创建客户端socket对象
2. 与服务端socket建立连接
3. 发送数据
4. 接受数据
5. 关闭客户端套接字

```python
import socket

if __name__ == '__main__':
    # 1. 创建tcp客户端套接字
    # AF_INET：ipV4的地址类型
    # SOCK_STREAM：tcp的传输协议
    tcp_client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 2. 和服务端套接字建立连接
    tcp_client_socket.connect(（"192.168.1.5", 9090）)
    # 3. 发送数据到服务端
    # 数据为2进制
    send_content = "hello"
    send_data = send_content.encode("gbk")
    tcp_client_socket.send(send_data)
    # 4. 接受数据
    # 1024:表示每次接受的最大字节数
    recv_data = tcp_client_socket.recv(1024)
    # 对2进制数据进行解码
    recv_conten = recv_data.decode("gbk")
    print("接受服务端的数据为：",recv_conten)
    # 5. 关闭套接字
    tcp_client_socket.close()

```

tcp服务端步骤

1. 创建服务端套接字
2. 绑定套接字bind
3. 设置监听listen（backlog）：backlog参数表示最大等待建立连接个输
4. 等待接受客户端的连接请求accept
5. 接收数据recv
6. 发送数据send
7. 关闭套接字close

```python
import socket

if __name__ == '__main__':
    # 1. 创建tcp客户端套接字
    # AF_INET:ipv4
    # SOCK_STREAM:端口号
    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 2. 绑定端口号
    # 第一个参数表示ip地址一般不用指定，表示本机的任何一个ip
    # 第二个参数表示端口号
    tcp_server_socket.bind(("", 9090))
    # 3. 设置监听
    # 128 :表示最大等待建立连接的个数
    tcp_server_socket.listen(128)
    # 4. 等待接受客户端的连接请求
    # *当客户端和服务端建立连接成功都会返回一个新的套接字
    # tcp_server_socket 只负责等待客户端的连接请求，收发消息不使用该套接字
    new_client, ip_port = tcp_server_socket.accept()
    # 代码执行到此，说明客户端和服务端建立连接成功
    # 返回的是元组tuple

    # 5. 接受客户端的数据
    # 收发消息都使用新 套接字
    recv_data = new_client.recv(1024)
    # 对二进制数据进行解码
    recv_content = recv_data.decode("gbk")
    print("接受客户端的数据为：", recv_content)
    # 6. 发送数据到客户端
    send_content = "问题正在处理啊中"
    send_data = send_content.encode("gbk")
    new_client.send(send_data)
    #关闭 服务于客户端的套接字，表示和客户端中止通信
    new_client.close()
    # 7. 关闭服务端套接字，表示服务端以后不再等待客户端的连接请求
    tcp_server_socket.close()

```

当客户端和服务端建立连接之后，服务端程序退出后端口号不会立即释放，需要等待1-2分钟。

解决方法：

1. 更换服务端端口号

2. ==推荐== 设置端口号复用，让服务端程序推出后端口号立即释放

   代码如下

   ```python
   # 参数1：表示当前套接字
   # 参数2：设置端口号复用选项
   # 参数3：设置端口号，复用选项对应的值
   tcp_server_socket.setsockopt(socker_SOCKET,socket.SO_REUSEADDR,True)
   ```

   

###2、tcp网络应用程序的注意点

1. 当TCP客户端程序想要和TCP幅度段程序进行通信是必须要==建立连接==
2. TCP客户端程序一般不需要绑定端口号，因为是客户端主动发起连接
3. tcp服务端程序必须绑定端口号，否则可河段找不到TCP服务端程序
4. listen后的套接字是被动套接字，只负责接受新的客户端的连接请求，不能收发消息
5. 当TCP客户端程序和TCP服务端程序连接成功后，TCP服务端程序会产生一个新的套接字，收发客户端消息使用该套接字
6. 关闭accept返回套接字意味着和这个客户端已经通信完毕
7. 关闭listen后的套机子意味着服务端的套接字关闭了，导致新的客户端不能连接服务器，但已经连接成功的还能正常通信
8. 当客户端的套接字调用close，服务端的recv会解阻塞没返回数据长度为0，服务端可以通过返回数据的长度来判断客户端是否下线，反之服务端关闭套接字，客户端也会解阻塞，返回的数据长度也是0

### 3、 案例多任务版的TCP服务端程序开发

```python
import socket
import threading


# 处理接受客户端请求的操作
def handle_client_request(ip_port, new_client):
    print("客户端的ip和端口号：", ip_port)
    # 5. 接受客户端的数据
    # 收发消息都使用新 套接字
    # 循环接受客户端的消息
    while True:
        recv_data = new_client.recv(1024)
        if recv_data:
            # 对二进制数据进行解码
            recv_content = recv_data.decode("gbk")
            print("接受客户端的数据为：", recv_content, ip_port)
            # 6. 发送数据到客户端
            send_content = "问题正在处理啊中"
            send_data = send_content.encode("gbk")
            new_client.send(send_data)
        else:
            # 客户端关闭连接
            print("客户端下线了：", ip_port)
            break
    # 关闭 服务于客户端的套接字，表示和客户端中止通信
    new_client.close()


if __name__ == '__main__':
    # 1. 创建tcp客户端套接字
    # AF_INET:ipv4
    # SOCK_STREAM:端口号
    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 2. 绑定端口号
    # 第一个参数表示ip地址一般不用指定，表示本机的任何一个ip
    # 第二个参数表示端口号
    tcp_server_socket.bind(("", 9090))
    # 3. 设置监听
    # 128 :表示最大等待建立连接的个数
    tcp_server_socket.listen(128)
    # 4. 等待接受客户端的连接请求
    # *当客户端和服务端建立连接成功都会返回一个新的套接字
    # tcp_server_socket 只负责等待客户端的连接请求，收发消息不使用该套接字
    # 循环等待接受客户端的连接请求
    while True:
        new_client, ip_port = tcp_server_socket.accept()
        # 代码执行到此，说明客户端和服务端建立连接成功
        # 返回的是元组tuple
        # 代码执行到此，说明客户端和服务端建立陈工
        # 当客户端和服务端建立连接成功，创建子线程，让子线程专门负责接受客户端的消息
        sub_thread = threading.Thread(target=handle_client_request, args=(ip_port, new_client))
        # 设置守护主线程，主线程退出子线程直接销毁
        sub_thread.setDaemon(True)
        # 启动子线程执行对应的任务
        sub_thread.start()

    # 7. 关闭服务端套接字，表示服务端以后不再等待客户端的连接请求
    # tcp_server_socket.close()#因为服务点的程序需要一直进行，所以关闭服务端可以省略不写

```

###4、send 和recv

不管是recv还是send 都不是直接接收到对方的数据和发送数据对方，发送数据会写入发送缓冲区，接受数据是从接收缓冲区来读取，发送数据和接收数据最终是由操作系统控制网卡来完成的

###5、搭建python自带金泰web服务器

####5.1 如何搭建python自带的静态Web服务器

```python
python -m http.server [端口号]
```

-m:表示运行包内的模块

端口号默认8000

## sql与python

### 1.sql



安装pymysql第三方包：

```python
pip install pymysql
```

查询数据库

```python
# 1导入包
import pymysql

# 2创建连接对象connect（）
# connect=Connection=Connect
conn = pymysql.connect(host="localhost",
                       port=3306,
                       user="root",
                       password="Password",
                       database="python41",
                       charset="utf8")

# 3获取游标，执行sql语句
cursor = conn.cursor()
sql = "select * from students;"
# 4执行sql语句
cursor.execute(sql)
# #获取一行查询结果
# row=cursor.fetchone()
# print(row)
# 获取全部查询结果
result = cursor.fetchall()
for row in result:
    print(row)

# 5关闭游标
cursor.close()

# 6关闭连接
conn.close()

```

增删改

```python
# 1导入包
import pymysql

# 2创建连接对象connect（）
# connect=Connection=Connect
conn = pymysql.connect(host="localhost",
                       port=3306,
                       user="root",
                       password="Password",
                       database="python41",
                       charset="utf8")

# 3获取游标，执行sql语句
cursor = conn.cursor()

try:
    sql = "insert into students(name) values('刘璐'),('王美丽');"
    # 4执行sql语句
    row_count = cursor.execute(sql)
    # 看收影响行数
    print(row_count)
    # 提交数据
    conn.commit()
except Exception as e:
    # 撤销
    conn.rollback()

# 5关闭游标
cursor.close()

# 6关闭连接
conn.close()

```

### 2.sql注入与防止

```python
sql = "select * from students where name='%s'; "%"黄蓉'or 1=1 or '"
```

```sql
select * from students where name='黄蓉'or 1=1 or ''; 
```

防止sql注入：sql语句参数化 - 在sql语句用%s,

(查询)

```python
# 1导入包
import pymysql

# 2创建连接对象connect（）
# connect=Connection=Connect
conn = pymysql.connect(host="localhost",
                       port=3306,
                       user="root",
                       password="Password",
                       database="python41",
                       charset="utf8")

# 3获取游标，执行sql语句
cursor = conn.cursor()

#
s = ("黄蓉' or 1=1 or '",)
sql = "select * from students where name= %s;"
# 4执行sql语句
row_count = cursor.execute(sql, s)
# 看收影响行数
print(row_count)

result =cursor.fetchall()
for row in result:
    print(row)

# 5关闭游标
cursor.close()

# 6关闭连接
conn.close()

```

（插入）llist dict

```python
# 1导入包
import pymysql

# 2创建连接对象connect（）
# connect=Connection=Connect
conn = pymysql.connect(host="localhost",
                       port=3306,
                       user="root",
                       password="Password",
                       database="python41",
                       charset="utf8")

# 3获取游标，执行sql语句
cursor = conn.cursor()

try:
    sql = "insert into students(name,age,gender) values(%s,%s,%s);"
    # 4执行sql语句
    list1 = ['黄蓉', 20, '女']
    row_count = cursor.execute(sql, list1)
    # 看收影响行数
    print(row_count)
    # 提交数据
    conn.commit()
except Exception as e:
    # 撤销
    conn.rollback()
finally:
    # 5关闭游标
    cursor.close()

    # 6关闭连接
    conn.close()

```



### 3.事务

在使用事务之前，先确保表的存储引擎是InnoDB类型，只有这个类型才可以使用事务，Mysql数据库中的表的存储引擎默认是InnoDB类型。

==表的存储引擎说明==：提供存储数据的一种机制，不同的存储引擎提供不同的存储机制

查看表存储引擎

```mysql
show engines;
```

![image-20210128204822301](C:\Users\yyf\AppData\Roaming\Typora\typora-user-images\image-20210128204822301.png)

说明：

- 常用的表的存储引擎是InnoDB和myISAM
- InnoDB是支持事务的
- MyISAM是不支持事务的，优势是访问速度亏啊，对事务没有要求，或者以select、insert位置的都可以使用该存储引擎创建表

查看表的创表语句

```mysql
--选择数据库
use 数据库名;
--查看表
show create table 表名；
```

- 修改表的存储引擎语句

  ```mysql
  alter table 表名 engine = 引擎类型名
  引擎类型名=‘MyISAM’
  ```

开启事务

```mysql
begin;
或者
start transaction;
```

说明：

- 开启事务后执行修改命令，变更数据会保存到mysql服务器的缓存文件中，而不维护到物理表中

- Mysql数据库默认采用自动提交（autocommit）模式

  ，如果没有显示的开启一个事务，那么每条sql语句都会被当做一个事务执行提交的操作

- 当设置autocommit=0就是取消了自动提交事务模式，知道显示执行commit和rollback表示该事务结束

  - set autocommit=0表示取消自动提交事务模式，需要手动执行commit完成事务的提交

提交事务：

​	将本地缓存文件的数据提交到物理表中，完成数据的更新。

```mysql
commit;
```

回滚事务：

​	放弃本地缓存文件中的缓存数据，表示回到开始事务前的状态。

```mysql
rollback;
```

pymysql中conn.commit()就是提交事务

​			中conn.rollback()就是回滚事务

###4.索引

查看表中已有索引：

```mysql
show index from 表名
```

说明：

 主键能自动创建索引

索引的创建：

```mysql
-- 创建索引的语法结构
-- alter table 表名 add index 索引名[可选](列名，。。。)
-- 给name字段添加索引
alter table classes add index my_name(name);
```

说明：

索引名，不指定，默认使用字段名

索引的删除：



```mysql
-- 删除索引的语法结构
-- alter table 表名 drop index 索引名
-- 给name字段添加索引
alter table classes drop index my_name;
```

### 检验索引查询性能

```mysql
create table test_index(title varchar(10));
```

```python
import pymysql

if __name__ == '__main__':
    # 创建连接独显
    conn = pymysql.connect(host="localhost",
                           port=3306,
                           user="root",
                           password="Password",
                           database="python41",
                           charset="utf8"
                           )
    # 获取游标，目的执行sql语句
    cursor = conn.cursor()
    # 准备sql，
    sql = "insert into test_index(title) values(%s);"
    try:
        # 循环执行1w次
        for i in range(10000):
            cursor.execute(sql, ["test" + str(i)])
        # 提交数据
        conn.commit()
    except Exception as e:
        # 回滚数据
        conn.rollback()
        print(1)
    finally:
        cursor.close()
        conn.close()

```

#### 联合索引

减少磁盘开销，因为每创建一个索引，就是创建一个索引文件

==联合索引的最左原则==

即index（name,age),支持name 、那name和age组合查询，不支持age单独查询

## 闭包

###1.闭包的定义

闭包：在调用换函数后，依旧保存函数的变量

​	在函数嵌套的前提下，内部函数使用了外部函数的变量，并且外部函数返回了内部函数，我们把这个使用外部函数变脸和内部函数称为闭包。

### 2.闭包的构成条件

通过闭包的定义，我们可以得知闭包的形成条件：

1. 在函数嵌套的前提下
2. 内部函数使用了外部函数的变量
3. 外部函数返回内部函数

### 3.简单闭包代码

```python
# 闭包的作用可以保存外部函数的变量

# 闭包的形成条件
# 1. 函数嵌套
# 2. 内部函数使用了外部函数的变量（参数）
# 3. 外部函数返回内部函数，这个使用了外部函数变量的内部函数称为==闭包==

# 1.函数嵌套
def func_out():#外部函数
    num1=10
    def func_inner():#内部函数
        #2.内部函数使用外部函数的变量
        result=num1+10
        print(result)
    #外部函数返回内部函数
    return func_inner()

if __name__ == '__main__':
    func_out()
```

作用：保存外部函数

==注意点==：闭包函数引用了外部函数的变量，外部函数的变量没有及时释放，消耗内存。

### 4.闭包的使用（实例）

更具配置信息使用闭包实现不同人的对话信息

- 定义外部函数接受不同配置信息参数，参数是人名
- 定义内部函数接受对话信息参数
- 在内部函数里面把配置信息和对话信息进行拼接输出



```python
# 外部函数接受姓名参数
def config_name(name):
#内部函数保存外部的参数，并且完成数据显示的组成
    def inner(msg):
        print(name+":"+msg)
    # print(id(inner))
#外部函数返回内部函数
    return inner

if __name__ == '__main__':
    tom=config_name("tom")#1590812560240
    jerry=config_name("jerry")#1590812560920
    tom("哥们过来一下")#tom:哥们过来一下
```

### 5.闭包内使用的外部变量

```python
def func_out():
    # 外部函数的变量
    num1 = 10

    def func_inner():
        # 内部函数使用外部函数的变量
        # num1 = 20  # 修改外部函数的变量（错误）
        nonlocal num1  # 修改外闭包函数需要用到nonlocal
        num1 = 20
        result = num1 + 10
        print(result)

    return func_inner()


if __name__ == '__main__':
    func_out()
```

## 装饰器

### 1.装饰器定义

就是给已有函数增加额外功能的函数，它本质上是一个闭包函数。

装饰器的特点：

1. 不修改已有函数代码
2. 不修改已有函数调用方式
3. 给已有函数增加额外功能

装饰器实例代码

```python
# 学习装饰器：对已有函数进行额外的功能拓展

# 装饰器的特点
# 1.不修改已有函数的源代码
# 2.不修改已有函数的调用方式
# 3.给已有函数添加额外功能
# *装饰器本质上是一个闭包函数

# 定义装饰器
def decorator(func):  # 如果闭包函数参数有且只有一个并且是函数类型，就是装饰器
    def inner():
        # 在内部函数对已有函数进行装饰
        print("已添加登录验证")
        func()

    return inner


def comment():
    print("发表评论")


if __name__ == '__main__':
    #用装饰器对已有函数进行装饰
    comment=decorator(comment)
    # 调用方式不变
    comment()

```

装饰器语法糖的写法

@装饰器名

```python
# 学习装饰器：对已有函数进行额外的功能拓展

# 装饰器的特点
# 1.不修改已有函数的源代码
# 2.不修改已有函数的调用方式
# 3.给已有函数添加额外功能
# *装饰器本质上是一个闭包函数

# 定义装饰器
def decorator(func):  # 如果闭包函数参数有且只有一个并且是函数类型，就是装饰器
    def inner():
        # 在内部函数对已有函数进行装饰
        print("已添加登录验证")
        func()

    return inner

 #装饰器的语法糖写法：@装饰器名，装饰以后函数的写法更加简单
@decorator # 相当于comment= decorator(comment)
def comment():
    print("发表评论")


if __name__ == '__main__':

    # 调用方式不变
    comment()
```

### 2.装饰器实现已有函数的执行时间的统计

```python
import time

def decorator(func):
    def inner():
        #内部函数对已有函数进行装饰
        #获取当前时间,距离1970-1-1 0:0:1的时间差
        begin=time.time()
        func()
        end=time.time()
        result=end-begin
        print("函数执行耗时：",result)
    return inner

@decorator #work=decorator(work)
def work():
    for i in range(100000):
        print(i)


if __name__ == '__main__':
    work()
```

### 3.通用的装饰器

```python
# 通用的装饰器：可以装饰任意类型的函数

# -----------装饰带有参数的韩式-------------
#使用装饰器装饰已有函数的时候，内部函数类型和要装饰的已有函数类型保持一致
def decorater(func):
    def inner(a, b):
        # 在内部函数对已有函数进行装饰
        print("正在努力进行加法运算")
        func(a, b)

    return inner


@decorater  # 装饰器语法糖:add_num=decorater(add_num),add_num=inner
def add__num(num1, num2):
    result = num1 + num2
    print("结果为：", result)


if __name__ == '__main__':
    add__num(1, 2)
    # 正在努力进行加法运算
    # 结果为： 3
```

```python
# 通用的装饰器：可以装饰任意类型的函数

# -----------装饰带有参数和返回值的函数-------------

def decorater(func):
    def inner(a, b):
        # 在内部函数对已有函数进行装饰
        print("正在努力进行加法运算")
        num = func(a,b)
        return num

    return inner


@decorater  # 装饰器语法糖:add_num=decorater(add_num),add_num=inner
def add__num(num1, num2):
    result = num1 + num2
    return result


if __name__ == '__main__':
    result=add__num(1, 2)
    print("结果为：",result)
    # 正在努力进行加法运算
    # 结果为： 3
```

```python
# 通用的装饰器：可以装饰任意类型的函数

# -----------装饰带有不定长参数和返回值的函数-------------
# 该装饰器还可以称为通用的装饰器

def decorater(func):
    def inner(*args, **kwargs):
        # 在内部函数对已有函数进行装饰
        print("正在努力进行加法运算")
        # *args:把元组里面的每一个元素，按照位置的参数方式进行传参
        # **kwargs：把字典里面的每一个键值对，按照关键子的方式进行传参
        # 这里对元组和字典进行拆包，仅限于解和不定长参数的函数使用
        num = func(*args, **kwargs)
    	return num

    return inner


@decorater  # 装饰器语法糖:add_num=decorater(add_num),add_num=inner
def add__num(*args, **kwargs):
    result = 0
    # args 元组类型
    # kwargs 字段类型
    for value in args:
        result += value

    for value in kwargs.values():
        result += value

    return result


if __name__ == '__main__':
    my=(1,2)
    result = add__num(*my)
    print("结果为：", re
          sult)
    # 正在努力进行加法运算
    # 结果为： 3
```

### 4.多个装饰器的使用

```python
def make_p(func):
    def inner():
        # 在内部函数对已有函数进行装饰
        print("make p执行了")
        result = "<p>" + func() + "</p>"
        return result

    return inner


def make_div(func):
    def inner():
        # 在内部函数对已有函数进行装饰
        print("make div执行了")
        result = "<div>" + func() + "</div>"
        return result

    return inner


# 多个装饰器的过程：由内到外的一个装饰过程，先执行内部的装饰器，在实行外部的装饰器
# 原理刨析：content=make_div(make_p(content))
# 分布拆解:content=make_p(content),内部函数装饰完成content=make_p.inner
# content=make_div(make_p.inner)
@make_div  # content=make_p(content) content=inner
@make_p
def content():
    return "结果"


"""<p>结果</p>"""
if __name__ == '__main__':
    result = content()
    print(result)
```

### 5.带有参数的装饰器

装饰器不能接收2个参数

```python
def return_decorate(flag):
    def decorator(func):
        def inner(a, b):
            if flag == "+":
                print("正在努力进行加法")
            elif flag == "-":
                print("正在努力进行减法")
            func(a, b)

        return inner

    # 当调用的时候可以返回一个装饰器
    return decorator


# 加法
@return_decorate("+")  # decorator = return_decorator("+"),@decorator=>add_num=decorator(add_num)
def add_num(a, b):
    result = a + b
    print(result)


# 减法
@return_decorate("-")
def sub_num(a, b):
    result = a - b
    print(result)


if __name__ == '__main__':
    add_num(1, 2)
    sub_num(2, 1)
```

- 使用带有参数的装饰器，其实是在装饰器外面由包裹一个函数，使用该函数接受参数，返回是装饰器，因为@符号需要配合装饰器的实例使用

### 6.类装饰器

```python
#类装饰器：使用类装饰已有函数

class MyDecorator(object):
    # 需要init函数
    def __init__(self,func):

        self.func=func
    # 实现call方法，让对象变成可调用对象，能够像函数一样使用
    def __call__(self, *args, **kwargs):
        # 对已有函数进行封装
        print("课已讲完了")
        self.func()

@MyDecorator # @MyDecorator  => show = MyDecorator(show)
def show():
    print("快要下学了")

if __name__ == '__main__':
    #执行show # 执行MyDecorator类创建实例对象-》show（）=》对象（）

    show()
```

##Mini-Web框架

web框架：为web服务器提供服务的应用程序

![image-20210216182850652](C:\Users\yyf\AppData\Roaming\Typora\typora-user-images\image-20210216182850652.png)

- web服务器接受浏览器发送的请求，如果是动态资源请求找web框架来处理
- web框架负责处理浏览器的动态资源请求，按处理结果发给服务器
- web服务器把web框架处理的结果封装成http响应报文发送给浏览器

WSGI协议规定web服务器吧动态资源的请求信息传给web框架处理，web框架把处理好的结果返回给web服务器

###1.框架程序开发

###2.模板功能的替换

请求index时，打开index模板文件，从数据库在查找数据塞入网页

```python
"""web框架的职责专门处理动态资源请求"""
import time


# 获取首页数据
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 获取个人中心数据
def center():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/center.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 处理没有找到的动态资源
def not_found():
    # 状态信息、
    status = "404 Not Found"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]

    # web处理后的数据
    data = "not found"

    # 这里返回的是一个元组
    return status, response_header, data


# 处理动态资源请求
def handle_request(env):
    # 获取动态的请求资源路径
    request_path = env["request_path"]
    print("动态资源请求的地址是:" + request_path)
    # 判断请求的动态资源路径，选择指定的函数处理对应的动态资源请求
    if request_path == "/index.html":
        # 获取首页数据
        result = index()
        # 把处理后的结果返回给web服务器使用，让web服务器拼接响应报文时使用
        return result
    elif request_path == "/center.html":
        # 获取个人中心数据
        result = center()
        return result
    else:
        # 没有动态资源数据，返回404状态信息
        result = not_found()
        return result
```

###3.路由列表功能

django开发是使用



==路由==什么是路由？路由就是请求的URL到处理函数的映射，也就是说提前把请求的URL和处理函数关联好。

==路由列表==：这么多路由怎么管理，可以使用路由列表进行管理，通过路由列表保存每一个路由

| 请求路径     | 处理函数   |
| ------------ | ---------- |
| /login.html  | login函数  |
| /index.html  | index函数  |
| /center.html | center函数 |

没一条都是一个路由



```python
 """web框架的职责专门处理动态资源请求"""
import time


# 获取首页数据
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 获取个人中心数据
def center():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/center.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 处理没有找到的动态资源
def not_found():
    # 状态信息、
    status = "404 Not Found"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]

    # web处理后的数据
    data = "not found"

    # 这里返回的是一个元组
    return status, response_header, data


# 路由列表，列表里面的每一条记录都是一个路由
route_list = [
    ("/index.html", index),
    ("/center.html", center)
]


# 处理动态资源请求
def handle_request(env):
    # 获取动态的请求资源路径
    request_path = env["request_path"]
    print("动态资源请求的地址是:" + request_path)
    # 判断请求的动态资源路径，选择指定的函数处理对应的动态资源请求
    
    # 遍历路由列表，匹配请求的URL
    for path,func in route_list:
        if request_path==path:
            #找到路由，执行对应的处理函数
            result = func()
            return result
        
        else:
            # 没有动态资源数据，返回404状态信息
            result = not_found()
            return result
    
    # if request_path == "/index.html":
    #     # 获取首页数据
    #     result = index()
    #     # 把处理后的结果返回给web服务器使用，让web服务器拼接响应报文时使用
    #     return result
    # elif request_path == "/center.html":
    #     # 获取个人中心数据
    #     result = center()
    #     return result
    # else:
    #     # 没有动态资源数据，返回404状态信息
    #     result = not_found()
    #     return result
```

###4.装饰器的方式添加路由

####1.使用带有参数的装饰器添加路由

前面是实现了路由列表，但是每次添加路由都需要手动添加来完成，接下来我们都想要完成路由的自动添加，可以通过装饰器来实现，在使用装饰器对处理函数进行装饰的时候我们需要知道装饰的函数和那个请求路径进行关联，也就是说装饰器需要接受一个url参数，这样的装饰器称为带有参数的装饰器。

```python
"""web框架的职责专门处理动态资源请求"""
import time

# 路由列表，列表里面的每一条记录都是一个路由
route_list = [
    # ("/index.html", index),
    # ("/center.html", center)
]


# 带有参数的装饰器
def route(path):
    # 装饰器
    def decorator(func):
        # 当执行装饰器的时候就需要把路由添加到路由列表内
        # 当装饰函数的时候只添加一次路由即可
        route_list.append((path, func))

        def inner():
            result = func()
            return result

        return inner

    # 返回一个装饰器
    return decorator


# 获取首页数据
"/index.html"


@route("/index.html")  # => @decorator =>index=decorator(index)
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 获取个人中心数据
@route("/center.html")
def center():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/center.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 处理没有找到的动态资源
def not_found():
    # 状态信息、
    status = "404 Not Found"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]

    # web处理后的数据
    data = "not found"

    # 这里返回的是一个元组
    return status, response_header, data


# 处理动态资源请求
def handle_request(env):
    # 获取动态的请求资源路径
    request_path = env["request_path"]
    print("动态资源请求的地址是:" + request_path)
    # 判断请求的动态资源路径，选择指定的函数处理对应的动态资源请求

    for path, func in route_list:
        if request_path == path:
            # 找到路由，执行对应的处理函数
            result = func()
            return result

        else:
            # 没有动态资源数据，返回404状态信息
            result = not_found()
            return result


if __name__ == '__main__':
    print(route_list)    # [('/index.html', <function index at 0x000002A5F65D4E18>), ('/center.html', <function center at 0x000002A5F65EA268>)]
```

###5.显示股票信息页面开发

#### 1.数据准备

stock_db

```mysql
-- MySQL dump 10.13  Distrib 5.7.17, for Linux (x86_64)
--
-- Host: localhost    Database: stock_db
-- ------------------------------------------------------
-- Server version	5.7.13-0ubuntu0.16.04.2

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `focus`
--

DROP TABLE IF EXISTS `focus`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `focus` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `note_info` varchar(200) DEFAULT '',
  `info_id` int(10) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `info_id` (`info_id`),
  CONSTRAINT `focus_ibfk_1` FOREIGN KEY (`info_id`) REFERENCES `info` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `focus`
--

LOCK TABLES `focus` WRITE;
/*!40000 ALTER TABLE `focus` DISABLE KEYS */;
INSERT INTO `focus` VALUES (2,'你确定要买这个？',36),(3,'利好',37),(9,'',88),(10,'',89),(13,'',1);
/*!40000 ALTER TABLE `focus` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `info`
--

DROP TABLE IF EXISTS `info`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `info` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `code` varchar(6) NOT NULL COMMENT '股票代码',
  `short` varchar(10) NOT NULL COMMENT '股票简称',
  `chg` varchar(10) NOT NULL COMMENT '涨跌幅',
  `turnover` varchar(255) NOT NULL COMMENT '换手率',
  `price` decimal(10,2) NOT NULL COMMENT '最新价',
  `highs` decimal(10,2) NOT NULL COMMENT '前期高点',
  `time` date DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=95 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `info`
--

LOCK TABLES `info` WRITE;
/*!40000 ALTER TABLE `info` DISABLE KEYS */;
INSERT INTO `info` VALUES (1,'000007','全新好','10.01%','4.40%',16.05,14.60,'2017-07-18'),(2,'000036','华联控股','10.04%','10.80%',11.29,10.26,'2017-07-20'),(3,'000039','中集集团','1.35%','1.78%',18.07,18.06,'2017-06-28'),(4,'000050','深天马A','4.38%','4.65%',22.86,22.02,'2017-07-19'),(5,'000056','皇庭国际','0.39%','0.65%',12.96,12.91,'2017-07-20'),(6,'000059','华锦股份','3.37%','7.16%',12.26,12.24,'2017-04-11'),(7,'000060','中金岭南','1.34%','3.39%',12.08,11.92,'2017-07-20'),(8,'000426','兴业矿业','0.41%','2.17%',9.71,9.67,'2017-07-20'),(9,'000488','晨鸣纸业','6.30%','5.50%',16.37,15.59,'2017-07-10'),(10,'000528','柳工','1.84%','3.03%',9.42,9.33,'2017-07-19'),(11,'000540','中天金融','0.37%','5.46%',8.11,8.08,'2017-07-20'),(12,'000581','威孚高科','3.49%','3.72%',27.00,26.86,'2017-06-26'),(13,'000627','天茂集团','5.81%','12.51%',10.93,10.33,'2017-07-20'),(14,'000683','远兴能源','6.42%','21.27%',3.48,3.29,'2017-07-19'),(15,'000703','恒逸石化','0.24%','1.65%',16.92,16.88,'2017-07-20'),(16,'000822','山东海化','6.60%','8.54%',9.05,8.75,'2017-07-06'),(17,'000830','鲁西化工','1.38%','4.80%',7.36,7.26,'2017-07-20'),(18,'000878','云南铜业','1.26%','3.23%',14.50,14.47,'2017-07-19'),(19,'000905','厦门港务','5.44%','10.85%',15.90,15.60,'2017-04-20'),(20,'000990','诚志股份','0.53%','1.00%',16.99,16.90,'2017-07-20'),(21,'002019','亿帆医药','1.19%','2.81%',17.05,16.85,'2017-07-20'),(22,'002078','太阳纸业','2.05%','1.90%',8.45,8.29,'2017-07-19'),(23,'002092','中泰化学','7.25%','6.20%',15.53,14.48,'2017-07-20'),(24,'002145','中核钛白','2.43%','7.68%',6.75,6.61,'2017-07-19'),(25,'002285','世联行','8.59%','5.66%',9.23,8.50,'2017-07-20'),(26,'002311','海大集团','1.13%','0.24%',18.81,18.63,'2017-07-19'),(27,'002460','赣锋锂业','9.41%','9.00%',63.70,58.22,'2017-07-20'),(28,'002466','天齐锂业','3.62%','3.66%',68.44,66.05,'2017-07-20'),(29,'002470','金正大','2.30%','0.99%',8.00,7.82,'2017-07-20'),(30,'002496','辉丰股份','3.15%','4.29%',5.24,5.08,'2017-04-10'),(31,'002497','雅化集团','0.38%','12.36%',13.10,13.05,'2017-07-20'),(32,'002500','山西证券','0.44%','3.70%',11.49,11.44,'2017-07-20'),(33,'002636','金安国纪','2.70%','11.59%',19.80,19.42,'2017-07-19'),(34,'300032','金龙机电','0.66%','0.72%',15.28,15.18,'2017-07-20'),(35,'300115','长盈精密','0.60%','0.59%',33.50,33.41,'2017-07-19'),(36,'300268','万福生科','-10.00%','0.27%',31.77,13.57,'2017-04-10'),(37,'300280','南通锻压','3.31%','0.66%',32.20,32.00,'2017-04-11'),(38,'300320','海达股份','0.28%','0.82%',18.26,18.21,'2017-07-20'),(39,'300408','三环集团','1.69%','0.81%',23.42,23.17,'2017-07-19'),(40,'300477','合纵科技','2.84%','5.12%',22.10,22.00,'2017-07-12'),(41,'600020','中原高速','5.46%','4.48%',5.60,5.31,'2017-07-20'),(42,'600033','福建高速','1.01%','1.77%',4.00,3.99,'2017-06-26'),(43,'600066','宇通客车','4.15%','1.49%',23.08,23.05,'2017-06-13'),(44,'600067','冠城大通','0.40%','2.97%',7.56,7.53,'2017-07-20'),(45,'600110','诺德股份','2.08%','4.26%',16.16,15.83,'2017-07-20'),(46,'600133','东湖高新','9.65%','21.74%',13.64,12.44,'2017-07-20'),(47,'600153','建发股份','3.65%','2.03%',13.35,13.21,'2017-07-10'),(48,'600180','瑞茂通','2.20%','1.07%',14.86,14.54,'2017-07-20'),(49,'600183','生益科技','6.94%','4.06%',14.94,14.12,'2017-07-19'),(50,'600188','兖州煤业','1.53%','0.99%',14.56,14.43,'2017-07-19'),(51,'600191','华资实业','10.03%','11.72%',15.80,14.36,'2017-07-20'),(52,'600210','紫江企业','6.03%','10.90%',6.68,6.30,'2017-07-20'),(53,'600212','江泉实业','1.39%','1.78%',10.20,10.15,'2017-07-19'),(54,'600225','*ST松江','4.96%','2.47%',5.71,5.61,'2017-04-13'),(55,'600230','沧州大化','5.74%','13.54%',43.26,40.91,'2017-07-20'),(56,'600231','凌钢股份','2.79%','3.77%',3.68,3.60,'2017-07-19'),(57,'600291','西水股份','10.02%','9.23%',34.71,31.55,'2017-07-20'),(58,'600295','鄂尔多斯','4.96%','12.62%',16.51,15.73,'2017-07-20'),(59,'600303','曙光股份','8.37%','14.53%',11.53,10.64,'2017-07-20'),(60,'600308','华泰股份','1.12%','2.66%',6.30,6.26,'2017-07-19'),(61,'600309','万华化学','0.03%','1.78%',31.81,31.80,'2017-07-20'),(62,'600352','浙江龙盛','0.39%','1.85%',10.32,10.28,'2017-07-20'),(63,'600354','敦煌种业','7.89%','18.74%',9.44,8.75,'2017-07-20'),(64,'600408','安泰集团','1.98%','3.38%',4.13,4.12,'2017-04-13'),(65,'600409','三友化工','0.62%','3.78%',11.36,11.29,'2017-07-20'),(66,'600499','科达洁能','0.46%','3.94%',8.84,8.80,'2017-07-20'),(67,'600508','上海能源','3.26%','2.99%',13.32,13.01,'2017-07-19'),(68,'600563','法拉电子','0.32%','1.36%',53.67,53.50,'2017-07-20'),(69,'600567','山鹰纸业','0.76%','2.85%',3.98,3.96,'2017-07-19'),(70,'600585','海螺水泥','0.45%','0.61%',24.51,24.44,'2017-07-19'),(71,'600668','尖峰集团','4.35%','6.43%',18.70,18.36,'2017-04-13'),(72,'600688','上海石化','2.72%','0.91%',6.80,6.74,'2017-06-01'),(73,'600729','重庆百货','5.70%','3.34%',27.45,27.13,'2017-06-29'),(74,'600739','辽宁成大','3.30%','3.50%',19.74,19.11,'2017-07-20'),(75,'600779','水井坊','3.85%','2.77%',29.39,28.30,'2017-07-20'),(76,'600781','辅仁药业','8.61%','4.16%',23.46,21.89,'2017-05-02'),(77,'600801','华新水泥','4.00%','10.15%',12.99,12.49,'2017-07-20'),(78,'600846','同济科技','2.06%','17.41%',9.39,9.26,'2017-04-13'),(79,'600884','杉杉股份','1.08%','3.53%',20.67,20.45,'2017-07-20'),(80,'600966','博汇纸业','2.89%','5.54%',6.41,6.28,'2017-07-19'),(81,'600971','恒源煤电','2.36%','8.81%',12.16,11.88,'2017-07-20'),(82,'601012','隆基股份','0.76%','1.30%',19.93,19.78,'2017-07-20'),(83,'601100','恒立液压','4.78%','0.92%',19.31,18.97,'2017-07-13'),(84,'601101','昊华能源','4.03%','6.06%',11.10,10.80,'2017-07-19'),(85,'601216','君正集团','2.16%','2.26%',5.20,5.10,'2017-04-17'),(86,'601666','平煤股份','2.81%','6.14%',6.96,6.77,'2017-07-20'),(87,'601668','中国建筑','2.39%','1.42%',10.70,10.45,'2017-07-20'),(88,'601678','滨化股份','0.13%','2.47%',7.92,7.91,'2017-07-20'),(89,'601918','新集能源','1.23%','3.11%',4.93,4.92,'2017-07-19'),(90,'603167','渤海轮渡','2.77%','3.34%',11.87,11.61,'2017-04-13'),(91,'603369','今世缘','3.34%','2.13%',14.24,13.78,'2017-07-20'),(92,'603589','口子窖','3.99%','1.84%',39.37,39.04,'2017-06-26'),(93,'603799','华友钴业','2.38%','7.19%',67.46,65.89,'2017-07-20'),(94,'603993','洛阳钼业','2.94%','2.50%',7.36,7.16,'2017-07-19');
/*!40000 ALTER TABLE `info` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2017-08-18 20:53:58

```

create database stock_db charset=utf8;

```
create database stock_db charset=utf8;
quite;
#转至文件目录
cd /d %userprofile%\desktop
mysql -u root -p
use stock_db;

#执行这个文件
source stock_db.sql

#显示一下是否成功
show tables;
```

```python
@route("/index.html")  # => @decorator =>index=decorator(index)
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据
        # 创建连接对象
        conn = pymysql.connect(host="localhost",
                               port=3306,
                               user="root",
                               password="Password",
                               database="stock_db",
                               charset="utf8",
                               )
        # 获取游标
        cursor = conn.cursor()

        # 准备sql
        sql = 'select * from info;'

        # 执行sql
        cursor.execute(sql)
        # 获取查询结果
        result = cursor.fetchall()
        print(result)
        # 关闭游标
        cursor.close()
        # 关闭连接
        conn.close()

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    # 遍历每一条数据，完成数据的tr标签的封装
    data = ""
    for row in result:
        data += """<tr>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td><input type="button" value="添加" id="toAdd" name="toAdd" systemidvaule="000007"></td>
                   </tr>""" % row

    response_body = file_data.replace("{%content%}", data)
   

    # 这里返回的是一个元组
    return status, response_header, response_body

```

### 6.个人中心数据接口的开发

```python
# 个人中心数据接口
@route("/center_data.html")
def center_data():
    # 从数据库把数据查询出来，然后把查询出来的数据转成json数据
    # 创建连接对象
    conn = pymysql.connect(host="localhost",
                           port=3306,
                           user="root",
                           password="Password",
                           database="stock_db",
                           charset="utf8",
                           )
    # 获取游标
    cursor = conn.cursor()
    # 执行sql
    sql = '''select i.code,i.short,i.chg,i.turnover,i.price,i.highs,f.note_info 
            from info i inner join focus f 
            on i.id=f.info_id;
          '''
    # 执行sql
    cursor.execute(sql)
    # 获取查询结果
    result = cursor.fetchall()
    print(result)
    # 把元组转成列表字典
    center_data_list = [{
        "code": row[0],
        "short": row[1],
        "chg": row[2],
        "turnover": row[3],
        "price": str(row[4]),
        "highs": str(row[5]),
        "note_info": row[6],
    } for row in result]
    print(center_data_list)
    # 把列表转成json字符串数据
    # ensure_ascii=False在控制台能显示中文
    json_str = json.dumps(center_data_list, ensure_ascii=False)
    print(json_str)
    print(type(json_str))
    # 关闭游标
    cursor.close()
    # 关闭连接
    conn.close()
    # 状态信息
    status = "200 OK"
    # 响应头信息
    response_header = [
        ("Server", "PWS/1.1"),
        # 指定编码格式，因为没有模板文件，可以通过响应头指定编码格式
        ("Content-Type"," text/html;charset=utf-8")
                       ]
    return status, response_header, json_str
```

### 7.ajax请求数据渲染个人中心页面

```html
$(document).ready(function(){
    // 发送ajax请求，获取个人中心数据
    $.get("center_data.html",function (data) {
        // ajax 成功回调函数
        // 获取table标签
        var $table = $(".table");
        // 如果指定了返回数据的解析方式是json，那么data就是一个js对象
        for(var i = 0; i < data.length; i++){
            // 根据下标获取每一个个人中心数据js对象
            var oCenterData = data[i];

            // 封装后的每一个tr标签
            var oTr = '<tr>' +
                        '<td>'+ oCenterData.code +'</td>' +
                        '<td>'+ oCenterData.short +'</td>' +
                        '<td>'+ oCenterData.chg +'</td>' +
                        '<td>'+ oCenterData.turnover +'</td>' +
                        '<td>'+ oCenterData.price +'</td>' +
                        '<td>'+ oCenterData.highs +'</td>' +
                        '<td>'+ oCenterData.note_info +'</td>' +
                        '<td><a type="button" class="btn btn-default btn-xs" href="/update/000007.html"> <span class="glyphicon glyphicon-star" aria-hidden="true"></span> 修改 </a></td>' +
                        '<td><input type="button" value="删除" id="toDel" name="toDel" systemidvaule="000007"></td>' +
                        '</tr>'
            // 给table标签追加每一行tr标签
            $table.append(oTr)

        }

    }, "json");
```

###8.logging日志

用于记录程序中的日志信息

使用logging这个包来完成

####记录日志的目的：

1. 可以很方便的了解程序的运行情况
2. 可以分析用户的操作行为、喜好等信息
3. 方便开发人员检查bug

####日志级别介绍：

日志等级可以分为5个，从低刀高分为

1. DEBUG
2. INFO
3. WARNING
4. ERROR
5. CRITICAL(critical)

####日志等级说明：

- DEBUG:程序调试bug时使用
- INFO：程序正常运行时使用
- WARNING：程序未按照预期运行时使用，但并不是错误，如：用户登录的密码错误
- ERROR：程序出错时候使用，
- CRITICAL：特别严重的问题，导致程序不能再继续运行的时候使用，如：磁盘空间为空，一般很少使用
- 默认的是WARNING等级，当在WARNING或者以上时候菜进行记录
- 日志等级由低到高为：debug<info<warning<error<critical

####logging日志的使用

在logging包中记录日志的方式有2种：

1. 输出到控制台
2. 保存到日志文件

####日志信息输出到控制台的示例：

```python
# logging日志用来记录程序运行使用的日志信息
import logging

logging.debug("i'm debug")
logging.info("i'm info")
# 默认是warning级别
logging.warning("i'm warning")
logging.error("i'm error")
logging.critical("i'm critical")
```

控制台输出结果：

```
WARNING:root:i'm warning
ERROR:root:i'm error
CRITICAL:root:i'm critical

```

####日志等级和输出格式的设置：

```python
# logging日志用来记录程序运行使用的日志信息
import logging

# 设置logging日志的配置信息
# level 表示设置级别
# %(asctime)s 表示当前时间
# %(filename)s 文件名
# %(lineno)d 表示日志行数
# %(levelname)s 表示日志等级
# %(message)s 表示日志内容

# filename输出日志的文件名
# filemode打开文件后的操作方式-详见python文件，w表示每次清空重新写入，a表示每次在已有记录后继续写入

logging.basicConfig(level=logging.DEBUG,
                    format="%(asctime)s-%(filename)s[lineno:%(lineno)d]-%(levelname)s-%(message)s",
                    filename="log.txt",
                    filemode="w"
                    )
```

日志文件：

``` 
2021-02-22 21:16:39,027-01.py[lineno:22]-DEBUG-i'm debug
2021-02-22 21:16:39,027-01.py[lineno:23]-INFO-i'm info
2021-02-22 21:16:39,035-01.py[lineno:25]-WARNING-i'm warning
2021-02-22 21:16:39,035-01.py[lineno:26]-ERROR-i'm error
2021-02-22 21:16:39,035-01.py[lineno:27]-CRITICAL-i'm critical

```

#### 项目中的应用

```python
# 在程序入口模块，设置logging日志的配置信息，只配置一次，整个程序都可以使用，好比单例
logging.basicConfig(level=logging.DEBUG,
                    format="%(asctime)s-%(filename)s[lineno:%(lineno)d]-%(levelname)s-%(message)s",
                    filename="log.txt",
                    filemode="a")
```

### 其他

上个项目在windows上面会遇到3个问题

1. log.txt中乱码

   windows默认保存txt是acsll模式，print传的是gbk模式，整个项目是utf-8模式需要调一下

2. 读文件时候需要额外添加encoding

   ![image-20210223190202347](C:\Users\yyf\AppData\Roaming\Typora\typora-user-images\image-20210223190202347.png)

3. 循环有问题

   ![image-20210223190241521](C:\Users\yyf\AppData\Roaming\Typora\typora-user-images\image-20210223190241521.png)

   前面代码的循环只能显示index界面，此处怀疑是python版本和系统问题。就是显示path=index后直接匹配不对就已经输出not found了 不会在for循环一下一个路由匹配