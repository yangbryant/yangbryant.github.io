---
layout: post
title: 计算机网络基础(四) -- Socket编程实例
date: 2017-01-26 10:58:59 +08:00
tags: 温故而知新
---

***

#### 计算机网络基础:

[计算机网络基础(一) -- HTTP协议][http_basic]  
[计算机网络基础(二) -- TCP协议][tcp_basic]  
[计算机网络基础(三) -- IP协议][ip_basic]  
[计算机网络基础(四) -- Socket编程实例][socket_programming]  

***

### 四. Socket 编程

* Socket是对 **TCP/IP协议簇** 的一种封装,是应用层与TCP/IP协议簇通信的 **中间软件抽象层**.

#### 简易的 WebServer 实例

* 流程如下:
1. 建立连接: 接受一个客户端连接.
2. 接受请求: 从网络中读取一条 HTTP 请求报文.
3. 处理请求: 访问资源.
4. 构建响应: 创建带有 **Header** 的 HTTP 响应报文.
5. 发送响应: 传给客户端.

* 函数逻辑如下:
1. socket(): 创建套接字.
2. bind(): 分配套接字地址.
3. listen(): 等待连接请求.
4. accept(): 允许连接请求.
5. read()/write(): 数据交换.
6. close(): 关闭连接.

```C
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <unistd.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <string>
#include <cstring>
#include <iostream>

using namespace std;

const int port = 9090;
const int buffer_size = 1<<20;
const int method_size = 1<<10;
const int filename_size = 1<<10;
const int common_buffer_size = 1<<10;

void handleError(const string &message);
void requestHandling(int *sock);
void sendError(int *sock);
void sendData(int *sock, char *filename);
void sendHTML(int *sock, char *filename);
void sendJPG(int *sock, char *filename);

int main()
{
    int server_sock;
    int client_sock;

    struct sockaddr_in server_address;
    struct sockaddr_in client_address;

    socklen_t client_address_size;

    server_sock = socket(PF_INET, SOCK_STREAM, 0);

    if (server_sock == -1)
    {
        handleError("socket error");
    }

    memset(&server_address,0,sizeof(server_address));
    server_address.sin_family = AF_INET;
    server_address.sin_addr.s_addr = htonl(INADDR_ANY);
    server_address.sin_port = htons(port);

    if(bind(server_sock,(struct sockaddr*)&server_address, sizeof(server_address)) == -1){
        handleError("bind error");
    }

    if(listen(server_sock, 5) == -1) {
        handleError("listen error");
    }

    while(true) {
        client_address_size = sizeof(client_address);
        client_sock = accept(server_sock, (struct sockaddr*) &client_address, &client_address_size);

        if (client_sock == -1) {
            handleError("accept error");
        }
        requestHandling(&client_sock);
    }

    //system("open http://127.0.0.1:9090/index.html");
    close(server_sock);

    return 0;
}

void requestHandling(int *sock){
    int client_sock = *sock;
    char buffer[buffer_size];
    char method[method_size];
    char filename[filename_size];

    read(client_sock, buffer, sizeof(buffer)-1);

    if(!strstr(buffer, "HTTP/")) {
        sendError(sock);
        close(client_sock);
        return;
    }

    strcpy(method, strtok(buffer," /"));
    strcpy(filename, strtok(NULL, " /"));

    if(0 != strcmp(method, "GET")) {
        sendError(sock);
        close(client_sock);
        return;
    }

    sendData(sock, filename);
}

void sendData(int *sock, char *filename) {
    int client_sock = *sock;
    char buffer[common_buffer_size];
    char type[common_buffer_size];

    strcpy(buffer, filename);

    strtok(buffer, ".");
    strcpy(type, strtok(NULL, "."));

    if(0 == strcmp(type, "html")){
        sendHTML(sock, filename);
    }else if(0 == strcmp(type, "jpg")){
        sendJPG(sock, filename);
    }else{
        sendError(sock);
        close(client_sock);
        return ;
    }
}

void sendHTML(int *sock, char *filename) {
    int client_sock = *sock;
    char buffer[buffer_size];
    FILE *fp;

    char status[] = "HTTP/1.0 200 OK\r\n";
    char header[] = "Server: A Simple Web Server\r\nContent-Type: text/html\r\n\r\n";

    write(client_sock, status, strlen(status));
    write(client_sock, header, strlen(header));

    fp = fopen(filename, "r");
    if(!fp){
        sendError(sock);
        close(client_sock);
        handleError("failed to open file");
        return ;
    }

    fgets(buffer,sizeof(buffer), fp);
    while(!feof(fp)) {
        write(client_sock, buffer, strlen(buffer));
        fgets(buffer, sizeof(buffer), fp);
    }

    fclose(fp);
    close(client_sock);
}

void sendJPG(int *sock, char *filename) {
    int client_sock = *sock;
    char buffer[buffer_size];
    FILE *fp;
    FILE *fw;

    char status[] = "HTTP/1.0 200 OK\r\n";
    char header[] = "Server: A Simple Web Server\r\nContent-Type: image/jpeg\r\n\r\n";

    write(client_sock, status, strlen(status));
    write(client_sock, header, strlen(header));

    fp = fopen(filename, "rb");
    if(NULL == fp){
        sendError(sock);
        close(client_sock);
        handleError("failed to open file");
        return ;
    }

    fw = fdopen(client_sock, "w");
    fread(buffer, 1, sizeof(buffer), fp);
    while (!feof(fp)){
        fwrite(buffer, 1, sizeof(buffer), fw);
        fread(buffer, 1, sizeof(buffer), fp);
    }

    fclose(fw);
    fclose(fp);
    close(client_sock);
}

void handleError(const string &message) {
    cout<<message;
    exit(1);
}

void sendError(int *sock){
    int client_sock = *sock;

    char status[] = "HTTP/1.0 400 Bad Request\r\n";
    char header[] = "Server: A Simple Web Server\r\nContent-Type: text/html\r\n\r\n";
    char body[] = "<html><head><title>Bad Request</title></head><body><p>400 Bad Request</p></body></html>";

    write(client_sock, status, sizeof(status));
    write(client_sock, header, sizeof(header));
    write(client_sock, body, sizeof(body));
}
```

***

此文参考于 [hit-alibaba.github.io][hit-alibaba.github.io],十分感谢.  
所有引用内容版权归原作者所有.  
使用 [知识共享“署名-非商业性使用-相同方式共享 3.0 中国大陆”许可协议][Lisence] 授权.

[hit-alibaba.github.io]: https://hit-alibaba.github.io/interview/
[Lisence]: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/

[http_basic]: /2017/01/basics-about-network-1-http/
[tcp_basic]: /2017/01/basics-about-network-2-tcp/
[ip_basic]: /2017/01/basics-about-network-3-ip/
[socket_programming]: /2017/01/basics-about-network-4-socket-programming/