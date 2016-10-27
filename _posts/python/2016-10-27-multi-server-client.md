---
layout: post
title: "thread를 이용한 다중 채팅 구현"
date: 2016-10-27
comments: true
categories: python
---


하나의 클라이언트만 연결하던 코드와 큰 차이는 없다. 
아래 코드를 보면 연결된 클라이언트 소켓들을 리스트에 넣고 스레드를 생성하여 멀티 스레드를 동작시키면서 메시지를 뿌려주는 내용이다.  


```{.python}
# multiServer.py


from socket import socket, AF_INET, SOCK_STREAM
import threading
import sys

connection_list = []    # connected client list
thread_list = []        # started thread list


def conn_client(sock, address):
    global connection_list

    for cs in connection_list:
        cs.sendall(('유저({}:{})가 접속했습니다.'.format(address[0], address[1])).encode())     # string은 인코딩해서 보내야 한다

    print('유저({}:{})가 접속했습니다.'.format(address[0], address[1]))

    while True:
        msg = sock.recv(1024)
        if not msg:
            break
        print(msg.decode())
        
        # 연결된 모든 클라이언트에게 메시지 전송
        for cs in connection_list:
            cs.sendall(msg)

    print('Disconnected...')
    connection_list.remove(sock)
    sock.close()

if __name__ == '__main__':
    HOST = ''
    PORT = 50005
    ADDR = (HOST, PORT)

    serverSock = socket(AF_INET, SOCK_STREAM)
    serverSock.bind(ADDR)
    serverSock.listen(3)

    try:
        while True:
            clientSock, address = serverSock.accept()
            connection_list.append(clientSock)
            t = threading.Thread(target=conn_client, args=(clientSock, address))
            thread_list.append(t)
            t.start()
    except KeyboardInterrupt:
        serverSock.close()
    finally:
        for t in thread_list:
            t.join()
        sys.exit()


```

```{.python}
# multiClient.py

from socket import socket, AF_INET, SOCK_STREAM
import threading


def send_handler(sock):
    while True:
        data = input('>>')
        if not data:
            continue
        sock.sendall(data.encode())
    sock.close()


def recv_handler(sock):
    while True:
        data = sock.recv(1024)
        if not data:
            continue
        print(data.decode())


if __name__ == '__main__':
    HOST = ''
    PORT = 50005
    ADDR = (HOST, PORT)
    sock = socket(AF_INET, SOCK_STREAM)
    sock.connect(ADDR)

    t_send = threading.Thread(target=send_handler, args=(sock,))
    t_recv = threading.Thread(target=recv_handler, args=(sock,))

    t_send.start()
    t_recv.start()
```

많은 연결이 이뤄지면 많은 스레드의 생성 및 제거와 같은 작업이 이뤄지는데, 성능 저하 및 자원 소모가 많아질 것이라고 생각했다.  
이를 위해 스레드 갯수를 제한하는 세마포어(semaphore)나 스레드풀(threadpool)을 사용해 좀 더 괜찮은 코드를 만들어 봐야 겠다.
