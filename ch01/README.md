# 简介

- **一个简单的时间获取客户程序**

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

    - **







