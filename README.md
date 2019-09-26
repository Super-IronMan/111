----------------------------------------------------------------
--------------------------------------------------------
2019年06月27日 
数据库-mysql
--查询每种类型中最贵的电脑的信息
----最贵的价格
select max(price) from goods;
----所有的商品的类型名
select cate_name from goods group by cate_name;
----每种类型中最贵的价格
select cate_name,max(price) from goods group by cate_name;
----每种类型中最贵的电脑的信息
select g.cate_name,g.name,g.price
from (select cate_name,max(price) as max_price(这里的max(price)若不起别名,下文直接引用max(price)时，会报错）from goods group by cate_name)as new 
left join goods as g
on new.cate_name=g.cate_name and new.max_price=g.price;

--去重
distinct()
select distinct(cate_name) from goods; 
group by
select cate_name from goods group by cate_name;

--增删改查
----增(当插入的是查询出的多行数据，不能加values)
insert into 表名(可指定字段) values(...)
----删
update 表名 set is_delete=1 where ××××;(逻辑删除)
delete from 表名 where 条件;
----改
update 表名 set 字段1=××,字段2=×× where 条件;
----查
select *(或指定字段) from 表名;

--将goods的cate_name新建为一张表，并将原cate_name改成新表对应的id
----错误
update goods_cates(此处应输入需要修改的表名) left(应用inner) join goods(此处应输入需关联的表名) 
set(goods.cate_name=goods_cates.id)(不能用括号) 
on(用where) goods_cates.name=goods.cate_name;
----正确
update goods as g 
inner join goods_cates as c 
set g.cate_name=c.id
where g.cate_name=c.name;
----正确(教程的)
update goods as g 
inner join goods_cates as c 
on g.cate_name=c.name
set g.cate_name=c.id;

--将goods的brand_name新建为一张表，并将原表的brand_name改成新表对应的id
----将原表的brand_name复制到新表，将原表的brand_name改成brand_id，为防止新插入的
----brand_id数据超出范围，需将brand_id设为外键。
update goods as g 
inner join goods_brands as b 
set g.brand_name=b.id
where g.brand_name=b.name;

--外键
----尽量少使用外键约束，因为会极大的降低表更新的效率，因为更新一次查询一次是否约束
----增加外键
alter table goods add foreign key (cate_id) references goods_cates(id);
----删除外键约束(****在show create table 的 constraint 后)
alter table goods drop foreign key ****;


2019年06月28日 
Python和mysql交互
--apt命令
用法： apt [选项] 命令
命令行软件包管理器 apt 提供软件包搜索，管理和信息查询等功能。
它提供的功能与其他 APT 工具相同（像 apt-get 和 apt-cache），
但是默认情况下被设置得更适合交互。
常用命令：
  list - 根据名称列出软件包
  search - 搜索软件包描述
  show - 显示软件包细节
  install - 安装软件包
  remove - 移除软件包
  autoremove - 卸载所有自动安装且不再使用的软件包
  update - 更新可用软件包列表
  upgrade - 通过 安装/升级 软件来更新系统
  full-upgrade - 通过 卸载/安装/升级 来更新系统
  edit-sources - 编辑软件源信息文件

--安装包管理工具pip3，通过pip安装pymysql
sudo apt-get install python3-pip(而非python-pip3)
pip3 install pymsql

--复制文件
sudo copy -a '文件名' 路径

--视图
视图就是将多个表关联查询出的结果创建成一个新的虚拟的表，但只支持查询操作。
create view 视图名 as select语句
drop view 视图名

--事物
是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位。
----创建事物(开启事物后执行修改命令，变更缓存到本地缓存中，而不维护到物理表中)
begin;
start transaction;
----提交事务
commit;
----回滚事物
rollback;

--索引
索引是一种特殊的文件，它们包含着对数据表里所有记录的引用指针。
通俗说，数据库索引就像书的目录，能加快数据库的查询速度。
----查看索引
show index from 表名;
----创建索引(当指定字段为字符串，需指定长度为字段定义时的长度;非字符串可不填长度)
create index 索引名称 on 表名(字段名称(长度))
----删除索引
drop index 索引名称 on 表名

--运行时间监测
----开启
set profiling=1;
----查看
show profiles;


2019年07月05日 
"网络编程"
--网络通信
网络就是辅助双方或多方能连接在一起的工具
目的：连通多方然后进行通信，即把数据从一方到另外一方
网络编程：让不同的电脑上的软件进行数据传递，即进程间的通信

--ip地址
Internet Protocol Address 互联网协议地址
ip地址用来在网络中标记一台电脑，在本地局域网是唯一的
1.ip地址共4字节/byte，一个字节表示8位/bit，一个字节能表示0-255共2**8个数。
2.每个ip地址分两部分：网络号(1-3字节)和主机号(3-1字节)
3.127.0.0.1 是本地回环地址，同一台电脑上的程序可通过这个ip通信
4.私有ip：在这么多网络IP中，国际规定有一部分IP地址是用于我们的局域网使用，也就
是属于私网IP，不在公网中使用的，它们的范围是：
10.0.0.0～10.255.255.255
172.16.0.0～172.31.255.255
192.168.0.0～192.168.255.255

----查看网卡配置信息
Linux：ifconfig
Windows：ipconfig

----禁用/打开网卡
sudo ifconfig（网卡名）down/up

----修改网卡ip地址
sudo ifconfig （网卡名） 指定ip

----通常用ping来检测网络/远程主机是否正常
ping 域名/ip

--端口
端口是一种特殊通道（端口就像一个房子的门，是出入这间房子的必经之路。）一个程序需
要收发网络数据，就需要有这样的端口。
linux系统中，端口可以有65536（2的16次方）个.
端口是通过端口号来标记的，端口号只有整数，范围是从0到65535。
ip地址区分电脑，端口号区分程序,'ip地址+端口号'区分不同网络服务。TCP的端口号和UDP的端口号可以相同，因为端口号是对于网络服务而言的
知名端口：0-1023     （80端口分配给HTTP服务，21端口分配给FTP服务）
动态端口：1024-65535     （之所以称为动态端口，是因为它一般不固定分配某种服务，而是动态分配。动态分配是指当一个系统程序或应用程序程序需要网络通信时，它向主机申请一个端口，主机从可用的端口号中分配一个供它使用。当这个程序关闭时，同时也就释放了所占用的端口号）
查看端口状态：'netstat -an'
小结：端口有什么用？我们知道，一台拥有IP地址的主机可以提供许多服务，比如HTTP（万维网服务）、FTP（文件传输）、SMTP（电子邮件）等，这些服务完全可以通过1个IP地址来实现。那么，主机是怎样区分不同的网络服务呢？显然不能只靠IP地址，因为IP地址与网络服务的关系是一对多的关系。实际上是通过“IP地址+端口号”来区分不同的服务的。 需要注意的是，端口并不是一一对应的。比如你的电脑作为客户机访问一台WWW服务器时，WWW服务器使用“80”端口与你的电脑通信，但你的电脑则可能使用“3457”这样的端口。

--socket
----不同电脑上的进程之间如何通信
首要解决的问题是如何唯一标识一个进程，否则通信无从谈起！
在1台电脑上可以通过进程号（PID）来唯一标识一个进程，但是在网络中这是行不通的。
其实TCP/IP协议族已经帮我们解决了这个问题，网络层的“ip地址”可以唯一标识网络中的
主机，而传输层的“协议+端口”可以唯一标识主机中的应用进程（进程）。
这样利用ip地址，协议，端口就可以标识网络的进程了，网络中的进程通信就可以利用这个
标志与其它进程进行交互。

----socket概念
进程间通信的一种方式，特点是在于能实现不同主机间的进程间通信，
网络上的各种服务大多是基于socket来完成通信的，比如浏览网页，QQ，email

----创建套接字对象
socket.socket(family=AF_INET, type=SOCK_STREAM, proto=0, fileno=None)
socket.socket(AddressFamily-协议族, Type-套接字类型)
-AddressFamily:默认AF_INET(用于Internet进程间通信) / AF_UNIX(用于同一台机器进程间通信)
-Type:默认SOCK_STREAM(流式套接字，主要用于TCP协议(Transmission Control Protocol 传输控制协议))  / SOCK_DGRAM(数据报套接字，主要用于UDP协议(User Datagram Protocol 用户数据报协议))     
# AddressFamily-协议族/地址类型/寻址方案；datagram-数据电报 ；STREAM-溪流，流

----套接字使用流程 (类似使用文件)
1.创建套接字
2.使用套接字收发数据
3.关闭套接字
import socket
# 创建tcp套接字对象
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
# 创建udp套接字对象
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
# ......使用套接字的功能(省略)......
s.close() # 不用的时候，关闭套接字

----udp绑定信息
一般情况下，在一台电脑上运行的网络程序有很多，为了不与其他的网络程序占用同一个端
口号，往往在编程中，udp的端口号一般不绑定。但是如果需要做成一个服务器端的程序的
话，是需要绑定的，想想看这又是为什么呢？如果报警电话每天都在变，想必世界就会乱了，
所以一般服务性的程序，往往需要一个固定的端口号，这就是所谓的端口绑定

----网络通信过程
类似寄快递（发货，打包，运输，运输，投递，收货）
网络通信过程中，之所需要ip、port等，就是为了能够将一个复杂的通信过程进行任务划
分，从而保证数据准确无误的传递。

-----------------------------------------------------------------
-------------------------UDP聊天器--------------------------------
import socket

def send_msg(udp_socket):
	"""发送消息"""
	dest_addr = ('192.168.169.1', 8080)
	send_data = input("请输入要发送的消息：")
	udp_socket.sendto(send_data.encode('utf-8')), dest_addr)


def recv_msg(udp_socket):
	"""接收数据"""
	recv_data = udp_socket.recvfrom(1024)  # 1024表示本次接收的最大字节数
	print("%s:%s" % (str(recv_data[1]), recv_data[0].decode('utf-8')))
    # 接收到的数据recv_data是一个元组
    # 第1个元素是对方发送的数据,第2个元素是对方的ip和端口

def main():
	# 创建套接字
	udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
	# 绑定ip
	udp_socket.bind(('', 55555)) 
    # ip地址和端口号，ip一般不用写，表示本机的任何一个ip
	# 循环处理事情
	while True:
		# 发送
		send_msg(udp_socket)
		# 接收并显示
		recv_msg(udp_socket)
	# 关闭套接字
	udp_socket.close()
if __name__ == "__main__":
	main()

-----------------------------------------------------------------
-------------------------UDP聊天器--------------------------------



2019年07月07日
"TCP"
--TCP介绍
TCP-传输控制协议(Transmission Control Protocol )-打电话 / UDP-用户数据报协议(User Datagram Protocol )-写信
TCP：一种面向连接的,可靠的,基于字节流的传输层控制协议
TCP通信流程：创建连接，数据传送，终止连接
TCP通信模型中，在通信开始之前，一定要先建立相关的链接，才能发送数据，类似于生活中，"打电话"

----TCP特点
1.面向连接
通信双方必须先建立连接才能进行数据的传输，双方都必须为该连接分配必要的系统内核资源，以管理连接的状态和连接上的传输。
双方间的数据传输都可以通过这一个连接进行。
完成数据交换后，双方必须断开此连接，以释放系统资源。
这种连接是一对一的，因此TCP不适用于广播的应用程序，基于广播的应用程序请使用UDP协议。
2.可靠传输
    1.TCP采用发送应答机制
    TCP发送的每个报文段都必须得到接收方的应答才认为这个TCP报文段传输成功
    2.超时重传
    发送端发出一个报文段之后就启动定时器，如果在定时时间内没有收到应答就重新发送这个报文段。
    TCP为了保证不发生丢包，就给每个包一个序号，同时序号也保证了传送到接收端实体的包的按序接收。然后接收端实体对已成功收到的包发回一个相应的确认（ACK）；如果发送端实体在合理的往返时延（RTT）内未收到确认，那么对应的数据包就被假设为已丢失将会被进行重传。
    3.错误校验
    TCP用一个校验和函数来检验数据是否有错误；在发送和接收时都要计算校验和。
    4.流量控制和阻塞管理
    流量控制用来避免主机发送得过快而使接收方来不及完全收下。

----TCP和UDP的区别
面向连接（确认有创建三方交握，连接已创建才作传输。）
有序数据传输
重发丢失的数据包
舍弃重复的数据包
无差错的数据传输
阻塞/流量控制

----通信模型
UDP通信模型中，在通信开始之前，不需要建立相关的链接，只需要发送数据即可，类似于
生活中的"写信"。
TCP通信模型中，在通信开始之前，一定要先建立相关的链接，才能发送数据，类似于生活
中的"打电话"。

----服务器端：提供服务的一方；客户端：需要服务的一方

----TCP客户端使用流程
tcp的客户端要比服务器端简单很多，如果说服务器端是需要自己买手机、插手机卡、设置
铃声、等待别人打电话流程的话，那么客户端就只需要找一个电话亭，拿起电话拨打即可，
流程要少很多。
1.创建socket
2.连接服务器
3.发/收数据
4.关闭套接字
------------------------------------------------------------------
-----------------------------TCP_Client---------------------------
import socket


def main():
    # 1. 创建tcp的套接字
    tcp_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # 2. 链接服务器
    tcp_socket.connect(("192.168.169.1", 8080))
    #server_ip = input("请输入要链接的服务器的ip:")
    #server_port = int(input("请输入要链接的服务器的port:"))
    #tcp_socket.connect((server_ip, server_port))

    # 3. 发送数据/接收数据
    send_data = input("请输入要发送的数据:")
    tcp_socket.send(send_data.encode("gbk"))
    
    recv_data = tcp_socket.recv(1024)
    print('接收到的数据为：',recv_data.decode('gbk') )

    # 4. 关闭套接字
    tcp_socket.close()


if __name__ == "__main__":
    main()
------------------------------------------------------------------
-----------------------------TCP_Client---------------------------

----TCP服务器
如果想让别人能更够打通咱们的电话获取相应服务的话，需要做以下几件事情：
买个手机，插上手机卡，设计手机为正常接听状态（即能够响铃），静静的等着别人拨打。
1.socket创建一个套接字
2.bind绑定地址
3.listen使套接字变为被动连接，监听状态（套接字默认为主动状态）
4.accept等待客户端的连接
5.recv/send收发数据
6.close关闭套接字

------------------------------------------------------------------
-----------------------------TCP_Server---------------------------
import socket


def main():
    # 1. 买个手机(创建套接字 socket)
    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    # 2. 插入手机卡(绑定地址 bind)
    tcp_server_socket.bind(("", 7434))

    # 3. 将手机设置为正常的 响铃模式(让套接字由默认的主动变为被动 listen)
    tcp_server_socket.listen(128)  # 128表示能同时服务的客户端数量上限

    print("-----1----")
    # 4. 等待别人的电话到来(等待客户端的链接 accept)

    # 如果有新的客户端来链接服务器，会产生一个新的套接字专门为这个客户端服务
    # tcp_server_socket继续保持监听状态，等待其他新客户端的链接
    # new_client_socket用来为这个客户端服务
    new_client_socket, client_addr = tcp_server_socket.accept()
    print("-----2----")

    # 接收客户端发送过来的数据
    recv_data = new_client_socket.recv(1024)
    print('接收到的数据为:', recv_data.decode('gbk'))

    # 发送数据给客户端
    new_client_socket.send("-----收到------".encode("gbk"))

    # 关闭套接字
    # 1.关闭为这个客户端服务的套接字，只要关闭了，就意味着为不能再为这个客户端服务了，如果还需要服务，只能再次重新连接
    new_client_socket.close()
    # 2.关闭监听套接字
    tcp_server_socket.close()


if __name__ == "__main__":
    main()
------------------------------------------------------------------
-----------------------------TCP_Server---------------------------

--如果server的recv解堵塞，有两种情况：
1.客户端发来了数据
2.客户端调用close，返回的数据会为空。所以可以通过返回的数据长度，而判断客户端是
否已经下线。

--注意点
1.当一个tcp客户端连接服务器时，服务器端会产生1个新的套接字，这个套接字用来标记
这个客户端，单独为这个客户端服务。
2.关闭listen后的套接字意味着被动套接字关闭了，会导致新的客户端不能够链接服务器，
但是之前已经链接成功的客户端正常通信。

------------------------------------------------------------------
-----------------------文件下载器-服务器-----------------------------
from socket import *
import sys


def get_file_content(file_name):
    """获取文件的内容"""
    try:
        with open(file_name, "rb") as f:
            content = f.read()
        return content
    except:
        print("没有下载的文件:%s" % file_name)


def main():

    if len(sys.argv) != 2:
        print("请按照如下方式运行：python3 xxx.py 7890")
        return
    else:
        # 运行方式为python3 xxx.py 7890
        port = int(sys.argv[1])


    # 创建socket
    tcp_server_socket = socket(AF_INET, SOCK_STREAM)
    # 本地信息
    address = ('', port)
    # 绑定本地信息
    tcp_server_socket.bind(address)
    # 将主动套接字变为被动套接字
    tcp_server_socket.listen(128)

    while True:
        # 等待客户端的链接，即为这个客户端发送文件
        client_socket, clientAddr = tcp_server_socket.accept()
        # 接收对方发送过来的数据
        recv_data = client_socket.recv(1024)  # 接收1024个字节
        file_name = recv_data.decode("utf-8")
        print("对方请求下载的文件名为:%s" % file_name)
        file_content = get_file_content(file_name)
        # 发送文件的数据给客户端
        # 因为获取打开文件时是以rb方式打开，所以file_content中的数据已经是二进制的格式，因此不需要encode编码
        if file_content:
            client_socket.send(file_content)
        # 关闭这个套接字
        client_socket.close()

    # 关闭监听套接字
    tcp_server_socket.close()


if __name__ == "__main__":
    main()
------------------------------------------------------------------
-----------------------文件下载器-服务器-----------------------------

------------------------------------------------------------------
-----------------------文件下载器-客户端-----------------------------
from socket import *


def main():

    # 创建socket
    tcp_client_socket = socket(AF_INET, SOCK_STREAM)

    # 目的信息
    server_ip = input("请输入服务器ip:")
    server_port = int(input("请输入服务器port:"))

    # 链接服务器
    tcp_client_socket.connect((server_ip, server_port))

    # 输入需要下载的文件名
    file_name = input("请输入要下载的文件名：")

    # 发送文件下载请求
    tcp_client_socket.send(file_name.encode("utf-8"))

    # 接收对方发送过来的数据，最大接收1024个字节（1K）
    recv_data = tcp_client_socket.recv(1024)
    # print('接收到的数据为:', recv_data.decode('utf-8'))
    # 如果接收到数据再创建文件，否则不创建
    if recv_data:
        with open("[接收]"+file_name, "wb") as f:
            f.write(recv_data)

    # 关闭套接字
    tcp_client_socket.close()


if __name__ == "__main__":
    main()
------------------------------------------------------------------
-----------------------文件下载器-客户端-----------------------------

--vim
----配置vim（需要通过命令'vim ~/.vimrc'修改配置文件）
设置缩进值：set ts=4 
显示行号：set number
----命令模式下的操作（esc）
复制：y / yy(复制当前行)
粘贴：p
剪切：d / dd(剪切当前行) 
撤销：u
恢复：ctrl+r
跳到行首/行末：I/A
跳到指定行：行数+G
选中多行：V
新建空白行：O(光标上) / o(光标下)

--判断
'if（非空的元组/列表/字符串，非0的值，True）'    代表 if True：
'if（0,None,False,空的数据类型）'   代表if False：

--Linux命令
删除：rm
重命名/移动：mv
创建文件夹/目录：mkdir 文件名
查看当前路径：pwd
复制文件到指定路径：cp '文件名' '指定路径' 



"TCP的三次握手和四次挥手"
--TCP的三次握手和四次挥手
1.连接建立>>数据传送>>连接释放
2.三次握手简单来说：客户端请求建立连接>>服务端确认建立连接并返回确认信息>>客户端收到确认信息并返回---->三次握手成功.
    关于三次握手发送的信息：
    第一次：客户端发送（发起连接SYN+序号seq=i）
    第二次：服务端发送（确认ACK+确认序号ack=i+1；发起连接SYN+序号seq=j）
    第三次：客户端发送（确认ACK+确认序号ack=j+1）
3.三次握手实际是客户端和服务端准备资源的过程，连接建立；四次挥手实际是释放资源的过程，连接释放。
4.准备好资源后，即建立连接后，才能收发数据。
5.客户端和服务端谁先调用close，谁最终会等待2MSL(Maximum Segment Lifetime-最长报文段寿命)以保留资源，等待2MSL后未收到数据再释放资源。
6.一般客户端先close，因客户端close后，虽保留资源，但因不绑定地址，所以再次启动后会随机分配端口，则可以继续正常启动。若服务器先close，则会因为保留资源导致再次启动时资源被占用而地址不可用。
7.解决端口占用问题：创建套接字后设置sock选项:xxx_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR,1)

--TCP报文重点字段
-序号：seq-Sequence number：占32位，用来标识从TCP源端向目的端发送的字节流，发起方发送数据时对此进行标记。
-确认序号：ack-Acknowledge number：占32位，只有ACK标志位为1时，确认序号字段才有效，Ack=Seq+1。
-标志位：共6个，即SYN、ACK、FIN、PSH、RST、URG等，具体含义如下：
    1.URG：紧急指针（urgent pointer）。
    2.ACK：确认。(acknowledgement-确认)
    3.PSH：接收方应该尽快将这个报文交给应用层。
    4.RST：重置连接。
    5.SYN：发起一个新连接。(synchronous-建立连接) 
    6.FIN：释放一个连接。(finish结束)

--TCP三次握手
三次握手即建立TCP连接，就是指建立一个TCP连接时，需要客户端和服务端总共发送3个包以确认连接的建立。在socket编程中，这一过程由客户端执行connect来触发，整个流程如下图所示：https://www.2cto.com/net/201310/251896.html。TCP协议更多参考：https://www.cnblogs.com/kzang/articles/2582957.html
1.第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。
2.第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，ack=J+1，随机产生一个值seq=K，并将该数据包发送给Client以确认连接请求，Server进入SYN_RCVD状态。
3.第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否为1，如果正确则连接建立成功，Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据了。

--TCP四次挥手
四次挥手即终止TCP连接，就是指断开一个TCP连接时，需要客户端和服务端总共发送4个包以确认连接的断开。在socket编程中，这一过程由客户端或服务端任一方执行close来触发，整个流程如下图所示：https://www.2cto.com/net/201310/251896.html
由于TCP连接是全双工的，因此，每个方向都必须要单独进行关闭，这一原则是当一方完成数据发送任务后，发送一个FIN来终止这一方向的连接，收到一个FIN只是意味着这一方向上没有数据流动了，即不会再收到数据了，但是在这个TCP连接上仍然能够发送数据，直到这一方向也发送了FIN。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上图描述的即是如此。
1.第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。
2.第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。
3.第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。
4.第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。

--全双工/半双工/单工
串行通信中,数据通常是在两个站(如终端和微机)之间进行传送,按照数据流的方向可分成三种基本的传送方式:全双工、半双工、和单工。
1.单工：数据只在一个方向上传输，不能实现双方通信。电视、广播。
2.半双工：允许数据在两个方向上传输，但是同一时间数据只能在一个方向上传输，其实际上是切换的单工。对讲机。
3.全双工：允许数据在两个方向上同时传输。手机通话。

--长连接和短连接
1.短连接的操作步骤：建立连接——数据传输——关闭连接...建立连接——数据传输——关闭连接。
短连接只发送一次数据。像WEB网站的http服务一般都用短连接，因为长连接对于服务端来说会耗费一定的资源。如果用长连接，而且同时有成千上万的用户，如果每个用户都占用一个连接的话，支撑不起。所以并发量大，但每个用户无需频繁操作情况下需用短连好。
2.长连接的操作步骤：建立连接——数据传输...（保持连接）...数据传输——关闭连接。
长连接多用于操作频繁，点对点的通讯，而且连接数不能太多。



2019年07月08日
"HTTP"
--HTTP （HyperText Transfer Protocol-超文本传输协议）
在Web应用中，服务器把网页传给浏览器，实际上就是把网页的HTML代码发送给浏览器，
让浏览器显示出来,而浏览器和服务器之间的传输协议是HTTP，所以：
-HTML（超文本标记语言-Hyper Text Markup Language）是一种用来定义网页的文本。
-HTTP是在网络上传输HTML的协议，用于浏览器和服务器的通信。就是简单的请求-响应协议
 
--chrome的开发者模式中
Elements显示网页的结构
Network显示浏览器和服务器的通信

--HTTP分析实例
当我们在地址栏输入www.sina.com时，浏览器将显示新浪的首页。在这个过程中，浏览器都干了哪些事情呢？
1.浏览器请求
    # HTTP请求分为Header和Body两部分（post请求有Body），以下是get请求的header
    GET / HTTP/1.1  # GET表示一个读取请求，将从服务器获得网页数据，/表示URL的路径，URL总是以/开头，/就表示首页，最后的HTTP/1.1指示采用的HTTP协议版本是1.1。
    Host: www.cctv.com  # 从第二行开始，每一行都类似于xxx: abcd；host表示请求的域名是www.sina.com。如果一台服务器有多个网站，服务器就需要通过Host来区分浏览器请求的是哪个网站。
    Connection: keep-alive
    Cache-Control: max-age=0
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
    Accept-Encoding: gzip, deflate
    Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
    Cookie: country_code=CN
2.服务器响应 
    # HTTP响应分为Header和Body两部分（Body是可选项），以下是header；HTTP响应的Body就是HTML源码，通过查看网页源码在浏览器中直接查看HTML源码
    HTTP/1.1 200 OK  # 200表示一个成功的响应，后面的OK是说明。404 Not Found：网页不存在，500 Internal Server Error：服务器内部出错
    Date: Thu, 19 Sep 2019 02:43:27 GMT
    Content-Type: text/html # 响应的内容，这里是text/html表示HTML网页。注意，浏览器就是依靠Content-Type来判断响应的内容是网页还是图片，是视频还是音乐。浏览器并不靠URL来判断响应的内容，所以，即使URL是http://www.baidu.com/meimei.jpg，它也不一定就是图片。
    Transfer-Encoding: chunked 
    Connection: keep-alive
    Expires: Thu, 19 Sep 2019 02:44:17 GMT
    Server: CCTV_WebServer
    Cache-Control: max-age=180
    X-UA-Compatible: IE=Edge
    Age: 130
3.浏览器解析
    当浏览器读取到HTML源码后，它会解析HTML，显示页面，然后，根据HTML里面的各种链接，再发送HTTP请求给新浪服务器，
    拿到相应的图片、视频、Flash、JavaScript脚本、CSS等各种资源，最终显示出一个完整的页面。所以我们在Network下面能看到很多额外的HTTP请求。

--HTTP请求分析总结
1.浏览器首先向服务器发送HTTP请求，请求包括：
方法：GET还是POST，GET仅请求资源，POST会附带用户数据；
路径：/full/url/path；
域名：由Host头指定：Host: www.sina.com
以及其他相关的Header；
如果是POST，那么请求还包括一个Body，包含用户数据
2.服务器向浏览器返回HTTP响应，响应包括：
响应代码：200表示成功，3xx表示重定向，4xx表示客户端发送的请求有错误，5xx表示服务器端处理时发生了错误；
响应类型：由Content-Type指定；
以及其他相关的Header；
通常服务器的HTTP响应会携带内容，也就是有一个Body，包含响应的内容，网页的HTML源码就在Body中。
3.如果浏览器还需要继续向服务器请求其他资源，比如图片，就再次发出HTTP请求，重复步骤1、2。
Web采用的HTTP协议采用了非常简单的请求-响应模式，从而大大简化了开发。当我们编写一个页面时，我们只需要在HTTP请求中把HTML发送出去，不需要考虑如何附带图片、视频等，浏览器如果需要请求图片和视频，它会发送另一个HTTP请求，因此，一个HTTP请求只处理一个资源(此时就可以理解为TCP协议中的短连接，每个链接只获取一个资源，如需要多个就需要建立多个链接)
HTTP协议同时具备极强的扩展性，虽然浏览器请求的是http://www.sina.com的首页，但是新浪在HTML中可以链入其他服务器的资源，比如<img src="http://i1.sinaimg.cn/home/2013/1008/U8455P30DT20131008135420.png">，从而将请求压力分散到各个服务器上，并且，一个站点可以链接到其他站点，无数个站点互相链接起来，就形成了World Wide Web，简称WWW。

--HTTP格式
每个HTTP请求和响应都遵循相同的格式，一个HTTP包含Header和Body两部分，其中Body是可选的。HTTP协议是一种文本协议，所以，它的格式也非常简单。
1.HTTP GET请求的格式：
    GET /path HTTP/1.1
    Header1: Value1
    Header2: Value2
    Header3: Value3
    # 每个Header一行一个，换行符是'\r\n'
2.HTTP POST请求的格式：
    POST /path HTTP/1.1
    Header1: Value1
    Header2: Value2
    Header3: Value3

    body data(发送的用户数据)
    # 当遇到连续两个'\r\n'时，Header部分结束，后面的数据全部是Body
3.HTTP响应的格式：
    HTTP/1.1 200 OK
    Header1: Value1
    Header2: Value2
    Header3: Value3

    body data(网页的HTML源码)
    # HTTP响应如果包含body，也是通过'\r\n\r\n'来分隔的。
    # 请再次注意，Body的数据类型由Content-Type头来确定，如果是网页，Body就是文本，如果是图片，Body就是图片的二进制数据。
    # 当存在Content-Encoding时，Body数据是被压缩的，最常见的压缩方式是gzip，所以，看到Content-Encoding: gzip时，需要将Body数据先解压缩，才能得到真正的数据。压缩的目的在于减少Body的大小，加快网络传输。

--服务器响应传送的数据源码
response_headers = 'HTTP/1.1 200 OK\r\n'
response_headers += '\r\n'
response_body = 'body数据'
response = response_headers + response_body



2019年07月10日 
"网络通信"
--TCP/IP协议族
互联网协议包含上百种协议标准，最重要的两个协议是TCP和IP协议
所以把互联网的协议简称TCP/IP协议族

--TCP/IP协议族的四层结构
应用层-->传输层-->网络层-->网络接口层

--端到端发送‘你好’实例
发送：
应用层(你好+app协议)->传输层(再+端口号)->网络层(再+IP地址)->网络接口层(再+MAC地址)->网络传输
接收：
反向解析组包好的数据
发送信息是组包过程(变大)，接受信息是解包过程(变小)
# MAC地址(Media Access Control Address)媒体访问控制地址,用来确认网络设备的地址,或称硬件地址，实际地址，物理地址。在网络中唯一标识一个网卡，一台设备若有一或多个网卡，则每个网卡都需要并会有一个唯一的MAC地址。

--网络通信过程
----两台电脑的网络
两台电脑之间通过网线可以直接通信，前提是在同一网络号下(网段内)
所以需要设置好IP地址和子网掩码
因为即使IP地址相接近，若不清楚网络号，就不能确定是否在同一网段内
# 确定网络号：将IP和子网掩码进行按位与操作，得到的0部分即为主机号，其余为网络号
----使用集线器组成网络
网络集线器HUB以广播方式发送数据，连接集线器的设备都会收到数据，会造成拥堵，所以有了交换机。# 多端口的转发器，也称集线器
----使用交换机组成网络
能单播/广播，替代集线器
----使用路由器组成网络
路由器：连接不同的网段，让他们之间可以收发数据
发送过程中，每个设备拿到数据后，源IP和目的IP不变，MAC地址变化
(类似传递东西，起点终点确定，经手人依次传递，左右传右手，需要传给谁，就把目的MAC地址写谁的)
# 默认网关：当需要发送的目标IP不在本网段时，就会将数据发送给默认的一台电脑进行转发，这就是网关拥有转发功能，像中间人。

--访问网站的通信过程
1.在浏览器中输入一个网址时，需要将它先解析出ip地址
2.当得到ip地址之后，浏览器以tcp的方式3次握手链接服务器
3.以tcp的方式发送http协议的请求数据 给 服务器
4.服务器以tcp的方式回应http协议的应答数据 给 浏览器

--ARP广播和ARP(地址解析协议)协议
发送数据需要指定IP地址和MAC地址，但是平时发数据只知道IP，并未设定MAC，那是怎么通信的呢？：
1.先看缓存区(命令'arp -a')是否存在目标IP的MAC地址，若无下一步
2.发送方会发送一条ARP广播，包含（通用MAC地址(FF:FF:FF:FF:FF:FF)+指定IP）的数据，
所有的接收方都能通过MAC验证，但只有实际接收方才能通过IP验证，
接收方再单播回发送方，这样就得到了目标接收方的MAC地址，就能正常通信了。
# 所有网卡都只能接收自己的MAC地址号和通用MAC地址

--广播
把目标IP设置为(网络号+255)(192.168.169.255)后，目标MAC地址会自动设为通用MAC地址，
可用于局域网共享和ARP攻击

--DNS服务器
DNS(Domain Name Server)
域名服务器，是进行域名和与之对应的IP地址转换的服务器。用来解析出IP，类似电话本

--总结：
MAC地址：在设备与设备之间数据通信时用来标记收发双方（网卡的序列号）
IP地址：在逻辑上标记一台电脑，用来指引数据包的收发方向（相当于电脑的序列号）
网络掩码：用来区分ip地址的网络号和主机号
默认网关：当需要发送的数据包的目的ip不在本网段内时，就会发送给默认的一台电脑，成为网关
集线器：已过时，用来连接多态电脑，缺点：每次收发数据都进行广播，网络会变的拥堵
交换机：集线器的升级版，有学习功能知道需要发送给哪台设备，根据需要进行单播、广播
路由器：连接多个不同的网段，让他们之间可以进行收发数据，每次收到数据后，ip不变，但是MAC地址会变化
DNS：用来解析出IP（类似电话簿）
http服务器：提供浏览器能够访问到的数据


2019年7月11日
"爬虫基本概念"
--爬虫定义
网络爬虫就是模拟客户端发网络请求，接收请求响应，一种按照一定的规则，自动抓取
互联网信息的程序。(只要是浏览器能做的事情，原则上爬虫都能做)
通用爬虫：通常指搜索引擎的爬虫
聚焦爬虫：针对特定网址的爬虫

--聚焦爬虫流程
URL List-->响应内容(-->提取URl-->URL List)-->提取数据-->入库

--ROBOTS协议
网站通过ROBOTS协议告诉搜索引擎哪些页面可以抓取，哪些不可以

--HTTP和HTTPS
HTTP：超文本传输协议，默认端口号：80
HTTPS：HTTP + SSL(安全套接字层)，默认端口号：443
HTTP明文传输数据，存在安全问题，HTTPS把收发的数据加密后进行传输
所以收发会多出加密解密的过程，更安全但是性能降低

--HTTP的请求与响应
HTTP通信由两部分组成：客户端请求消息 与 服务器响应消息

--HTTP请求
爬虫要根据当前url地址对应的响应为准，url的响应和当前url地址的elements的内容不一样，
因为页面上的所有数据包括：当前url地址的响应+响应中被引用的其他文件对应的url的响应(js,css,jpg)
即浏览器渲染出来的页面和爬虫请求的页面并不一样

--浏览器发送HTTP请求的过程：
1.当用户在浏览器的地址栏中输入一个URL并按回车键之后，浏览器会向HTTP服务器发送HTTP请求去获取 http://www.baidu.com 的html文件，服务器把Response文件对象发送回给浏览器。
2.浏览器分析Response中的 HTML，发现其中引用了很多其他文件，比如Images文件，CSS文件，JS文件。 浏览器会自动再次发送Request去获取图片，CSS文件，或者JS文件。
3.当所有的文件都下载成功后，网页会根据HTML语法结构，完整的显示出来了。

--url
URL（Uniform Universal Resource Locator）：统一资源定位符，是用于完整地描述Internet上网页和其他资源的地址的一种标识方法。
格式：'scheme://host[:port#]/path/.../[?query-string参数][#anchor锚]'
    scheme：协议(例如：http, https, ftp)
    host：服务器的IP地址或者域名
    port#：服务器的端口（如果是走协议默认端口，缺省端口80）
    path：访问资源的路径
    query-string：参数，发送给http服务器的数据
    anchor：锚（跳转到网页的指定锚点位置）
例如：
ftp://192.168.0.116:8080/index
http://www.baidu.com
http://item.jd.com/11936238.html#product-detail

--客户端HTTP请求
URL只是标识资源的位置，而HTTP是用来提交和获取资源。客户端发送一个HTTP请求到服务器的请求消息，包括以下格式：请求行、请求头部、空行、请求数据
GET https://www.baidu.com/ HTTP/1.1     # 请求行
Host: www.baidu.com     # 请求头部
Connection: keep-alive  #请求头部
                        # 空行
                        # 请求数据

--HTTP请求主要分为Get和Post两种方法
GET是从服务器上获取数据，POST是向服务器传送数据
GET请求参数显示，都显示在浏览器网址上，HTTP服务器根据该请求所包含URL中的参数来产生响应内容，即“Get”请求的参数是URL的一部分。 例如： http://www.baidu.com/s?wd=Chinese
POST请求参数在请求体当中，消息长度没有限制而且以隐式的方式进行发送，通常用来向HTTP服务器提交量比较大的数据（比如请求中包含许多参数或者文件上传操作等），请求的参数包含在“Content-Type”消息头里，指明该消息体的媒体类型和编码，
注意：避免使用Get方式提交表单，因为有可能会导致安全问题。 比如说在登陆表单中用Get方式，用户输入的用户名和密码将在地址栏中暴露无遗。

--字节与位
数据存储以字节(Byte，简写B)为单位，数据传输大多以位(bit又名比特，简写b)为单位
每8位组成一个字节，1位代表一个0或1
1KB = 1024 B
1B  = 8 bit
一个ASCII码为一个字节
UTF-8编码是UTF编码的子集,一个英文字符一个字节，一个中文字符三个字节
解码方式和编码方式必须一样，否则乱码

--编码转换
bytes：二进制，互联网上的数据都是以二进制的方式传输的
str：unicode的呈现形式
bytes->str: decode解码 str.decode(encoding="utf-8", errors="strict")
str->bytes: encode编码 bytes.encode(encoding="utf-8", errors="strict")
字符串通过编码成为字节码，字节码通过解码成为字符串。
>>> text = '我是文本'
>>> text
'我是文本'
>>> print(text)
我是文本
>>> bytesText = text.encode()
>>> bytesText
b'\xe6\x88\x91\xe6\x98\xaf\xe6\x96\x87\xe6\x9c\xac'
>>> print(bytesText)
b'\xe6\x88\x91\xe6\x98\xaf\xe6\x96\x87\xe6\x9c\xac'
>>> type(text)
<'class' 'str'>
>>> type(bytesText)
<'class' 'bytes'>
>>> textDecode = bytesText.decode()
>>> textDecode
'我是文本'
>>> print(textDecode)
我是文本



"爬虫Request库"
--request作用：发送网络请求，返回响应数据
rp = requests.get(url)

--rp.content和rp.text的比较
----rp.content(推荐)
类型：bytes(纯正的字节类型)
解码类型：没指定
修改编码方式：rp.content.decode('默认utf-8')
----rp.text
类型：str
解码类型：靠推测
（rp.text是按照它所推测的编码方式去解码，rp.encoding返回rp.text推测的rp编码方法）
修改编码方式：rp.encoding=('utf-8')

--获取响应的html页面的方法：(依次尝试)
rp.content.decode()
rp.content.decode('gbk')
rp.text



2019年07月12日
"爬虫基本概念"
--响应的属性
response = request.get(url)
response.url
response.status_code
response.headers
response.cookies
response.encoding

--请求的属性
response.request.headers(而非requests)
response.request.url

--使用assert断言，判断请求是否成功
'assert response.status_code == 200'

--发送带headers的请求
作用：模拟浏览器，欺骗服务器，获取和浏览器一致的内容
hearders形式：字典，headers中最重要的是User-Agent和Cookies # user agent-用户代理
headers = {'User-Agent':'XXX'，'...':'...',....}
request.get(url, headers=headers)

--发送带参数的请求
参数形式：字典
params = {'wd':'python'}
request.get(url, params=params)
还可以使用format方法将params传入url

--字符串格式化的另一种方式：format方法
和%s功能一样，但更好用，{}用来占位：'...{}...'.format(xxx)   
(xxx可以填写列表元组等数据结构，也可多个{}，一一对应)
例子：
url = 'https://www.baidu.com/s?wd={}'.format('python')

--url编码
url是特殊的编码方式，和uhf-8/gbk不同

----------------------------------------------------------------
---------------------------百度贴吧爬虫-----------------------------
import requests


class TiebaSpider:
    def __init__(self, tieba_name):
        self.tieba_name = tieba_name
        self.url_temp = 
        "https://tieba.baidu.com/f?kw=" + tieba_name + "&ie=utf-8&pn={}"
        self.headers = 
        {"User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36"}

    def get_url_list(self):  # 1.构造url列表
        # url_list = []
        # for i in range(1000):
        #     url_list.append(self.url_temp.format(i*50))
        # return url_list
        return [self.url_temp.format(i * 50) for i in range(1000)]

    def parse_url(self, url):  # 发送请求，获取响应(parse-解析)
        print(url)
        response = requests.get(url, headers=self.headers)
        return response.content.decode()

    def save_html(self, html_str, page_num):  # 保存html字符串
        file_path = "{}—第{}页.html".format(self.tieba_name, page_num)
        with open(file_path, "w", encoding="utf-8") as f:  # "李毅—第4页.html"
            f.write(html_str)

    def run(self):  # 实现主要逻辑
        # 1.构造url列表
        url_list = self.get_url_list()
        # 2.遍历，发送请求，获取响应
        for url in url_list:
            html_str = self.parse_url(url)
            # 3.保存
            page_num = url_list.index(url) + 1  # 页码数
            self.save_html(html_str, page_num)


if __name__ == '__main__':
    tieba_spider = TiebaSpider("lol")
    tieba_spider.run()
----------------------------------------------------------------
---------------------------百度贴吧爬虫-----------------------------

--实现过程遇到的bug
1. 函数中调用函数-->self.XX()
2. 头部：headers 而非 header
3. class XX: 和 if __name__.. 无缩进
4. 函数内调用的变量必须在函数里面定义，否则需要在函数里面定义参数或者在类属性里面定义
5. 属性用名词命名，方法用动词命名

--列表推导式
# 扁平胜于嵌套
['a' for i in range(3)] --> ['a','a','a']
['a' for i in range(3) if i%2 == 0] --> ['a','a']

url_temp = 'https://tieba.baidu.com/f?kw='+self.tieba_name+'&ie=utf-8&pn={}' # template模板
[url_temp.format(50*i) for i in range(1000)]

--字典推导式
{i:i+5 for i in range(3)}
>>{0:5, 1:6, 2:7}
实例：
cookies='''anonymid=j3jxk555-nrn0wh; _r01_=1; _ga=GA1.2.1274811859.1497951251; _de=BF09EE3A28DED52E6B65F6A4705D973F1383380866D39FF5; ln_uact=mr_mao_hacker@163.com;
 depovince=BJ; jebecookies=54f5d0fd-9299-4bb4-801c-eefa4fd3012b|||||;
  JSESSIONID=abcI6TfWH4N4t_aWJnvdw; ick_login=4be198ce-1f9c-4eab-971d-48abfda70a50; 
  p=0cbee3304bce1ede82a56e901916d0949; first_login_flag=1; 
  ln_hurl=http://hdn.xnimg.cn/photos/hdn421/20171230/1635/main_JQzq_ae7b0000a8791986.jpg; 
  t=79bdd322e760beae79c0b511b8c92a6b9; societyguester=79bdd322e760beae79c0b511b8c92a6b9;
   id=327550029; xnsid=2ac9a5d8; loginfrom=syshome; ch_id=10016; wp_fold=0'''
>>cookies={
i.split("=")[0]:i.split("=")[1] for i in cookies.split("; ")
}



2019年07月13日
"爬虫的基本概念"
--发送post请求
登录注册(POST比GET更安全) / 需要传输大量文本的时候(POST请求对数据长度没有要求)/一般用于用户表单提交，登录等操作，data为字典
--用法
response = requests.post(
'http://www.baidu.com', data=data, headers=headers)

-----------------------------------------------------------
-----------------------post函数实例------------------------
import requests
import json
import sys

query_string = sys.argv[1]

headers = {"User-Agent":"Mozilla/5.0 (Linux; Android 5.1.1;Nexus 6 Build/LYZ28E) AppleWebKit/537.36 (KHTML, like Gecko)Chrome/63.0.3239.84 Mobile Safari/537.36"}

post_data = {
    "query":query_string,
    "from":"zh",
    "to":"en",
}

post_url = "http://fanyi.baidu.com/basetrans"

r = requests.post(post_url,data=post_data,headers=headers)
# print(r.content.decode())
dict_ret = json.loads(r.content.decode())
ret = dict_ret["trans"][0]["dst"]
print("result is :",ret)
----------------------post函数实例-------------------------
-----------------------------------------------------------
--注意
1. 爬虫不必强刚正面，比如百度翻译，网页不行可以上手机端，微信文章不行，可以上微信搜狗接口
2. 爬虫首要目标：务必确定目标url

--代码bug
1. 注意区分使用https和http
2. 注意看全看懂post的数据，区分有用无用数据，有些数据是反爬虫的，忽略直接导致错误
3. 注意区分json字符串/bytes型字符串/utf-8字符串

--知识点
1. json字符串转化为字典json.loads(XXX) 
2. sys.argv
一个列表，其中包含了被传递给 Python 脚本的命令行参数。
argv[0] 为脚本的名称（是否是完整的路径名取决于操作系统）。如果是通过 Python 解释器的命令行参数 -c 来执行的， argv[0] 会被设置成字符串 '-c' 。
如果没有脚本名被传递给 Python 解释器， argv[0] 为空字符串。
# demo.py
import sys
print(sys.argv)
>>python demo.py 1 2 3
>>['demo.py','1','2','3']
3. alias实现命令别名:~/.bashrc或者vi/etc/bashrc进入设置,
输入alias [-p] [name[=value] ... ]，注意=和字符串之间不能包含空格，
source /etc/bashrc 使配置生效

--代理  
浏览器 -> 代理 -> web服务器
----代理的好处：用户直接访问的代理，可以保护服务器，也可以实现负载均衡
正向代理: 对于客户端而言，知道最终服务器信息的
反向代理: 不知道的
----爬虫为什么需要代理
1. 让服务器以为不是同一个客户端在请求
2. 防止真实地址被泄露，防止追究
----用法
# porxies格式:字典
requests.get('https://www.baidu.com', proxies=proxies) #proxies-代理
proxy = '60.186.9.233'
proxies = {
    'http': 'http://' + proxy,
    'https': 'https://' + proxy
}
----使用代理ip注意点
1. 检查ip可用性，设置超时参数，删除延时久的
2. 代理ip质量在线检测网站
3. 准备代理ip池，随机选择ip使用。为防止单纯的随机导致部分ip使用次数过多，让使用次数较少的ip地址有更大的可能性被用到，可以：
 {"ip":ip,"times":0}->[{},{},{},{},{}],对这个ip的列表进行排序，
按照使用次数进行排序选择使用次数较少的部分ip，从中随机选择
 
--cookies和session
服务器和客户端的交互仅限于请求/响应过程，结束之后便断开，在下一次请求时，服务器会认为新的客户端。
为了维护他们之间的链接，让服务器知道这是前一个用户发送的请求，必须在一个地方保存客户端的信息。
Cookie：通过在 客户端 记录的信息确定用户的身份。
Session：通过在 服务器端 记录的信息确定用户的身份。
cookies:字典,一条条的数据
带上cookies能够请求到登录之后的界面
原则：不需要的时候尽量不使用，需要获取登录之后的页面，必须发送带cookies的请求
一套cookies和session识别为一个用户，所以可以组成cookies池

-session
requests提供的session类，来实现客户端和服务端的会话保持。
因为使用session会保存登录后生成的cookies，使用session再次登录，会自动携带之前的cookis访问。session提供的方法和requests一模一样
使用方法：
    1.实例化一个session对象
    2.让session发送get/POST请求
    session = requests.session()
    response = session.get(url, headers)

-使用session请求登录之后的网站的原理
    1. 实例化session对象
    2. 先使用session发送请求，成功登录网站，cookie会自动保存在session中
    3. 再使用session请求登陆之后才能访问的网站，session能够自动的携带登录成功时保存在其中的cookie，进行请求

-不发送post请求，使用cookie获取登录后的页面
1. cookie过期时间很长的网站
2. 在cookie过期之前能够拿到所有的数据，比较麻烦
3. 配合其他程序一起使用，其他程序专门获取cookie，当前程序专门请求页面

--获取登录后的页面的三种方式 
1. 实例化session，使用session发送post请求，再使用它自动保存的cookies获取登陆后的页面
2. headers中添加键cookies，值为cookies字符串。
cookies='''anonymid=j3jxk555-nrn0wh; _r01_=1; _ga=GA1.2.1274811859.1497951251;
 depovince=BJ; jebecookies=54f5d0fd-9299-4bb4-801c-eefa4fd3012b|||||;
  JSESSIONID=abcI6TfWH4N4t_aWJnvdw; ick_login=4be198ce-1f9c-4eab-971d-48abfda70a50; 
  p=0cbee3304bce1ede82a56e901916d0949; first_login_flag=1; 
  ln_hurl=http://hdn.xnimg.cn/photos/hdn421/20171230/1635/main_JQzq_ae7b0000a8791986.jpg; 
  t=79bdd322e760beae79c0b511b8c92a6b9; societyguester=79bdd322e760beae79c0b511b8c92a6b9;
   id=327550029; xnsid=2ac9a5d8; loginfrom=syshome; ch_id=10016; wp_fold=0'''
res = requests.get(url,headers={"User-Agent":"...","cookies":cookies}）
3. 在get请求方法中添加cookies参数，接收字典形式的cookie。
res = requests.get(url,headers={"User-Agent":"..."},cookies = {"key":"value","key2":"value2"...}）



2019年07月14日
"分析post请求和js操作"
--post请求的重点
1.post的数据的键名要确定，可以用选择工具查看键名。
2.post的实际请求地址。

--寻找post请求的url地址和post的数据
1.在elements的form表单中寻找action对应的url地址
    --post的数据是input标签中‘name’的值作为键，实际数据作为值的字典，post的url地址就是action对应的url地址
2.若form表单没有action对应的url地址，则通过抓包寻找登录的url地址
    --输入错误的密码，获得登录操作的实际网络请求
    --勾选perserve log（保存日志）功能，防止页面跳转找不到登录的url。
3.寻找post的数据，确定post提交了什么参数
    -参数不会变的，直接放入请求的post data中使用。
    -参数会变
        -参数在当前/其他响应中，通过先请求相应url获取参数（比如人人网的rkey）
        -参数通过js生成（个人想法：简单的通过python模仿实现，复杂的比如人人网的用户密码被js动态加密生成，该如何处理？？封装js代码？？）

--定位目标js
1.选中会触发js事件的按钮，点击elements->event listener，找到js
2.通过chrome中的'search all file'来搜索相应url中的关键字找到js
3.找到js后分析js中的操作：添加断点的方式来逐步分析js的操作（可结合console查询相关变量的值），再通过python来进行同样的操作得到js操作的结果，或其他更简单的方法。

--chrome开发者工具中的‘console-控制台’可以完成交互工作，可以检查js的操作（人人网中，输入用户名的变量名，console输出对应的值）

--requests的技巧
1.cookies对象转化为字典
'requests.utils.dict_from_cookiejar(response.cookies)'
# 字典转化为cookies对象：cookies_from_dict
实例：
>>response = requests.get('http://www.baidu.com')
>>response.cookies
>><RequestsCookieJar[Cookie(version=0, name='BDORZ', 
    value='27315', port=None, port_specified=False, domain=
    '.baidu.com', domain_specified=True, domain_initial_dot=True,
    ath='/', path_specified=True, secure=False, expires=1563189920, 
    iscard=False, comment=None, comment_url=None, rest={}, 
    rfc2109=False)]>
>>requests.utils.dict_from_cookiejar(response.cookies)
>>{'BDORZ': '27315'}

2.url地址解码
'requests.utils.unquote()'
# 编码quote
实例：
>>requests.utils.unquote(
    'https://tieba.baidu.com/f?kw=%E6%9D%8E%E6%AF%85')
>>'https://tieba.baidu.com/f?kw=李毅'

3.跳过SSL证书验证
'requests.get(url, verify=False)'

4.设置超时参数
'response = requests.get(url, timeout=10)'
时间内响应，继续往下执行，超过十秒未响应，直接报错

--简洁之道：
可以将发送请求获取响应封装为一个函数，还可以加入post,get等操作，重复调用
导入其他py文件的函数：xxx.py 的 yyy()
'from xxx import yyy'
导入同一工程下的函数，需要根据在哪个目录下执行程序，去使用绝对路径

--'try except'
A()函数里面有多个可能报错的原因，那么可以直接try A() 再 except
实例：
def A():
    ...
def B():
    try:
        xx = A():
    except:
        xx = None
    return xx
if __name__ == '__main__':
    ret = B()
    print(ret)

--retrying模块的retry方法
通过装饰器的方法使用，装饰一个函数
原理：重复执行所装饰的函数，并进行异常捕获，函数最多运行限制数次后，再向下执行
使用:'from retrying import retry'
# 最多运行3次
@retry(stop_max_attempt_number=3)
def A():
    ....

--安装第三方模块
1.pip install retrying
2.***.whl，安装方法，pip install ***.whl
3.下载源码解压，进入解压后的目录，python setup.py install

--简洁之道
a = 3 if 3>2 else 4



2019年07月15日
"提取数据"
--数据提取
简言，数据提取就是从响应中获取我们想要的数据的过程

--数据分类
1.结构化数据----结构清晰/规律明显----json/xml...处理方法：转化为python数据类型
2.非结构化数据----结构不清晰/规律不明显----html...处理方法：使用正则表达式/xpath

--json模块
JSON(JavaScript Object Notation-,javascript对象表示法)是一种轻量级的数据交换格式，使得人们很容易的进行阅读和编写，
同时也方便了机器进行解析和生成，适用于进行数据交互的场景，比如网站前后端之间的数据交互和传递。
    --由于把json数据转化为python数据类型再从中提取目标数剧很简单，所以爬虫中，如果目标数据存在json中,就尽量请求返回json的url。
    --如何找到返回json数据的url
        1. 切换手机界面尝试
        2. 使用fiddler抓包手机app（不推荐，因为app数据一般加密）

--寻找想要的url地址的方式
1. 筛选响应类型，但结果不准确，存在分类错误
2. 全局查找（推荐查找英文和数字，因为中文会被转码）
3. 遍历请求列表
  
--数据肯定在某个url响应里，部分数据直接在响应里，部分是通过js生成的，但js生成的一般是页码/价格等简单数据，数据多的一般不用js生成。

--jsonp
当遇到（'jsonp'+json）这种数据格式时，取json数据需要处理
1. 正则表达式
2. 进浏览器，直接把url地址里的’callback=jsonpn‘删除就行

--jsonview 插件
浏览器直接将json数据美化显示

--json.dump     编码     python-->json     dump-转储        # 详细文档：https://docs.python.org/zh-cn/3/library/json.html?highlight=loads#py-to-json-table   
1.json.dump(obj, fp, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)
使用 python-json转换表 将 obj 序列化为 JSON 格式的流并输出到 fp （一个支持 .write() 的 file-like object，即类文件对象）。
2.json.dumps(obj, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)
使用 python-json转换表 将 obj 序列化为 JSON 格式的 str。 其参数的含义与 dump() 中的相同。
注解：JSON 中的键-值对中的键永远是 str 类型的。当一个对象被转化为 JSON 时，字典中所有的键都会被强制转换为字符串。这所造成的结果是字典被转换为 JSON 然后转换回字典时可能和原来的不相等。换句话说，如果 x 具有非字符串的键，则有 loads(dumps(x)) != x。
>>> json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
>>>'["foo", {"bar": ["baz", null, 1.0, 2]}]'
>>> print(json.dumps({"c": 0, "b": 0, "a": 0}, sort_keys=True))
>>>{"a": 0, "b": 0, "c": 0}
>>> print(json.dumps({'4': 5, '6': 7}, sort_keys=True, indent=4))
>>>
{
    "4": 5,
    "6": 7
}

--json.load     解码     json-->python     load-加载
1.json.load(fp, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)
使用 python-json转换表  将 fp (一个支持 .read() 并包含一个 JSON 文档的 text file 或者 binary file) 反序列化为一个 Python象。
2.json.loads(s, *, encoding=None, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)¶
使用 python-json转换表 将s（包含JSON文档的str，bytes或bytearray实例）反序列化为一个 Python对象。 其参数与load（）的含义相同。
>>> import json
>>> json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
['foo', {'bar': ['baz', None, 1.0, 2]}]
>>> json.loads('"\\"foo\\bar"')
'"foo\x08ar'

--类文件对象
具有 read()或者 write()方法的对象就是类文件对象
'f = open('a.txt', 'r')',f 就是类文件对象

--使用json数据类型写入数据的好处
1.写入字符串有中文时，会自动转化成ASCII码写入，结果不清晰
解决办法：设置'ensure_asccii=False'
2.数据排版杂乱
解决办法：改变上下级间的缩进，可设置'indent = 2'
实例：
f.write(json.dumps(response), ensure_asccii=False, indent =2)
# json数据转化为python数据类型，可直接使用 str(response),但这样就不能设置ensure_asccii等操作，实现效果,

--写入数据到文件时，只能写入字符串，不能写字典/列表。
需要写入python 字典列表类型数据时，需使用dumps将字典转化为json类型数据，再将json类型数据写入文件或者 str(dic)
实例：
import json
>>> data = {'name': 'xiecl', 'age': 16}
>>> # Writing JSON data
... with open('data.json', 'w') as f:
...     json.dump(data, f)
...
>>> # Reading data back
... with open('data.json', 'r') as f:
...     data_back = json.load(f)
...
>>> data_back
{'name': 'xiecl', 'age': 16}

--json和python数据比较
json:       '{"name": "xiecl", "age": 16}'
python dict: {'name': 'xiecl', 'age': 16}

--json中的字符串都是双引号引起来的
1.如果不是双引号
    - eval：能实现简单的字符串和python类型的转化，
            但是只能处理简单数据，嵌套多层的不能处理
    - replace：把单引号替换为双引号
2.往一个文件中写入多个json字符串，不能json.loads()直接读取
    - 一行写一个json字符串，按照行来读取,readline

--json作用
python后端-->json-->js
python后端-->json-->安卓
1.Json在数据交换中起到了一个载体的作用，承载着相互传递的数据
为什么：因为python和php等语言不互通，不能互相识别理解
2.xml也能传递数据，Json和各种语言有一一对应的关系，但是xml没有，所以json更方便，使用广泛

--打开网站，出现部分数据后，出现加载动态图，说明部分数据是通过js生成的

--dict
与以连续整数为索引的序列不同，字典是以 关键字 为索引的，关键字可以是'任意不可变类型，通常是字符串或数字'。
如果一个元组只包含字符串、数字或元组，那么这个元组也可以用作关键字。但如果元组直接或间接地包含了可变对象，
那么它就不能用作关键字。列表不能用作关键字，因为列表可以通过索引、切片或 append() 和 extend() 之类的方法来改变。

--pprint
对输出进行格式化的处理，美化输出
'from pprint import pprint'
'pprint()'

-正则表达式
--定义：用事先定义好的一些特殊字符、及这些特定字符串的组合，组成一个
'规则字符串'，这个'规则字符串'用来表达对字符串的一种过滤逻辑。
--正则表达式更适用于小范围单个数据的查找，整片的数据更适合XPATH

--常用正则表达式的方法
re.compile()       编译
pattern.match()    从头找一个 
pattern.search()   找一个 
pattern.findall()  找所有
pattern.sub()      替换  

--re.DOTALL/re.S
'.'匹配任意除换行符'\n'外的字符，但在re.DOTALL/re.S模式下能匹配'\n'
实例：
re.findall('.','\n',re.DOTALL)
re.findall('.','\n',re.S)
-->['\n']

--r'.....'
忽略转义符，表达原始字符串



2019年07月17日
"xml"
-xml的了解
--为什么学习XPATH和LXML类库：
lxml是一款高性能的Python HTML/XML解析器，我们可以利用Xpath快速的
定位特定元素以及获取节点信息
--什么是XPATH：
XPATH（XML Path Language）是一门在HTML/XML文档中查找信息的语言，
可用来在HTML/XML文档中对属性和元素进行遍历

--XML和HTML
XML（可扩展标记语言）
设计目标：传输数据和储存数据，其焦点是数据的内容
HTML（超文本标记语言）
设计目标：显示数据以及如何更好显示数据

--XML的每个标签称之为节点/元素

--xpath节点选择语法
XPATH使用路径表达式来选取XML文档中的节点或者节点集。
这些路径表达式和常规的电脑文件系统中看到的表达式非常相似
1. nodename 选取此节点的所以子节点
2. /        从根节点选取
3. //       从匹配选择的当前节点选择文档中的节点，而不考虑他们的位置
4. .        选取当前节点
5. ..       选取当前节点的父节点
6. @        选取属性

--匹配范式
a/text()             获取a下的文本
a//text()            获取a下的所有标签的文本
//a[text()='下一页']  选择文本为‘下一页’的a标签

--@符号，表示属性，@A可用来获取属性/[@B='XX']定位属性
a/@href
//ul[@id="detail-list"]
//     在xpath最前面表示从当前html中任意位置开始选择
li//   表示的是li下任何一个标签

--可根据文本内容匹配
实例：
//a[text()='下一页>']
匹配所有节点下的a节点下文本='下一页>'的 
//a[@class='n']/@href
匹配所有节点下的属性class='n'的a节点的href属性

--个人理解
// 所有
/  当前

--XPATH语言 语法扩展
1.选择指定位置的节点
    1. [n] 表示选择匹配结果中的第n个结果，n从1开始，正向选择
    2. [last()] 最后一个，反向选择
    3. [last()-n] 倒数第n+1个
    4. [position()<n] 位置小于n的

2.查找某个特定的节点或者包含某个指定的值的节点
    1. //title[@lang]   选择所有拥有lang属性的title元素
    2. //title[@lang='eng']     选取所有lang属性的值为‘eng’的title元素
    3. /bookstore/book[price>35.0]   选取bookstore元素的所有book元素，且其中的price元素的值须大于35.0（用book的子节点price的值来修饰book）
    4. /bookstore/book[price>35.0]/title     选取bookstore元素中的book元素的title元素，且其中的price元素的值须大于35.0

3.选择未知节点
    1. *      匹配任何元素节点
    2. @*     匹配任何属性节点
    3. node() 匹配任何类型的节点
        例子：
        1. /bookstore/*        选取bookstore元素的所有子元素
        2. //*                 选取文档中的所有元素
        3. html/node()/meta/@* 选择html下面任意节点的meta节点的所有属性
        4. //title[@*]         选取所有带有属性的title元素

--选取若干路径‘|’  # 或操作
//book/title | //book/price 选取book元素的所有title，和price元素

/bookstore/book | //price   选取属于bookstore元素的book元素，和文档中所有的price元素

--注意
1.使用xpath对element进行分组，一般拟分组的上一级都是必须写的，否则容易出错。
2.使用xpath helper或chrome中的copy xpath都是从element中提取数据，
但是爬虫获取到的是url对应的响应，和elements不一样，
因为整个网页的element由所有url的响应组成，
所以，我们需要根据当前url对应的响应内容，来写XPATH
只有当目标响应返回的网页源码和element一样时，才能根据element直接写XAPATH，
可以通过开发者工具中每条网络请求的response和preview来快速检查数据是否存在目标url。



2019年07月18日
"lxml模块"
--使用入门
1. 导入lxml的etree库
   'from lxml import etree'
2. 利用etree.HTML()，将字符串转化为element对象，其能接收str和bytes类型的字符串.
3. element对象具有xpath的方法
   html = etree.HTML(text)
   html.xpath('xpath语句')

--etree.HTML(text)可以在转化过程可以自动修正代码，但是可能改错
    所以可以通过etree.tostring()查看修改后的html的样子，
    根据修改后的html字符串写XPATH
-------------------------------------------------------------
-----------------------lxml函数实例--------------------------
from lxml import etree
text = ''' <div> <ul> 
        <li class="item-1"><a>first item</a></li> 
        <li class="item-1"><a href="link2.html">second item</a></li> 
        <li class="item-inactive"><a href="link3.html">third item</a></li> 
        <li class="item-1"><a href="link4.html">fourth item</a></li> 
        <li class="item-0"><a href="link5.html">fifth item</a>  
        </ul> </div> '''
html = etree.HTML(text)
print(html)

# 分组，根据li标签进行分组，对每一组继续写xpath
ret3 = html.xpath("//li[@class='item-1']")
print(ret3)
for i in ret3:
    item=  {}
    item["title"] = i.xpath("./a/text()")[0] if len(i.xpath("./a/text()"))>0 else None
    item["href"] = i.xpath("./a/@href")[0] if len( i.xpath("./a/@href"))>0 else None
    print(item)
-------------------------------------------------------------
-----------------------lxml函数实例--------------------------

--大段字符可以用三个'''...'''，以防止字符内有双引号'或者''导致错误',"()"里面不能有""，会报错

--lxml提取页面数据的思路
1. 先对数据分组，即取出一个包含分组节点的element列表
2. 遍历element列表，再分别取其中每一组再进行数据的提取，不会造成数据的对应错乱
3. 数据分组后再处理，条理清晰简单，而且可以用'.'表示选择当前节点，省略xpath语句的前部分

--xpath语句的包含
//div[contains(@class,'key')] class属性包含'key'关键字的所有div节点

--列表的内容扩展
表格中的 s 是可变序列类型的实例，t 是任意可迭代对象（t不能为int），而 x 是符合
对 s 所规定类型与值限制的任何对象 (例如，bytearray 仅接受满足 0 <= x <= 255 值限制的整数)。
1.s.extend(t) 或 s += t         
用 t 的'内容'扩展 s (基本上等同于 s[len(s):len(s)] = t)
2.区别于s.append(x)
将 x 添加到序列的末尾 (等同于 s[len(s):len(s)] = [x])
实例：
>>> a=[1,2]
>>> a.extend([4,5])
>>> a
[1, 2, 4, 5]
>>> a.append([4,5])
>>> a
[1, 2, 4, 5, [4, 5]]

--字符串拼接
str.join(iterable)
返回一个由 iterable 中的字符串拼接而成的字符串。
如果 iterable 中存在任何非字符串值包括bytes对象则会引发TypeError。调用该方法的字符串将作为元素之间的分隔。可用于csv文件存储
实例：
>>> ','.join('你好啊中国')
'你,好,啊,中,国'
>>> ','.join(['你好啊','中国'])
'你好啊,中国'
>>> ','.join(['你好啊','中国','人'])
'你好啊,中国,人'

>>> s = 'hello'
>>> s.join('1.')
'1hello.'
>>> s.join('a3.')
'ahello3hello.'
>>> s.join('123.')
'1hello2hello3hello.'

--csv
逗号分隔符文件
按照下面的格式写入保存后，可以用excel打开
1，2，4
sas,aw,dwad



2019年07月20日
"实现tieba_spider"
--知识点
1. 编码问题
实例：
html_str = etree.HTML(response.content)
# 和decode('gbk')结果一样
str = etree.tostring().html_str.decode()
with open('str.html', 'w') as f:
    f.write(str)
html中出现：&#36276;&#30528;&#30561 

&#36276;&#30528;&#30561
unicode-->中文
趴着睡

趴着睡
中文-->unicode
\u8db4\u7740\u7761

趴着睡
中文-->utf-8
&#x8DB4;&#x7740;&#x7761;

\u8db4\u7740\u7761
unicode-->中文
趴着睡

2. 字符串切片
'str.split(str="", num=string.count(str))'
split() 通过指定分隔符对字符串进行切片，如果参数 num 有指定值，则分隔成 num+1 个子字符串。返回切片后的字符串列表。
参数：
str -- 分隔符，默认为所有的空字符，包括空格、换行(\n)、制表符(\t)等。
num -- 切片次数。默认为 -1, 即分隔所有。
实例：
str = "Line1-abcdef \nLine2-abc \nLine4-abcd";
print str.split( );       # 以空格为分隔符，包含 \n，分隔所有
>>>['Line1-abcdef', 'Line2-abc', 'Line4-abcd']
print str.split(' ', 1 ); # 以空格为分隔符，分隔成两个
>>>['Line1-abcdef', '\nLine2-abc \nLine4-abcd']

3. 批量命名，解决难看多余的多行命名
a,b,c = 2,3,4

4. 当需要提取的目标数据在字符串尾部或者头部，可以直接使用字符串切片

5. 数据存储应尽量简单，层次少
content = {'title_pic': ['url_a', 'url_b'], 'title': '你好', 'comment_pic': ['url_a', 'url_b', 'url_c']}
而非：
content = [{'title_pic': ['url_a', 'url_b']}, {'title': '你好'}, {'comment_pic': ['url_a', 'url_b', 'url_c']}]

6. 遍历url可以使用三种方式：递归循环请求下一页/构造url列表/起始页+1

--bug
1. 注意xpath语句的书写问题，特别是空格，容易遗漏，少了空格直接返回空

   实际原因：从element复制class="tl_shadow tl_shadow_new "进xpath
   语句，发生引号错误，遂删除双引号，误删空格，导致xpath返回空列表
   结果：四个小时没解出，痛心疾首，心情忧郁，哈哈哈哈哈哈哈哈哈
   解bug过程：
   考虑解码问题，在编码转码和中文编码上排查了很久，卒
   考虑修正产生错误，tree.tostring检查修正后的element，却又不知
   etree.tostring(html_str)可直接使用etree.tostring(html_str).decode()
   将bytes类型转化为str类型，导致查看element源码花费较长时间，卒
   因xpath('//li')能返回结果，考虑过被反爬或目标element不在目标响应中，卒
   最终：检查element源码，复制路径，出现了结果，才发现是少了空格。。。。
   反思：应该先直接检查根源的出错点，也就是xpath语句，这样就不会浪费这么多时间
   url_list = html_str.xpath("//li[@class='tl_shadow tl_shadow_new ']")

2. xpath helper 是辅助工具，写xpath语句应该根据etree.HTML()处理后
   得到的element源码书写，网页上element匹配的结果跟实际结果是不一定相符的
   因为目标element可能不在响应中，或者通过js生成，或者看到的数据实际是其他
   位置调用而来,或者目标数据的属性被修改或隐藏
   当实际结果和 xpath helper 的匹配结果不一致时，应该先检查xpath语句是否
   有错误，无错说明不是xpath语句没写清楚，而是开发者特殊处理过，应使用
   etree.tostring检查源码中需要的数据是否存在
   '''其实在确定爬取目标后，首要
   目的是确定数据是否存在响应中，所以应该先直接用google开发工具，检查目标
   url的response，搜索查看目标数据是否存在于response，检查存放数据的节点
   和属性是否被修改,否则徒劳'''
   数据的节点关系是否被修改，
   当数据不存在时，就可以通过全局搜索，元素搜索，资源搜索，抽丝剥茧
        实例：tieba_pro.py
        element中：
        value="1/3752"
        xpath无法提取，html源码搜索value等关键词没有，浏览器全局搜索发现：
        value="' + t.currentPage + "/" + t.totalPage + '"
        进而得到totalPage数据
        反思：排查bug思路和流程很重要，而不是瞎搞瞎猜。
        应该先直接用Google开发者模式，在response中搜索目标数据值'3752'，
        先确定目标数据是否存在源码中，若不在，再搜索对应的关键词'value'，
        查找目标数据的来源

3.  xpath语句要精确详细，少用//，一次性写到位，目标节点的父节点一定要写，防止后面导致广泛匹配，再去重写

4.  检查经etree.HTML转化并修正后的html样式，是用来确认转化后的节点等是否被修改
    但是检查目标数据是否存在，节点属性是否存在，最高效直接的办法是
    在googl 开发者工具的response中，查看目标数据是否存在响应中，查看节点
    属性对应关系是否被修改和隐藏，若一致，就可以根据element去书写xpath语句
    而不是直接去根据element写语句，发现错误再自己生成源码，再去检查数据是否存在那样极不方便和多余
        # 检查经etree.HTML转化并修正后的html样式
        # str = etree.tostring(html_str).decode()
        # with open('str.html', 'w') as f:
        #    f.write(str)

        # 直接这样写入字符串，显示效果类似text文件，会将body的各节点信息
        # 用一行显示，不换行，影响浏览
        # with open('000.html', 'w', encoding='utf-8') as f:
        #    f.write(response.content.decode())

5.  其实很多地方并不需要随时空格，影响美观，这个要注意

--实现爬虫的详细流程
1.准备url（应先确定目标数据是否存在响应中）
  - 准备start_url
    - url地址规律不明显，总数不确定
    - 通过代码提取下一页的url
      - 下一页url地址存在于节点中，则使用xpath直接匹配
      - 需要自行构造url地址，在当前响应中搜索关键参数（当前页、总页码），根据关键参数构造url
  - 准备url_list
    - 页码总数明确
    - url地址规律明显

2.发送请求，获取响应
  - 添加随机的User-Agent,反反爬虫
  - 添加随机的代理ip，反反爬虫
  - 在对方判断出我们是爬虫之后，应该添加更多的headers字段，包括cookie
  - cookie的处理可以使用session来解决
  - 准备一堆能用的cookie，组成cookie池
    - 如果不登录
      - 准备刚开始能够成功请求对方网站的cookie，即接收对方网站设置在response的cookie
      - 下一次请求的时候，使用之前的列表中的cookie来请求
    - 如果登录
      - 准备多个账号
      - 使用程序获取每个账号的cookie
      - 之后请求登录之后才能访问的网站，随机的选择cookie

3.提取数据
  - 确定数据位置
    - 如果数据在当前的url地址响应中
      - 拟提取的是列表页的数据
        - 直接请求列表页的url地址，不用进入详情页
      - 提取的是详情页的数据
        - 1. 确定详情页url
        - 2. 发送请求
        - 3. 提取数据
        - 4. 返回

    - 如果数据不在当前的url地址中
      - 在其他的响应中，寻找数据的位置
        - 0. 使用chrome的search all file，搜索数字和英文
        - 1. 从network中从上往下找
        - 2. 使用chrome中的过滤条件，选择出了js,css,img之外的按钮
  - 进行数据提取
    - xpath,从html中提取整块的数据，先分组，之后遍历每一组再提取
    - re，提取html中不在节点中的数据（max_time,price）
    - json

4.保存
  - 保存在本地，text,json,csv
  - 保存在数据库

--后续爬虫代码的建议
1.尽量减少请求次数
    1. 能抓列表页就不抓详情页
    2. 保存获取到的html页面，供查错和重复请求使用
2.关注网站的所有类型的页面
    1  web页面，触屏版页面
    2. H5页面
    3. APP 
3.多伪装
    1. 动态的UA
    2. 代理ip 
    3. 不使用cookie 
4.使用多线程分布式
    在不被ban的前提下尽可能的提高速度



2019年07月25日
"爬取动态html数据"
--Selenium 和 PhantomJS
1.Selenium
（模块，主要功能控制浏览器，需要下载专用浏览器）
Selenium是一个Web的自动化测试工具，最初是为网站自动化测试而开发的，
Selenium可以直接运行在浏览器上，它支持所有主流的浏览器（包括PhantomJS
无界面的浏览器），可以接受命令，控制浏览器进行点击按钮，前进后退，让
浏览器自动加载页面，获取需要的数据，和页面截屏。

2.PhantomJS
PhantomJS是一个基于Webkit的‘无界面(headless)浏览器’，它会把网站加载到内存
并执行页面上的JavaScript，无界面，适用于服务器端

3.chromedirver/phantomjs下载    # driver-驱动器
    1. 下载地址：
    chromedirver下载地址:https://npm.taobao.org/mirrors/chromedriver
    phantomjs下载地址:http://phantomjs.org/download.html
    2.Windows安装：
    下载好，进入 Chrome()源码中将chromedriver的路径指定，运行程序依然报错，
    报错涉及firefox和init，将webdriver的init中的firefox删除，报错消失
    注意Chromedirver和Chrome必须同一个版本

4.chromedriver
from selenium import webdriver
import time

# 实例化一个浏览器（注意大写C)  
driver = webdriver.Chrome()
# 发送请求
driver.get("http://www.baidu.com")
# 元素定位的方法(注意只有input元素有send_keys()方法)
driver.find_element_by_id("kw").send_keys("python")
# 元素的点击方法
driver.find_element_by_id('su').click()
time.sleep(7)
# 退出浏览器
driver.quit()

5.phantomjs
from selenium import webdriver
import time
# 实例化一个浏览器（注意大写C)
driver = webdriver.PhantomJS()
# 发送请求
driver.get("http://www.baidu.com")
# 设置窗口大小
driver.set_windows_size(1920,1080)
# 最大化窗口
driver.maximize.windows()
# 保存截屏
driver.save_screenshot("./baidu.png")
# 获取cookie
driver.get_cookies()
# 获取html字符串,,获取的是elements内容
print(driver.page_source)
# 输出当前链接
print(diver.current_url)
# 退出当前页面
driver.close()
# 退出浏览器
time.sleep(7)
driver.quit()

--页面元素定位
element和elements区别:
elements返回一个webdriver-element对象的列表，若xpath语句错误，则返回空列表
element返回一个webdriver-element对象，若xpath语句错误，则报错
.get_attribute() 获取属性
.text            获取文本

--find_elements_by_xpath
根据xpath寻找element
-----------------------------------------------------------------------
------------------------------web_driver-------------------------------

from selenium import webdriver
import time
from pprint import pprint

# 实例化一个浏览器（注意大写C)
driver = webdriver.Chrome()
# 发送请求
driver.get("https://tieba.baidu.com/f?kw=%E6%9D%8E%E6%AF%85%E5%90%A7")
# 根据xpath寻找所有 class=' j_thread_list clearfix'] 的li节点的webdriver-element
# 起初因为//ul[@id='thread_list']/li匹配的第一个li节点下并无 div[@class='threadlist_text pull_left'] 节点
# 导致后面的程序，对一个webdriver-element执行xpath语句时报错
# 再次筛选后，后面的程序都能正常运行
ret1 = driver.find_elements_by_xpath("//ul[@id='thread_list']/li[@class=' j_thread_list clearfix']")
pprint(ret1)
print("**0", type(ret1))
print('*'*100)
try:
    for ele in ret1:
        print("**1",type(ele))
        # 之前前部分用的是./div ，因为目标div节点并不是在li节点的下一级，而是下很多级别，所有匹配错误，返回空列表
        a = ele.find_element_by_xpath(".//div[@class='threadlist_text pull_left']").text

        """
        对webdriver-element对象使用get_attribute()方法，获取节点属性的值
        a = ele.find_element_by_xpath(".//span[@class='tb_icon_author ']|.//span[@class='tb_icon_author no_icon_author']").get_attribute("title")
        """
        print(a) if len(a)>0 else print("-"*200)
        print("**2",type(a))
        print("**3",len(a))
finally:
    # 退出浏览器
    time.sleep(10)
    driver.quit()
-----------------------------------------------------------------------
------------------------------web_driver-------------------------------

--find_elements_by_link_text()
根据链接文本寻找element
根据的是网页上描述或者指代链接的文字
p = driver.find_element_by_link_text("下一页>").get_attribute("href")

--find_elements_by_partial_link_text()
根据局部链接文本寻找element（partial 局部的）
p = driver.find_elements_by_partial_link_text("下一")

--find_elements_by_tag_name()
根据标签名寻找element
p = driver.find_elements_by_tag_name("div")

--find_elements_by_class_name()
根据class名寻找element

--find_elements_by_CSS_selector

--switch_to.frame()
按索引、名称或webelement将焦点切换到指定的框架。（frame 框架）
参数：要切换到的窗口的名称，表示索引的整数，
或者一个要切换到的(i)frame的webelement
使用方法：
driver.switch_to.frame('frame_name')
driver.switch_to.frame(1)
driver.switch_to.frame(driver.find_elements_by_tag_name("iframe")[0])

--cookies的用法：
{cookie["name"]:cookie["value"] for cookie in driver.get_cookies()}

--selenium使用注意
- 获取文本和获取属性
  - 先定位到元素，然后调用`.text`或者`get_attribute`方法
- selenium获取的页面数据是浏览器中elements的内容
- 如果页面中含有iframe、frame，需要先调用switch_to.frame()切换到frame中才
  能定位frame中的元素
- selenium请求第一页的时候回等待页面加载完了之后在获取数据，但在点击翻页之后，
  会默认直接开始获取数据，此时可能会报错，因为数据还没有加载出来，需要先time.sleep()
- selenium中find_element_by_class_name只能接收一个class对应的一个值，
  不能传入多个



2019年07月30日
"验证码识别"
----Base64编码
Base64是网络上最常见的用于传输8Bit字节码的编码方式之一，Base64就是一种基于
64个可打印字符来表示二进制数据的方法。
Base64编码是从二进制到字符的过程，可用于在HTTP环境下传递较长的标识信息。采用
Base64编码具有不可读性，需要解码后才能阅读。

应用:编码/解码
1. base64码直接编解码
# base64码解码为图片
# 多行字符串需要使用'''<str>'''
import base64
src = '''data:image/gif;base64,R0lGODlhMwAxAIAAAAAAAP///yH5BAAAAAAALAAAAAAzADEAAAK8jI+pBr0PowytzotTtbm/DTqQ6C3hGX
ElcraA9jIr66ozVpM3nseUvYP1UEHF0FUUHkNJxhLZfEJNvol06tzwrgd
LbXsFZYmSMPnHLB+zNJFbq15+SOf50+6rG7lKOjwV1ibGdhHYRVYVJ9Wn
k2HWtLdIWMSH9lfyODZoZTb4xdnpxQSEF9oyOWIqp6gaI9pI1Qo7BijbF
ZkoaAtEeiiLeKn72xM7vMZofJy8zJys2UxsCT3kO229LH1tXAAAOw=='''
data = '''R0lGODlhMwAxAIAAAAAAAP///yH5BAAAAAAALAAAAAAzADEAAAK8jI+pBr0PowytzotTtbm/DTqQ6C3hGX
ElcraA9jIr66ozVpM3nseUvYP1UEHF0FUUHkNJxhLZfEJNvol06tzwrgd
LbXsFZYmSMPnHLB+zNJFbq15+SOf50+6rG7lKOjwV1ibGdhHYRVYVJ9Wn
k2HWtLdIWMSH9lfyODZoZTb4xdnpxQSEF9oyOWIqp6gaI9pI1Qo7BijbF
ZkoaAtEeiiLeKn72xM7vMZofJy8zJys2UxsCT3kO229LH1tXAAAOw=='''
content = base64.b64decode(data)
f = open("src.png","wb")
f.write(content)
print(content)
-->
b"GIF89a3\x001\x00\x80\x00\x00\x00\x00\x00\xff\xff\xff!\xf9\x04\x00\x00\x00\x00\x00,\x00\x00\x00\x003\x001\x00\x00\x02\xbc\x8c\x8f\xa9\x06\xbd\x0f\xa3\x0c\xad\xce\x8bS\xb5\xb9\xbf\r:\x90\xe8-\xe1\x19q%r\xb6\x80\xf62+\xeb\xaa3V\x937\x9e\xc7\x94\xbd\x83\xf5PA\xc5\xd0U\x14\x1eCI\xc6\x12\xd9|BM\xbe\x89t\xea\xdc\xf0\xae\x07Km{\x05e\x89\x920\xf9\xc7,\x1f\xb34\x91[\xab^~H\xe7\xf9\xd3\xee\xab\x1b\xb9J:<\x15\xd6&\xc6v\x11\xd8EV\x15'\xd5\xa7\x93a\xd6\xb4\xb7HX\xc4\x87\xf6W\xf286he6\xf8\xc5\xd9\xe9\xc5\x04\x84\x17\xda29b*\xa7\xa8\x1a#\xdaH\xd5\n;\x06(\xdb\x15\x99(h\x0bDz(\x8bx\xa9\xfb\xdb\x13;\xbc\xc6h|\x9c\xbc\xcc\x9c\xac\xd9Ll\t=\xe4;m\xbd,}m\\\x00\x00;"

# 图片解码为base64码
p = open("src.png", "rb")
b = p.read()
print(b)
-->
b"GIF89a3\x001\x00\x80\x00\x00\x00\x00\x00\xff\xff\xff!\xf9\x04\x00\x00\x00\x00\x00,\x00\x00\x00\x003\x001\x00\x00\x02\xbc\x8c\x8f\xa9\x06\xbd\x0f\xa3\x0c\xad\xce\x8bS\xb5\xb9\xbf\r:\x90\xe8-\xe1\x19q%r\xb6\x80\xf62+\xeb\xaa3V\x937\x9e\xc7\x94\xbd\x83\xf5PA\xc5\xd0U\x14\x1eCI\xc6\x12\xd9|BM\xbe\x89t\xea\xdc\xf0\xae\x07Km{\x05e\x89\x920\xf9\xc7,\x1f\xb34\x91[\xab^~H\xe7\xf9\xd3\xee\xab\x1b\xb9J:<\x15\xd6&\xc6v\x11\xd8EV\x15'\xd5\xa7\x93a\xd6\xb4\xb7HX\xc4\x87\xf6W\xf286he6\xf8\xc5\xd9\xe9\xc5\x04\x84\x17\xda29b*\xa7\xa8\x1a#\xdaH\xd5\n;\x06(\xdb\x15\x99(h\x0bDz(\x8bx\xa9\xfb\xdb\x13;\xbc\xc6h|\x9c\xbc\xcc\x9c\xac\xd9Ll\t=\xe4;m\xbd,}m\\\x00\x00;"
c = base64.b64encode(b)
print(c)
-->
b'R0lGODlhMwAxAIAAAAAAAP///yH5BAAAAAAALAAAAAAzADEAAAK8jI+pBr0PowytzotTtbm/DTqQ6C3hGXElcraA9jIr66ozVpM3nseUvYP1UEHF0FUUHkNJxhLZfEJNvol06tzwrgdLbXsFZYmSMPnHLB+zNJFbq15+SOf50+6rG7lKOjwV1ibGdhHYRVYVJ9Wnk2HWtLdIWMSH9lfyODZoZTb4xdnpxQSEF9oyOWIqp6gaI9pI1Qo7BijbFZkoaAtEeiiLeKn72xM7vMZofJy8zJys2UxsCT3kO229LH1tXAAAOw=='

2. 二进制 文件 编码解码
# base64.encode/decode(input,output)
# input,output 为二进制文件
import base64
# base64码解码为图片
a = open("a.text", "rb")
b = open("b.png", "wb")
base64.decode(a,b)
# 图片编码为base64码
f = open('ppp.png', 'rb')
p = open('ppp.text', 'wb')
base64.encode(f, p)

----Data URI的内嵌式图片
格式：
data:image/png;base64,(+base64码)
在RFC2397中定义的Data URI scheme，目的是将一些小的数据，直接嵌入到网页中，
从而不用再从外部文件载入，不需要再从服务器加载，节省了HTTP连接，起到加速网页
的作用。
传统的图片HTML是这样用的：
img src="images/image.png"

----重点
验证码的识别
- url不变，验证码不变
  - 请求验证码的地址，获得响应，识别

- url不变，验证码变(导致：登陆时显示一验证码，再次请求url获取验证码后验证码改变)
  - 思路：服务器发送验证码到用户的时候，会将用户的识别信息和验证码进行一个对应，
  之后，在用户发送post请求的时候，会将post请求中的验证码和当前用户真正的存储在
  服务器端的验证码对比是否相同
  所以我们的目的就是，保证服务器保存的识别信息和发送post请求时的识别信息的一致

  - 服务端登录，遇到验证码
    - 1.实例化session
    - 2.使用seesion请求登录页面，获取验证码的地址
    - 3.使用session请求验证码，识别
    - 4.使用session发送post请求

  - selenium登录，遇到验证码
    - url不变，验证码不变，同上
    - url不变，验证码会变
      - 1.selenium请求登录页面，拿到验证码url
      - 2.获取登录页面driver中已保存的cookie，requests模块带上cookie发送
          获取验证码的请求，识别验证码
      - 3.selenium输入验证码，点击登录

----字典的key和value
in:
dic = {22: 'q', 33: 't'}
for key in dic:
    print(key)
    print(dic[key])  
out:
22
q
33
t

----字典的赋值
in：
files = {'file': 'getimage.jpg'}
for key in files:
    print(key)
    print(files[key])
    files[key] = open(files[key], 'rb')
    print(key)
    print(files[key])
    print(files)
out：
file
getimage.jpg
file
<_io.BufferedReader name='getimage.jpg'>
{'file': <_io.BufferedReader name='getimage.jpg'>}

----从其他py文件调用 指定类/函数/*
# 从spider文件夹的YDMDemo.py文件调用其中的YDMHttp类
from spider.YDMDemo import YDMHttp
r = YDMHttp()
# 调用类中的方法
result = r.decode("getimage.jpg", 1004, 60)
print(result)

--tesseract
----定义
Tesseract是一个将图像翻译成文字的OCR库(光学文字识别，Optical Character Recognition)
----安装
终端安装：
sudo apt-get install tesseract-ocr
在python中调用Tesseract：
pip install pytesseract
----使用 
在终端中：
tesseract test.jpg <储存文件名前缀>
在python代码中：
import pytesseract
from PIL import Image
image = Image.open(<文件名>)
pytesseract.image_to_string(image)



"斗鱼爬虫实现"
-----------------------------------------------------------------------
-------------------------使用selenium完成斗鱼爬虫-------------------------
# coding=utf-8
from selenium import webdriver
import time

class DouyuSpider:
    def __init__(self):
        self.start_url = "https://www.douyu.com/directory/all"
        self.driver = webdriver.Chrome()

    # 获取内容使用selenium执行缓慢，可以结合其他方法实现
    # 先用driver.page_source获取页面内容,再用lxml的etree.HTML()方法转化为element对象，再使用xpath
    def get_content_list(self):
        li_list = self.driver.find_elements_by_xpath("//ul[@id='live-list-contentbox']/li")
        content_list = []
        for li in li_list:
            item = {}
            item["room_img"]=li.find_element_by_xpath(".//span[@class='imgbox']/img").get_attribute("src")
            item["room_title"] = li.find_element_by_xpath("./a").get_attribute("title")
            item["room_cate"] = li.find_element_by_xpath(".//span[@class='tag ellipsis']").text
            item["anchor_name"] = li.find_element_by_xpath(".//span[@class='dy-name ellipsis fl']").text
            item["watch_num"] = li.find_element_by_xpath(".//span[@class='dy-num fr']").text
            print(item)
            content_list.append(item)
        # 获取下一页的元素
        next_url = self.driver.find_elements_by_xpath("//a[@class='shark-pager-next']")
        next_url = next_url[0] if len(next_url)>0 else None
        return content_list,next_url

    def save_content_list(self,content_list):
        pass


    def run(self):  # 实现主要逻辑
        # 1.start_url
        # 2.发送请求，获取响应
        self.driver.get(self.start_url)
        # 3.提取数据，提取下一页的元素
        content_list,next_url = self.get_content_list()
        # 4.保存数据
        self.save_content_list(content_list)
        # 5.点击下一页元素，循环
        while next_url is not None:
            next_url.click()
            time.sleep(3)
            content_list,next_url = self.get_content_list()
            self.save_content_list(content_list)


if __name__ == '__main__':
    douyu = DouyuSpider()
    douyu.run()
-----------------------------------------------------------------------
------------------------使用selenium完成斗鱼爬虫--------------------------



2019年07月31日
"scrapy"


"scrapy的概念"
--为何学习scrapy
requests和selenium能解决90%的爬虫需求，而scrapy是为了让爬虫更快更强，也能减少代码量

--scrapy的概念
scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架，只需要实现少量代码，能够快速抓取。
scrapy使用了Twisted异步网络框架，可以加快下载速度。
1.框架：解决特定需求下的所有功能，包含很多解决小问题的模块
2.异步和非阻塞的区别
阻塞和非阻塞指的是得到函数返回结果前进程的状态，若得到结果前，进程处于等待状态，则是阻塞状态，
若函数调用后，进程不等待结果，而是直接调用其他函数，则是非阻塞状态。
同步异步指的是过程，当完成整件事时，都是阻塞状态，则是同步的过程。当完成整件事时，都是非阻塞状态，则是异步的过程。
（异步类似做菜，在做每个菜的等待过程去准备下一个菜，就是异步的过程）


"scrapy入门"
----scrapy的流程（流程图和各模块作用的图片存在spider文件夹）
1.创建一个scrapy项目
scrapy startproject <mySpider（项目名）>
（诸如此类，都是在终端操作，而非ipython）
2.生成一个爬虫
cd <mySpider（项目名）>
scrapy genspider <itcast（爬虫名）> <itcast.cn（允许爬取范围）>
# gen-generate(生成)
3.提取数据
完善spider，使用xpath等方法
4.保存数据
pipeline中保存数据

----Scheduler调度器
存放requests对象，有url/hearder/post数据等

----Scrapy Engine
Scrapy Engine负责调度所有数据，Scrapy Engine的存在让四个模块不相互
联系，之间的数据不直接传递，而是经过Scrapy Engine传递，达到解耦目的，
容错率更高

----运行爬虫
scrapy crawl <itcast（爬虫名）>
运行前需要cd到爬虫项目下，再执行命令（crawl抓取/爬行）

----提取字符串需要调用.extract()方法 
li_list = response.xpath("//div[@class='tea_con']//li").extract()
返回一个列表，匹配不到值时返回空列表
li_list = response.xpath("//div[@class='tea_con']//li").extract_first()
提取列表的第一条数据，匹配不到值时返回None
# 使用extract()方法获取的数据为Selector对象,返回的像列表，但不是列表
# 是scrapy定义的特殊类型，如下
>>[<Selector xpath="//div[@class='tea_con']//li//h3/text()" data='朱老师'>,<...>,...]


"pipeline使用"
----pipeline
将爬虫获得的数据传递给pipeline，需要在setting文件将ITEM_PIPELINES开启
，然后在爬虫中使用yield <item（数据名）>，将数据传递给pipeline，
yield的数据只能是Request, BaseItem， dict or None
ITEM_PIPELINES = {'myspider.pipelines.MyspiderPipeline': 300,}
ITEM_PIPELINES是字典，key为项目名.文件名.类名，value为权重，指距离引擎
的远近,ITEM_PIPELINES中可以定义多个pipeline，数据会先经过距离近的ITEM_P
IPELINES。注意所有pipeline需要返回值，否则后面的pipeline和存储阶段无数据。
ITEM_PIPELINES = {
   'myspider.pipelines.MyspiderPipeline': 300,
   'myspider.pipelines.MyspiderPipeline1': 200,
}

----为什么使用多个pipeline
1.一个spider的数据，可使用多个pipeline不同的操作/存入不同的数据库
2.一个项目里，可生成多个spider，每个都在spider文件里，每个spider可以爬不
同的网站，可以在一个或多个pipeline中处理不同网站的不同数据。 
    --一个pipeline：可以在每个spider的item中添加一个值["come_from"："jd/tb"]
      然后在pipeline中，使用if/elif/else item["come_from"]==判断处理
    --多个pipeline：
        1.item中添加值["come_from"："jd/tb"]，每个类使用if item["come_from"]
          判断进行相应处理x
        2.不添加值，使用spider.name
          '''class MyspiderPipeline(object):
              def process_item(self, item, spider):'''
          process_item()的参数spider是传入的spider类，使用spider.name方法
          可以获取传入的spider类的name，再通过if spider.name==判断处理

----为什么要使用yield
让整个函数变成一个生成器，变成generator（生成器）有什么好处？
每次遍历的时候挨个读到内存中，不会导致内存的占用量瞬间变高，range同理

----实现储存
在pipeline的process_item()函数下实现
import json
with open('temp.text','a')as f: 
    json.dump(item,f,ensure_asccii=False,indent=2)

----open/close_spider(self,spider)函数
import json
class JsonWriterPipeline(object)

    """
    1.可以将建立数据库的连接放open，断开连接放close
    2.可以存储数据,注意使用文件存储数据时，文件close后数据才会保
      存成功，若未close文件之前程序报错停止，则写入失败，使用数据
      库不会存在这种问题
    3.也可以在open中定义属性/添加内容，然后爬虫中使用self.hello调用
    """
    def open_spider(self,spider):  # 在爬虫开启时执行，仅一次
        self.file = open(spider.settings.get("SAVA_FILES","./temp.json"),"w")
        spider.hello = "world"
    def close_spider(self,spider):  # 在爬虫关闭时执行，仅一次
        self.file.close()
    def process_item(self,item,spider):
        line = json.dumps(dict(item) + "\n")
        self.file.write(line)
        return item  # 不return的情况下，下一个pipeline获取不到该item


"logging模块"
logging模块是内嵌的生成日志的模块
使用log的好处是可以将输出和log信息保存到本地，不要盯着输出结果，方便
检查，也能在程序很多时，清楚显示某文件某行代码输出的内容和log

----log等级：dubug/info/warning/error
----logging模块的使用：
1.普通项目中
    import logging
    logging.basicConfig('...') # 设置日志输出样式，网查,可定义存储位置
    logger = logging.getLogger(__name__) # 实例化一个logger
    logger.warning('....') # 输出log
    任何py文件中调用logger即可使用
    from log_a import logger
2.scrapy中
  - 先settings中设置LOG_LEVEL="WARNING" # 表示只输出log等级高于等于WARNING的log
  - 输出log
      - 不显示log位置
        import logging 
        logging.warning(item) # logging.info()
        使用logging模块打印item的内容，输出中[root]表示存储路径
      - 显示log位置
        import logging 
        logger = logging.getLogger(__name__)
        logging.warning(item) # logging.info()
        # 实例化logger，传入参数__name_实现将py文件名记录，在输出log
        # 时，能知道log位置
  - 保存log到本地
    在settings中设置LOG_FILE="./log.log"  # 设置日志保存的位置，设置会后终端不显示日志内容


----Pycharm快捷键
代码块缩进:选中+tab
代码块左移:选中+tab+shift
注释:ctrl+/
切到下一行行首:shift+enter
切到上一行行首:ctrl+enter
全文添加空格:ctrl+alt+l 
剪切:ctrl+x
删除:ctrl+y
格式化：ctrl+alt+l 

----Python IDLE或shell中切换路径
在Python自带的编辑器IDLE中或者python shell中不能使用cd命令，
跳到目标路径方法是,使用os包下的相关函数实现路径切换功能。
import os  
os.getcwd() #获取当前路径  
os.chdir("D:\\test") #跳到目标路径下,单引号、双引号都可以 

----Pycharm中更新pip
pip install --upgrade pip

----win32api
ModuleNotFoundError: No module named 'win32api'
是一个缺少依赖包的错误，好像是发生在Windows系统中，
只要给python装个库：pypiwin32。
pip install pypiwin32

----字典批量赋值
>>> item = dict(a="2",b="1",c="0")
>>> item
{'a': '2', 'b': '1', 'c': '0'}


"构造请求"
scrapy.Request()能构建一个requests，同时指定参数
scrapy.Request(url,
[callback,method="get",headers,body,cookies,meta,dont_filter=False])

----相关参数
1.cookies在scrapy中不能放到headers中
2.meta:实现在不同的解析函数(parse就是)中传递数据，meta会携带部分信息，
  比如下载延迟，请求深度
3.dont_filter=True 不过滤（scrapy默认有url去重的功能）让scrapy的去重
  不过滤当前url，对需要重复请求的url有重要用途，比如一直更新的网页
4.UA在setting中定义
-----------------------------------------------------------------------
----------------------使用scrapy完成腾讯招聘爬虫-------------------------
class HrSpider(scrapy.Spider):
    name = 'hr'
    allowed_domains = ['tencent.com']
    start_urls = ['http://hr.tencent.com/position.php']
def parse(self, response):
    tr_list = response.xpath("//table[@class='tablelist']/tr")[1:-1]
    # 使用切片对selector列表取部分
    for tr in tr_list:
        item = TencentItem()
        item["title"] = tr.xpath("./td[1]/a/text()").extract_first()
        item["position"] = tr.xpath("./td[2]/text()").extract_first()
        item["publish_date"] = tr.xpath("./td[5]/text()").extract_first()
        yield item
    #找到下一页的url地址
    next_url = response.xpath("//a[@id='next']/@href").extract_first()
    if next_url != "javascript:;":
        next_url = "http://hr.tencent.com/" +next_url
        # 使用scrapy.Request()实例化一个Requests类
        yield scrapy.Request(
            next_url,
            callback=self.parse1,
            meta = {"item":item}  # meta为字典
        )

def parse1(self,response):
    item = response.meta["item"]
-----------------------------------------------------------------------
----------------------使用scrapy完成腾讯招聘爬虫-------------------------



"2019年08月5日"
scrapy


"items"
----好处
1.对不同的爬虫爬到的数据分类处理，分别赋值，分类存储
2.他人从items.py中能清楚知道所爬取的字段
3.获取数据时，不会手误导致将数据的key命名错误
----item使用
1. 在items.py中预先定义要爬取的字段后，在爬虫部分可以直接使用，无需
再重复定义字典
class TencentItem(scrapy.Item):
    title = scrapy.Filed()
    position = scrapy.Filed()
查看源码得知Item继承自DicItem,DicItem继承自BaseItem（类似字典的类型）
2.在爬虫文件中，先导入TencentItem类，再通过'item = TencentItem()'
实例化一个TencentItem类，item即为BaseItem字典对象，操作和字典一致，
通过'item["title"] = xx.xpath(...).extract_first()'
对数据赋值，当item[xx]中使用未在items.py中预先定义的字段，会报错
3.传入数据给MongoDB时，因为MongoDB只支持字典，所以需要注意使用
'dict(item)'将item转化为字典
4.可以在items.py中定义多个Item类，比如JdItem()/TaobaoItem()/...
不同的爬虫用其对应的Item类处理数据。
在存储阶段使用'if isinstance(item,TencentItem):'判断item属于哪个
Item类，进行相应的分类处理和存储


----判断数据是否属于某类型/类(instance-实例)
isinstance(value, type)
>>> isinstance('a',str)
True
>>> isinstance(2,int)
True

----使用xpath时注意，elements中的tbody节点在response中是不存在的

----去除列表中的多余的空格
content = [re.sub(r"\s","",i) for i in content]
content = [i for i in content if len(i)>0] #去除列表中的空字符串

----start_url 不被allowed_domains限制


"scrapy shell"
scrapy shell是一个交互终端，我们可以在未启动spider的情况下，尝试
及调试代码，也可以用来测试xpath表达式。通过
'scrapy shell <http://www.itcast.com目标请求url>'
命令进入后，会显示有哪些可以操作的对象。
部分命令：
response.url  # 当前响应的url
response.request.url  #当前响应对应的请求的url 
response.body  # 响应体，也就是html代码，默认byte类型
response.body.decode()  # str类型
spider.name


"setting"
配置文件中存放一些公共的变量，比如数据库的地址，账号密码等，用的时候
只需要调用就行，而且修改时也方便，一般用全大写

----部分setting
# 设置最大并发请求，默认为16，过大易被识别为爬虫 
concurrent_requests = 32
# scrapy的cookies默认开启
Disable cookies (enabled by default)
COOKIES_ENABLED = False

----使用方法
setting定义一个值
ai = "hello"
1.直接导入
from yangguang.settings import  ai
2.爬虫文件中使用，spider具有setting属性，下面的self代表spider
self.settings["ai"]
self.settings.get["ai"]  # 不存在时，返回None
self.settings.get["ai","xx"]  # 不存在时，返回xx
3.其他文件使用
spider.settings.get["ai"] 
spider.settings.get["ai","xx"]


"苏宁电子书爬虫程序实现"

----赋值
>>> a
{2: 3, 4: 6}
>>> b = a  
>>> b
{2: 3, 4: 6}
>>> b[2]=100//或者//a[2]=100
>>> b
{2: 100, 4: 6}
>>> a
{2: 100, 4: 6}
# 'b = a'，实际是a,b都指向数据{2: 3, 4: 6}的位置
# 'b[2]=100//或者//a[2]=100'实际是直接对内存中的字典数据进行修改，
# 变量名a和b依然指向原字典的位置，所以都改变
>>> c={2:3,4:6}
>>> d=c
>>> c={0:0}
>>> c
{0: 0}
>>> d
{2: 3, 4: 6}
# "c={0:0}"实际是在内存中创建新的数据{0：0}，并把c指向{0：0}的位置
# d的指向未变，所以d未变

----深层复制deepcopy
Python中赋值语句不复制对象，而是在目标和对象之间创建绑定关系。对于自
身可变或者包含可变项的集合对象，开发者有时会需要生成其【副本】用于改变
操作，进而避免改变原对象。
>>> from copy import deepcopy
>>> a = {2: 3, 4: 6}
>>> b = deepcopy(a)
>>> b
{2: 3, 4: 6}
>>> b[2]=0
>>> b
{2: 0, 4: 6}
>>> a
{2: 3, 4: 6}

--注意点<suning.py>
----数据重复
1.因为scrapy使用了Twisted异步网络框架，且变量名都是item，所以会出
现当某一个item字典还未完成赋值并输出时，前部分的for in循环已经开始
，并对同一个item进行修改，还未完成赋值的item的值发生变化，导致item
数据重复。原理参考上面的<----赋值>内容。
2.为何腾讯招聘和阳光政务平台同样使用scrapy框架，未出现数据重复：
因为腾讯和阳光中，是按照每条招聘信息进行分组，分组后，每一条招聘信
都是一个item，当前item和下一item不是同一item，互不影响。而在苏宁中，
需要的是每本书的信息，但是是按照图书大分类进行分组并定义的item，所
以小分类下的所有书，都是共用的同一个item，所以一旦item发生变化，其
他item也会变
3.使用深层复制可以解决这个问题
深度复制后的变量，与原变量不再指向同一个位置，所以互不影响。
----点击下一页，url未变化，ajax请求
----response中url为空，那么url是js生成，都能找到current/count_page



"CrawlSpider"
爬取一般网站常用的spider。其定义了一些规则(rule)来提供跟进link的方
便的机制。 也许该spider并不是完全适合您的特定网站或项目，但其对很多
情况都使用。 因此您可以以其为起点，根据需求修改部分方法。当然您也可以
实现自己的spider。
'class scrapy.spiders.CrawlSpider'
----作用
自动提取url地址，向url地址发送请求获取响应，将响应交给指定函数处理。
简化代码，
----使用
1.创建爬虫
scrapy startproject circ
cd circ 
scrapy genspider -t crawl cf circ.gov.cn
2.爬虫内部区别
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
import re

class CfSpider(CrawlSpider): # 继承自spider的CS类，父类以前是ScrapySpider
    name = 'cf'
    allowed_domains = ['circ.gov.cn']
    start_urls = ['http://www.circ.gov.cn/web/site0/tab5240/module14430/page1.htm']

    start_urls的响应会经过rules提取url地址
    #定义提取url地址规则
    rules = (
        #Rule为类
        #LinkExtractor 链接提取器，提取url地址
        #callback 从LinkExtractor每获取到链接时，参数所指的的值作为回调函数
        #follow response中提取的url的响应是否需要跟进
        Rule(lian'jie(allow=r'/web/site0/tab5240/info\d+\.htm'), callback='parse_item'),
        Rule(LinkExtractor(allow=r'/web/site0/tab5240/module14430/page\d+\.htm'),follow=True),
    )

    #parse函数有特殊功能，不能定义
    def parse_item(self, response):
        item = {}
        item["title"] = re.findall("<!--TitleStart-->(.*?)<!--TitleEnd-->",response.body.decode())[0]
        item["publish_date"] = re.findall("发布时间：(20\d{2}-\d{2}-\d{2})",response.body.decode())[0]
        print(item)
        # 在使用了crawl spider的情况下，也可以使用scrapy.Request()自行构建
        # 请求，配合rules的使用
        yield scrapy.Request(
            url,
            callback=self.parse_detail,
            meta = {"item":item}
        )
    
    def parse_detail(self,response):
        item = response.meta["item"]
        item["price"] =  "///"
        yield item
3.运行 
scrapy crawl cf

----注意点
1.rules是元组，其中的Rule()有先后顺序
2.follow 是一个布尔(boolean)值，指定了根据该规则从response提取的链
接是否需要跟进。 如果 callback 为None， follow 默认设置为 True ，否
则默认为 False 。
3.follow 当前url地址的响应是否重新经过【rules】,而非经过Rule()
4.在使用了crawl spider的情况下，也可以使用scrapy.Request()自行构建
请求，配合rules的使用
5.rules中的Rule之间，不能传输数据
6.start_urls是第一次会请求的url，如果对这个url有特殊的需求，可以定义
一个parse_start_url函数专门处理这个url所对应的响应
7.可以通过命令创建一个crawlspider模板，也可以导入类手动创建
8.如果一个url满足多个Rule，会从rules中选择第一个满足的进行操作，所以
链接提取器要尽量精确
9.CrawlSpider能自动补全url地址

----LinkExtractor的其他参数
链家提取器，其定义了如何从爬取到的页面提取链接
1.deny（正则表达式或正则列表）
URL必须被单个正则表达式（或正则表达式列表）匹配才能被排除（即未提取）。
它优先于allow参数。
2.allow_domains (str or list) 
deny_domains (str or list)
restrict_xpaths (str or list) 
XPath（或XPath列表），它定义响应中应提取链接的区域。如果给定，则仅扫
描由这些XPath选择的文本以获取链接。

----spider.Rule其他参数
1.process_links
从LinkExtractor每获取到链接时，参数所指的的值作为回调函数，处理url，
用来过滤url
2.process_requests
从LinkExtractor每获取到链接时，构造成requests时，参数所指的的值作为
回调函数，处理requests，用来过滤request



"Downloader Middleware"
下载器中间件(Downloader Middleware)
下载器中间件是介于Scrapy的request/response处理的钩子框架。 是用于全
局修改Scrapy request和response的一个轻量、底层的系统。
钩子框架类比钩子(hook)函数：与某一事件的发生挂钩，发生事件后调用这个函数

----使用
1.激活中间件
要激活下载器中间件组件，将其加入到 DOWNLOADER_MIDDLEWARES 设置中。
该设置是一个字典(dict)，键为中间件类的路径，值为其中间件的顺序(order)。
2.编写中间件
每个中间件组件是一个定义了以下一个或多个方法的Python类:
class scrapy.downloadermiddlewares.DownloaderMiddleware
process_request(request, spider)
process_response(request, response, spider)
...

----process_request(request, spider)
1.当每个request通过下载中间件时，该方法被调用
2.process_request() 必须返回其中之一: 返回 None 、返回一个 Response 对
象、返回一个 Request 对象或raise IgnoreRequest 。
3.request (Request 对象) – 处理的request
spider (Spider 对象) – 该request对应的spider

----process_response(request, response, spider)
1.当下载器完成http请求，传递响应给引擎的时候调用
2.process_response() 必须返回以下之一: 返回一个 Response 对象、 返回一个
Request 对象或raise一个 IgnoreRequest 异常。
3 .  
request (Request 对象) – response所对应的request
response (Response 对象) – 被处理的response
spider (Spider 对象) – response所对应的spider

----下载器中间件编写实例
1.激活
DOWNLOADER_MIDDLEWARES = {
   'circ.middlewares.RandomUserAgentMiddleware': 543,
   'circ.middlewares.CheckUserAgent': 544,
}
2.编写
class RandomUserAgentMiddleware:
    def process_request(self,request,spider):
        ua = random.choice(spider.settings.get("USER_AGENTS_LIST"))
        request.headers["User-Agent"] = ua
        # USER_AGENTS_LIST在setting中定义

class CheckUserAgent:
    def process_response(self,request,response,spider):
        # print(dir(response.request))
        print(request.headers["User-Agent"])
        return response

class ProxyMiddleware(object)
    def process_request(self, request, spider):
        request.meta["proxy"] = "http://124.115.126.76:808"
        # 添加代理，需要在request的meta信息中添加proxy字段
        # 代理形式：协议+ip地址+端口

----dir([object])
如果没有实参，则返回当前本地作用域中的名称列表。如果有实参，它会尝试返回
该对象的有效属性列表。
1.
dir(dict)
-->['__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__init__', '__iter__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'clear', 'copy', 'fromkeys', 'get', 'items', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values']
2.
import requests
res = requests.get("http://www.baidu.com")
print(dir(res))
-->['__attrs__', '__bool__', '__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__enter__', '__eq__',
 '__exit__', '__format__', '__ge__', '__getattribute__', '__getstate__', '__gt__', '__hash__', '__init__',
 '__init_subclass__', '__iter__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__nonzero__', '__reduce__',
 '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__',
 '_content', '_content_consumed', '_next', 'apparent_encoding', 'close', 'connection', 'content', 'cookies', 'elapsed',
 'encoding', 'headers', 'history', 'is_permanent_redirect', 'is_redirect', 'iter_content', 'iter_lines', 'json',
 'links', 'next', 'ok', 'raise_for_status', 'raw', 'reason', 'request', 'status_code', 'text', 'url']



"scrapy模拟登录"
目的：获取cookie，能够爬取登录后的页面
scrapy默认使用cookie，下次请求会自动带上cookie

--回顾：
----requests如何模拟登录：
1.携带cookies请求页面
2.找接口发送post请求存储cookie
3.使用session发送post请求
----selenium如何模拟登录：
找到对应input标签，输入信息点击登陆

----scrapy模拟登录的方法
1.直接携带cookie
2.找到发送post请求的url地址，带上信息，发送请求
3.自动登录（当form表单中有action对应的url地址时可用）

----应用场景
1.cookie过期时间很长，常见于一些不规范的网站
2.能在cookie过期之前把搜有的数据拿到
3.配合其他程序使用，比如其使用selenium把登陆之后的cookie获取到保存到本地，
scrapy发送请求之前先读取本地cookie

----模拟登录的实现
1.直接携带cookie。(使用已登录后的获取的cookie去登录)
重写start_requests()方法，指定start_urls的处理方式。达到对start_urls构
造请求前进行处理的目的

class RenrenSpider(scrapy.Spider):
    name = 'renren'
    allowed_domains = ['renren.com']
    start_urls = ['http://www.renren.com/327550029/profile']

    def start_requests(self):
        cookies = "anonymid=jcokuqturos8ql; depovince=GW; jebecookies=f90c9e96-78d7-4f74-b1c8-b6448492995b|||||; _r01_=1; JSESSIONID=abcx4tkKLbB1-hVwvcyew; ick_login=ff436c18-ec61-4d65-8c56-a7962af397f4; _de=BF09EE3A28DED52E6B65F6A4705D973F1383380866D39FF5; p=90dea4bfc79ef80402417810c0de60989; first_login_flag=1; ln_uact=mr_mao_hacker@163.com; ln_hurl=http://hdn.xnimg.cn/photos/hdn421/20171230/1635/main_JQzq_ae7b0000a8791986.jpg; t=24ee96e2e2301bf2c350d7102956540a9; societyguester=24ee96e2e2301bf2c350d7102956540a9; id=327550029; xnsid=e7f66e0b; loginfrom=syshome; ch_id=10016"
        cookies = {i.split("=")[0]:i.split("=")[1] for i in cookies.split("; ")}
        # headers = {"Cookie":cookies}
        "scrapy中将cookies放在headers中是无效的，因为cookis和headers专门有位置单独存放的"
        yield scrapy.Request(
            self.start_urls[0],
            callback=self.parse,
            cookies=cookies
            # headers = headers
        )

    def parse(self, response):
        print(re.findall("毛兆军",response.body.decode()))
        yield scrapy.Request(
            "http://www.renren.com/327550029/profile?v=info_timeline",
            callback=self.parse_detial
        )

    def parse_detial(self,response):
        print(re.findall("毛兆军",response.body.decode()))

2.找到发送post请求的url地址，带上信息，发送请求。(没有cookie，使用body携带账
号密码信息直接登录)
还可以使用scrapy.Request(method="post")发送post请求

# -*- coding: utf-8 -*-
import scrapy
import re

class GithubSpider(scrapy.Spider):
    name = 'github'
    allowed_domains = ['github.com']
    start_urls = ['https://github.com/login']

    def parse(self, response):
        authenticity_token = response.xpath("//input[@name='authenticity_token']/@value").extract_first()
        utf8 = response.xpath("//input[@name='utf8']/@value").extract_first()
        commit = response.xpath("//input[@name='commit']/@value").extract_first()
        post_data = dict(
            login="noobpythoner",
            password="zhoudawei123",
            authenticity_token=authenticity_token,
            utf8=utf8,
            commit=commit
        )
        '''FormRequest类是Request的子类，它扩展了基本Request，其中包含处
        理HTML表单的功能。它通过lxml.html表单 使用Response对象中的表单数据
        去预填充表单字段'''
        yield scrapy.FormRequest(
            "https://github.com/session",
            formdata=post_data,
            callback=self.after_login
        )

    def after_login(self,response):
        # with open("a.html","w",encoding="utf-8") as f:
        #     f.write(response.body.decode())
        print(re.findall("noobpythoner|NoobPythoner",response.body.decode()))

3.自动登录（当form表单中有action对应的url地址时可用）
逻辑：找到登录action的url地址，将账号密码数据发送过去

class Github2Spider(scrapy.Spider):
    name = 'github2'
    allowed_domains = ['github.com']
    start_urls = ['https://github.com/login']

    def parse(self, response):
        '''from_response
        返回一个新的FormRequest对象，其表单字段值预填充了在给定响应中包含
        的HTML <form>元素中找到的那些表单数据
        当存在多个表单时，可以通过参数：formname=None, formnumber=0, 
        formdata=None, formxpath=None定位目标表单
        查看源码得知，from_response中会自动寻找action的url'''
        yield scrapy.FormRequest.from_response(
            response, 
        '''formdata
        在表单数据中需要覆盖的字段。如果响应<form>元素中已存在某个
        字段，则该值将被此参数中传递的值覆盖。'''
            formdata={"login":"noobpythoner","password":"zhoudawei123"},
            callback = self.after_login
        )

    def after_login(self,response):
        print(re.findall("noobpythoner|NoobPythoner",response.body.decode()))

----Request objects
class scrapy.http.Request(url[, callback, method='GET', headers, body, cookies, meta, encoding='utf-8', priority=0, dont_filter=False, errback])
Request对象表示HTTP请求，该请求通常在Spider中生成并由Downloader执行，从而
生成Response。

----COOKIE_DEBUG
在setting中添加参数COOKIE_DEBUG = True，显示cookie在不同解析函数间的传
递过程。前提是COOKIES_ENABLE = True。enable-启用

----字典的dict()赋值操作
>>> a=10
>>> b='llo'
>>> c = dict(a=a,b=b)
>>> c
{'b': 'llo', 'a': 10}
# 使用dict()可以在定义字符串类的key时，不写引号
# key不能为数字

>>> c = {'b':'llo','a':10}
>>> c
{'b': 'llo', 'a': 10}

----注意点
1.scrapy中将cookies放在headers中是无效的，因为cookis和headers专门有位
置单独存放的
2.CrawlSpider能自动补全url地址，ScrapySpider不能
可通过urllib.parse.urljoin()方法补全
>>>import urllib
>>> a='http://www.baidu.com/mw654849464616mw'
>>> b='mwhelloworld'
>>> urllib.parse.urljoin(a,b)
'http://www.baidu.com/mwhelloworld'



"环境搭建"
--构建python
1.官网下载'Python-3.7.4.tgz',移动到目的路径/usr/local/bin
2.解压
sudo tar -zxvf  Python-3.7.4.tgz
3.构建
./configure
make
make altinstall

--设定python解释器默认版本
# 末尾权重大的优先
'''sudo update-alternatives --install /usr/bin/python python /usr
/bin/python3.7 210'''
python3.7装虚拟环境遇到不兼容问题,还是使用3.5

--创建虚拟环境
Python“虚拟环境”允许将Python 包安装在特定应用程序的隔离位置，而不是全局安装.
venv 模块支持使用自己的站点目录创建轻量级“虚拟环境”，可选择与系统站点目录隔离。
每个虚拟环境都有自己的 Python 二进制文件（与用于创建此环境的二进制文件的版本相
匹配），并且可以在其站点目录中拥有自己独立的已安装 Python 软件包集。
cd ~/env # 创建一个专门储存虚拟环境的文件
python3 -m venv btf-env  # 注意必须是3或3.5 python和python3.7都错误
source ./btf-env/bin/activate



"Redis"
Redis是一个高性能的key-value数据库

----nosql介绍
NoSQL：一类新出现的数据库,常见的解释是'non-relational','not only sql'
它的特点：
1.不支持SQL语法
2.存储结构跟传统关系型数据库的关系表完全不同，nosql中存储的数据都是K-V形式
3.NoSQL的世界中没有一种通用的语言，每种nosql数据库都有自己的api和语法，以
及擅长的业务场景
NoSQL中的产品种类：Mongodb/RedisHbase/hadoopCassandra/hadoop

----sql
结构化查询语言(Structured Query Language)简称SQL，是一种特殊目的的编程
语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数
据库系统。

----NoSQL和SQL数据库的比较
适用场景不同：sql数据库适合用于关系特别复杂的数据查询场景，nosql反之
事务特性：sql对事务的支持非常完善，而nosql基本不支持事务
两者在不断地取长补短，呈现融合趋势

----Redis简介
Redis是一个开源的使用C语言编写、支持网络、可基于内存亦可持久化的日志型、
Key-Value数据库，并提供多种语言的API。
Redis是 NoSQL技术阵营中的一员，它通过多种键值数据类型来适应不同场景下的存储
需求，借助一些高层级的接口使用其可以胜任如缓存/队列系统的不同角色

----Redis特性
Redis与其他key-value缓存产品有以下三个特点：
1.Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次
加载进行使用。
2.Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，
hash等数据结构的存储。
3.Redis支持数据的备份，即master-slave模式的数据备份。

----Redis 优势
1.性能极高: Redis读的速度是110000次/s,写的速度是81000次/s 。
2.丰富的数据类型: Redis支持二进制安全的Strings, Lists,Hashes,Sets及
Ordered Sets 数据类型操作。
3.原子: Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原
子性执行。
4.丰富的特性:Redis还支持 publish/subscribe, 通知, key 过期等等特性。

----redis应用场景
1.用来做缓存(ehcache/memcached):redis的所有数据是放在内存中的（内存数据库）
2.可以在某些特定应用场景下替代传统数据库: 比如社交类的应用
3.在一些大型系统中，巧妙地实现一些特定的功能: session共享、购物车



"redis安装和配置"
--安装 
----Ubuntu 下安装(官网推荐使用压缩文件安装,apt容易出问题)
# $sudo apt-get update
# $sudo apt-get install redis-server
----启动 Redis
$ sudo redis-server /etc/redis/redis.conf
----查看 redis 是否启动？
$ redis-cli
以上命令将打开以下终端：
redis 127.0.0.1:6379>
127.0.0.1 是本机 IP ，6379 是 redis 服务端口。现在我们输入 PING 命令。
redis 127.0.0.1:6379> ping
PONG
----获取配置信息
redis-cli进入客户端
config get *

--配置
1.配置⽂件⽬录为/usr/local/redis/redis.conf
sudo cp /usr/local/redis/redis.conf /etc/redis/redis.conf
2.查看
sudo vi /etc/redis/redis.conf
3.核心配置选项：
-绑定ip：如果需要远程访问，可将此⾏注释，或绑定⼀个真实ip
bind 127.0.0.1
-端⼝，默认为6379
port 6379
-是否以守护进程运⾏
如果以守护进程运⾏，则不会在命令⾏阻塞，类似于服务
如果以⾮守护进程运⾏，则当前终端被阻塞
设置为yes表示守护进程，设置为no表示⾮守护进程
推荐设置为yes
daemonize yes
-数据⽂件
dbfilename dump.rdb
-数据⽂件存储路径
dir /var/lib/redis
-⽇志⽂件
logfile /var/log/redis/redis-server.log
-数据库，默认有16个
database 16
-主从复制，类似于双机备份。
slaveof



"redis启动服务端和客户端"
--服务器端
服务器端的命令为redis-server
1.可以使⽤help查看帮助⽂档
redis-server --help/-h
2.redis服务管理(通过压缩包安装的)
# 启动服务端-指定加载的配置文件
sudo redis-server /etc/redis/redis.conf 
ps -ef|grep redis 查看redis服务器进程
sudo kill -9 (redis的pid) 杀死redis服务器
2.使⽤服务的⽅式管理redis服务(安装方式不同,启动方式不同)
启动
sudo service redis start
停⽌
sudo service redis stop
重启 sudo service redis restart

--客户端
1.客户端启动,连接redis
redis-cli
2.可以使⽤help查看帮助⽂档
redis-cli --help
3.运⾏测试命令
ping
-->pong
4.切换数据库
数据库没有名称，默认有16个，通过0-15来标识，连接redis默认选择第一个数据库
select n



"redis value 的数据类型及操作"
--简介
redis是key-value的数据结构，每条数据都是⼀个键值对。
键的类型是字符串，键不能重复
值的类型分为五种：字符串string/哈希hash/列表list/集合set/有序集合zset
数据操作行为:保存/修改/获取/删除

--string类型的value
字符串类型是Redis中最为基础的数据存储类型，它在Redis中是二进制安全的，这便
意味着该类型可以接受任何格式的数据，如JPEG图像数据或Json对象描述信息等。在
Redis中字符串类型的Value最多可以容纳的数据长度是512M。
1.保存
如果设置的键不存在则为添加，如果设置的键已经存在则修改
2.设置键值
set key value
例1：设置键为name值为itcast的数据
set name itcast
3.设置键值及过期时间，以秒为单位
setex key seconds value
例2：设置键为aa值为aa过期时间为3秒的数据
setex aa 3 aa
4.设置多个键值
mset key1 value1 key2 value2 ...
例3：设置键为'a1'值为'python'、键为'a2'值为'java'、键为'a3'值为'c'
mset a1 python a2 java a3 c
5.追加值
append key value
例4：向键为a1中追加值' haha'
append 'a1' 'haha'
6.获取
获取：根据键获取值，如果不存在此键则返回nil
get key
例5：获取键'name'的值
get 'name'
7.根据多个键获取多个值
mget key1 key2 ...
例6：获取键a1、a2、a3的值
mget a1 a2 a3

--hash类型的value
hash⽤于存储对象，对象的结构为属性、值. 值的类型为string
1.设置单个属性
hset key field value
例1：设置键user的属性name的值为itheima
hset user name itheima
2.设置多个属性
hmset key field1 value1 field2 value2 ...
例2：设置键u2的属性name为itcast、属性age为11
hmset u2 name itcast age 11
3.获取指定键所有的属性
hkeys key
例3：获取键u2的所有属性
hkeys u2
4.获取⼀个属性的值
hget key field
例4：获取键u2属性'name'的值
hget u2 'name'
5.获取多个属性的值
hmget key field1 field2 ...
例5：获取键u2属性'name'、'age'的值
hmget u2 name age
6.获取所有属性的值
hvals key
例6：获取键'u2'所有属性的值
hvals u2
7.删除整个hash键及值，使⽤del命令
del key
例7:del u2
8.删除属性，属性对应的值会被⼀起删除
hdel key field1 field2 ...
例8：删除键'u2'的属性'age'
hdel u2 age

--list类型的value
列表的元素类型为string,按照插入顺序排序
1.从左侧插⼊数据
lpush key value1 value2 ...
例1：从键为'a1'的列表左侧加⼊数据a 、 b 、c
lpush a1 a b c
2.获取
返回列表⾥指定范围内的元素
start、stop为元素的下标索引,索引从左侧开始，第⼀个元素为0.索引可以是负数，
表示从尾部开始计数，如-1表示最后⼀个元素
lrange key start stop
例4：获取键为'a1'的列表所有元素
lrange a1 0 -1
    1) "c"
    2) "b"
    3) "a"
3.从右侧插入数据
rpush a1 0 1
lrange a1 0 -1
    1) "c"
    2) "b"
    3) "a"
    4) "0"
    5) "1"
4.在指定元素前/后插入新元素
linsert key before/after 目标位置元素 新元素
例3：在键为'a1'的列表中的元素'b'前加⼊'3'
linsert a1 before b 3
5.设置指定索引位置的元素值
索引从左侧开始，第⼀个元素索引为0.索引可为负，表示尾部开始计数,-1表示最后⼀个
lset key index value
例5：修改键为'a1'的列表中索引为1的元素值为'z'
lset a1 1 z
lrange a1 0 -1
    1) "c"
    2) "z"
    3) "b"
    4) "a"
    5) "0"
    6) "1"
6.删除指定元素
将列表中前count次出现的值为value的元素移除
count > 0: 从左往右移除
count < 0: 从右往走移除
count = 0: 移除所有
lrem key count value
例6.1：向列表'a2'中加⼊元素'a'、'b'、'a'、'b'、'a'、'b'
lpush a2 a b a b a b
例6.2：从'a2'列表右侧开始删除2个'b'
lrem a2 -2 b
例6.3：查看列表'py12'的所有元素
lrange a2 0 -1
    1) "b"
    2) "a"
    3) "a"
    4) "a"
7.lpop key
移除并返回列表 key 的头元素。
返回值：列表的头元素。当key不存在时，返回nil 。

--set类型的value
⽆序集合,元素为string类型,元素具有唯⼀性，不重复.说明：对于集合没有修改操作
1.添加元素
sadd key member1 member2 ...
例1：向键'dalao'的集合中添加元素tzh,zj,zd,sjm
sadd dalao tzh zj zd sjm
2.返回所有的元素
smembers key
例2:smembers dalao
    1) "sjm"
    2) "tzh"
    3) "zj"
    4) "zd"     
3.删除指定元素
srem key
例3：删除键'dalao'的集合中元素'sjm'
srem dalao sjm

--zset类型的value
sorted set 有序集合.元素为string类型.元素具有唯⼀性，不重复.没有修改操作
每个元素都会关联⼀个double类型的score，表示权重，通过权重将元素从⼩到⼤排序
1.添加
zadd key score1 member1 score2 member2 ...
例1:向键'shadiao'的集合中添加元素hj,tzh,sjm,cjx,yx,dw.权重分别为-1~4
zadd shadiao -1 hj 0 tzh 1 sjm 2 cjx 3 yx 4 dw
2.返回指定索引范围内的元素
start、stop为元素的下标索引.索引从左侧开始，第⼀个元素为0
索引可以是负数，表示从尾部开始计数，如-1表示最后⼀个元素
zrange shadiao start stop
例2：获取键'shadiao'的集合中所有元素
zrange shadiao 0 -1 / (4 4)
3.返回score值在min和max之间的成员,含min和start
zrangebyscore key min max
例3：获取键'shadiao'的集合中权重值在5和6之间的成员
zrangebyscore shadiao 3 5
    1) "yx"
    2) "dw"
4.返回成员member的score值
zscore key member
例4：获取键'shadiao'的集合中元素'tzh'的权重
zscore shadiao tzh
    '0'
5.删除指定元素
zrem key member1 member2 ...
例5：删除集合'shadiao'中元素'sjm'
zrem shadiao sjm
zrange shadiao 0 -1
    1) "hj"
    2) "tzh"
    3) "cjx"
    4) "yx"
    5) "dw"
6.删除权重在指定范围的元素
zremrangebyscore key min max
例6：删除集合'shdiao'中权限在-5、6之间的元素
zremrangebyscore shadiao -2 0



"redis key 命令"
1.查找键，参数⽀持正则表达式
keys pattern
例1：查看所有键
keys *
例2：查看名称中a开头的键
keys 'a*'
2.判断键是否存在，如果存在返回1，不存在返回0
exists key1
例3：判断键a1是否存在
exists a1
3.查看键对应的value的类型
type key
例4：查看键a1的值类型，为redis⽀持的五种类型中的⼀种
type a1
4.删除键及对应的值
del key1 key2 ...
例5：删除键a2、a3
del a2 a3
5.设置过期时间，以秒为单位
如果没有指定过期时间则⼀直存在，直到使⽤DEL移除
expire key seconds
例6：设置键'a1'的过期时间为3秒
expire 'a1' 3
6.查看有效时间，以秒为单位
ttl key
例7：查看键'bb'的有效时间
ttl bb

--知识点
1.搜索进程
ps aux|grep (进程名)
ps ef|grep (进程名)
2.杀死所有redis相关进程,但不杀死grep
kill -9 $(ps -ef|grep redis|grep -v grep|awk '{print $2}')
3.清理残留数据
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P 
4.查看使用apt-get install安装的文件的目录
dpkg -L (redis-server程序名)
5.apt下载安装目录
apt下载的软件的存放位置：/var/cache/apt/archives
安装后软件的默认位置：/usr/share
可执行文件位置：/usr/bin
配置文件位置：/etc
lib文件位置：/usr/lib
6.完全卸载
sudo apt-get autoremove (xxx)
7.redis命令不分大小写,但是key和value还是区分大小写的
8.查看已安装包 'pip freeze'



"redis与python交互"
--安装redis
进⼊虚拟环境，联⽹安装包redis
pip install redis
--使用
1.调⽤模块
导入模块
from redis import *
这个模块中提供了StrictRedis对象(Strict严谨)，⽤于连接redis服务器，并按
照不同类型提供了不同⽅法，进⾏交互操作
2.实例化StrictRedis对象
通过init创建对象，指定参数host、port与指定的服务器和端⼝连接，host默认为
localhost，port默认为6379，db默认为0
'sr = StrictRedis(host='localhost', port=6379, db=0)'
sr则具备set/get/zset...等方法
如果参数都是默认值,可以不写参数
'sr=StrictRedis()'
根据不同的数据类型，拥有不同的实例⽅法可以调⽤，与前⾯学的redis命令对应，⽅法需
要的参数与命令的参数⼀致
3.操作(在python shell操作)
>>> sr.set('name','tzh')
True
>>> sr.get('tzh')
>>> print(sr.get('name'))
b'tzh'  # 在py里面返回的也是b'xxx'
>>> print(sr.delete('name'))
1
>>> print(sr.keys())  # keys()中默认为*
[b'ss', b'xinxi', b'hello', b'niiii', b'hight', b'namesas']
>>> 



"scrapy-redis基础概念"
Redis-based components for Scrapy.

--目前存在的问题:
1.虽然scrapy自带去重功能,但是当爬虫中断或者重新开启后,爬虫会重新请求已经
请求过的url.
2.如何才能做到有同样爬虫程序的多台电脑能同时爬一个任务.
解决思路:若各电脑的request都来自于自身的调度器,那么电脑之前都是各爬各的,
会重复请求.所以为了相互之间能协同工作,需要找一个公共的存放request的位置

--scrapy-redis
scrapy-redis是基于redis的scrapy组件,是在scrapy的基础上,做了功能的扩展
1.具体体现:request去重,爬虫持久化,轻松实现分布式
2.实现:在scrapy框架中增加了redis组件,调度器和管道与redis连接.
通过redis实现调度器的队列和指纹集合.调度器和redist之间相互传递request,
调度器将requests存到redis,也能从redis读取requests.管道默认将数据保存到
redis,也可以存到其他
3.去重原理:在存入redis之前,通过指纹标记唯一request,实现去重.在取出时使用
pop操作,取一删一,别的调度器取不到已经取过的.
4.断点续爬原理:重启爬虫后,因为request存在redis中,再次读取时判断重复,所以 
实现断点续爬功能

--redis复习
1.redis是一个开源的内存数据库,它可以用作数据库/缓存/消息中间件(保存临时数
据).它支持多种的数据结构,如字符串/哈希/列表/集合/有序集合等.
2.redis不是实时存储,是间隔时间存储,所以还是可能出现数据丢失
3.启动服务端方式:
    -使用apt安装/etc/init.d/redis-server (stop/start/restart)
    -使用源码安装:redis-server /...redis.conf
  启动客户端:redis-cli <-h -p>
4.清空当前db-"flushdb"  清除所以db-"flushall"  
5.scard 获取数量 
zadd 如果存在,更新分数,分数可以相同
zrange myset 0 -1 <withscores> 获取数据<和分数>
zcard 获取数量

----源码文件下载
git clone https://github.com/rmax/scrapy-redis.git
其中自带三个demo:
1.dmoz
'This spider simply scrapes dmoz.org.'
这个爬虫简单地抓取了dmoz.org。
2.myspider_redis
'This spider uses redis as a shared requests queue and uses myspider:start_urls as start URLs seed. For each URL, the spider outputs one item.'
这个爬虫使用redis作为共享请求队列，并使用myspider：start_urls作为起始URL
种子。对于每个URL，爬虫输出一个item。
3.mycrawler_redis
'This spider uses redis as a shared requests queue and uses mycrawler:start_urls as start URLs seed. For each URL, the spider follows are links.'
这个爬虫使用redis作为共享请求队列，并使用myspider：start_urls作为起始URL
种子。对于每个URL，蜘蛛跟随的是链接。



"dmoz.py 模板+源码分析"
--dmoz模板分析
scrapy_redis.spiders.dmoz.py
"实现功能:持久化(增量式爬虫)+断点续爬+request去重"
1.dmoz.py中定义的DmozSpider继承自CrawlSpider,内容无特殊
2.实现功能的原因是settings.py中:
    -指定了去重方法给request对象去重
    -指定scheduler队列
    -设置了队列的内容是否持久保存为True,为FALSE时关闭redis即清空redis
    -ITEM_PIPELINES增加了权重大的RedisPipeline(保存数据到redis)
    -若需要将item存入redis,需设置:
        -REDIS_URL='redis://127.0.0.1:6379'
        -REDIS_HOST='127.0.0.1'
         REDIS_PORT=6379
3.运行爬虫命令不变scrapy crawl dmoz
4.运行后redis数据库中增加了三个键:
    -"dmoz:requests"-zset-scheduler队列,存放的是待爬取的request对
    象,获取过程是pop操作(redis不能存取对象,所以redis对对象做了类似
    json.dump的序列化操作,将其转化成字符串再储存,读取需先进行反序列化操作)
    -"dmoz:items"-list-存放的是通过RedisPipeline保存的item数据
    -"dmoz:dumpfilter"-set-指纹集合,存放的是已经进入scheduler队列的
    request对象的指纹,指纹默认由请求方法,url和请求体组成.

--dmoz涉及源码分析
scrapy_redis.spiders.settings
class scrapy_redis.pipelines.RedisPipeline
    "将序列化的item保存到redis列表"
    def process_item(self, item, spider):
        # 调用一个异步线程去处理这个item
        return deferToThread(self._process_item, item, spider)

    def _process_item(self, item, spider):
        key = self.item_key(item, spider)
        data = self.serialize(item) # 序列化
        self.server.rpush(key, data)
        return item

    def item_key(self, item, spider):
        """Returns redis key based on given spider.
        Override this function to use a different key depending on the item
        and/or spider.
        返回基于给定爬行器的redis键。
        重写此函数以根据项使用不同的键和/或蜘蛛。
        """
        return self.key % {'spider': spider.name}

class scrapy_redis.dupefilter.RFPDupeFilter
    "基于redis的请求去重器 + 指纹生成及存储"
    "duplicates-重复 filter-过滤器"
    def request_seen(self, request):
        """Returns True if request was already seen."""
        fp = self.request_fingerprint(request)
        # This returns the number of values added, zero if already exists.
        added = self.server.sadd(self.key, fp)
        return added == 0
    def request_fingerprint(self, request):
        return request_fingerprint(request)
    def request_fingerprint(request)
    '''1.请求指纹是唯一标识请求指向资源的散列.生成fingerprint指纹时,会
         先对url中的参数进行整理,去重url字符串不同,但实际指向相同的资源
         的request.
       2.生成指纹时忽略请求头,否则,不同cookis+相同url生成不同的指纹
       3.此方法来自scrapy框架,scrapy生成指纹也是使用此方法'''
    if include_headers not in cache:
        fp = hashlib.sha1()  # 实例化一个sha1加密方法
        fp.update(to_bytes(request.method))  # 请求方法,update只能接受byte类型 
        fp.update(to_bytes(canonicalize_url(request.url)))  #规范化url 
        fp.update(request.body or b'')
        cache[include_headers] = fp.hexdigest() # 将fp转化为16进制字符串

class scrapy_redis.scheduler.Scheduler
    "request是否入队"
    def close(self, reason): # 关闭scheduler时,执行close()
        if not self.persist: # self.persist 持久化True/False
            self.flush()     # 若持久化为false,执行self.flush()
    def flush(self): # 清空 存放指纹的redis+存放requests的redis
        self.df.clear()
        self.queue.clear()
    def enqueue_request(self, request): # request存入队列
        if not request.dont_filter and self.df.request_seen(request):
            ''' 只有当not request.dont_filter 和 self.df.request_seen(request)都为True
            即需要去重且request已看过(已存在)
            此判断成立,被执行,不存入队列,返回False
            
            只要not request.dont_filter为False,即不去重,
            或只要self.df.request_seen(request)为False,即request不存在/未存过
            则判断不成立,执行self.queue.push(request),存入队列'''
            # 由此,对于百度贴吧这种页面内容会更新的网址,可设置dont_filter为ture
            self.df.log(request, self.spider)
            return False
        self.queue.push(request) # 存入队列
        return True

--总结
1.request对象什么时候入队
- dont_filter = True ,构造请求的时候，把dont_filter置为True，该url会被反复抓取（url地址对应的内容会更新的情况）
- 一个全新的url地址被抓到的时候，构造request请求
- url地址在start_urls中的时候，会入队，不管之前是否请求过
  - 构造start_url地址的请求时候，dont_filter = True
2.scrapy_redis去重方法
- 使用sha1加密request得到指纹
- 把指纹存在redis的集合中
- 下一次新来一个request，同样的方式生成指纹，判断指纹是否存在reids的集合中

--知识点
1.hashlib.sha1()
>>> import hashlib
>>> f = hashlib.sha1()
>>> f.update('唐志鸿'.encode())
>>> f.hexdigest()
'a819c542e2abe2e5eb20f8c39681e4530f798adb'

2.os.path.join(path, *paths)
智能地加入一个或多个路径组件。返回值是路径和*路径的任何成员的串联，在除了最后一
个之外的每个非空部分之后只有一个目录分隔符（os.sep），这意味着如果最后一部分为
空，结果将仅在分隔符中结束。
f = open(os.path.join(path, 'requests.seen'), 'w')

3.当前系统的分隔符和换行符
分隔符-separator
换行符-line separator/break
# linux下
>>> import os
>>> os.sep
'/'
>>> os.linesep
'\n'
# 实际应用
# 这样程序运行在linux和windows都能写入正确的换行符
f.write('content' + os.linesep)

4.sha1加密生成指纹可以用在其他去重数据的程序中

5.str.strip([chars])
返回原字符串的副本，移除其中的前导和末尾字符。chars 参数为指定要移除字符的
字符串。如果省略或为 None，则 chars 参数默认移除空格符。实际上chars参数并
非指定单个前缀或后缀；而是会移除参数值的所有组合:
>>> '   spacious   '.strip()
'spacious'
>>> 'www.example.com'.strip('cmowz.')
'example'
最外侧的前导和末尾 chars 参数值将从字符串中移除。开头端的字符的移除将在遇
到一个未包含于 chars 所指定字符集的字符时停止。类似的操作也将在结尾端发生。 例如:
>>> comment_string = '#....... Section 3.2.1 Issue #32 .......'
>>> comment_string.strip('.#! ')
'Section 3.2.1 Issue #32'
实际应用:'item["m_cate"] = [i.strip() for i in item["m_cate"] if len(i.strip())>0][0]'

6.str.rstrip([chars]) # str.lstrip([chars])移除前导
返回原字符串的副本，移除其中的末尾字符。chars 参数为指定要移除字符的字符串。
如果省略或为 None，则 chars 参数默认移除空格符。
实际上 chars 参数并非指定单个后缀；而是会移除参数值的所有组合:
>>> '   spacious   '.rstrip()
'   spacious'
>>> 'mississippi'.rstrip('ipz')
'mississ'



"myspider_redis.py 源码分析"
"实现功能:分布式爬虫"
scrapy_redis.spiders.myspider_redis.py
--和普通爬虫区别:
1.继承自RedisSpider类.
'from scrapy_redis.spiders import RedisSpider'
2.从redis队列读取starturl,而非通过start_urls='...'.
"redis_key = 'myspider:start_urls'"
3.定义__ini__方法动态定义允许的域列表.可删除并自定义allowed_domains ='...,...'

--实际应用方法:(和普通爬虫一样)
1.创建一个scrapy项目
scrapy startproject <mySpider（项目名）>
2.生成一个爬虫
cd <mySpider（项目名）>
scrapy genspider <itcast（爬虫名）> <itcast.cn（允许爬取范围）>
3.导入RedisSpider
'from scrapy_redis.spiders import RedisSpider'
修改为继承自RedisSpider类
定义存放start_urls的redis队列的键名
"redis_key = 'myspider:start_urls'"
4.setting中:"实现功能:持久化(增量式爬虫)+断点续爬+request去重"
    -指定去重方法给request对象去重
    -指定scheduler队列
    -设置队列的内容是否持久保存为True,为FALSE时关闭redis即清空redis
5.编写爬虫



"mycrawler_redis.py 源码分析"
"实现功能:分布式爬虫 + 自动提取地址"
scrapy_redis.spiders.mycrawler_redis.py
--和普通爬虫区别:
1.继承自RedisCrawlSpider-crawlspider的分布式爬虫类
'from scrapy_redis.spiders import RedisCrawlSpider'
2.从redis队列读取starturl,而非通过start_urls='...'.
"redis_key = 'myspider:start_urls'"
3.定义__ini__方法动态定义允许的域列表.可删除并自定义allowed_domains ='...,...'
4.rules = (...)
5.CrawlSpider中没有parse()方法,parse方法默认用来发生链接提取器的生成的请求

--实际应用:
1.scrapy genspider -t crawl amazon amazon.cn
2.导入RedisCrawlSpider
'from scrapy_redis.spiders import RedisCrawlSpider'
修改继承自RedisCrawlSpider类
定义存放start_urls的redis队列的键名
"redis_key = 'myspider:start_urls'"
3.setting中:"实现功能:持久化(增量式爬虫)+断点续爬+request去重"
    -指定去重方法给request对象去重
    -指定scheduler队列
    -设置队列的内容是否持久保存为True,为FALSE时关闭redis即清空redis
4.编写爬虫

--知识点
1.查看源码的方式
2.写xpath的方式:写每一条xpath前使用源码检查
3.搜中文出现的匹配结果会很多,可以搜class标签名快速定位
4.注意固定位置的信息和位置已发生改变的信息
5.[not(@class)]
'item["book_cate"] = response.xpath("//div[@id='wayfinding-breadcrumbs_feature_div']/ul/li[not(@class)]/span/a/text()").extract()'
6.列表推导式if ...后面还可以使用and ...
item["book_desc"] = [i.strip() for i in item["book_desc"] if len(i.strip())>0 and i!='海报：']
7.注意中文在html源码中,可能是经过html编码处理的字符串
8.<noscript>标签也可以尝试是否能被xpath处理



"发布代码到服务端"
--Pycharm
1.配置
tools->deployment->configuration->'+'号->输入name/type选择'sftp'->输入host等信息
2.测试连接
3.上传
upload to (name)



"Linux Crontab"
Linux Crontab定时任务是用来定期执行程序的命令.
每分锺会定期检查是否有要执行的工作，如果有要执行的工作便会自动执行该工作。

--语法
crontab [ -u user ] file
crontab [ -u user ] { -l | -r | -e }

-命令:
      service cron start    //启动服务
    　　service cron stop     //关闭服务
    　　service cron restart  //重启服务
    　　service cron reload   //重新载入配置
    　　service cron status   //查看服务状态 

--说明：
1.crontab 是用来让使用者在固定时间或固定间隔执行程序之用，换句话说，也就是类
似使用者的时程表。
2.-u user 是指设定指定 user 的时程表，这个前提是你必须要有其权限(比如说是
root)才能够指定他人的时程表。如果不使用 -u user 的话，就是表示设定自己的时程表。
3.参数说明：
-e :执行文字编辑器来设定时程表，内定的文字编辑器是 VI，如果你想用别的文字编辑器，
    则请先设定 VISUAL 环境变数来指定使用那个文字编辑器
-r :删除目前的时程表
-l :列出目前的时程表
4.格式:
# use '*' in these fields (for 'any')
# day of month (dom)/day of week (dow)
# 星期中0表示周日
 m     h    dom   mon   dow  command
0-55  0-23  1-31  1-12  0-6  command
m 为  *  表示每分钟都要执行command,以此类推
m 为 a-b 表示从第 a 分钟到第 b 分钟这段时间内要执行,以此类推
m 为 */n 表示每 n 分钟个时间间隔执行一次,以此类推
m 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行
5.示例:
每月每天每小时的第 0 分钟执行一次 /bin/ls
0 * * * * /bin/ls
在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 /usr/bin/backup
0 6-12/3 * 12 * /usr/bin/backup
周一到周五每天下午 5:00 寄一封信给 alex@domain.name
0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata
每月每天的午夜 0 点 20 分, 2 点 20 分, 4 点 20 分....执行 echo "haha"
20 0-23/2 * * * echo "haha"
意思是每两个小时重启一次apache 
0 */2 * * * /sbin/service httpd restart  
每月1号和15号检查/home 磁盘 
0 0 1,15 * * fsck /home  
意思是每月的1、11、21、31日是的6：30执行一次ls命令
30 6 */10 * * ls  

--知识点：
1.安装sudp apt-get install cron # 服务器环境下默认已安装
2.当程序在你所指定的时间执行后，系统会寄一封信给你，显示该程序执行的内容，若是你不
希望收到这样的信，请在每一行空一格之后加上 > /dev/null 2>&1 即可
3.windows使用计划任务功能完成定时任务
4.可执行文件绿色
5.crontab编辑好即开始执行定时任务,且每个任务需在一行写完.
6.Linux tail 命令
tail 命令可用于查看文件的内容，有一个常用的参数 -f(-f 循环读取) 常用于查阅正在改
变的日志文件。tail -f filename 会把 filename 文件里的最尾部的内容显示在屏幕上，
并且不断刷新，只要 filename 更新就可以看到最新的文件内容。
命令格式：'tail [参数] [文件]  '
tail -f run.log
7.nano 是一个字符终端的文本编辑器,比vi/vim简单

--使用
可以直接使用crontab执行定时任务,也可以配合sh脚本使用
配合sh脚本:
1.将执行命令写入.sh脚本
2.给.sh脚本添加可执行权限 'chmod +x xxx.sh'
3.将.sh程序写入crontab配置文件中
示例:
<hello.sh>
#!/bin/sh   # 使用/bin/sh执行下面内容,注意开头的/不能忘
cd `dirname $0` || exit 1  # cd到当前目录,失败则执行退出,'`'在~键位置
python ./hello.py >> hellosh.log 2>&1 # 将屏幕输出的内容重定向到run.log
# 加入'2>&1'表示 把执行错误也输出到hellosh.log 本只输出标准输出
# 0表示标准输入 1表示标准输出 2 表示标准错误
# 这个重定向表示将py程序的输出重定向到hellosh.log
# 若取消cd到当前目录的操作,则需要将所执行程序使用绝对路径定位

<hello.py>
import time
print('-'*10)
print('hello,world')
print(time.ctime())

<crontab>
# 注意写绝对路径
* * * * * /home/ironman/hello.sh >> /home/ironman/hellopy.log 2>&1
# 将屏幕输出的内容重定向到run.log,同时把执行错误一起输出到hellopy.log
# 这个重定向表示将sh脚本程序的输出重定向到hellopy.log
# 在终端输入'./hi.sh' 或 'sh hi.sh'即可执行/home/iron下的hi.sh
# hello.sh文件不需要指定sh执行
'''本示例所示结构中,crontab输出的是sh脚本的输出,sh脚本输出的是py的输出.所以在sh
脚本不将输出重定向的情况下,crontab输出的是py的输出.'''



"MongoDB"
--介绍
1.非关系型数据库
2.没有数据表的存在,数据之间没有对应关系,每条数据都是一个独立的整体

--比较
关系型数据库扩展性差,大数据下IO压力大,表结构更改困难.MongoDB易扩展,大数据
量高性能,灵活的数据模型,高可用.
关系型的数据就像把一台车拆成很多零件,MongoDB就像一台整车.

--优点:
1.易扩展:NoSQL数据库种类繁多,但是一个共同的特点就是去掉关系型数据库的关系型
特征.数据之间无关系,这样就容易扩展
2.大数据量/高性能:NoSQL数据库都具有非常高的读写性能,尤其是在大数据量下,同样
表现优秀.这得益于它的无关系性,数据库的结构简单
3.灵活的数据模型:NoSQL无需事先为要存储的数据建立字段,随时可以存储自定义的数
据格式.而在关系数据库里,增删字段是非常麻烦的事情.

--缺点
1.多次使用的数据会重复存储,占用空间

--安装
'apt方式'
sudo apt-get install -y mongodb # '-y'默认后面的提示输入yes  ubuntu适用
'tar方式'
tar -zxvf mongodb-linux....3.4.0.tgz # 解压
sudo mv -r mongodb-linux....3.4.0.tgz /usr/local/mongodb
export PATH=/usr/local/mongodb/bin:$PATH # 将可执行文件添加到PATH路径中

--启动服务端mongod
'apt方式'
sudo service mongodb start
sudo service mongodb stop
sudo service mongodb restart
'tar方式'
sudo mongod --config /usr/local/momgod.conf

--启动客户端mongo
1.启动本地客户端 mongo <-h -p远程>
2.查看帮助 mongo -help
3.退出 exit ctrl+c

--其他 
1.查看帮助 mongod -help
2.查看启动是否成功 ps ajx|grep mongod
3.默认端口:27017
4.日志:/var/log/momgoddb/mongod.log



"MongoDB操作"
--数据库操作
查看当前数据库: db
查看所以数据库: show dbs/databases
切换数据库: use db_name # db_name不存在时,自动创建数据库
删除当前数据库: db.dropDatabase()

--关于集合的基础命令
1.MongoDB没有表的概念,数据(字典/文档)存在集合中.
2.一般不手动创建集合:向不存在的集合中第一次加入数据时,集合自动创建
3.创建集合: 
db.createCollection(name,options) # db当前数据库
db.createCollection('test')
db.createCollection('teach',{capped:true,size:10})
参数capped默认为false表示不设置上限,为true表示设置上限,size表示上限的字
节数,当文档达到上限时,会覆盖之前的数据.(相当于先进先出的队列)
4.查看集合:show collections
5.删除集合:db.集合名.drop() # db.test.drop()

--数据类型
Object ID ：自生成的文档id
# 每个文档都有一个属性,为_id,保证文档的唯一性. 可以自行去设置_id插入文档
# id为12字节的十六进制数:(4当前时间戳+3机器id+2MongoDB服务进程id+3简单的增量值)
string： 字符串，必须是有效的utf-8
Boolean：布尔值，true 或者false (小写)
Integer：整数 (32/64位,取决于服务器)
Double：浮点数 (没有float类型,所有小数都是Double)
Arrays：数组或者列表，多个值存储到一个键
Object：文档,类似json字典格式
Null：Null值
Timestamp：时间戳,表示1970/1/1到现在的总秒数
Date：存储当前日期或时间的unix时间格式(格式为'YYYY-MM-DD')
# 自动将其他时间格式转化为此时间格式
> new Date('2018.05.11')
ISODate("2018-05-10T16:00:00Z")

--文档操作
1.增
'db.集合名.insert(<_id='xx'>)'
> db.stu.insert({name:'sjm'})
WriteResult({ "nInserted" : 1 })
db.stu.insert({_id:007,name:'tzh'})
WriteResult({ "nInserted" : 1 })

2.查 
'db.集合名.find()'
> db.stu.find()
{ "_id" : 7, "name" : "tzh" }
{ "_id" : ObjectId("5d5e966487f7c7d9fbfb85eb"), "name" : "sjm" }

3.保存 
'db.集合名.save(document)'
db.stu.save({_id:7,name:'zd',age:20})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
'''db.collecion.insert({}) 插入数据，`_id`存在就报错  
   db.collection.save({}) 保存数据，`_id`存在会覆盖(全部)'''
db.stu.insert({_id:7,name:'zd',age:20})
>报错

4.改
----全部更新 'db.集合名.update({筛选条件},{更新目标}<,{multi:布林}>)'
{ "_id" : ObjectId("5d5e9c7328d6556e3d7586ab"), "name" : "yx", "age" : 24 }
>db.stu.update({name:'yx'},{age:88})
{ "_id" : ObjectId("5d5e9c7328d6556e3d7586ab"), "age" : 88 }
# multi 默认false,表示只更新匹配的第一条文档,为true表示满足条件的所有文档
# multi只能和$一起使用

----指定数据更新 'db.集合名.update({筛选条件},{$set:{更新目标}},{<multi:布林}>)'
> db.stu.update({name:'zj'},{$set:{age:99}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

5.删
'db.集合名.remove({筛选条件},<{justOne:布林}>)'
# justOne 默认false,表示删除多条. 设为true/1,表示删除一条

--知识点
1.时间格式
>>> import time
>>> time.ctime()
'Thu Aug 22 21:07:37 2019'
>>> import datetime
>>> datetime.datetime.now()
datetime.datetime(2019, 8, 22, 21 , 8, 31, 595358)



"MongoDB高级查询"
--数据查询
1.查询 find()
db.集合名.find({条件})
> db.stu.find({age:25})

2.查询,只返回一个 findOne()
db.集合名.findOne({条件})
> db.stu.findOne({age:25})

3.结果格式化 pretty()
db.集合名.find({条件}).pretty()
> db.stu.find().pretty()

--比较运算符
db.集合名.find({key:{条件}})
1.无运算符
>db.stu.find({name:'tzh'})

2.有运算符 
小于:$lt (less than)
小于等于:$lte (less than equal)
大于:$gt (greater than)
大于等于:$gte
不等于:$ne
> db.stu.find({where:'hunan',age:{$gte:18}})

--范围运算符
1.在范围内 $in
db.集合名.find({key:{$in:条件}})
> db.stu.find({age:{$in:[18,25]}})
2.不在范围内 $nin
db.集合名.find({key:{$nin:条件}})
> db.stu.find({age:{$nin:[18,25]}})

--逻辑运算符
1.and 在json中写多个条件即可
> db.stu.find({where:'hunan',age:20})
2.or 使用$or,值为数组,数组中每个元素为json
> db.stu.find({$or:[{where:'hunan'},{age:{$lte:24}}]})
3.and or 一起用 
> db.stu.find({$or:[{where:'hunan'},{age:24}],name:'tzh'})
# 编辑麻烦可以在编辑器写mongodb语句再去mongo执行

--支持正则表达式
使用/.../ 或 $regex编写正则表达式
# ^以什么开始/$以什么结尾
> db.stu.find({name:/^z/})
> db.stu.find({where:{$regex:'i$'}})

--limit和skip
1.limit() 读取指定数量的文档
db.集合名.find().limit()
>db.stu.find().limit(2)
2.skip() 跳过指定数量的文档
db.集合名.find().skip()
>db.stu.find().skip(4)
3.同时使用 # 用于翻页
db.stu.find().skip(4).find(2)
db.stu.find().find(2).skip(4) 

--自定义查询
$where 在其后写一个js函数,返回满足条件的数据
> db.stu.find({$where:function(){return this.age<20;}})
# 实际使用中,可以在python中使用python语句更方便. 在终端适合使用js语句

--投影
显示指定的字段
db.集合名.find({条件},{字段名:1,...}) 
# 置为1表示显示.未置1的即为不显示的
# _id默认显示,不显示需置0
> db.stu.find({$where:function(){return this.age<24;}},{name:1,_id:0})

--排序
sort() 对集合进行排序 # 1升序/-1降序
db.集合名.find().sort({字段:1/-1,...})
> db.stu.find().sort({age:1,name:-1}) # 先age再name

--统计个数
count() 统计结果集合中的文档条数
db.集合名.find({条件}).count() #count不能也不需要再筛选
db.集合名.count({条件})
> db.stu.find({age:25}).count()
> db.stu.count({age:25})

--消除重复
distinct() 对数据去重
db.集合名.distinct('去重字段',{条件})
> db.stu.distinct('where') # 显示去重后的where字段的值
> db.stu.distinct('where',{age:{$gt:20}}) 



"MongoDB的备份恢复"
--备份
'mongodump -h dbhost -d dbname -o dbdirectory'
-h:服务器地址,也可以指定端口号 -d 需要备份的数据库名称 -o 备份数据存放位置
'ironman@IM:~$ mongodump -d test1 -o ~/mongobackup/'

--恢复 
'mongorestore -h dbhost -d dbname --dir dbdirectory'
-h:服务器地址 -d:需要恢复的数据库实例 --dir: 备份数据所在位置
'ironman@IM:~$ mongorestore -d test2 --dir ~/mongobackup/test1'



"聚合"
聚合(aggregate)是基于数据处理的聚合管道,每个文档通过一个由多个阶段(stage)
组成的管道,可以对每个阶段的管道进行分组/过滤等功能,然后经过一系列的处理,输出
相应的结果.
'db.集合名.aggregate({管道:{表达式}})'

db.stu.aggregate(
    {$match:{age:25}},
    {$group:{_id:'$where',counter:{$sum:1}}}
    {$project:{_id:0,counter:0,where:'$_id',avg:'$counter'}})



--知识点
1.安装deb软件包
sudo dpkg -i <package.deb>
# 更多https://blog.csdn.net/zhuhan_june/article/details/79823562



"线程"
--多任务的概念
简单地说，就是操作系统可以同时运行多个任务。
打个比方，一边在用浏览器上网，一边在听MP3，一边在用Word，这就是多任务，至少
同时有3个任务正在运行。
之前,一个程序只能做一件事.使用多任务之后,一个py文件内的多个函数能同时执行.

--单核CPU怎么执行多任务的呢
单核意味着同一时刻只能做一件事
操作系统轮流让各个任务交替执行，任务1执行0.01秒，切换到任务2，任务2执行
0.01秒，再切换到任务3，执行0.01秒……这样反复执行下去。其实,每个任务都
是交替执行的，但是，由于CPU的执行速度实在是太快了，我们感觉就像所有任务都在
同时执行一样。-时间片轮转(高频切换)
调度方式:时间片轮转(高频切换)/优先级调度(对于实时要求性不强的,可以延后执行)

--并行和并发
真正的并行执行多任务只能在多核CPU上实现，但是，由于任务数量远远多于CPU的核
心数量，所以，操作系统也会自动把很多任务轮流调度到每个核上执行。
1.并发：指的是任务数多于cpu核数，通过操作系统的各种任务调度算法，实现多个任务
“一起”执行（实际上总有一些任务不在执行，因为切换任务的速度相当快，看上去一起
执行而已）-假的多任务
2.并行：指的是任务数小于等于cpu核数，即任务真的是一起执行-真的多任务

--创建线程执行任务
线程即实现多任务的一种手段
# coding=utf-8
import threading   -0-->  # 一个程序运行后,一定有个执行代码的东西,这个东西称之为线程
                         # 看代码,箭头在往下走,这个箭头就可以看作线程
import time

def saySorry(): -1-->     # 定义一个函数,箭头不会进去执行
    print("我错了，我能吃饭了吗？")
    time.sleep(1)

if __name__ == "__main__":   # 运行到这,为ture,所以进去执行
    for i in range(5):
        t = threading.Thread(target=saySorry) # 创建一个实例对象；'target=saySorry'中'say..'就是一个变量名,指向函数'say..'
        t.start() -0--> + -1--> # 启动子线程，即让子线程开始执行      
                                # 调用start后,生成了一个子线程,也就是新的小箭头.因为上一行代码指定了函数,所以小箭头会去执行指定函数
                                # 两个箭头都可以单独执行代码/每个程序都会有一个主线程
1.当调用start()时，才会创建子线程，并且开始执行子线程. 调用Thread类只是创建了一个对象
2.线程的运行没有先后顺序,是随机的.可以通过延迟操作,调整执行顺序
3.start()后,创建Thread时所指定的函数运行结束则子线程结束
4.子线程结束后,主线程才能结束.因为主线程结束即程序结束,主线程若先结束,则子线
程无法执行.若主线程意外结束,程序则结束,即子线程也会结束.

--知识点
1.'类名()'为创建一个对象
2.'函数名()'为调用函数 / '函数名'为变量名,相当于指示函数位置
3.看别人代码需要先找main函数,而不是从上往下看
4.enumerate(iterable, start=0) # enumerate-列举/枚举
    -定义:返回一个枚举对象。iterable 必须是一个序列或 iterator，或其他支持迭代的对象
    enumerate() 返回的迭代器的 __next__() 方法返回一个元组，里面包含一个计数值（从
    start 开始，默认为 0）和通过迭代 iterable 获得的值。
    >>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
    >>> list(enumerate(seasons))
    [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
    >>> list(enumerate(seasons, start=1))
    [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
    等价于:
    def enumerate(sequence, start=0):
        n = start
        for elem in sequence:
            yield n, elem
            n += 1
 
    --使用
    l = ['a','b','c']
    for i in enumerate(l):
    ...     print(i)
    ... 
    (0, 'a')
    (1, 'b')
    (2, 'c')
    >>> for i,j in enumerate(l, 1):
    ...     print(i,j)
    ... 
    1 a
    2 b
    3 c
    >>> print(enumerate(l))
    <enumerate object at 0x7fcad349a360>
5.拆包
>>> k = ['hello','world']
>>> a,b = k
>>> a
'hello'
>>> b
'world'
>>> 
>>>a,b
('hello','world')

--threading.enumerate()
以列表形式返回当前所有存活的 Thread 对象。该列表包含守护线程，current_thread() 创建的虚拟线程对象和主线程。它不包含已终结的线程和尚未开始的线程.
print(threading.enumerate()) # 返回所有线程
>>>[<_MainThread(MainThread, started 139934431356672)>, <Thread(Thread-1, started 139934400153344)>, <Thread(Thread-2, started 139934391760640)>]
print(len(threading.enumerate()) # 返回线程数量
>>>3

--通过继承Thread类完成创建线程
之前是直接通过"t = threading.Thread(target=XXX)"创建线程完成多任务的程
序开发，为了让每个线程的封装性更完美，能执行复杂的线程任务,可以通过定义一个继
承threading.Thread的子类，再重写run方法即可
'''
class MyThread(threading.Thread):
    def run(self):
        self.login()
        self.register()
        pass
    def login(self):
        pass
    def register(self):
        pass
        
t = MyThread() # 创建一个MyThread类的实例对象
t.start() # 调用start方法,start方法内就是调用线程对象的run方法
'''
threading.Thread类的run方法，用于定义线程的功能函数，可以在自己的线程类中
覆盖该方法。而创建自己的线程实例后，通过Thread类的start方法，可以启动该线程，
交给系统进行调度，当该线程获得执行的机会时，就会调用run方法执行线程。

--知识点
1.多线程程序的执行顺序是不确定的。当执行到sleep语句时，线程将被阻塞(Blocked)
到sleep结束后，线程进入就绪（Runnable）状态，等待调度。而线程调度将自行选择
一个线程执行。只能保证每个线程都运行完整个run函数，但是线程的启动顺序、run函
数中每次循环的执行顺序都不能确定。
2.每个线程默认有一个名字，尽管上面的例子中没有指定线程对象的name，但是python
会自动为线程指定一个名字。self.name属性中保存的是当前线程的名字
3.线程的run()方法结束时该线程完成。

--全局变量
1.全局变量-函数外 / 局部变量-函数内
2.函数内修改全局变量,是否需要使用global进行说明?
要看是否对全局变量的指向进行了修改.
若修改了指向,即让全局变量指向了一个新的地方,则必须使用global.
若仅仅修改了指向位置的数据,此时不必使用global.
3.不可变的数据类型:数字/字符串/元组. 因为这三类数据不可变,修改指向,才能修改数
据.所以必须使用global说明.
'''
num = 100
nums = [11, 22]
def test():
    global num
    num += 100
def test2():
    nums.append(33)
'''

--多线程共享全局变量
在一个进程内的所有线程共享全局变量,很方便在多个线程间共享数据.
(思想:其实各个子线程可看做函数,函数共享数据)，
(若多线程不共享数据,在爬虫中,只能自己下数据自己发数据,又变回了单线程)
缺点，线程对全局变量的随意更改可能造成多线程之间对全局变量的混乱,即线程非安全.
共享全局变量的两种方式:
1.线程中直接使用. 注意:修改不可变数据类型时,需使用'global xxx'说明 
2.通过创建线程时将全局变量作为实参传递
实参'args'指定传递到函数的参数,其为元组,注意+','
def test1(temp):
    temp.append(33)
    print("-----in test1 temp=%s----" % str(temp))
def test2(temp):
    print("-----in test2 temp=%s----" % str(temp))
    '''
def test2()
    global g_num   # 共享方法1:直接调用去使用
    g_num += 100
    print("-----in test2 g_num=%d=----" % g_num)
    '''
g_nums = [11, 22]
def main():
    # target指定将来这个线程去哪个函数执行代码
    # args指定将来调用函数的时候,传递什么数据过去
    t1 = threading.Thread(target=test1, args=(g_nums,))
    t2 = threading.Thread(target=test2, args=(g_nums,))
    t1.start()
    time.sleep(1)
    t2.start()
    time.sleep(1)
    print("-----in main Thread g_nums = %s---" % str(g_nums))
if __name__ == "__main__":
    main()

--多线程共享全局变量遇到的问题
假设两个线程t1和t2都要对全局变量g_num进行加1运算，t1和t2都各对g_num加10次，
g_num的最终的结果应该为20. 但是由于是多线程同时操作，有可能出现下面情况：
在g_num=0时，t1取得g_num=0。此时系统把t1调度为”sleeping”状态，把t2转换为
”running”状态，t2也获得g_num=0.然后t2对得到的值进行加1并赋给g_num，使得
g_num=1然后系统又把t2调度为”sleeping”，把t1转为”running”。线程t1又把它之
前得到的0加1后赋值给g_num。这样导致虽然t1和t2都对g_num加1，但结果仍然是g_num=1
------------------------------------------------------------------
----------------------------资源竞争问题----------------------------
import threading
import time

g_num = 0

def work1(num):
    global g_num
    for i in range(num):
        g_num += 1
    print("----in work1, g_num is %d---"%g_num)


def work2(num):
    global g_num
    for i in range(num):
        g_num += 1
    print("----in work2, g_num is %d---"%g_num)


print("---线程创建之前g_num is %d---"%g_num)

t1 = threading.Thread(target=work1, args=(100000,)) 
t1.start()
# args较小时正常运行. 个人理解是因为在切换子线程之前,之前的子线程就已经完成

t2 = threading.Thread(target=work2, args=(100000,))
t2.start()

while len(threading.enumerate()) != 1:
    time.sleep(1)

print("2个线程对同一个全局变量操作之后的最终结果是:%s" % g_num)
------------------------------------------------------------------
----------------------------资源竞争问题----------------------------
-出错过程分析:
关键操作'g_num += 1'实际交给cpu执行时,cpu是一句句执行的:
1.获取g_num的值 
2.获取到的值+1 
3.第2步得到的结果存储到g_num中
-某线程可能在执行完2操作后,还未执行3,cpu就去处理其他线程,导致结果并未储存生效,
进而导致全局变量的计算出错. 任何语言都会有这个问题,因为问题出在cpu.
-根本原因:一个操作未完成,就去执行其他操作
-结论:
如果多个线程同时对同一个全局变量操作，会出现资源竞争问题，从而操作结果错误
解决思路:一个操作要么不做,要么做完.即为原子性.互相配合,你做完我再做----引出同步概念

--同步的概念
-同步就是协同步调，按预定的先后次序进行运行。如:你说完，我再说。
"同"字从字面上容易理解为一起动作,其实不是，"同"字应是指协同、协助、互相配合。
如进程、线程同步，可理解为进程或线程A和B一块配合，A执行到一定程度时要依靠B的
某个结果，于是停下来，示意B运行;B执行，再将结果给A;A再继续操作。
-解决线程同时修改全局变量的方式
对于上一小节提出的那个计算错误的问题，可以通过线程同步来进行解决
-思路，如下:
1.系统调用t1,然后获取到g_num的值为0,此时上一把锁,即不允许其他线程操作g_num
2.t1对g_num的值进行+1
3.t1解锁，此时g_num的值为1，其他的线程就可以使用g_num了，此时g_num的值不
是0而是1
4.同理其他线程在对g_num进行修改时，都要先上锁，处理完后再解锁，在解锁前的整个
过程中不允许其他线程访问，就保证了数据的正确性.----引出锁的概念

--互斥锁
互斥锁是一个在锁定时不属于特定线程的同步基元组件。在Python中，它是能用的最低
级的同步基元组件，由 _thread 扩展模块直接实现。
原始锁处于 "锁定" 或者 "非锁定" 两种状态之一。它被创建时为非锁定状态。它有两
个基本方法， acquire() 和 release() 。当状态为非锁定时，acquire()将状态
改为锁定并立即返回。当状态是锁定时，acquire()将阻塞至其他线程调用 release()
将其改为非锁定状态，然后 acquire() 调用重置其为锁定状态并返回。 release() 
只在锁定状态下调用； 它将状态改为非锁定并立即返回。如果尝试释放一个非锁定的锁，
则会引发 RuntimeError  异常。
锁同样支持 上下文管理协议。
当多个线程在 acquire() 等待 状态转变为未锁定 被阻塞，然后 release() 重置状态
为未锁定时，只有一个线程能继续执行；至于哪个等待线程继续执行没有定义，并且会根
据实现而不同.
所有方法都是自动执行的。
-概念
当多个线程几乎同时修改某一个共享数据的时候，需要进行同步控制.
线程同步能够保证多个线程安全访问竞争资源，最简单的同步机制是引入互斥锁.
互斥锁为资源引入一个状态：锁定/非锁定.
某个线程要更改共享数据时，先将其锁定，此时资源的状态为“锁定”，其他线程不能更改；
直到该线程释放资源，将资源的状态变成“非锁定”，其他的线程才能再次锁定该资源。互
斥锁保证了每次只有一个线程进行写入操作，从而保证了多线程情况下数据的正确性。
-使用
threading模块中定义了Lock类，可以方便的处理锁：
# 创建锁
mutex = threading.Lock()
# 获得锁
mutex.acquire()
# 释放锁
mutex.release()
-实例:
'''
...
def test1(num):
    global g_num
    for i in range(num):
        mutex.acquire()  # 上锁
        g_num += 1
        mutex.release()  # 解锁

    print("---test1---g_num=%d"%g_num)

def test2(num):
    global g_num
    for i in range(num):
        mutex.acquire()  # 上锁
        g_num += 1
        mutex.release()  # 解锁
mutex = threading.Lock()
...
'''
-上锁解锁过程
当一个线程调用锁的 acquire()方法获得锁时，锁就进入“locked”状态。
每次只有一个线程可以获得锁。如果此时另一个线程试图获得这个锁，该线程就会变为
“blocked”状态，称为“阻塞”，直到拥有锁的线程调用锁的release()方法释放锁之后，
锁进入“unlocked”状态。
线程调度程序从处于同步阻塞状态的线程中选择一个来获得锁，并使得该线程进入 运行(running)状态。
-总结
锁的好处：
确保了某段关键代码只能由一个线程从头到尾完整地执行
锁的坏处：
阻止了多线程并发执行，包含锁的某段代码实际上只能以单线程模式执行，效率就大大地下降了
由于可以存在多个锁，不同的线程持有不同的锁，并试图获取对方持有的锁时，可能会造成死锁

--死锁
-死锁是一种状态
在线程间共享多个资源的时候，如果两个线程分别占有一部分资源并且同时等待对方的资
源，就会造成死锁。
尽管死锁很少发生，但一旦发生就会造成应用的停止响应. 
------------------------------------------------------------------
----------------------------死锁问题-------------------------------
#coding=utf-8
import threading
import time

class MyThread1(threading.Thread):
    def run(self):
        # 对mutexA上锁
        mutexA.acquire()

        # mutexA上锁后，延时1秒，等待另外那个线程 把mutexB上锁
        print(self.name+'----do1---up----')
        time.sleep(1)

        # 此时会堵塞，因为这个mutexB已经被另外的线程抢先上锁了
        mutexB.acquire()
        print(self.name+'----do1---down----')
        mutexB.release()

        # 对mutexA解锁
        mutexA.release()

class MyThread2(threading.Thread):
    def run(self):
        # 对mutexB上锁
        mutexB.acquire()

        # mutexB上锁后，延时1秒，等待另外那个线程 把mutexA上锁
        print(self.name+'----do2---up----')
        time.sleep(1)

        # 此时会堵塞，因为这个mutexA已经被另外的线程抢先上锁了
        mutexA.acquire()
        print(self.name+'----do2---down----')
        mutexA.release()

        # 对mutexB解锁
        mutexB.release()

mutexA = threading.Lock()
mutexB = threading.Lock()

if __name__ == '__main__':
    t1 = MyThread1()
    t2 = MyThread2()
    t1.start()
    t2.start()
------------------------------------------------------------------
----------------------------死锁问题-------------------------------
-避免死锁
1.程序设计时要尽量避免（银行家算法-指的是按照预想的流程处理上锁和解锁)
2.添加超时时间等(长时间锁不上就不锁了...)



"进程"
--进程的概念
程序：例如xxx.py/xxx.exe这是程序，是一个静态的。（exe是可执行二进制文件）
进程：一个程序运行起来后，代码+用到的资源（摄像头、键盘等）称之为进程，它是操
作系统分配资源的基本单元。
多进程是实现多任务的另一种方式。

--进程的状态
工作中，任务数往往大于cpu的核数，即一定有一些任务正在执行，而另外一些任务在等
待cpu进行执行，因此导致了有了不同的状态。
就绪态：运行的条件都已经满足，正在等待cpu执行
执行态：cpu正在执行其功能
等待态：等待某些条件满足，例如一个程序sleep了，此时就处于等待态

--使用
import multiprocessing  # 注意不是processing
p1 = multiprocessing.Process(target=xxx)
p2 = multiprocessing.Process(target=xxx)
p1.start()  # 创建新的子进程
p2.start()  # 子进程的代码和使用到的资源和主进程完全一样，pid不同
1.多进程完成多任务占的资源最大，因为子进程的代码和使用到的资源和主进程完全一样
2.进程数在一定数量内，比单任务高效。但进程数不是越多越好，进程数达到一定数量后，
运行会越来越慢。这个数量根据电脑情况决定
3.多进程中能共享的资源会自动尽量共享，不会复制，这样节约资源。比如代码不需要复制，可以共享。

--任务查看
ps -aux     # 显示所有进程
'''
USER-指明了是哪个用户启动了这个命令
%CPU-用户可以查看某个进程占用了多少CPU
%MEM-内存使用率
VSZ-虚拟内存大小
RSS-常驻集大小
VSZ-表示如果一个程序完全驻留在内存的话需要占用多少内存空间
RSS-指明了当前实际占用了多少内存
STAT-显示了进程当前的状态
STAT状态有很多中，Ss、Ss1、Ss+、S<、R+、S<s1、S<s
"S":进程处在睡眠状态(idle)，但可以被喚醒(signal)，表明这些进程在等待某些事件发生--可能是用户输入或者系统资源的可用性
D不可中断 Uninterruptible（usually IO）,不可被喚醒的睡眠狀態，通常這个程序可能在等待I/O的情況(ex>列印)
R正在运行，或在队列中的进程
T停止狀態(stop)，可能是在工作控制(背景暫停)或除錯 (traced) 狀態；
Z (Zombie)僵屍狀態，程序已經終止但卻無法被移除至記憶體外。
W进入内存交换（从内核2.6开始无效）
"X":死掉的进程
"L":有些页被锁进内存
"<":高优先级
"n":低优先级
"s":包含子进程
"+":位于后台的进程组
"l":多线程，克隆线程multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
"WCHAH":正在等待的进程资源
START-行程开始时间
TIME-执行的时间
COMMAND-所执行的指令的名称和参数
'''
ls -a  # 显示隐藏文件；隐藏文件前缀是'.'



"进程和线程的区别"
--功能
进程，能够完成多任务，比如 在一台电脑上能够同时运行多个QQ
线程，能够完成多任务，比如 一个QQ中的多个聊天窗口

--定义的不同
进程是系统进行资源分配的单位.
线程是进程的一个实体，是CPU调度的基本单位，它是比进程更小的能独立运行的基本单
位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,
一组寄存器和栈)，但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.

--区别
一个程序至少有一个进程，一个进程至少有一个线程.
线程的划分尺度小于进程(资源比进程少)，使得多线程程序的并发性高。
进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的
运行效率。
先有的进程，才有的线程。线程不能够独立执行，必须依存在进程中。
可以将进程理解为工厂中的一条流水线，而其中的线程就是这个流水线上的工人。要提高
生产效率，先在原生产线上加工人，再加生产线。
代码执行后产生进程（包含代码+资源），进程中实际拿资源去运行的是线程

--优缺点
线程和进程在使用上各有优缺点：线程执行开销小，但不利于资源的管理和保护；而进程
正相反。

--多进程和多线程
多线程是在一个进程中有多个线程同时执行，多进程是多个进程同时执行。
多进程使用多份资源，多线程只需要一份资源。



"进程间的通信"
多线程间，数据是共享的，但是进程间的数据是独立的。
进程间通信的方式：
1.socket，通过ip+port通信，需要通信的进程可以不在同一电脑。
2.queue，本身是消息列队程序，数据存在内存中，读取速度快，先进先出。与栈相对应，
  栈先进后出。只能用在同一电脑同一程序的多进程。（redis可以多电脑）
  queue的一个目的是打通进程间的通信，一个是解耦
------------------------------------------------------------------
----------------------------Queue---------------------------------
# 以Queue为例，在父进程中创建两个子进程，一个往Queue里写数据，一个从Queue里读数据：
from multiprocessing import Process, Queue
import os, time, random

# 写数据进程执行的代码:
def write(q):
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        q.put(value)
        time.sleep(random.random())

# 读数据进程执行的代码:
def read(q):
    while True:
        if not q.empty():
            value = q.get(True)
            print('Get %s from queue.' % value)
            time.sleep(random.random())  
            # random.random()随机生成0~1之间的浮点数
        else:
            break

if __name__=='__main__':
    # 父进程创建Queue，并传给各个子进程：
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程pw，写入:
    pw.start()    
    # 等待pw结束:
    pw.join()
    # 启动子进程pr，读取:
    pr.start()
    pr.join()
    # pr进程里是死循环，无法等待其结束，只能强行终止:
    print('')
    print('所有数据都写入并且读完')
------------------------------------------------------------------
----------------------------Queue---------------------------------
'''
# 推荐的方式，先判断消息列队是否已满，再写入
if not q.full():
    q.put_nowait("消息4")

# 读取消息时，先判断消息列队是否为空，再读取
if not q.empty():
    for i in range(q.qsize()):
        print(q.get_nowait())
'''
--说明
初始化Queue()对象时（例如：q=Queue()），若括号中没有指定最大可接收的消息数量，或数量为负值，那么就代表可接受的消息数量没有上限（直到内存的尽头）；
Queue.qsize()：返回当前队列包含的消息数量；
Queue.empty()：如果队列为空，返回True，反之False；
Queue.full()：如果队列满了，返回True,反之False；
Queue.get([block[, timeout]])：获取队列中的一条消息，然后将其从列队中移除，block默认值为True；
1）如果block使用默认值，且没有设置timeout（单位秒），消息列队如果为空，此时程序将被阻塞（停在读取状态），直到从消息列队读到消息为止，如果设置了timeout，则会等待timeout秒，若还没读取到任何消息，则抛出"Queue.Empty"异常；
2）如果block值为False，消息列队如果为空，则会立刻抛出"Queue.Empty"异常；
Queue.get_nowait()：相当Queue.get(False)；
Queue.put(item,[block[, timeout]])：将item消息写入队列，block默认值为True；
1）如果block使用默认值，且没有设置timeout（单位秒），消息列队如果已经没有空间可写入，此时程序将被阻塞（停在写入状态），直到从消息列队腾出空间为止，如果设置了timeout，则会等待timeout秒，若还没空间，则抛出"Queue.Full"异常；
2）如果block值为False，消息列队如果没有空间可写入，则会立刻抛出"Queue.Full"异常；
Queue.put_nowait(item)：相当Queue.put(item, False)；

 

"进程池 Pool"
--原理
先创建一个池，里面包含一定数量（数量根据压力测试达到最高性能时的情况）的进程，
来了大量任务先运行一定数量的进程，当有进程结束后，进程池自动调用其他任务去运行，
达到重复利用的特点。创建进程销毁进程需要大量的时间和资源，在使用进程池时，在未
创建新进程的前提下，完成了重复利用，提升了效率。-类比公园的游船

--实例
初始化Pool时，可以指定一个最大进程数，当有新的请求提交到Pool中时，如果池还没
有满，那么就会创建一个新的进程用来执行该请求；但如果池中的进程数已经达到指定的
最大值，那么该请求就会等待，直到池中有进程结束，才会用之前的进程来执行新的任务
-----------------------------------------------------------------
----------------------------Pool---------------------------------

# -*- coding:utf-8 -*-
from multiprocessing import Pool
import os, time, random

def worker(msg):
    t_start = time.time()
    print("%s开始执行,进程号为%d" % (msg,os.getpid()))
    time.sleep(random.random()) 
    t_stop = time.time()
    print(msg,"执行完毕，耗时%0.2f" % (t_stop-t_start))

po = Pool(3)  # 定义一个进程池，最大进程数3
for i in range(0,10):
    # Pool().apply_async(要调用的目标,(传递给目标的参数元组,))
    # 每次循环将会用空闲出来的子进程去调用目标
    po.apply_async(worker,(i,)) # i为传进worker函数的参数；apply-请求/async-异步
    '''添加任务不会因为进程池满了而失败或堵塞。这里会一次性创建10个任务，进程
    池会拿三个去执行，当有进程结束后，再自动调度其他任务'''

print("----start----")
po.close()  # 关闭进程池，关闭后po不再接收新的请求
# 使用进程池时，主线程不会等待进程池中的进程完成再结束，而是直接结束。
# 所以需要使用 po.join() 让主线程阻塞，等待所有子线程完成
po.join()  # 等待po中所有子进程执行完成，必须放在close语句之后
print("-----end-----")

>>
----start----
0开始执行,进程号为9247      # 进程号只有三个
1开始执行,进程号为9248
2开始执行,进程号为9249
0 执行完毕，耗时0.33
3开始执行,进程号为9247
3 执行完毕，耗时0.10
4开始执行,进程号为9247
4 执行完毕，耗时0.23
5开始执行,进程号为9247
...
-----------------------------------------------------------------
----------------------------Pool---------------------------------
--注意
1.当进程数较多或者进程数不确定时，使用进程池解决。当进程数确定且较少，直接使用
process解决。
2.调用Pool()时，只创建了进程池，其中无进程，当需要使用时，才会创建进程。



"文件夹copy实例"
-----------------------------------------------------------------
----------------------------folder_copy--------------------------------
import multiprocessing
import os
import time
import random


def copy_file(queue, file_name,source_folder_name,  dest_folder_name):
    """copy文件到指定的路径"""
    f_read = open(source_folder_name + "/" + file_name, "rb")
    f_write = open(dest_folder_name + "/" + file_name, "wb")
    while True:
        time.sleep(random.random())
        content = f_read.read(1024)
        if content:
            f_write.write(content)
        else:
            break
    f_read.close()     # 使用with...open简洁
    f_write.close()

    # 发送已经拷贝完毕的文件名字
    queue.put(file_name)


def main():
    # 获取要复制的文件夹
    source_folder_name = input("请输入要复制文件夹名字:")

    # 生成目标文件夹名
    dest_folder_name = source_folder_name + "[副本]"

    # 创建目标文件夹
    try: # 使用try、except 当文件夹存在时，也不会报错
        os.mkdir(dest_folder_name)
    except:
        pass  # 如果文件夹已经存在，那么会跳过创建

    # 获取这个文件夹中所有的普通文件名
    file_names = os.listdir(source_folder_name)

    # 创建Queue
    queue = multiprocessing.Manager().Queue()

    # 创建进程池
    pool = multiprocessing.Pool(3)

    for file_name in file_names:
        # 向进程池中添加任务
        pool.apply_async(copy_file, args=(queue, file_name, source_folder_name, dest_folder_name))

    pool.close()
    # pool.join() 可以用下面的进度条来替代 主线程等待线程池完成 的阻塞

    # 主进程显示进度
    all_file_num = len(file_names)
    while True:
        file_name = queue.get()
        if file_name in file_names:
            file_names.remove(file_name)

        copy_rate = (all_file_num-len(file_names))*100/all_file_num
        print("\r%.2f...(%s)" % (copy_rate, file_name) + " "*50, end="")
        if copy_rate >= 100:
            break
    print()


if __name__ == "__main__":
    main()
-----------------------------------------------------------------
----------------------------folder_copy--------------------------------

--知识点
1.touch命令
-用来创建新的空文件。(可批量创建，touch pic{1..10}.jpg 两个点而非三个）
-用于把已存在文件的时间标签更新为系统当前的时间（默认方式），它们的数据将原
封不动地保留下来；
2.查看标准模块所在路径'import xx   xx.__file__ '
3.ll/ls命令 
ll:会列出该文件下的文件详细信息 
ls:只列出文件名或目录名
4.os.listdir(path='.'）返回一个列表，其中包含path给出的目录中的条目名称。
该列表按任意顺序排列，不包括特殊条目'.'和'..'即使它们存在于目录中。
5.os.mkdir(path) 创建名为path的目录。 os-操作系统接口模块
6.dir(Pool()) 返回一个列表，其中包含Pool()对象的所有属性和方法
7.进程池的产生异常也不会提示
8.进程池的通信需要使用multiprocessing.Manager()对象的Queue()方法
'queue = multiprocessing.Manager().Queue()'
9.操作系统的控制符：
''' 
\r： 将光标移动到当前行的首位而不换行；
\n：将光标移动到下一行，并不移动到首位；
\r\n：将光标移动到下一行首 '''



"迭代器"
--迭代
迭代是访问集合元素的一种方式。对list、tuple、str等类型的数据使用for in的
循环语法从其中依次拿到数据进行使用，我们把这样的过程称为遍历，也叫迭代。

--可迭代对象 iterable object
-概念
能够逐一返回其成员项的对象。可迭代对象的例子包括所有序列类型（例 list、str
和 tuple）以及某些非序列类型例如 dict、文件对象 以及定义了 __iter__() 方
法或是实现了 Sequence 语义的 __getitem__() 方法的任意自定义类对象。
可迭代对象被可用于for循环以及许多其他需要一个序列的地方（zip()、map()..）。
当一个可迭代对象作为参数传给内置函数 iter() 时，它会返回该对象的迭代器。
这种迭代器适用于对值集合的一次性遍历。在使用可迭代对象时，你通常不需要调用 
iter() 或者自己处理迭代器对象。for语句会为你自动处理那些操作，创建一个临时的
未命名变量用来在循环期间保存迭代器。
-判断是否为可迭代对象
1.具备'__iter__'方法的对象，就是可迭代对象
2.使用'isinstance([], Iterable)'判断
3.是否可以通过for...in这类语句迭代读取一条数据供使用

--迭代器 iterator
-概念
用来表示一连串数据流的对象。重复调用迭代器的 __next__() 方法（或将其传给内
置函数 next()）将逐个返回流中的项。当没有数据可用时则将引发 StopIteration 
异常。到这时迭代器对象中的数据项已耗尽，继续调用其 __next__() 方法只会再次
引发 StopIteration 异常。迭代器必须具有 __iter__() 方法用来返回该迭代器
对象自身，因此迭代器必定也是可迭代对象，可被用于其他可迭代对象适用的大部分场
合。一个显著的例外是那些会多次重复访问迭代项的代码。容器对象（例如 list）在
你每次向其传入 iter() 函数或是在 for 循环中使用它时都会产生一个新的迭代器。
如果在此情况下你尝试用迭代器则会返回在之前迭代过程中被耗尽的同一迭代器对象，
使其看起来就像是一个空容器。
-简单概念
记录着生成数据的方式，优点是节省内存空间
-判断是否为迭代器
1.实现了 __iter__ 方法和 __next__ 方法的对象，就是迭代器。
2.使用'isinstance(iter([]), Iterator)'
'''
from collections import Iterator
In [57]: isinstance([], Iterator)
Out[57]: False

In [58]: isinstance(iter([]), Iterator)
Out[58]: True
'''

--iter()函数 和 next()函数
list、tuple 等都是可迭代对象，通过 iter()函数获取这些可迭代对象的迭代器。
对获取到的迭代器不断使用 next()函数来获取下一条数据。iter()函数实际上就是
调用了可迭代对象的 __iter__ 方法。

--'for...in...'循环的本质
'for item in Iterable'循环的本质就是先通过 iter()函数获取可迭代对象
Iterable的迭代器，然后对获取到的迭代器不断调用 next()方法来获取下一个值并
将其赋值给item，当遇到StopIteration的异常后循环结束。

--并不是只有for循环能接收可迭代对象
除了for循环能接收可迭代对象，list、tuple等也能接收。
li = list(FibIterator(15))
print(li)
tp = tuple(FibIterator(6))
print(tp)

--迭代器的应用场景
迭代器最核心的功能就是可以通过 next()函数的调用来返回下一个数据值。
如果每次返回的数据值不是在一个已有的数据集合中读取的，而是通过程序按照一定的
规律计算生成的，那么也就意味着可以不用再依赖一个已有的数据集合，也就是说不用
再将所有要迭代的数据都一次性缓存下来供后续依次读取，这样可以节省大量的存储空间。
举个例子，数学中有个著名的斐波拉契数列（Fibonacci），数列中第一个数为0，第二
个数为1，其后的每一个数都可由前两个数相加得到：
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...
现在我们想要通过for...in...循环来遍历迭代斐波那契数列中的前n个数。那么这个
斐波那契数列我们就可以用迭代器来实现，每次迭代都通过数学计算来生成下一个数。

-----------------------------------------------------------------
----------------------------迭代器实例----------------------------
class FibIterator(object):
    """斐波那契数列迭代器"""
    def __init__(self, n):
        """
        param n: int, 指明生成数列的前n个数
        """
        self.n = n
        # current用来保存当前生成到数列中的第几个数了
        self.current = 0
        # num1用来保存前前一个数，初始值为数列中的第一个数0
        self.num1 = 0
        # num2用来保存前一个数，初始值为数列中的第二个数1
        self.num2 = 1

    def __next__(self):
        """被next()函数调用来获取下一个数"""
        if self.current < self.n:
            num = self.num1
            self.num1, self.num2 = self.num2, self.num1+self.num2
            self.current += 1
            return num
        else:
            raise StopIteration

    def __iter__(self):
        """迭代器的__iter__返回自身即可"""
        return self


if __name__ == '__main__':
    fib = FibIterator(10)
    for num in fib:
        print(num, end=" ")
-----------------------------------------------------------------
----------------------------迭代器实例-----------------------------



"生成器"
--概念
生成器generator是一类特殊的迭代器

--创建生成器的方法
-方法一：for循环
In [15]: L = [ x*2 for x in range(5)] # 列表推导式
In [16]: L
Out[16]: [0, 2, 4, 6, 8]

In [17]: G = ( x*2 for x in range(5)) # 生成器
In [18]: G
Out[18]: <generator object <genexpr> at 0x7f626c132db0>
L是一个列表，G是一个生成器。可以直接打印出L的每一个元素，对于生成器G，可以按
照迭代器的使用方法来使用，即可以通过 next()函数、for循环、list()等方法使用。
区别在于：L储存的是一个列表数据，G储存的是生成数据的方式，更节省空间。

-方法二：定义包含yield的函数
如果推算的算法比较复杂，用类似列表生成式的 for 循环无法实现的时候，还可以用函
数来实现。
-----------------------------------------------------------------
----------------------------生成器实例-----------------------------
In [30]: def fib(n):
   ....:     current = 0
   ....:     num1, num2 = 0, 1
   ....:     while current < n:
   ....:         num = num1
   ....:         num1, num2 = num2, num1+num2
   ....:         current += 1
   ....:         yield num
   ....:     return 'done'
   ....:
In [31]: F = fib(5)

In [32]: next(F)
Out[32]: 1
In [33]: next(F)
Out[33]: 1
In [34]: next(F)
Out[34]: 2
-----------------------------------------------------------------
----------------------------生成器实例-----------------------------
将原本在迭代器__next__方法中实现的基本逻辑放到一个函数中来实现，再将每次迭代
返回数值的return换成了yield，此时新定义的函数便不再是函数，而是一个生成器。
简单来说：只要在def中有yield关键字的，就称为生成器。
此时按照调用函数的方式(案例中为F = fib(5))使用生成器就不再是执行函数体，而是
会返回一个生成器对象（案例中为F），可以按照使用迭代器的方式来使用生成器。

--总结
1.使用了yield关键字的函数不再是函数，而是生成器。
2.yield关键字有两点作用：
-保存当前运行状态（断点），然后暂停执行，即将生成器（函数）挂起，使用next()函
数让生成器从断点处继续执行，即唤醒生成器
-将yield关键字后面表达式的值作为返回值返回，此时可以理解为起到了return的作用
3.生成器特殊在没有__iter__/__next__方法,通过yield完成
4.生成器也是通过 next()启动
5.若需拿到return的返回值，必须捕获StopIteration错误，返回值包含在
StopIteration的value中。 
    '''
    In [39]: g = fib(5)
    In [40]: while True:
       ....:     try:
       ....:         x = next(g)
       ....:         print("value:%d"%x)      
       ....:     except StopIteration as e:
       ....:         print("生成器返回值:%s" % e.value)
       ....:         break
    '''
6.使用yield和return的区别：
执行return后函数会直接结束运行；执行yield，程序暂停运行，记录断点，并能再次
从断点继续执行。
7.迭代器、生成器最大的特点是其保存的是生成数据的方式，而不是数据的结果。

--使用send唤醒
除了可以使用 next()函数来唤醒生成器继续执行外，还可以使用 send()函数来唤醒
继续执行。使用 send()函数的一个好处是可以在唤醒的同时向断点处传入一个附加数
据。
例子：执行到yield时，gen函数作用暂时保存，返回i的值; temp接收下次
f.send("python")，send发送过来的值，next(f)等价f.send(None)
In [10]: def gen():
   ....:     i = 0  #0
   ....:     while i<5:  #1
   ....:         temp = yield i  #2
   ....:         print(temp)    #3
   ....:         i+=1           #4
In [43]: f = gen()

In [44]: next(f) # 执行 ’yield i‘ 后暂停。  0.1.2
Out[44]: 0

In [45]: f.send('haha') # 执行 ’temp='haha'‘   2.3.4.1.2
haha
Out[45]: 1

In [46]: next(f)
None
Out[46]: 2
-注意：一般第一次启动用 next()，若用 send('xxx')会报错，因为刚开始执行时没
有变量接收send的数据，除非使用 send(None)。一般 send()都是用来传数据的，next()用来启动。



"协程"
--概念
协程，又称微线程Coroutine（协同程序），routine（程序）
协程是python中另外一种实现多任务的方式，比线程更小占用更小执行单元（理解为需
要的资源）。它是一个执行单元，因为它自带CPU上下文。这样只要在合适的时机，我们
可以把一个协程切换到另一个协程。只要这个过程中保存或恢复CPU上下文，那么程序还
是可以运行的。
通俗的理解：在一个线程中的某个函数，可以在任何地方保存当前函数的一些临时变量等
信息，然后切换到另外一个函数中执行，注意不是通过调用函数的方式做到的，并且切换
的次数以及什么时候再切换到原来的函数都由开发者自己确定。

--协程和线程差异
在实现多任务时，线程切换从系统层面远不止保存和恢复CPU上下文这么简单。操作系统
为了程序运行的高效性，每个线程都有自己缓存Cache等等数据，操作系统还会帮你做这
些数据的恢复操作。所以线程的切换非常耗性能。但是协程的切换只是单纯的操作CPU的
上下文，所以一秒钟切换个上百万次系统都抗的住。

--简单实现协程
import time

def work1():
    while True:
        print("----work1---")
        yield
        time.sleep(0.5)

def work2():
    while True:
        print("----work2---")
        yield
        time.sleep(0.5)

def main():
    w1 = work1()    # 创建生成器对象，而非调用函数
    w2 = work2()
    while True:
        next(w1) # 启动生成器；并发，假的多任务，因为只有一个线程在执行。
        next(w2) # 此whileTrue部分，相当于管理线程的运行

if __name__ == "__main__":
    main()

--greenlet
为了更好使用协程来完成多任务，python中的greenlet模块对yield封装，从而使得
切换任务变的更加简单。
-安装 'sudo pip3 install greenlet'
-使用：
from greenlet import greenlet
import time

def test1():
    while True:
        print "---A--"
        gr2.switch() # 自行来回切换
        time.sleep(0.5)

def test2():
    while True:
        print "---B--"
        gr1.switch()
        time.sleep(0.5)

gr1 = greenlet(test1) # 调用greenlet类创建greenlet对象，类似生成器对象
gr2 = greenlet(test2)

#切换到gr1中运行
gr1.switch()

--gevent
比greenlet更强大的并且能够自动切换任务的模块，gevent-网络异步并发库-采用协程实现
（greenlet已经实现了协程，但是得人工切换，太麻烦了）
其原理是当一个greenlet遇到IO(指的是input/output/输入输出，比如网络、文件
操作等)操作时，比如访问网络，就自动切换到其他的greenlet，等到IO操作完成，再
在适当的时候切换回来继续执行。由于IO操作非常耗时，经常使程序处于等待状态，有了
gevent为我们自动切换协程，就保证总有greenlet在运行，而不是等待IO。在greenlet
中，若有耗时操作，线程会等待其完成，这样降低效率。
-greenlet封装了yield，gevent封装了greenlet
-安装 'sudo pip3 install gevent'
-使用
import gevent # 单线程下，在线程执行耗时操作时，同时执行其他任务以节约时间

def f(n):
    for i in range(n):
        print(gevent.getcurrent(), i)
        # 用来模拟一个耗时操作，注意不是time模块中的sleep
        gevent.sleep(1)

g1 = gevent.spawn(f, 5) # spawn 繁衍，大量生产
g2 = gevent.spawn(f, 5) # 创建一个greenlet对象。创建对象时并不执行任务
g3 = gevent.spawn(f, 5) 
g1.join()
g2.join()
g3.join()
-打补丁
# 上面的函数，若每个耗时操作都去改用gevent的，太麻烦。应用此补丁解决问题
from gevent import monkey
import gevent
import random
import time

# 有耗时操作时使用这个方法批量修改；patch-打补丁、修补
monkey.patch_all()  # 将程序中用到的耗时操作函数，换为gevent的相应模块

def coroutine_work(coroutine_name):
    for i in range(10):
        print(coroutine_name, i)
        time.sleep(random.random()) # 因此可以继续使用原函数，会自动换成gevent的sleep
 
gevent.joinall([    # 更简洁
        gevent.spawn(coroutine_work, "work1"),
        gevent.spawn(coroutine_work, "work2")
])
-注意
1.协程最大的意义：在执行耗时操作时，会同时去执行其他任务。
2.协程倚赖线程，线程倚赖进程。
3.需要使用协程时，用gevent即可，yield和greenlet是协程的原理。



"多任务总结"
--简单总结
1.运行py程序，产生的是一个进程，进程里面默认的线程是主线程，主线程真正执行代码，即进程是资源分配的单位，线程是真正执行代码的也就是系统真正调度的。一进程一线程，即一个箭头，一个线程在执行，即单任务。一个进程两个线程，即两个箭头，两个地方执行，即多任务。
2.进程是资源分配的单位
3.线程是操作系统调度的单位
4.进程切换需要的资源很最大，效率很低
5.线程切换需要的资源一般，效率一般（当然了在不考虑GIL的情况下）
6.协程切换任务资源很小，效率高
7.多进程、多线程根据cpu核数不一样可能是并行的，但是协程是在一个线程中，所以是并发
8.进程最稳定，一个线程挂掉不会影响其他线程；线程切换资源少；协程利用线程等待的时间去处理其他任务。
9.具体使用什么实现多任务应根据实际情况考虑。

--通俗描述
1.有一个老板想要开个工厂进行生产某件商品（例如剪子）
2.他需要花一些财力物力制作一条生产线，这个生产线上有很多的器件以及材料这些所有的为了能够生产剪子而准备的资源称之为：进程
3.只有生产线是不能够进行生产的，所以老板的找个工人来进行生产，这个工人能够利用这些材料最终一步步的将剪子做出来，这个来做事情的工人称之为：线程
4.这个老板为了提高生产率，想到3种办法：
    -在这条生产线上多招些工人，一起来做剪子，这样效率是成倍増长，即：单进程多线程方式
    -老板发现这条生产线上的工人不是越多越好，因为一条生产线的资源以及材料毕竟有限，所以老板又花了些财力物力购置了另外一条生产线，然后再招些工人这样效率又再一步提高了，即：多进程多线程
    -老板发现，现在已经有了很多条生产线，并且每条生产线上已经有很多工人了（即程序是多进程的，每个进程中又有多个线程），为了再次提高效率，老板想了个损招，规定：如果某个员工在上班时临时没事或者再等待某些条件（比如等待另一个工人生产完某道工序之后他才能再次工作），那么这个员工就利用这个时间去做其它的事情，那么也就是说：如果一个线程等待某些条件，可以充分利用这个时间去做其它事情，其实这就是：协程


"知识点"
1.树形结构显示文件目录结构 'tree -l'
2.





# （3500-3800）共4900
9.19>>1093
9.20>>1200
9.21>>1700
9.24>>2120
/////问题//////
1.  1113 通过js生成的动态加密的密码该如何解决？？？
2.  1313 正则表达式复习
3.  1519 编码问题
4.       时间格式问题
5.       ssh
6.
