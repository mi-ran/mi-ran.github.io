---
layout: archive
title: "File Descriptor"
modified:
categories: etc
tags: [etc]
---

### File Descriptor
 리눅스, 유닉스 시스템에서 모든 것은 파일로 지칭하고 다룬다. 일반적인 File, Directory(vim으로 디렉토리를 열 수 있는 이유가 아닐까?), Socket, PIPE, 블록 디바이스, 캐릭터 디바이스 등 모든 것을 파일로 관리한다.

 파일 디스크립터는 0이 아닌 정수 값을 가진다. 프로세스 실행 중 파일을 열게 되면 커널은 해당 프로세스의 파일 디스크립터 숫자 중에 사용하지 않는 가장 작은 값을 할당해준다. 그 다음에는 프로세스가 열려있는 파일에 시스템 콜을 이용해서 접근할때, 파일 디스크립터 값을 이용해서 해당 파일을 이용할 수 있다.

 프로그램이 프로세스로 메모리에서 실행될 때 기본적으로 할당되는 파일 디스크립터들이 있는데, 표준 입력, 표준 출력, 표준 에러 값이다. 각각 0, 1, 2라는 정수가 할당된다. (프로세스 하나 당 파일 디스크립터 테이블이 하나씩 있는 듯하다.

 윈도우즈 운영체제에서는 파일 디스크립터 값으로 랜덤한 값이 지정된다. 그리고 file과 socket은 서로 다른 개념으로 관리 된다고 한다.

### select 함수
select함수는 socket/pipe 등에서 동시에 여러개의 I/O를 대기할 경우에 특정한 파일 디스크립터에 blocking 되지 않고I/O를 할 수 있는 상태인 지 모니터링 하여 I/O 가능한 파일 디스크립터와 I/O 할 수 있도록 검사하는 함수이다.

보통 통신에서 read/recv하면, 통신의 대상이 데이터를 전송하지 않으면 전송할 때 까지 해당 프로세스는 멈춰있게 된다. 마피아 게임 프로그램에 들어갈 소캣 통신은 다중 채팅을 지원해야하기 때문에 그러면 안됨. select 함수는 여러 개의 파일 디스크립터를 동시에 모니터링 하다가 하나라도 메시지를 보낸 상태가 되면 blocking을 해제하여 다중채팅이 가능하게 한다. 현재는 사용상의 불편한 점(아직 안써봐서 모름)으로 인해 poll등의 함수로 대체되고 있음

* 파라미터
    * nfds : FD_SET()함수로 설정한 파일 디스크립터 값들 중에 가장 큰 값 +1을 설정해야함
    * readfds
        : FD_SET(fd, readfds)로 설정한 파일 디스크립터 중 읽을 수 있는 상태가 될 때까지 대기
        : select 함수가 return된 후에는 readfds에 읽을 수 있는 상태에 있는 fd를 제외하고 모두 clear된다.
    * writefds
        : FD_SET(fd, writefds)로 설정한 파일 디스크립터들이 데이터를 쓸 수 있는 상태가 될 때까지 대기
        : select 함수가 return된 후에는 writefds에 쓸 수 있는 상태에 있는 파일 디스크립터를 제외하고 모두 clear된다.
    * exceptfds
        : 위와 마찬가지지만 오류 상태에 대해서 작동
    * timeout
        : 위의 세개의 dfs들이 timeout 시간동안 상태 변화가 없으면 함수가 종료된다.
* RETURN
    * 0보다 큰 값 : readfds, writefds, exceptfds에 대해 처리 가능한 상태인 파일 디스크립터의 건수를 return함
    * 0 : timeout이 발생하였으며, 처리 가능 상태인 파일 디스크립터가 하나도 없음
    * -1 : 오류가 발생하였다.

### FD_ZERO
fd_set은 여러 개의 파일 디스크립터를 관리하고 event(read, write, except)가 발생하였는지 여부를 기록하는 구조체이다.
fd_set 구조체를 초기화함.
`void FD_ZERO(fd_set* pSet);`

### FD_SET
 fd_set에 fd를 추가함.
`void FD_SET(int fd, fd_set* pSet);`
read/write/except용에 대한 set이 있으며, 각각의 set에 fd를 추가할 수 있다.

### FD_CLEAR
 set에 포함된 fd를 제거한다. but 잘 사용되진 않음
select함수를 실행될 때마다 처리가능한 fd를 제외한 fd들이 다 클리어 되기 때문에 FD_ZERO, FD_SET을 해야하기 때문이다.

### FD_ISSET
 set에 fd가 포함되어 있는지 확인하는 함수
select 함수가 호출된 수에는 처리 가능한 fd만 남게 되므로 읽기 쓰기 준비가 되어있는지 여부를 알 수 있다.
`void FD_ISSET(int fd, fd_set* pSet);`