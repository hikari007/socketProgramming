# 简介

- **简单的时间获取程序(客户端)**
        
    [daytimetcpcli.c](../intro/daytimetcpcli.c)

    - **总体流程**
        
        - 使用socket()创建tcp套接字
        - 使用servaddr_in指定服务器IP地址和端口
        - 使用connect()建立与服务器连接
        - read()读取服务器应答
        
    - **结构体sockaddr_in**

        ```c
        struct sockaddr_in
          {
            __SOCKADDR_COMMON (sin_);
            in_port_t sin_port;			/* Port number.  */
            struct in_addr sin_addr;		/* Internet address.  */
            
            /* Pad to size of `struct sockaddr'.  */
            unsigned char sin_zero[sizeof (struct sockaddr) -
                 __SOCKADDR_COMMON_SIZE -
                 sizeof (in_port_t) -
                 sizeof (struct in_addr)];
          };
        ```
        `sockaddr_in`一共占16个字节，同`sockaddr`，成员如下
        | 成员|字节 |说明 |
        |:---:|:---:|:---:|
        |sin_family|2|地址族|
        |sin_port  |2|端口  |
        |sin_addr  |4|IP地址|
        |sin_zero  |8|填充  |

        ```c
        #define	__SOCKADDR_COMMON(sa_prefix) \
          sa_family_t sa_prefix##family
        ```
        
        宏定义中`##`表示拼接字符串
        所以`__SOCKADDR_COMMON (sin_)`等同于`sa_family_t sin_family`

    - **套接字函数socket()**
    
        使用<sys/socket.h>头文件的`socket()`函数来创建套接字,接口如下:
        ```c
        int socket(int af, int type, int protocol);
        ```
        (1) af(Address Family)，IP地址类型
        |地址族| 表示 |
        |:---:|:---:|
        |AF_INET|IPv4|
        |AF_INET6|IPv6|

        (2) type为套接字类型
        |套接字|含义|
        |:---:|:---:|
        |SOCK_STREAM|流格式/面向连接|
        |SOCK_DGRAM|数据报格式/无连接|

        (3) protocol为协议类型，置0即可自动推断出协议

        (4) 发生错误时返回-1

        综上，用法如下:
        ```c
        int tcp_socket = socket(AF_INET, SOCK_STREAM, 0);
        int udp_socket = socket(AF_INET, SOCK_DGRAM, 0);
        ```
    - **bzero()函数**

        `void bzero(void *__s, size_t __n)`
        包含头文件`strings.h`，将地址开始的前n个字节置0

    - **htons()函数**

        网络字节序采用**大端模式(big-endian)**，即**高字节存放在低地址，低字节存放在高地址**

        x86 CPU采用**小端模式(little-endian)**，即**高字节存放在高地址，低字节存放在低地址**

        例如，port: 0x1234，在网络字节存储为address, address + 1: 0x12, 0x34，而在x86 CPU内存中存储为address, address + 1:0x34, 0x12

        而htons()函数就是为了将**主机字节顺序(Host Byte Order)** 转换为 **网络字节顺序(Network Byte Order)**

        向套接字fd写入buf缓冲区的n个字节，写入成功返回写入字节数，返回-1表示写入失败，一般不返回0，仅当第三参数为0时什么也不做，返回0
        |函数|用途|
        |:---:|:---:|
        |htonl|host to network long(32位)|
        |ntohl|network to host long(32位)| 
        |htons|host to network short(16位)|
        |ntohs|network to host short(16位)|

    - **inet_pton()函数**
        
        向套接字fd写入buf缓冲区的n个字节，写入成功返回写入字节数，返回-1表示写入失败，一般不返回0，仅当第三参数为0时什么也不做，返回0
        我们用点分十进制表示IP地址，例如192.168.0.1，简单来说`inet_pton()`是为了将这种字符串格式转化为二进制网络字节序的IP地址
        ```c
        #include <inet.h>
        
        //__af常用AF_INET(IPv4), AF_INET6(IPv6)

        int inet_pton (int __af, const char *__restrict __cp,void *__restrict __buf);
        //成功返回1，不是有效表达式返回0，出错返回-1

        const char *inet_ntop (int __af, const void *__restrict __cp, char *__restrict __buf, socklen_t __len)
        //成功返回该字符串指针，出错或__len不够容纳转换结过，返回NULL
        ```

    - **read(), write()函数**
        
        需包括头文件<unistd.h>

        ```c
        ssize_t read(int __fd, void *__buf, size_t __nbytes);
        ```
        从套接字fd中读取n个字节，存入buf指针指向位置，读取成功返回读取字节数，返回-1时表示读取失败，返回0表示读取结束

        ```c
        ssize_t write (int __fd, const void *__buf, size_t __n);
        ```
        向套接字fd写入buf缓冲区的n个字节，写入成功返回写入字节数，返回-1表示写入失败，一般不返回0，仅当第三参数为0时什么也不做，返回0
    
    
- **简单的时间获取程序(服务端)**

    - **总体流程**
        
        - 使用socket创建套接字
        - 使用bind函数将套接字与
    - **bind()函数**

        ```c
        int bind (int __fd, __CONST_SOCKADDR_ARG __addr, socklen_t __len);
        ```
        将一本地地址与套接字捆绑，无错误发生返回0，否则返回-1

    - **listen()函数**
        
        ```c
        int listen (int __fd, int __n);
        ```
        使套接字fd处于监听状态，n表示请求队列数，队列外的请求将被拒绝

        成功时返回0，否则返回-1

    - **accept()函数**

        ```c
        int accept (int __fd, __SOCKADDR_ARG __addr, socklen_t *__restrict __addr_len);
        ```
        
        在fd的等待连接队列中抽取第一个连接，创建一个与fd同类的新套接字返回

    - **time()函数**
        
        ```c
        time_t time (time_t *__timer);
        ```
        返回当前日历时间，若timer不为NULL，时间也存储在timer中

    - **ctime()函数**
        
        ```c
        char *ctime (const time_t *__timer);
        ```
        返回一个表示当地时间的字符串，当地时间基于timer

    - **snprintf()函数**
        
        ```c
        int snprintf (char *__restrict __s, size_t __maxlen, const char *__restrict __format, ...)
        ```
        将可变个参数按照format格式化为字符串，然后复制到字符串s中

