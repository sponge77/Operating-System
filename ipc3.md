Sleep and Wakeup

바쁜 대기의 문제점 : 시간을 남비한다, priority inverson problem

​

L은 CPU를 뺏김

H는 CPU를 독차지

L은 leave region call을 해야 하는데 문제 발생

H는 critical region에서 빠져올 기회가 없음

​

해결법 : sleep and wakeup

sleep : 호출자를 블록 상태로 만드는 시스템 호출

wakeup : 깨울 프로세스를 가리키는 인자 하나를 가지고 있음

다른 프로세스가 호출자를 깨울 때까지 블록 상태에 머무름

​

producer - consumer 문제(생산자 - 소비자 문제)

bounded - buffer 문제

information = item 

두 개의 프로세스가 고정된 크기의 버퍼를 공유함

​

생산자 ​: 새로운 아이템을 버퍼에 넣음

버퍼가 가득 차 있으면 생산자가 잠들고 소비자가 아이템을 하나 제거할 때 깨워줌

​

소비자 : 아이템을 버퍼에서 가져옴

버퍼가 비어 있으면 소비자는 잠들고 생산자가 버퍼에 아이템을 넣을 때 깨워줌

​
```
#define N 100
int count=0;
void producer(void){
int item;
while(TRUE){
item = produce_item();
if(count==N) sleep();
insert_item(item);
count = count+1;
if(count == 1) wakeup(consumer);
}
}
void consumer(void){
int item;
while(TRUE){
if(count==0) sleep();
item = remove_item();
count = count-1;
if(count==N-1) wakeup(producer);
consumer_item(item);
}
}
```
race condtion

scheduling이 일어났다고 가정

consumer는 아직 sleep하지 않음, wakeup이 작동하지 않음

producer 계속 채움 → sleep()

둘 다 sleep함, 서로를 깨우지 못함 → 해결책 : wakeup waiting 비트를 추가(근본적인 해결책은 아님)

process마다 wakeup bit의 개수를 늘림

​

<Semaphore>

down(P), critical region, up(V)

1. down(P) operation

값이 0보다 크면 값을 감소시키고 수행을 계속함

값이 0이면 값을 감소하지 않고 바로 잠듦(=blocked 상태로 보내짐)

2. up(V) operation

세마포어의 값을 증가

하나 또는 그 이상의 프로세스가 잠들어 있으면 한 프로세스가 깨워지고 down 수행을 완료 가능

​

​

​

세마포어에 대해 아무도 잠자고 있지 않으면 값을 증가시킨다.

atomic operation(쪼갤 수 없음) = OS Scheduling이 일어날 수 없다.

세마포어는 lost - wakeup문제를 해결한다.

sleep and wakeup problem → wakeup bit를 사용(but 근본적인 해결은 아님!!)

​

consumer 아직 sleep 하지 않았는데 producer는 wakeup을 한다.

wakeup은 의미가 없다. 

특정 시간에는 한 프로세스만이 공유버퍼에 쓰거나 읽을 수 있다.

☆☆☆synchronization이 제공된다.

프로세스간의 순서를 조율한다.

mutex : 공유 버퍼를 동시에 access하지 못함

​

코드 분석

consumer : down(&full)에서 sleep을 함

producer : up(&full) 덕분에 consumer process는 sleep에서 풀린다.

​

<Mutex>

Mutex(세마포어와는 별개) : thread에서 보통 사용, 단순히 lock variable임

Unlocked : 0, locked : 1

mutex_lock, critical_region, mutex_unlock

TSL instruction과 비슷하다 but 결정적 차이점 : 

TSL은 Lock이 걸려 있는 경우 busy waiting을 함

CPU를 낭비하면서 loop를 돔

but, MUTEX → CPU를 잡고 잇지 않고 양보한다, 바쁜 대기가 X

처음에 unlocked 상태이면 → 바로 critical_region에 들어간다.
```
mutex_lock:
    TSL REGISTER, MUTEX
    CMP REGISTER, #0
    JZE ok
    call thread_yield
    JMP mutex_lock
    ok : RET | return to caller; //critical region entered
mutex_unlock:
    MOVE MUTEX, #0
    RET | return to caller
  ```
세마포어 : data 영역(kernel이 있는)에 세마포어를 저장

process 간에 공유?

데이터는 kernel에 의해서 관리된다.

up, down system call

turn variable 공유

자신의 address space의 일부를 share할 수 있는 매커니즘

메모리 공유 X, 파일을 공유 O(shared 파일)

​

핵심 : 커널에 공유 자료 구조를 저장하고 시스템 호출을 통해서만 접근함

프로세스가 자신의 주소 공간 일부분을 다른 프로세스와 공유한다.
