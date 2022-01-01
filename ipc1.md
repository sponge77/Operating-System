경쟁 조건(Race Condition)

둘 또는 그 이상의 프로세스가 공유 데이터를 읽거나 기록하는데 최종 결과는 누가 언제 수행되는가에 따라 달라지는 상황을 의미한다.

ex) Spooler 디렉토리(printer daemon 출력 process)

<수행되는 코드>

next = in;

next++;

in = next;

​

Mutual Exclusion(상호 배제)

한 프로세스가 공유 변수나 파일 사용 중이면 다른 프로세스들은 똑같은 일을 수행하지 못함

​

Critical Region(임계 영역)


공유 메모리를 접근하는 프로그램 부분

​

​
