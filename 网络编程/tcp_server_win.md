编译时注意添加  -lwsock32  监听地址为127.0.0.1

```
#include <stdio.h>
#include <stdlib.h>
#include <winsock2.h>
#include <inaddr.h>

void error_handling(char *message) ;

int main(int argc, char* argv[])
{
    WSADATA wsaData;
    SOCKET hServSock, hClntSock;
    SOCKADDR_IN servAddr, clntAddr;

    int szClntAddr;
    char message[] = "Hello world";
    if (argc != 2)
    {
        printf("Usage: %s <post>\n", argv[1]);
        exit(1);
    }

    if (WSAStartup(MAKEWORD(2,2), &wsaData) !=0)
        error_handling("WSAStartup() error");

    hServSock = socket(PF_INET, SOCK_STREAM, 0);
    if(hServSock == INVALID_SOCKET)
        error_handling("socket() error");

    memset(&servAddr, 0, sizeof(servAddr));
    servAddr.sin_family = AF_INET;
    servAddr.sin_addr.S_un.S_addr = htonl(INADDR_ANY);
    servAddr.sin_port = htons(atoi(argv[1]));

    if (bind(hServSock, (SOCKADDR*)&servAddr, sizeof(servAddr)) == SOCKET_ERROR)
        error_handling("bind error");

    if (listen(hServSock,5) == SOCKET_ERROR)
        error_handling("listen error");

    szClntAddr= sizeof(clntAddr);
    hClntSock =accept(hServSock, (SOCKADDR*)&clntAddr, &szClntAddr);
    if (hClntSock == INVALID_SOCKET)
        error_handling("accept error");

    send(hClntSock, message, sizeof(message), 0);
    closesocket(hClntSock);
    closesocket(hServSock);
    WSACleanup();
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```