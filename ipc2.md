Mutual Exclision with Busy Waiting

1. 인터럽트 끄기(하드웨어적 기능 사용, busy waiting 방법이 아님)

OS scheduling이 일어나지 않도록 한다.

CPU가 clock에서 들어오는 interrupt signal에 반응하지 않는다.

OS scheduler → clock interrupt를 이용

​

주기적으로 CPU에게 signal 을 보낸다.

interrupt handler routine이 수행

ready → running으로

disable interrupt  → 자신의 interrupt pin으로 들어오는 signal에 반응하지 않는다.

​

2. 락 변수

두 개의 프로세스가 동시에 임계구역에 들어감

소프트웨어 솔루션임, 공유 변수를 사용

lock == 1인 경우 : 프로세스는 이 변수가 0이 될때까지 기다림

lock == 0 인 경우 : 프로세스는 이를 1로 설정하고 임계구역에 진입한다.

​

3. Strict Alternation : 엄격한 교대 

mutual exclusion이 제공된다.

Busy Waiting : 변수가 특정 값이 될 때까지 계속해서 검사하는 것

바쁜 대기 → 좋은 솔루션이 아님

spin lock : 바쁜 대기를 사용하는 lock

한 프로세스가 다른 프로세스보다 상당히 느린 경우 교대로 일하는 것은 좋지 않음

​

spool

p0은 임계구역에 존재하지 않는 p1에 의해 블록된다.

또는 p0은 critical region 밖에 있으면서 p1을 막고 있음

​
```
//p0
while(TRUE){
    while(turn!=0)
    critical_region();
    turn = 1;
    noncritical_region();
}

//p1
while(TRUE){
    while(turn!=1)
    critical_region();
    turn=0;
    noncritical_region();
}
```
4. Peterson's Solution

두 프로세스가 거의 동시에 enter_region을 호출

turn, interested

먼저 온 p0이 먼저 critical region에 들어간다. 프로세스 0이 임계구역을 나올 때까지 프로세스1은 루프를 돌면서 임계구역에 진입하지 못한다.
```
#define FALSE 0
#define TRUE 1
#define N 2
int turn;
int interested[N];
void enter_region(int process)
{
int other;
other = 1 - process;
interested[process] = TRUE;
turn = process;
while(turn == process && interested[other] == TRUE) /* */
}
void leave_region(int process){
interested[process] = FALSE;
}
//p0
e_r(0);
c_r;
l_r(0);
//p1
e_r(1);
c_r;
l_r(1);
```
//공유 변수 : turn, interested[2]
※참고  : strict alternation, peterson의 방법 :  순수하게 소프트웨어적인 방법이다.

​

5. TSL instruction

TSL RX, LOCK(Test and SET LOCK)

메모리 워드 LOCK의 값을 읽어 레지스터 RX에 저장하고 메모리 주소 LOCK에 0이 아닌 값을 기록함

indivisible(쪼갤 수 없다) = 사이에 process scheduling이 일어날 수 X

﻿enter_region:
    TSL REGISTER, LOCK
    CMP REGISTER , #0
    JNE enter_region
    RET

leave_region:
    MOVE LOCK, #0
    RET
2 개의 operation을 순차적으로 수행한다.

individual 하므로 사이에 scheduling이 개입될 수 없다.
