# File System

## File and File System

### File

- a named collection of related information
- 일반적으로 비휘발성의 보조기억장치에 저장
- 운영체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌
- operation(연산)
    - create, read, write, reposition(lseek), delete, open, close 등

### File attribute(혹은 File의 **metadata**)

- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
    - 파일 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

### File system

- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등을 저장 및 관리
- 파일의 저장 방법 결정
- 파일 보호

## Directory and Logical Disk

### Directory

- 파일의 메타데이터 중 일부를 보관하고 있는 **일종의 특별한 파일**
- 그 디렉토리에 속한 파일 이름 및 파일 attribute들을 내용으로 갖는다.
- operation
    - search for a file, create a file, delete a file
    - list a directory, rename a file, traverse the file system

### Partition(=Logical Disk=논리적인 디스크)

- 하나의 (물리적)disk 안에 여러 partition을 두는게 일반적
- 여러개의 물리적인 disk를 하나의 partition으로 구성하기도 함
- (물리적)disk를 partition으로 구성한 뒤 각각의 partition에 file system을 깔거나 swapping등 다른 용도로 사용할 수 있음

## Open()

![https://user-images.githubusercontent.com/76726411/151666511-d065ee3c-e095-4b7b-9df7-1a1ee9c2130a.png](https://user-images.githubusercontent.com/76726411/151666511-d065ee3c-e095-4b7b-9df7-1a1ee9c2130a.png)

**open :** **file의 metadata를 memory로 올려놓는 것**을 말한다.

```jsx
open("/a/b")
```

open을 하면 일단 root directory는 운영체제가 알고 있기 때문에 거기서 root metadata를 찾아서 memory에 올린다. root의 metadata를 열어보면 거기에 root directory의 실제 위치정보가 있다. root directory의 내용(content)에는 a의 metadata가 있을 것이다. 그걸 memory에 올린다(a open). a의 metadata안에는 a의 위치정보가 들어있을 것이고, 그 안에는 b의 metadata가 있을 것이다. b의 metadata를 memory에 올린다(b open).

**open이 끝나면 어떤 값을 return할까?**

각 프로세스마다 그 프로세스가 open한 file들에 대한 metadata pointer를 가진 배열이 정의되어 있다. b라는 file의 metadata를 가리키는 포인터가 그 배열 어딘가에 만들어지고, **배열의 index가 b라는 file의 fd(file descriptor)가 되어 그 값을 사용자 프로세스에게 return 한다.**

이제 read(fd)의 system call을 부른다 그럼 system call이니까 CPU가 운영체제에게 넘어간다. process A가 b의 fd를 가지는 file을 읽어오라고 했으니까 A의 PCB에 가서 해당 descriptor에 대응하는 file(b)의 metadata부분을 open file table에서 찾은 다음 위치정보를 바탕으로 disk에서 값을 읽어온다. 그 내용을 메모리로 읽어서 프로그램에게 전달해주면 된다.

이때, 직접 전달해주는게 아니라 kernel 영역에 먼저 읽어놓고 사용자 프로그램에게 copy해서 전달해준다. 만약 이 프로그램 혹은 다른 프로그램이 동일 file 동일 위치를 요청하면 disk까지 가지 않고 운영체제에서 바로 전달해준다. 이걸 **buffer caching**이라고 한다.

buffer caching 환경에서는 system call이므로 **항상 운영체제로 CPU 제어권이 넘어오고**, 넘어온 다음에 disk에서 data를 가져올지, buffer cache에 있는 data를 줄지 결정한다.(**모든 정보를 운영체제가 알고 있다**) 따라서 virtual memory system의 paging기법과는 달리 **LRU, LFU 알고리즘을 사용할 수 있다.**

- file descriptor table은 프로세스마다 있기 때문에 **per-process file descriptor table**이라고 한다.
- open file table은 system에서 global하게 하나만 존재하고 이걸로 모두 관리한다. 따라서 **system-wide open file table**이라고 한다.

metadata가 disk에 있을 때는 이정도면 충분한데, 이걸 memory로 올려놓게 되면 추가적으로 한 가지 metadata가 더 필요하다 - 이 프로세스가 이 file의 어느 위치를 접근하고 있다는 offset이 필요하다.

## File Protection

file은 여러 사용자, 여러 프로그램이 같이 사용할 수도 있다. 따라서 file에 대한 접근 권한은 **접근권한이 누구한테 있느냐**에 해당하는 것과 **접근연산이 어떤게 가능하느냐**에 관한 것 두 가지를 같이 가지고 있어야 한다.

### Access control 방법 3가지

1. Access Control Matrix
- 2차원 행렬로 관리하게 되면 권한이 없는 사용자나 file에 대한 메모리가 많이 낭비된다.
- ACL(Access control list)을 만들어서 **file별**로 권한이 있는 user만 linked list로 묶어서 관리를 한다던가 / Capability list를 만들어서 **user를 중심으로** user에 대해 접근할 수 있는 file들을 linked list로 묶어서 관리를 하는 방식을 사용한다.
- 그러나 이것조차 overhead가 크기 때문에 **일반적인 OS에서는 grouping이라는 방법을 통해 file의 접근권한을 제어한다.** (단 9개의 bit만 있으면 된다.)
1. Grouping
- 모든 사용자에 대해서 접근 권한을 나누는 것이 아니라 사용자 그룹을 세 가지(owner, group, public)로 나눈다.
- 일반적으로 사용된다.
1. Password
- 모든 파일/디렉토리에 대해서 패스워드를 두어서 관리한다.

## File System의 Mounting

하나의 물리적인 disk를 partitioning을 통해서 여러개의 논리적인 disk로 나눌 수 있다.

각각의 논리적인 disk에는 file system을 설치해서 사용할 수 있다. 만약 다른 partition에 설치되어 있는 file system에 접근하려면 어떻게 해야 할까?

### mounting을 사용한다.

root file system에 있는 특정 directory 이름에다가 또 다른 partition에 있는 file system을 mount 해주면 연결할 수 있다.

![https://user-images.githubusercontent.com/76726411/151666531-e52d730b-a185-433a-9f48-bc80853f13c5.png](https://user-images.githubusercontent.com/76726411/151666531-e52d730b-a185-433a-9f48-bc80853f13c5.png)

## Access Methods

시스템이 제공하는 파일 정보의 접근 방식

1. 순차 접근(sequential access)
    - 카세트 테이프를 사용하는 방식처럼 접근한다.
    - 읽거나 쓰면 offset은 자동으로 증가한다.
2. 직접 접근(direct access, random access)
    - LP 레코드 판과 같이 접근하도록 한다.
    - file을 구성하는 레코드를 임의의 순서로 접근할 수 있다.