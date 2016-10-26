---
layout: post
title: "python socket and thread"
date: 2016-10-26
comments: true
categories: python
---

### socket을 이용한 단방향 통신

socket 라이브러리를 사용해서 message를 전달하는 tcp server/client를
간단히 구현해 봤다.    
클라이언트에서 메시지를 보내면 서버에서는 메시지를 받아서 출력만 하도록 구현했다.

```{.python}
# tcpServer.py

from socket import socket, AF_INET, SOCK_STREAM


def chat_handler(client_sock, client_addr):
    print('Connected from', client_addr)
    while True:
        msg = client_sock.recv(1024)
        if not msg:
            break
        print(msg)
        client_sock.sendall(msg)
    client_sock.close()


def chat_server(addr, backlog=5):
    sock = socket(AF_INET, SOCK_STREAM)	# socket 객체 생성
    sock.bind(addr)		# local address에 socket 연결
    sock.listen(backlog)
    while True:
        client_sock, client_addr = sock.accept()
        chat_handler(client_sock, client_addr)


if __name__ == '__main__':
    HOST = ''
    PORT = 5000
    ADDR = (HOST, PORT)
    chat_server(ADDR, 3)
```
위 코드에서 `backlog`가 뭘 의미하는지 몰랐는데 간단하게 말하면 개설되지 않고
보류중인 연결을 넣어두는 큐의 최대 크기를 의미한다.  
여러 클라이언트가 서버에 연결하고 싶어 요청을 보내면, 서버는 그 요청을 큐에 넣고 그 요청들을 순차적으로 처리한다.   
즉, 연결된 요청이 해제되야 큐에서 대기중인 요청이 연결된다.



```{.python}
# tcpClient.py


from socket import socket, AF_INET, SOCK_STREAM
import sys
import signal


def chat_handler(sock):
    name = input('이름을 입력해 주세요: ')
    while True:
        data = name+': '+input(name+': ')
        sock.send(data.encode())
        re_data = sock.recv(1024)
        # print('Received', repr(re_data))
	sock.close()


def chat_client(addr):
    sock = socket(AF_INET, SOCK_STREAM)

    # ctrl+c 입력시 종료 (SIGINT: ctrl+c)
    signal.signal(signal.SIGINT, lambda: sys.exit())

    try:
        sock.connect(addr)
    except Exception as e:
        print('서버({0}:{1})에 연결을 할 수 없습니다.'.format(HOST, PORT))
        sys.exit()
    print('서버({0}:{1})에 연결되었습니다.'.format(HOST, PORT))
    chat_handler(sock)


if __name__ == '__main__':
    HOST = ''
    PORT = 5000
    ADDR = (HOST, PORT)
    chat_client(ADDR)

```

서버와 클라이언트의 코드 중 다른 부분이 있는데, socket 연결시 사용된 `bind(), connect()` 메소드이다.  
서버에서 사용된 `bind()`는 local 주소를 소켓에 binding하여 소켓에 접근하는 클라이언트와 연결되도록 한다.  
클라이언트에서 사용된 `connect()`는 말그대로 서버의 주소로 요청을 보내 연결하는 기능이다.
아래 그림을 보면 이해하기 좀 수월할 것이다.

-----

<p><img src="https://upload.wikimedia.org/wikipedia/commons/a/a1/InternetSocketBasicDiagram_zhtw.png" width="400"></p>
        
[출처: 위키피디아](https://en.wikipedia.org/wiki/Berkeley_sockets)


-----

### socket과 thread를 이용한 양방향 통신

위와 다르게 thread를 이용하여 서버와 클라이언트에서 모두 메시지를 주고 받을 수 있도록 구현했다.   
기본적으로 recv()메소드가 호출되면 request가 오기 전까지 lock이 걸려 다른 작업은 할 수가 없다. 때문에 요청을 보내고 받는 함수를 따로 구현하여 thread에 넣음으로써 병렬로 작동하게 만들었다.  

```{.python}
# tcpServer.py

from socket import socket, AF_INET, SOCK_STREAM
import threading
import sys
import signal


def send_msg(sock):
    while True:
        msg = input('>>')
        sock.sendall(msg.encode())
    sock.close()


def recv_msg(sock):
    while True:
        msg = sock.recv(1024)
        if not msg:
            break
        print(msg)
    print('Disconnected...')
    sock.close()
    sys.exit()


if __name__ == '__main__':
    HOST = ''
    PORT = 5000
    ADDR = (HOST, PORT)
    
    sock = socket(AF_INET, SOCK_STREAM)
    sock.bind(ADDR)
    sock.listen(5)
    
    client_sock, client_addr = sock.accept()
    signal.signal(signal.SIGINT, lambda: sock.close())

    t_send = threading.Thread(target=send_msg, args=(client_sock,))
    t_recv = threading.Thread(target=recv_msg, args=(client_sock,))

    t_send.start()
    t_recv.start()
    
    t_send.join()			# 스레드가 종료할 때까지 대기
    print('Finish send_thread!')
    t_recv.join()			# 스레드가 종료할 때까지 대기
    print('Finish receive_thread!')

```

위 코드에 `join()` 메소드를 사용했는데, 이것은 스레드가 종료할 때까지 대기하도록 요청하는 기능이다.  
스레드가 실행중인지 여부를 확인할 때 사용할 수 있다. 


```{.python}
# tcpClient.py

from socket import socket, AF_INET, SOCK_STREAM
import sys
import signal
import threading


def send_msg(sock):
    while True:
        msg = input('>>')
        sock.sendall(msg.encode())
    sock.close()


def recv_msg(sock):
    while True:
        msg = sock.recv(1024)
        if not msg:
            break
        print(msg)
    print('Disconnected...')
    sock.close()
    

if __name__ == '__main__':
    HOST = ''
    PORT = 5000
    ADDR = (HOST, PORT)
    sock = socket(AF_INET, SOCK_STREAM)
    
    try:
        sock.connect(ADDR)
    except Exception as e:
        print('서버({0}:{1})에 연결을 할 수 없습니다.'.format(HOST, PORT))
        sys.exit()

    t_send = threading.Thread(target=send_msg, args=(sock,))
    t_recv = threading.Thread(target=recv_msg, args=(sock,))

    t_send.start()
    t_recv.start()
```
