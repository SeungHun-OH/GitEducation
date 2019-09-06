> # Git의 세 개의 트리

​	Git을 서로 다른 세 트리를 관리하는 컨텐츠 관리자로 생각하면 <kbd>reset</kbd>과 <kbd>checkout</kbd>을 더 쉽게 이해할 수 있다.

여기서 "트리"란 실제로는 "파일의 묶음"이다. 자료구조의 트리가 아니다 (세 트리 중 Index는 트리도 아니지만, 이해를 쉽게하려고 일단 트리라고 한다.)

Git은 일반적으로 세 가지 트리를 관리하는 시스템이다.

### Head

현재 브런치를 가리키는 포인터이며, 브랜치는 브랜치에 담긴 커밋 중 가장 마지막 커밋을 가리킨다. 지금의 HEAD가 가르키는 커밋은 다음 커밋의 부모가 된다. 단순하게 <u>HEAD는 마지막 커밋의 스탭샷</u>이다.

### Index

Index는 <u>바로 다음에 커밋할 것</u>들이며, "Staging Area" 라고도 한다. "Staging Area"는 <kbd>git commit</kbd> 명령을 실행 했을 때, Git이 처리할 것들이 있는 대기실이라고 할 수있다.  

### 워킹디렉토리

위의 두 트리는 파일과 그 내용을 효율적인 형태로 <kbd>.git</kbd> 디렉토리에 저장한다. 하지만, 사람이 알아보기 어렵다.워킹디렉토리는 실제 파일로 존재한다. 바로 눈에 보이기 때문에 사용자가 편집하기 수월하다. 샌드박스로 생각하자. 커밋하기 전에는 Index(Staging Area)에 올려놓고 얼마든지 변경이 가능하다.



## 워크플로

Git의 주목적은 프로젝트의 스냅샷을 지속적으로 저장하는 것이다. 이 트리 세개를 사용해 더 나은 상태로 관리한다.

![작업흐름](https://git-scm.com/book/en/v2/images/reset-workflow.png)

이 과정을 시각화해보자. 파일이 하나 있는 디렉토리로 이동한다. 이걸 파일의 **v1**이라고 하고 파란색으로 표시한다. `git init` 명령을 실행하면 Git 저장소가 생기고 HEAD는 아직 없는 브랜치를 가리킨다(`master` 는 아직 없다).

![git init](https://git-scm.com/book/en/v2/images/reset-ex1.png)

이 시점에서는 워킹 디렉토리 트리에만 데이터가 있다.
이제 파일을 커밋해보자. `git add` 명령으로 워킹 디렉토리의 내용을 Index로 복사한다.

![git add](https://git-scm.com/book/en/v2/images/reset-ex2.png)

그리고 `git commit` 명령을 실행한다. 그러면 Index의 내용을 스냅샷으로 영구히 저장하고 그 스냅샷을 가리키는 커밋 객체를 만든다. 그리고는 `master` 가 그 커밋 객체를 가리키도록 한다.

![git commit](https://git-scm.com/book/en/v2/images/reset-ex3.png)

`git status` 명령을 실행하면 아무것도 출력하지 않는다. 세 개의 트리의 내용이 같아졌기 때문이다.

브랜치를 바꾸거나 Clone 명령도 내부에서는 비슷한 절차를 밟는다. 브랜치를 Checkout 하면, **HEAD**가 새로운 브랜치를 가리키도록 바뀌고, 새로운 커밋의 스냅샷을 **Index**에 놓는다. 그리고 Index의 내용을 **워킹 디렉토리**로 복사한다.



> # reset의 역할

![예시1](https://git-scm.com/book/en/v2/images/reset-start.png)

위와 같은 히스토리가 있다고 가정한다. `reset` 명령은 이 세 트리를 간단하고 예측 가능한 방법으로 조작한다. 트리를 조작하는 동작은 세 단계 이하로 이루어진다.

#### **1단계: HEAD 이동**

`reset` 명령이 하는 첫 번째 일은 HEAD 브랜치를 이동시킨다. `checkout` 명령처럼 HEAD가 가리키는 브랜치를 바꾸지는 않는다. HEAD는 계속 현재 브랜치를 가리키고 있고, 현재 브랜치가 가리키는 커밋을 바꾼다. HEAD가 `master` 브랜치를 가리키고 있다면(즉 `master` 브랜치를 Checkout 하고 작업하고 있다면) `git reset 9e5e6a4` 명령은 `master` 브랜치가 `9e5e6a4`를 가리키게 한다.

![예시2](https://git-scm.com/book/en/v2/images/reset-soft.png)

`reset` 명령에 커밋을 넘기고 실행하면 언제나 이런 작업을 수행한다. `reset --soft` 옵션을 사용하면 딱 여기까지 진행하고 동작을 멈춘다.

이제 위의 다이어그램을 보고 어떤 일이 일어난 것인지 생각해보자. `reset` 명령은 가장 최근의 `git commit` 명령을 되돌린다. `git commit` 명령을 실행하면 Git은 새로운 커밋을 생성하고 HEAD가 가리키는 브랜치가 새로운 커밋을 가리키도록 업데이트한다. `reset` 명령 뒤에 `HEAD~` (HEAD의 부모 커밋)를 주면 Index나 워킹 디렉토리는 그대로 놔두고 브랜치가 가리키는 커밋만 이전으로 되돌린다. Index를 업데이트한 다음에 `git commit` 명령를 실행하면 `git commit --amend` 명령의 결과와 같아진다([마지막 커밋을 수정하기](https://git-scm.com/book/ko/v2/ch00/_git_amend)를 참조).



#### 2단계 : Index 업데이트 (--mixed)

여기서 `git status` 명령을 실행하면 Index와 `reset` 명령으로 이동시킨 HEAD의 다른 점이 녹색으로 출력된다.

![mixed](https://git-scm.com/book/en/v2/images/reset-mixed.png)

`--mixed` 옵션을 주고 실행하면 `reset` 명령은 여기까지 하고 멈춘다. `reset` 명령을 실행할 때 아무 옵션도 주지 않으면 기본적으로 `--mixed` 옵션으로 동작한다(예제와 같이 `git reset HEAD~` 처럼 명령을 실행하는 경우).

위의 다이어그램을 보고 어떤 일이 일어날지 한 번 더 생각해보자. 가리키는 대상을 가장 최근의 `commit` 으로 되돌리는 것은 같다. 그러고 나서 *Staging Area* 를 비우기까지 한다. <u>`git commit` 명령도 되돌리고 `git add` 명령까지 되돌리는 것</u>이다.



#### 3 단계: 워킹 디렉토리 업데이트 (--hard)

`reset` 명령은 세 번째로 워킹 디렉토리까지 업데이트한다. `--hard` 옵션을 사용하면 `reset` 명령은 이 단계까지 수행한다.

![hard](https://git-scm.com/book/en/v2/images/reset-hard.png)

이 과정은 어떻게 동작하는지 가늠해보자. `reset` 명령을 통해 `git add` 와 `git commit` 명령으로 생성한 마지막 커밋을 되돌린다. **그리고** 워킹 디렉토리의 내용까지도 되돌린다.

이 `--hard` 옵션은 매우 매우 중요하다. `reset` 명령을 위험하게 만드는 유일한 옵션이다. Git에는 데이터를 실제로 삭제하는 방법이 별로 없다. <u>이 삭제하는 방법은 그 중 하나</u>다. `reset` 명령을 어떻게 사용하더라도 간단히 결과를 되돌릴 수 있다. 하지만 `--hard` 옵션은 되돌리는 것이 불가능하다. 이 옵션을 사용하면 워킹 디렉토리의 파일까지 강제로 덮어쓴다. 이 예제는 파일의 **v3**버전을 아직 Git이 커밋으로 보관하고 있기 때문에 `reflog` 를 이용해서 다시 복원할 수 있다. 만약 커밋한 적 없다면 Git이 덮어쓴 데이터는 복원할 수 없다.



#### 요약

`reset` 명령은 정해진 순서대로 세 개의 트리를 덮어써 나가다가 옵션에 따라 지정한 곳에서 멈춘다.

1. HEAD가 가리키는 브랜치를 옮긴다. *(--soft 옵션이 붙으면 여기까지)*
2. Index를 HEAD가 가리키는 상태로 만든다. *(--hard 옵션이 붙지 않았으면 여기까지)*
3. 워킹 디렉토리를 Index의 상태로 만든다.







> #  Git 되돌리기 (Reset, Revert)

Git에서 이력을 되돌리는 방법은 여러가지가 있지만, 그 중 대표적인 것이 <kbd>reset</kbd>과 <kbd>revert</kbd>이다.

단어의 의미로만 보면 둘 차이를 알기 쉽지 않은데, 풀어서 설명해보면 <kbd>Reset</kbd>은 타임머신을 타고 시간을 되돌렸다고 생각하면 되고, <kbd>revert</kbd>는 특정 사건만 없었던 일로 만드는 것이다.



## 1. reset

​	앞에서 설명한대로 <kbd>reset</kbd>은 시계를 다시 맞추는 것이다. 돌아가려는 커밋으로 Repository는 재설정되고, 해당 커밋 이후에 이력은 사라진다.

![예시1](https://miro.medium.com/max/407/1*XKvSxRueC2HYlGj1O72woA.png)

위 그림을 보면 영화 중간에 스포일러를 보고 실망한 이력을 볼 수 있다. 그래서 스포일러를 보기전으로 되돌릴려고 한다. 커밋 <kbd>a3bbb3c</kbd>의 이후의 기억을 지우고 싶다고 가정하면 <kbd>Reset</kbd>은 다음과 같이 사용할 수 있다.

```cmd
git reset <옵션> <커밋ID>
```

옵션은 <kbd>a3bbb3c</kbd>이후의 변화에 대해서 어떻게 처리할 것인지 대해 결정한다.

이러한 옵션은 여러가지가 있으나 자주 사용하는 <kbd>hard</kbd>, <kbd>soft</kbd>, <kbd>mixed</kbd> 세 가지를 설명하겠다.

#### (1) hard

돌아가려는 이력 이후의 모든 내용을 지워 버린다. 이 옵션을 적용하면 <kbd>a3bbb3c</kbd> 이후의 모든 이력이 지워지고 초기화 된다.

```cmd
git reset --hard  a3bbb3c
```



![예시2](https://miro.medium.com/max/544/1*9eW06xF8GrySgK-mQyfnrg.png)



#### (2)soft

돌아가려 했던 커밋으로 되돌아 갔지만, 이후의 내용이 지워지지 않고, 해당 내용의 인덱스(또는 스테이지)가 그대로 남게할 수 있다. 

```
git reset --sorf a2bbb3c
```

![예시3](https://miro.medium.com/max/541/1*C5-ylGIrWwF3hez0_Ou_Kw.png)

![예시3](https://miro.medium.com/max/534/1*cwAFm6AcAkRwt-d1dYRdfg.png)



#### (3)mixed (옵션을 적지 않으면 mixed로 동작합니다.)

역시 돌아가려는 커밋으로 되돌아간다. 그리고 이후에 변경된 내용도 남아있지만, 인덱스는 초기화된다. 따라서 커밋을 하려면 다시 변경된 내용을 추가해야되는 생태이다.

```cmd
git reset --mixed a2bbb3c
```

![예시4](https://miro.medium.com/max/541/1*C5-ylGIrWwF3hez0_Ou_Kw.png)

![예시4](https://miro.medium.com/max/536/1*Pd0gWRSlba0rj3m9HjwVXg.png)



커밋의 해쉬를 통해서도 가능하지만 아래와 같은 현재부터 몇 개의 이전 커밋으로 되돌릴 것인가를 지정할 수도 있다.

```cmd
git reset HAED~숫자
```



## 2.revert

commit을 하고 push한 이후 작업이 잘못되었음을 깨닫고 원래 상태로 돌아가고 싶은 경우, local에서는 <kbd>reset</kbd>을 사용하면 해결되지만 이미 "Remote Repository"에 반영된 경우에 충돌을 피하기 위해 사용한다.

```cmd
git revert <되돌릴 커밋> 
```



![예시5](https://miro.medium.com/max/545/1*o38kXqhNFWHZl9mgUqc5FA.png)

이전 이력은 그대로 있고 스포일러에 관한 커밋만 되돌렸다. 마치 스포일러의 당한것에 대해서는 기억을 하지만, 그 내용을 알지 못하는 것처럼 말이다.

```cmd
git revert 2664ce8 
```

만약 되돌릴 커밋이 여러개일 경우 아래와 같이 범위를 주어서 선택할 수도 있다.

```cmd
git revert 2664ce8..15413dc
```



## 요약

### git reset

- commit과 내용을 아예 Rollback하는 경우에 사용.
  - `--soft` :  선택한 커밋으로 돌아가고, 현재 파일 상태는 "Staging Area"에 유지한다.
  - `--mixed` : 선택한 커밋으로 돌아가고, 현재 파일 상태는 "uncommited changes"로 남겨둔다.
  - `--hard` : 선택한 커밋으로 돌아가고, 현재 파일 상태와 상관없이, 그 이후의 버전 + 파일들은 다 삭제
- Local에서만 적용된 commit을 Rollback하기 좋은 명령어

### git revert

- commit을 유지하면서 내용을 Rollback하는 경우에 사용.
- commit이 유지되기 때문에 Remote Repository 충돌이 적게 난다.





[참고1] (https://git-scm.com/book/ko/v2/Git-도구-Reset-명확히-알고-가기)

[참고2]<http://www.devpools.kr/2017/01/31/개발바보들-1화-git-back-to-the-future/>

[참고3]<https://nesoy.github.io/articles/2018-07/Git-Revert>