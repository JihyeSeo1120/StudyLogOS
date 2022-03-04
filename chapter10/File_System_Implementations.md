# File System Implementations

## Allocation of File Data in Disk | 디스크에 파일 저장 방법 3가지

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd2kOOv%2FbtqCKA4XCFv%2F5l4qZWnrXuckaVUL0MMVb1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd2kOOv%2FbtqCKA4XCFv%2F5l4qZWnrXuckaVUL0MMVb1%2Fimg.png)

- 테이프: 순차 접근만 가능. 건너뛰기 불가능
- 하드 디스크, 플래시 메모리: 건너뛰기 가능. 직접 접근 가능한 매체

### Contiguous Allocation | 연속 할당

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FUL0Ix%2FbtqCKgyU7oB%2FGKE4TTCDNaJrtPK44Gxj91%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FUL0Ix%2FbtqCKgyU7oB%2FGKE4TTCDNaJrtPK44Gxj91%2Fimg.png)

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKOlYO%2FbtqCLNJxyet%2FmjIjd2J80MOdiT8cN2LLQk%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKOlYO%2FbtqCLNJxyet%2FmjIjd2J80MOdiT8cN2LLQk%2Fimg.png)

- 하나의 file이 disk 상에 연속해서 저장되는 방법. ex) 19~24번 블록까지 연속해서 할당됨
- directory라는 file은 directory 밑에있는 file들의 metadata들을 내용으로 한다(이름 및 위치정보).
- 단점
    1. 파일들의 길이가 균일하지 않기 때문에 중간의 free block으로 인한 외부 조각 발생
    2. 파일 생성 이후에 연속 할당이 가능하지 않은 크기로의 확장 제약 -> 이를 위해 미리 메모리상에서 빈 공간을 확보하는 방법 -> 당장 사용하지 않는 공간으로 인한 공간 낭비(중간에 홀이 생김)
- 장점
    1. 빠른 I/O 가능. 하드 디스크의 파일 I/O => 헤드의 이동. 어떤 파일을 전부 읽고 싶다면 한 번의 seek으로 시작 주소까지 찾아가면 더 이상 seek가 필요 없이 전부 읽어올 수 있음(물론 같은 트랙 상에 존재할 때) -> 때문에 파일 저장이 아닌, 프로세스의 주소 공간 일부를 물리적 메모리에서 쫓아내고 가져오는 Swap  area로 사용. 또는 데드라인이 있고 빠른 처리가 중요한 real time file용
    2. 파일이 시작 위치로부터 연속적으로 위치하기 때문에, 파일의 특정 위치만 궁금하다면 굳이 시작위치부터 볼 필요 없이 시작 위치 + 해당 위치로 접근해서 데이터 확인 가능(직접 접근 가능)

### Linked Allocation

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdBIkZA%2FbtqCID2nlQO%2FP9V2kdbo2JhGZqmcMzHYE1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdBIkZA%2FbtqCID2nlQO%2FP9V2kdbo2JhGZqmcMzHYE1%2Fimg.png)

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FuEsGz%2FbtqCMnRoELK%2F2q3695BrvQvFLKF3CEcu10%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FuEsGz%2FbtqCMnRoELK%2F2q3695BrvQvFLKF3CEcu10%2Fimg.png)

- file을 연속적으로 배치하지 않고 빈 위치면 아무곳이나 배치시킴
- 디렉토리에는 파일의 시작 주소만 저장해놓고, 해당 주소에 가보면 다음 주소가 저장되어 있음
- 단점:
    1. 해당 주소에 가야만 다음 주소를 알 수 있기 때문에 파일에서 특정 위치(중간에 위치한)의 데이터가 궁금하다면 그 시작 주소부터 하나하나 순차적으로 확인해야 함. 즉, 데이터에 직접 접근 불가능. 건너뛰는 게 불가능. 다음 위치로 이동하는 데 발생하는 헤드의 seek가 많이 발생
    2. 하나의 섹터에서 고장이 발생해 pointer가 유실되면 다음 데이터들의 주소 역시 망가지기 때문에 파일의 많은 부분을 유실할 수 있음
    3. 크기가 512Byte인 하나의 섹터에서 다음 주소의 포인터를 위해 할당해주는 크기가 4Byte. 여기서 메모리 낭비가 발생할 수 있음.
- 장점
    1. 메모리 공간에서 external fragmentation이 발생하지 않음. 즉, 각기 다른 크기의 파일로 인한 메모리 낭비가 없음
- 개선
    - File Allocation Table(FAT): 이러한 Linked allocation의 문제를 해결한 파일 시스템

### Indexed Allocation

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbs1dk2%2FbtqCLLE0dhB%2FKB4j90giiKtWY3h4poRkt0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbs1dk2%2FbtqCLLE0dhB%2FKB4j90giiKtWY3h4poRkt0%2Fimg.png)

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwDHF0%2FbtqCJmF9jao%2FyhuXLFnHoG0NrwkaEFKUo0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwDHF0%2FbtqCJmF9jao%2FyhuXLFnHoG0NrwkaEFKUo0%2Fimg.png)

- 하나의 파일을 구성하는 블록들의 주소를 index block에 저장.
- 디렉토리에는 하나의 파일당 하나의 index block을 마치 주소록처럼 가지고 있어서, 특정 파일에 접근하고 싶다면 그 파일의 index block을 먼저 확인해야 함. 그리고 index block에서 필요한 블록의 위치를 확인해서 직접 접근 가능.
- index block은 내용을 담고 있지 않고 file이 어디어디에 저장되어있다는 위치정보를 열거해놓는 블록이다.
- 장점
    1. External fragmentation이 발생하지 않음
    2. Direct access 가능
- 단점
    1. 아무리 작은 크기의 파일이라도 최소 두 개의 블록 필요(실제 메모리상의 블록과, 블록에서 index block).
    2. 굉장히 큰 파일의 경우 하나의 index block으로 모든 주소를 표시하기 어려움.
    - 해결방안 1) linked scheme: 하나의 index block으로 파일의 모든 주소를 표시할 수 없을 때, index block의 마지막 주소는 메모리상 파일의 데이터 주소가 아니라 또 다른 index block의 주소로 연결.
    - 해결방안 2) multi level index: 하나의 index block의 주소들이 메모리상 파일의 데이터 주소를 나타내는 게 아니라, 또 다른 index block을 가리키게 함.
    - → 모두 index block을 위한 공간 낭비가 존재

---

## Unix FIle System

![https://user-images.githubusercontent.com/76726411/151667529-5801f821-19e3-4c99-8598-ecd63ab4048c.png](https://user-images.githubusercontent.com/76726411/151667529-5801f821-19e3-4c99-8598-ecd63ab4048c.png)

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvlQdJ%2FbtqCHwWOrv1%2FrQRYeTndVasE9LAGoJZYOK%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvlQdJ%2FbtqCHwWOrv1%2FrQRYeTndVasE9LAGoJZYOK%2Fimg.png)

- Boot block: 부팅에 필요한 정보. 어떤 파일시스템이든 boot block이 가장 먼저 위치함(0번 블록에 위치).
- Super block: 파일시스템에 대한 총체적인 정보. 어떤 부분이 비어있는 블록이고, 어디에 파일이 저장된(사용 중인) 블록인지 구분
- Inode list: 디렉터리가 파일의 모든 메타 데이터를 가지고 있는 건 아님. 실제 파일의 메타 데이터를 별도로 저장하고 관리하는 공간이 Inode(Index node) list. 파일 하나당 inode가 하나씩 할당. inode에 메타 데이터를 저장하고 있는 구조. 하지만 파일의 이름은 inode가 아닌 디렉터리가 관리함. 디렉터리에서 파일의 이름과 inode를 저장하고 있어서, 파일의 이름을 찾은 다음 inode 주소를 확인하고 찾아가서 여러 메타 데이터를 이용.
- UNIX 파일 시스템 → 거의 indexed allocation. 가급적 작은 index block을 가지고 크기가 큰 파일을 표시해야하기 때문에, direct blocks, single indirect, double indirect, triple indirect 이렇게 4개의 block을 사용해서 표시. 크기가 작은 파일은 direct block으로만 표현 가능. 크기가 큰 파일은 index block의 층을 나타내는 single, double, triple indirect를 사용해서 표현.

## FAT File System

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FWspHu%2FbtqCJmznddY%2FilRBKXkb0HAxeIES9QNtNk%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FWspHu%2FbtqCJmznddY%2FilRBKXkb0HAxeIES9QNtNk%2Fimg.png)

- boot block: 어떤 파일 시스템이든 0번 위치. 부팅에 관한 데이터(커널의 위치 등등)
- FAT: 파일의 메타 데이터 중 일부를 저장. 하지만 굉장히 일부(오직 위치정보만 관리). 나머지 메타 데이터는 파일의 디렉토리가 관리(접근 권한, 소유주, 첫 번째 위치 등등..)

## Free-Space Management | 디스크의 비어있는블록 관리

1. Bit map
- 각 블록별로 번호가 매핑. Unix의 경우 Super block에서 0과 1을 통해 파일들의 상태를 표시.
- 연속적인 n개의 free block을 찾는데 효과적.
2. Linked list
- 비어있는 블록들을 연결. 어차피 비어있기 때문에 포인터를 통해 비어있는 다음 위치를 저장하는 게 가능. bit map에 비해 추가적인 공간낭비가 없음. 하지만 연속적인 빈 공간을 찾기 어려움.
3. Grouping
- 하나의 비어있는 블록이 index block 역할.
4. Counting
- 연속적인 빈 블록을 찾기 위해 빈 블록의 위치를 가리키고 몇 개의 블록이 비어있는지도 저장해서 관리

## Directory Implementation

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FehpqE5%2FbtqCNaqLUAT%2F7B5dZ4OTkUsbkNBAAlwV11%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FehpqE5%2FbtqCNaqLUAT%2F7B5dZ4OTkUsbkNBAAlwV11%2Fimg.png)

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcLDHIS%2FbtqCM9Fo7ib%2FzukwOxQ4DWoYaxyur7GRk0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcLDHIS%2FbtqCM9Fo7ib%2FzukwOxQ4DWoYaxyur7GRk0%2Fimg.png)

## VFS and NFS

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSdu63%2FbtqCLc3Tuxj%2Fi6k0kb7MvQ7tttHz4rUHl1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSdu63%2FbtqCLc3Tuxj%2Fi6k0kb7MvQ7tttHz4rUHl1%2Fimg.png)

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbGY8lW%2FbtqCHxBqmQM%2FXDO1Q7CWG3uqF785s5MJh0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbGY8lW%2FbtqCHxBqmQM%2FXDO1Q7CWG3uqF785s5MJh0%2Fimg.png)

- VFS
    - 파일 시스템의 종류는 굉장히 다양함. 사용자가 파일 시스템에 접근할 땐 syscall을 해야하는데, 파일 시스템별로 syscall 형식이 다르면 불편하기 때문에 개별 파일 시스템 위 계층에 VFS라는 인터페이스를 통해 syscall 호출. 사용자 입장에서는 동일한 API를 사용해서 syscall을 가능하게 해주는 게 VFS.
- NFS
    - 원격에 저장된 파일 시스템을 불러올 때 사용. 클라이언트와 서버가 네트워크로 연결되어 있을 때, 클라이언트가 자신의 로컬에 저장된 파일 시스템과 원격에 저장된 파일 시스템에도 접근이 가능하게 만들어줌. NFS를 통해 RPC(Remote PC)에 연결한 뒤 역시 VFS를 통해서 syscall 사용. 클라이언트와 서버 모두 NFS 모듈을 설치해야 함

## Page Cache and Buffer Cache

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fr5NdI%2FbtqCHxuKuNg%2FCAuTcftsCd75rM5rOpUC9k%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fr5NdI%2FbtqCHxuKuNg%2FCAuTcftsCd75rM5rOpUC9k%2Fimg.png)

- Page Cache
    - 가상 메모리 측면. 메모리에 올라간 page에 대해서는 하드웨어적인 주소전환만 이뤄지기 때문에 운영체제가 가지고 있는 정보가 제한적. 때문에 clock 알고리즘을 사용. 페이지 단위(4KB)
- Buffer Cache
    - 파일 시스템 측면. 파일 접근에는 무조건 syscall이 필요하기 때문에 운영체제가 모든 정보를 관리. 때문에 LRU, LFU 등등이 사용 가능.
    - 운영체제가 사용자의 요청을 받아서 디스크에서 가져온 정보를 사용자에게 바로 주지 않고 buffer cache에 저장한 뒤에 전달. 이후에 버퍼에 저장된 내용을 요청하면 다시 디스크까지 가지 않고 버퍼에서 데이터를 전달. 논리적 블록(디스크의 섹터, 예전에는 512Byte. 페이지 단위인 4KB에 비해 작음)

→ 예전에는 버퍼 캐시와 페이지 캐시를 구분해서 사용했다면, 최근에는 Unified Buffer Cache를 많이 사용. 버퍼와 페이지 캐시를 통합. 버퍼 캐시의 단위도 작은 단위가 아닌 페이지와 동일(4KB)한 단위의 블록을 사용함. 페이지를 용도에 맞춰서 할당해줌.

- Memory-Mapped I/O
    - 기존의 파일 사용은 파일은 OPEN()한 다음 write syscall, read syscall 등등을 통해 사용. 하지만 memory-mapped는 파일의 일정 부분을 메모리 영역에 맵핑한 뒤 사용. 한 번 맵핑을 해놓으면 파일을 호출해서 저장하는 게 아니라 메모리 영역에 write/read 가능.
- unified buffer cache
    - 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨. buffer cache도 page 단위로 관리

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtLXuv%2FbtqCOyyWtIj%2Fx7Va8uZiO8Kq9nJiu4cDO1%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtLXuv%2FbtqCOyyWtIj%2Fx7Va8uZiO8Kq9nJiu4cDO1%2Fimg.png)

1. Unified Buffer Cache를 사용하지 않는 File I/O
- memory-maped: 명령어를 통해 memory mapped 선언. 처음엔 똑같이 데이터를 디스크에서 버퍼 캐시로 가져옴. 그다음 그 내용을 page cache에 복사. 사용자는 이제 자신의 메모리 영역에 읽고 쓰듯이 작업을 하면서 파일 입/출력 가능. 일단 메모리에 올라오면 운영체제의 write/read syscall처럼 운영체제의 도움 없이 스스로 읽기/쓰기 작업이 가능함. 하지만 어떤 방법을 사용하든 버퍼 캐시에 접근을 해야 함
2. Unified Buffer Cache를 사용하는 File I/O
- 운영체제가 공간을 따로 나누지 않고 page cache에서 분할해서 사용하는 경우
    - I/O 작업 -> syscall을 통해 운영체제에게 CPU 제어권이 넘어감.
    - memory-mapped: 초반 메모리 주소-파일 맵핑 작업 이후에는, 사용자 프로그램의 주소 영역에 페이지 캐시가 맵핑이 됨. 그러면 페이지 캐시이자 버퍼 캐시가 모두 파일에 맵핑되었기 때문에 운영체제의 도움 없이 파일 I/O 작업이 가능

## 프로그램의 실행

![https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbSHEdd%2FbtqCM9fyTwh%2FrL7HDMgZ6Sdo9GvCzelpV0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbSHEdd%2FbtqCM9fyTwh%2FrL7HDMgZ6Sdo9GvCzelpV0%2Fimg.png)

> 실행파일 in 파일시스템 → 실행 파일을 실행시켜 프로세스가 됨 → 프로세스별 주소 공간 생성 (virtual menmory) → 주소변환 해주는 하드웨어에 의해서 필요한 부분들만 물리적 메모리에 올라감, 쫓겨나는 건 Swap Area로 넘어감

- 코드 부분 영역은 실행 파일에 존재하기 때문에 swapping에 의해 쫓겨날 때에도 **Swap area에 저장할 필요가 없음**(어차피 실행 파일에 있는 걸 가져오면 됨). 즉 가상 메모리에 존재하는 프로세스의 Code 영역은 파일 시스템의 실행 파일과 맵핑. 파일 IO에도 mmaped를 사용할 수 있지만, 실행 파일의 코드 부분을 맵핑하는 용도로도 사용.
- 프로그램이 실행되다가 파일 시스템에서 데이터를 가져오는데, I/O write/read가 아니라 memory mapped를 사용하고 싶으면 커널 영역에 mmap 요청(파일 시스템의 일부를 자신의 메모리 공간에 맵핑해달라는 요청). 그럼 운영체제가 데이터 파일의 일부를 프로세스의 가상 메모리 주소 공간에 맵핑. 이후에 프로세스는 해당 데이터에 접근할 때 OS의 도움 없이 그냥 데이터가 맵핑된 자신의 가상 메모리에 접근함으로써 write/read 작업이 가능(syscall 필요가 없으므로 더 빠름).
- 주의할 점
    - 하나의 프로세스가 아닌 여러 프로세스가 동시에 특정 데이터를 memory-mapped 방식으로 사용하면, unified buffer cache 방식에서는 이미 다른 프로세스에 의해 memory-mapped 되어 물리적 메모리에 올라온 데이터가 여러 프로세스에 의해 사용되기 때문에 데이터의 일관성 문제가 발생할 수 있음.
    - read()의 경우에는 그냥 물리적 메모리의 버퍼(&페이지) 캐시에서 값을 복사해서 사용하기 때문에 문제가 없지만, write()에서 일관성 문제가 발생할 수 있음
