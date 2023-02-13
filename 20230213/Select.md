# Select(I/O 완료 대기)

## **1. Select**
    소켓 프로그래밍에서 I/O 멀티플렉싱을 가능하게 하는 모듈!

## **2. I/O 멀티플렉싱**
    한개의 프로세스(전송로)로 두개 이상의 클라이언트 요청을 처리하는 것!

## **3. 숫자 맞추기 게임**
 ```Python
import socket
import random

HOST = ''
PORT = 50007

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    print('서버가 시작되었습니다.')
    conn, addr = s.accept()
    with conn:
        answer = random.randint(1, 9)
        print(f'클라이언트가 접속했습니다:{addr}, 정답은 {answer} 입니다.')
        while True:
            data = conn.recv(1024).decode('utf-8')
            print(f'데이터:{data}')

            try:
                n = int(data)
            except ValueError:
                conn.sendall(f'입력값이 올바르지 않습니다:{data}'.encode('utf-8'))
                continue

            if n == 0:
                conn.sendall(f"종료".encode('utf-8'))
                break
            if n > answer:
                conn.sendall("너무 높아요".encode('utf-8'))
            elif n < answer:
                conn.sendall("너무 낮아요".encode('utf-8'))
            else:
                conn.sendall("정답".encode('utf-8'))
                break
```

## **4. Select 모듈의 활용**
    socket 모듈을 활용하여 '숫자 맞추기 게임'을 만들었다.
    이때 발생하는 두가지 문제점은 아래와 같다.

    1. 클라이언트가 소켓 서버에 접속하여 게임을 진행한 후 접속을 종료하면
       소켓 서버 역시 종료되어 더는 다른 클라이언트가 연결할 수 없다.
    2. 소켓 서버가 여러 클라이언트와 동시에 게임을 진행할 수 없다.

    위의 문제점들은 해결하기 위하여 select 모듈을 사용,
    클라이언트 요청을 동시에 처리하여 한꺼번에 여러 명이 동시에 플레이할 수 있도록
    기존 소켓 서버 프로그램을 수정해준다

* 스레드를 사용하여 해결할 수도 있지만, 스레드 수가 늘어날 수록 시스템에 부담이므로 효율성이 낮다. 
* Select 방식은 특정 이벤트 발생시에만 작동하므로 스레드 보다 효율적이다.

```Python
import socket
import select
import random

HOST = ''
PORT = 50007

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    print('서버가 시작되었습니다.')

    readsocks = [s]
    answers = {}

    while True:
        readables, writeables, excpetions = select.select(readsocks, [], [])
        for sock in readables:
            if sock == s:  # 신규 클라이언트 접속
                newsock, addr = s.accept()
                answer = random.randint(1, 9)
                print(f'클라이언트가 접속했습니다:{addr}, 정답은 {answer} 입니다.')
                readsocks.append(newsock)
                answers[newsock] = answer  # 클라이언트 별 정답 생성
            else:  # 이미 접속한 클라이언트의 요청 (게임진행을 위한 요청)
                conn = sock
                data = conn.recv(1024).decode('utf-8')
                print(f'데이터:{data}')

                try:
                    n = int(data)
                except ValueError:
                    conn.sendall(f'입력값이 올바르지 않습니다:{data}'.encode('utf-8'))
                    continue

                answer = answers.get(conn)
                if n == 0:
                    conn.sendall(f"종료".encode('utf-8'))
                    conn.close()
                    readsocks.remove(conn)  # 클라이언트 접속 해제시 readsocks에서 제거
                if n > answer:
                    conn.sendall("너무 높아요".encode('utf-8'))
                elif n < answer:
                    conn.sendall("너무 낮아요".encode('utf-8'))
                else:
                    conn.sendall("정답".encode('utf-8'))
                    conn.close()
                    readsocks.remove(conn)  # 클라이언트 접속 해제시 readsocks에서 제거
```
*  select.select(readsocks, [], []): readsocks에 포함된 소켓에서 이벤트가 발생하는 지 감시, 발생시 문장 실행
*  readables: 수신한 데이터를 가진 소켓
*  writeables: 블로킹되지 않고 데이터를 전송할 수 있는 소켓
*  exceptions: 예외 상황이 발생한 소켓
