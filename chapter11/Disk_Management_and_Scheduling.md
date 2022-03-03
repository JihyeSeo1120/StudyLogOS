# Disk Management and Scheduling

## Disk Structure

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FOM1kC%2FbtqCM9tcXKA%2F9yhszhW2beZwff9ACeMX7k%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FOM1kC%2FbtqCM9tcXKA%2F9yhszhW2beZwff9ACeMX7k%2Fimg.png)

**logical block**: 디스크 외부에서는 logical block단위로 바라본다

**sector**: 디스크를 관리하는 최소 단위로써  logical block이 물리적인 디스크 sector에 매핑되어있다. 

## **Disk Management**

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdBA7WY%2FbtqCNGYAWtN%2FMn8UNJgDUE5xq8p1KRKhxK%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdBA7WY%2FbtqCNGYAWtN%2FMn8UNJgDUE5xq8p1KRKhxK%2Fimg.png)

### **physical formatting(Low-level formatting)**

- 공장에서 디스크를 만들 때 디스크를 sector들로 나누는 과정
- 각 섹터 = header + 실제 데이터(보통 512 bytes) + trailer
- header와 trailer는 sector number, ECC(Error-Correcting-Code) 등의 정보가 저장 됨. (Controller가 접근 및 운영)

### **Partitioning**

- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- ex) C드라이브, D드라이브
- OS는 이것을 **독립적 disk(logical disk)**로 취급

### **Logical formatting**

- 해당 파티션에 파일 시스템을 설치하는 경우
- **FAT, inode,** free space등의 구조 포함

### **Booting**

- ROM(Memory중 소량의 비휘발성 메모리)에 있는 **"small bootstrap loader"**의 실행
- sector 0 (boot block)을 load하여 실행
- sector 0 은 **"full bootstrap loader program"**
- OS를 디스크에서 load하여 실행
- 참고 ([https://movahws.tistory.com/158](https://movahws.tistory.com/158))

CPU는 오직 메모리 접근만 가능하고, 하드디스크 접근은 사실상 불가능.

메모리 영역 중에는 전원이 나가도 기록되는 소량의 메모리가 존재하는데 이게 바로 ROM. ROM에 부팅을 위한 간단한 loader가 저장되어 있음. 컴퓨터 전원을 켜면 CPU 제어권이 ROM의 주소 가리키고, "small bootstrap load"가 실행. 이 loader가 하드 디스크에서 0번 섹터에 있는 내용을 메모리에 올리고 그걸 실행하라는 지시. sector 0번은 무조건 부트 블록이 위치. 부트 블록을 메모리에 올리고, 그 메모리 영역을 실행하면 부트 블록은 파일 시스템에서 운영체제 커널의 위치를 찾아서 이걸 메모리에 올려서 실행하라고 명령. 이렇게 되면 운영체제가 메모리에 올라가고 운영체제 실행.

---

## **Disk Scheduling**

### **Access time 구성**

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdvraov%2FbtqCMnrIcZp%2FLzBouTnmHRKogpd8QbppD0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdvraov%2FbtqCMnrIcZp%2FLzBouTnmHRKogpd8QbppD0%2Fimg.png)

- **Seek time**: 헤드를 해당 **실린더**로 움직이는데 걸리는 시간**(가장 큰 시간요소)** 이게 디스크 접근 시간에서 가장 큰 비중을 차지함. 일종의 기계 장치가 이동하기 때문에 시간이 오래 걸림
- **Rotational latency:** 헤드가 원하는 **섹터**에 도달하기까지 걸리는 회전지연시간. 즉, 디스크가 회전을 해서 디스크 헤드까지 오는 시간
- **Transfer time:** 실제 데이터 **전송**시간. 실제로 데이터를 읽고/쓰는 시간. 굉장히 적은 시간을 차지.

### **Disk bandwith**

- 단위 시간당 전송된 바이트의 수. 디스크의 성능 척도. seek time을 줄이는 것이 bandwidth를 높이는 것의 주 과제이기 때문에 disk scheduling이 필요함.

→ **Disk scheduling**은 **seek time을 최소화**하는 것이 목표

---

## **Disk Scheduling Algorithm**

실린더 번호를 기준으로 알고리즘을 설명함

### **FCFS**

- 요청이 들어온 순서대로 처리. 안쪽과 바깥쪽 트랙이 연달아 오면 헤드의 이동 거리가 길어짐. 대단히 비효율적일 수 있음

![출처: 강의](https://blog.kakaocdn.net/dn/buxZxB/btqOxs1j5Nx/21NqMlCpaWg4unYdtyB9e1/img.png)


### **SSTF(Shortest Seek Time First)**

- 현재 헤드에서 제일 가까운 위치에 있는 요청을 처리
- starvation문제가 생김

![https://blog.kakaocdn.net/dn/kLDK0/btqOtguxrPo/LOAPclcbFIPEVz4JnzKRj0/img.png](https://blog.kakaocdn.net/dn/kLDK0/btqOtguxrPo/LOAPclcbFIPEVz4JnzKRj0/img.png)


### **SCAN**

- 엘리베이터 스케줄링과 유사
- 큐에 어떤 위치에 요청이 들어왔든지 디스크 헤드는 항상 가장 안쪽부터 바깥쪽까지 오가면서 가는 경로에 있는 요청을 처리
- 헤드가 떠나면 다시 돌아올 때까지 기다려야하는 문제점

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fp49u5%2FbtqCNGEkJI9%2FwRD6kGraBLiCCPcTVGZrNk%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fp49u5%2FbtqCNGEkJI9%2FwRD6kGraBLiCCPcTVGZrNk%2Fimg.png)

### **C-SCAN | Circular Scan**

- 돌아오는 요청은 처리하지 않고 한쪽 방향으로만 처리하는 방식
- SCAN보다 균일한 대기 시간을 제공

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcJqTsw%2FbtqCLLTQCXi%2F646athNU2CPFMtP2NZSVwK%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcJqTsw%2FbtqCLLTQCXi%2F646athNU2CPFMtP2NZSVwK%2Fimg.png)

### ETC

**N-SCAN** - 한 방향으로 움직일 때 들어오는 job들을 되돌아 올 때 처리

**Look and C-Look** - SCAN기법들의 단점을 극복, 만약에 끝까지 요청이 없으면 끝까지 가지 않고 바로 방향을 틈

![https://blog.kakaocdn.net/dn/PQvvK/btqOtFgwXXV/T1Sqq70jcG2qovHkdvLoqK/img.png](https://blog.kakaocdn.net/dn/PQvvK/btqOtFgwXXV/T1Sqq70jcG2qovHkdvLoqK/img.png)

현대 알고리즘은 **SCAN 알고리즘**들을 기반으로 사용하고 있음

File의 할당 방법에 따라 디스크 요청이 영향을 많이 받음

---

## **Swap-Space Management**

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsecQP%2FbtqCQnR0UjX%2Fm1WACWKYDAlhVDn7sJiU7k%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsecQP%2FbtqCQnR0UjX%2Fm1WACWKYDAlhVDn7sJiU7k%2Fimg.png)

### **Disk 사용 이유**

- 원이 나가면 저장한 데이터가 사라지는 메모리의 휘발성 때문
- DRAM 메모리 공간의 부족 -> swap area 용도로 사용

### **Swap-space**

- 메모리의 연장공간으로 사용
- 파일 시스템 내부에도 둘 수 있으나 별도 partition 사용이 일반적
- 속도 효율성이 우선
- 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨

### **RAID(Redundant Array of Independent Disks)**
![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsXn2o%2FbtqCQpoQpYB%2FGTbWP17iNqLHHkE2y6J0s1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsXn2o%2FbtqCQpoQpYB%2FGTbWP17iNqLHHkE2y6J0s1%2Fimg.png)
- 여러개의 디스크를 묶어서 사용
- 디스크 처리속도 향상: 여러 디스크에 분산 저장 된 것을 병렬적으로 읽어 오기 때문(= interleaving, striping)
- 신뢰성(Reliability) 향상: 동일 정보를 여러 디스크에 중복 저장, 디스크 고장나면 다른 디스크에서 읽어옴(= mirroring, shadowing)