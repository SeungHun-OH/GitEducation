> # 브랜치

Git은 데이터를 Change Set이나 변경사항(Diff)으로 기록하지 않고 일련의 스냅샷으로 기록한다는 것을 [Git 기초](.\Git 기초.md)에서 보여줬다.

커밋하면 Git은 "Staging Area"에 있는 데이터의 스냅샷에 대한 포인터, 저자나 커밋 메시지 같은 메타데이터, 이전 커밋에 대한 포인터 등을 포함하는 커밋 개체([커밋 Object](./Git의 내부구조))를 저장한다. 이전 커밋 포인터가 있어서 현재 커밋이 무엇을 기준으로 바뀌었는지 알 수 있다. 



## 브랜치란 무엇인가?

모든 버전 관리 시스템은 브랜치를 지원한다. 개발을 하다 보면 코드를  여러 개로 복사해야 하는 일이 자주 생긴다.  코드를 통째로 복사하고 나서 원래 코드와는 상관없이 독립적으로 개발을 진행할 수 있는데, 이렇게 <u>**독립적으로 개발하는 것**</u> 또는 <u>**커밋 사이를 이동할 수 있는 포인터**</u>를 브런치라고 한다.

Git의 브랜치는 어떠 한 커밋을 가리키는 40글자의 SHA-1체크섬 파일에 불과하기 때문에 만들기도 쉽고 지우기도 쉽다. 새 브런치를 하나 만드는 것은 41바이트 크기의 파일을 (40자와 줄 바꿈 문자) 하나 만드는 것에 불과하기 때문이다. 또한 프로젝트를 통째로 복사하는 것이 오래걸리는 다른 VCS와 달리 Git은 순식간이다. Git은 커밋을 할 때마다 이전 커밋의 정보를 저장하기 때문에 `Merge`할 때 어디서부터(Merge Base)합쳐야 하는지 알고 있다. 이러한 특징이 개발자들이 수시로 브랜치를 만들어 사용하게 된다.



#### 브랜치 목록 보기

```cmd
git branch
```

![마스터 브랜치](https://t1.daumcdn.net/cfile/tistory/9970983C5BFFBF321F)

Git은 생성할 때 부터 기본 브랜치를 생성하는데, 이 기본 브랜치를 `master`라고 부른다.



#### 새로운 브랜치 생성

```cmd
git branch <브랜치이름>
git checkout -b <브랜치이름>
```

![브랜치 추가](https://t1.daumcdn.net/cfile/tistory/999F19335BFFC0531F)

exp 브랜치를 추가한 것을 볼 수 있다. 현재 사용 중인 브랜치는 '*'표시가 있는 것이다.



<<<<<<< HEAD
#### 브랜치 삭제

```cmd
git branch -d <브랜치이름>
```



=======
>>>>>>> 30c7a29... Git 브랜치(완)
#### 브랜치 전환

```cmd
git check out <브랜치이름>
```

![브랜치 이동](https://t1.daumcdn.net/cfile/tistory/99F9EA4A5BFFC1A308)

master 브랜치에서 exp 브랜치로 이동한 것을 볼 수 있다.



#### 기타 명령어

```cmd
git log <브랜치명1>..<브랜치명2>	//"브랜치 간 커밋 차이 비교"
git diff <브랜치명1>..<브랜치명2>	//"브랜치 간 코드 차이를 비교"
git log --branches --graph --decorate	//"로그에 모든 브랜치 표시+그래프로 표시+브랜치명 표시"
```



#### 지금 작업 중인 브랜치가 무엇인지 Git은 어떻게 알고있을까?

다른 VCS들과 달리 Git은 'HEAD'라는 특수한 포인터가 존재한다. 이 포인터는 지금 작업하는 로컬 브랜치를 가리킨다. `git branch`명령은 브랜치를 만들기만 하고 브랜치를 옮기지는 않는다. 따라서 `git check out`명령어를 이용하여 새로 만든 브랜치로 이동할 수있다.





> # 브랜치 통합

Git에서 브랜치를 합치는 방법에는 **fast-forward와 3-way merge**가 있다. 어느 쪽을 사용하느냐에 따라 통합 후의 브랜치 이력이 달라지게된다.



## 1. 병합(merge)

A브랜치에서 B브랜치를 병합한다 (=A로 B를 가져온다. A<-B)

```cmd
git merge 병합시킬브랜치명
--squash : 대상브랜치를 병합할 때, 커밋 이력을 모두 제거하고 작업된 내용만 병합
```



`merge`에는 크게 두 가지 상황이 발생할 수 있다.

1. **서로 다른 파일을 수정했을 때**
   - `merge`를 한다면 git이 자동으로 소스코드를 합쳐준다. **(충돌없음)**
2. **서로 같은 파일을 수정했을 때**
   1. 서로 <u>같은 이름</u>의 파일을 수정했지만 **<u>수정한 부분이 다를때</u>**
      - 역시 `merge`를 한다면 git이 자동으로 소스코드를 합쳐준다. **(충돌없음)**
   2. 서로 <u>같은 이름</u>의 파일을 수정하고 **<u>수정한 부분이 겹칠 때</u>**
      -   git은 자동으로 merge를 해줄 수 없는 상황이라고 사용자에게 알리고 사용자가 그 부분을 수정해주기를 위임한다. **(conflict 발생)**



#### fast-foward

![fastfoward](https://git-scm.com/book/en/v2/images/basic-branching-4.png)

**상황** : 긴급한 'hotfix'를 처리 한 후, master로 다시 병합(fast-foward방식)하려한다.

master브랜치는 현재 아무런 commit을 생성하지 않은 상태이고, hotfix브랜치는 master브랜치에서 한 단계 커밋을 한 상태이다. 이러한 상태에서 master브랜치에서 hotfix브랜치를 `merge`하게되면

```cmd
$git merge hotfix
Updating f42c576..3a0874c
	Fast-forward
	index.html | 2 ++
	1 file changed, 2 insertions(+)
```

커밋(C4)는 master브랜치의 커밋(C2)에 기반한 브랜치이기 때문에 별도의 commit을 생성하여 병합을 하는 것이 아니라, master브랜치가 가리키고 있는 브랜치의 포인터만 hotfix로 바꾸기만 한다. 이후 중복된 브랜치 hotfix는 삭제한다.

![fastfoward](https://git-scm.com/book/en/v2/images/basic-branching-5.png)

따라서 master브랜치는 hotfix브랜치의 최신 commit log를 가리키게 만드는 것을 fast-foward(빨리감기)라고 한다.





#### 3-way-merge

![3way-merge](https://git-scm.com/book/en/v2/images/basic-branching-6.png)

**상황** : iss53은 커밋(C3)에서 커밋(C5)로 한 번더 commit을 진행하였고, master브랜치는 fast-foward를 하면서 커밋(C4)로 이동했다.

갈라진 두 브랜치를 병합하기 위해서는 공통의 커밋을 이용하여 `merge`를 진행해야 한다. master브랜치에서 iss53브랜치를 병합하게 되면

```cmd
$git merge iss53
	Merge made by the 'recursive' strategy.		//"3-way-merge가 성공적으로 일어난 경우 표시"
	index.html |    1 +
	1 file changed, 1 insertion(+)
```

hotfix를 `merge` 했을 때와 메시지가 다른 것을 알 수 있다. 현재 브랜치가 가리키는 커밋이 `merge`할  브랜치의 조상이 아니므로 Git은 'fast-foward'로 `merge` 하지 않는다. 이 경우에는 Git은 **<u>각 브랜치의 커밋 두개</u>**와 **<u>공통 조상 하나</u>**를 사용하여 **3-way-merge**를 한다.

![3-way-merge](https://git-scm.com/book/en/v2/images/basic-merging-1.png)

단순히 브랜치 포인터를 최신 커밋으로 옮기는 것이 아니라 3-way-merge의 결과를 별도의 커밋(C6)으로 만들고 나서 해당 브랜치가 그 커밋을 가리키도록 이동시킨다. 이러한 커밋은 부모가 여러개 이므로 **Merge commit**이라고 부른다.

![merge commit](https://git-scm.com/book/en/v2/images/basic-merging-2.png)

iss53 브랜치를 master브랜치에 `merge`하고 나면 더는 iss53은 필요없으므로 삭제한다.





## 2. 충돌(Conflict)

`merge` 하는 두 브랜치에서 같은 파일의 한 부분을 동시에 수정하고 Merge 하면 Git은 해당 부분을 Merge 하지 못한다. 만약 iss53브랜치와 hotfix브랜치가 같은 부분을 수정했다면 GIt은 `merge` 하지 못하고 아래와 같은 충돌(conflict) 메시지를 출력한다.

```cmd
$git merge iss53
	Auto-merging index.html
	CONFLICT (content): Merge conflict in index.html
	Automatic merge failed; fix conflicts and then commit the result.
```

Git은 자동으로 `merge`하지 못해서 새 커밋이 생기지 않는다. 이럴때 변경사항의 충돌을 개발자가 해결해야 `merge`과정을 진행할 수 있다. Git이 어떤 파일을 `merge` 할 수 없었는지를 살펴보려면 <kbd>git status</kbd>명령을 이용한다.

```cmd
$git status
	On branch master
	You have unmerged paths.
  		(fix conflicts and run "git commit")

	Unmerged paths:
  		(use "git add <file>..." to mark resolution)

    		both modified:      index.html

no changes added to commit (use "git add" and/or "git commit -a")
```

충돌이 일어난 파일은 `unmerged`상태로 표시된다. `unmerged`상태인 `index.html`파일을 열어보면 아래와 같은 내용을 볼 수 있다.

```cmd
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

**\=====** 위쪽의 내용은 HEAD 버전(merge명령을 실행할 때 작업하던 master 브랜치)의 내용이고 아래쪽은 iss53브랜치의 내용이다. 충돌을 해결하려면 위쪽이나 아래쪽 내용 중에서 고르거나 새로 작성해야한다.

```cmd
<div id="footer">
please contact us at email.support@github.com
</div>
```

충돌한 양쪽에서 조금씩 가져와서 새로 수정했다. 이렇게 충돌된 부분을 해결하고 `git add`명령으로 "Statging Area"에 저장됐는지 확인했으면 `git commit`명령으로 `merge`한 것을 커밋한다.















[참고1]<https://backlog.com/git-tutorial/kr/stepup/stepup2_3.html>

[참고2]<https://backlog.com/git-tutorial/kr/stepup/stepup1_1.html>

[참고3]<https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html>

[참고4]<https://www.zerocho.com/category/Git/post/582342a73fbde700178771f5>

[참고5]<https://psklog.tistory.com/20?category=772045>

[참고6]<https://cyberx.tistory.com/81>

[참고7]<https://m.blog.naver.com/imf4/220796681049>

[참고8]<https://git-scm.com/book/ko/v2/Git-브랜치-브랜치란-무엇인가>

[참고9]<https://github.com/progit/progit/blob/master/ko/03-git-branching/01-chapter3.markdown>

[참고10]<https://seonkyukim.github.io/git-tutorial/git-merge/#>