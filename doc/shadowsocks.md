1 基本原理
shadowsocks 的基本工作原理并不复杂。shadowsocks 包括 local 和 server 两个程序。local 运行在用户自己的机器上，server 运行在墙外的服务器上。正常工作模式下，local 通常会监听本地 1080 端口，提供 socks v5 协议接口。在用户本机进程和 local 的 1080 端口建立 TCP 连接之后，首先会发送一个 hello 包，或者叫 handshake 握手包。local 程序接收到这个包之后，进行简单的包数据检查之后就返回一个确认包。本机进程收到确认的包之后，会再发送一个请求包，包的主要内容就是目标服务的地址和端口号。local 程序接收到请求包之后，会和 server 程序建立一个 TCP 连接，连接建立之后会把上面的目标服务的地址和端口号传给 server。这个连接是穿墙的关键，连接里面传输的数据都是经过加密的，最常用的就是 aes-256-cfb。local 程序会对请求包返回一个确认的包。然后本机进程就开始向 local 传输实际的数据，local 接收到之后加密继续传给 server。server 接收到之后把数据解密出来，然后和目标服务建立 TCP 连接，并把解密后的数据发送出去。然后接收数据就是上述的反过来。

                                        great firewall
                                             +
                                             |
                                             |
                    +----------+             |            +-----------+
                    |          |             |            |           |
   socks5           |  local   |    encryption            |  server   |   raw data
<------------> 1080 |          | <-----------+----------> |           | <----------->
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    |          |             |            |           |
                    +----------+             |            +-----------+
                                             |
                                             |
                                             |
                                             +
可以有各种版本的实现