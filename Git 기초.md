> ## 형상관리(Version Control Revision Control)툴

### 집중식 버전 관리 시스템(Centeralized Version Control System; CVCS)

![CVCS구성](https://t1.daumcdn.net/cfile/tistory/27096736594B1C5B0A)

-   CVCS에서는 버전 관리되는 모든 파일을 저장하는 하나의 서버와, 이 중앙 서버에서 파일들을 가져오는(checkout) 다수의 클라이언트가 존재, 대표적으로 SVN이 있다.
-   단점이 하나 있는데 중앙 서버가 잘못되면 모든 것이 잘못된다는 점이다. 만약에 서버가 다운될 경우, 서버가 다시 복구 될 때까지 다른사람과의 협업이나, 진행 중이던 버전관리도 어려워지게 되는데, 프로젝트에 대한 **모든 이력을 중앙 서버 한 곳에서만 관리**하기 때문이다.

![Git 파일관리](https://git-scm.com/figures/18333fig0104-tn.png)

- SVN에서 각 파일에 대한 변화(델타[^1]), **파일의 집합을 시간순으로 저장**한다.



### 분산 버전 관리 시스템(Distributed Version Control System; DVCS)

![Git구성](https://t1.daumcdn.net/cfile/tistory/213CC73C594B1FDC11)

- 위와 같은 문제를 해결하기 위해 개발된 버전관리 시스템, 대표적으로 **Git**이 있다.
- DVCS에서는 클라이언트가 파일들의 마지막 스냅샷을 가져오는 대신 **저장소(Repository)를 통째로 복제**한다. 이는 서버에 문제가 생기면 모든 버전관리 작업이 All Stop되는 CVCS와 달리, 클라이언트의 내용을 서버에 복사하면 서버를 복구 할 수 있다.
- 게다가 대부분의 DVCS에서는 다수의 원격 저장소(remote repository)를 갖는 것이 가능하기 때문에 동시에 여러 그룹과 여러 방법으로 함께 작업할 수 있다. 이로 인해 CVCS에서는 할 수 없는 다양한 작업 방식 들을 사용할 수 있다.



![](https://git-scm.com/figures/18333fig0105-tn.png)

- Git의 데이터는 **파일 시스템의 스탭샷**이라 할 수 있으며 크기가 아주 작다.





> # Git

- 리누스 토발즈가 2005년 개발
- VCS(Version Control System)에 D를 더한 Distributed VCS라고 한다.
- 분산버전관리로 다수의 사람들과 공통의 프로젝트를 진행할때 효과적으로 협업할 수 있는 도구이다.

### 특징

1. **여러 저장소에 분산 저장된다.**
- 전체 개발 내역을 각 개발자의 로컬 컴퓨터로 복사할 수 있다. 추후 수정된 내역을 병합(Merge)할 수 있다.
  
2. **중앙저장 방식이 아닌 분산저장**
- 하나의 저장소가 문제가 생겨도 다른 작업자의 저장소에 접근하여 복구 및 계속 작업이 가능하다.
  
3. **포크 생성 및 자유로운 브랜치 생성**

   - 프로젝트를 진행하기 위한 다양한 형상관리 방법을 만날 수 있다.

4. **가장 마지막 revision을 스냅샷으로 통으로 저장하고, 그 전 버전들을 DELTA로 관리한다.**
- '사람이 돌아가는 시점은 가장 최근의 경우가 많다'라는 가정에서 시작한 DELTA관리 기법
   - 스냅샷 저장시 변경되지 않은 파일은 Link를 통해서만 관리, 즉 계속 재 생성하지는 않음
   
5. **무결성 관리**
- 파일이름을 저장하지 않고 해당 파일의 해시로 저장
   - SHA-1 해시를 사용한 체크섬(40자 길이의 16진수 문자열)
   - 처음 두글자는 디렉토리 이름으로 사용됨
   



### 동작 원리

​	기본적으로 깃 프로젝트에 담겨 있는 데이터들은 파일 시스템 상에서 **스냅샷(Snapshot)**이라고 볼 수 있다. 실제로 프로젝트를 커밋(Commit)하여 적용할 때의 순간을 중요시한다는 특징이 있다. 파일 자체를 저장하기보다 수정 내역 자체를 저장한다.

Git 프로젝트의 세 가지 구성요소에 대해서 이해할 필요가 있다.



**- Working Directory: **작업할 파일이 있는 디렉토리

​	Working Directory의 모든 파일은 크게 **Tracked(관리대상임)**와 **Untracked(관리대상 아님)**로 나눈다. Tracked 파일은 또 <u>unmodified(수정하지 않음), modified(수정함), staged(커밋으로 저장소에 기록할) 상태 중 하나</u>이다. 간단히 말하자만 Git이 알고 있는 파일이라는 것이다. 그리고 나머지 파일은 모두 Untracked 파일이다.  Untracked 파일은 Working Directory에 있는 파일 중 스냅샷에도 Staging Area에도 포함되지 않은 파일이다. 처음 저장소를 Clone 하면 모든 파일은 Tracked이면서 Unmodified 상태이다. 파일을 Checkout 하고 나서 아무것도 수정하지 않았기 때문에 그렇다. 마지막 커밋 이후 아직 아무것도 수정하지 않은 상태에서 어떤 파일을 수정하면 Git은 그 파일을 **Modified** 상태로 인식한다. 실제로 커밋을 하기 위해서는 이 수정한 파일을 Staged 상태로 만들고, Staged 상태의 파일을 커밋한다. 이런 라이프사이클을 계속 반복한다.

![파일의 라이프사이클](https://git-scm.com/book/en/v2/images/lifecycle.png)

**- Staging Area: **커밋(Commit)을 수행할 파일들이 올라가는 영역

**- Git Directory: **Git 프로젝트의 메타 데이터와 데이터 정보가 저장되는 디렉토리



![동작 과정](https://t1.daumcdn.net/cfile/tistory/9971A1405C1DDADF20)

리모트 저장소에서 클론을 받으면 자신의 컴퓨터로 프로젝트가 통째로 다운로드가 된다.

이후에 자신이 수정한 내역을 다시 리모트 저장소까지 반영하려면 <kbd>git add</kbd> :arrow_right: <kbd>git commit</kbd> :arrow_right: <kbd>git push</kbd> 의 과정을 거쳐야된다.

이후 다시 리모트 저장소의 수정된 내역을 로컬 저장소로 받으려면 <kbd>git fetch</kbd>를 통해 내려받는데, 기존 로컬 프로젝트와 리모트 프로젝트가 다를 수 있기 때문에 <kbd>git merge</kbd>를 수행하여 수정된 내용을 반영할 수 있다.

또한 <kbd>git pull</kbd>을 통해 <kbd>git fetch</kbd>와 <kbd>git merge</kbd>를 한번에 할 수 있다.



### 저장소(Repository)

​	저장소(Repo)는 <u>실제 소스코드가 담겨 있으면서 커밋(Commit) 내역 등의 모든 작업 이력이 담겨 있는 공간</u>을 의미한다. 프로젝트의 메타 데이터를 포함한 각종 데이터는 <kbd>.git 폴더</kbd>에 담기게 되는데, 실제로 열어보면 각종 데이터와 해시 값 등이 담겨 있는 것을 확인할 수 있다.

어떠한 파일을 <kbd>Commit</kbd>하게 되면 각 작업을 분류하기 위해 Hash 값을 이용한다. 일반적으로 Hash 값은 충돌이 발생하지 않아 정확한 커밋 내역 관리가 가능하다.

##### Git 저장소 만들기

```cmd
git init
```





### 프로젝트에 반영

​	일반적으로 소스코드를 수정하는 방법은 두 가지 사례로 나뉘게 된다.

1. **해당 프로젝트에 소속된 사람이 아닌 경우**
   - 특정한 커뮤니티(Community)의 구성원이 아니라서 스스로 커밋을 하여 저장소에 적용할 권한이 없다면, 소스코드를 수정하는 것에 제약이 있을 수 있다. 이런 경우 **PR(Pull Request)를 작성하여 오픈소스에 기여**할 수 있다.

2. **해당 프로젝트에 소속된 사람인 경우**
   - 해당 프로젝트에 대한 권한을 가지고 있으므로 언제든지 <kbd>Commit</kbd>과 <kbd>push</kbd>를 통해 수정 내역을 반영할 수 있다.



### 기본 명령어

```cmd
git add <파일이름>	//"Staging Area에 파일 추가"
git add .	//"Staging Area에 전체 파일 추가"
git reset <파일이름> //"Staging Area에서 제거"
git status //"깃 상태확인"
git log	//"커밋 내용 확인"
```



> # GItHub

Github는 Web Service인데 주요 기능으로 Git 원격저장소를 제공한다.



#### 리모트 저장소 연결

```cmd
git config --global user.name 아이디
git config --global user.name 비밀번호
git clone 리모트저장소주소
```

#### 리모트 저장소에 파일 업로드

```cmd
git add 파일이름 //"Staging Area로 추가, add .으로 하면 모든 파일 추가"
git reset 파일이름 //"Staging Area에서 삭제"
git commit -m "커밋메세지"
git push
```











[^1]: 두 버전을 비교했을 때 두 버전간의 파일 차이만을 기록하여 묶는 것을 뜻한다.