> # 리모트 저장소

리모트 저장소는 인터넷이나 네트워크 어딘가에 있는 저장소를 말한다. 저장소는 여러개가 있을 수 있는데, 어떤 저장소는 읽고 쓰기를 모두 할 수 있는 곳이 있고 다른 저장소는 읽기 권한만 있을 수 있다. 간단히 말해 다른 사람들과 함께 일한다는 것은 리모트 저장소를 관리하면서 데이터를 `push`하고 `pull`하는 것이다.

리모트 저장소를 관리하는다는 것은 저장소를 추가,  삭제하는 것 뿐만 아니라 브랜치를 관리하고 추적할지 말지 등을 관리한 것을 말한다.



# 리모트 저장소 명령

#### 리모트 저장소 확인

<kbd>git remote</kbd>명령으로 현재 프로젝에 등록된 저장소를 확인할 수 있다. 저장소를 `clone`하면 `origin`이라는 리모트 저장소가 자동으로 등록된다.

```cmd
git remote -v
	bakkdoor  git://github.com/bakkdoor/grit.git
	cho45     git://github.com/cho45/grit.git
	defunkt   git://github.com/defunkt/grit.git
	koke      git://github.com/koke/grit.git
	origin    git@github.com:mojombo/grit.git
```

`-v`옵션은 주면 단축이름과 URL을 함께 볼 수 있다. 저장소가 여러개 있다면 전부 보여준다.

``` cmd
git remote show [리모트저장소이름]
```

리모트 저장소의 구체적인 정보를 확인할 수 있다. 



#### 리모트 저장소 추가

```cmd
git remote add <단축이름> <url>
```

저장소를 추가하면 URL대신에 단축이름을 사용할 수 있다.



#### 리모트 저장소에서 데이터 가져오기

```cmd
git fetch <저장소이름>
```

이 명령은 로컬에는 없지만, 리모트 저장소에 있는 데이터를 모두 가져오지만 자동으로 `merge`하지는 않는다. 리모트 저장소의 모든 브랜치를 로컬에서 접근할 수 있어서 언제든지 `merge`하거나 내용을 살펴볼 수 있다.

```cmd
git pull <저장소이름>
```

이 명령은 저장소 브랜치에서 데이터를 가져올 뿐만 아니라 자동으로 로컬 브랜치와 `merge`시켜준다. 먼저 <kbd>git clone</kbd>명령은 자동으로 로컬의 master 브랜치가 리모트 저장소의 master 브랜치를 추적하도록 한다. 그리고 <kbd>git pull</kbd>명령을 통해 Clone한 서버에서 데이터를 가져오고 현재 작업하는 코드와 `merge`시킨다.



#### 리모트 저장소에 데이터 공유하기

```cmd
git push <저장소이름> <브랜치이름>
```

이 명령은 clone한 리모트 저장소에 쓰기 권한이 있고, clone하고 난 이후 아무도 리모트 저장소에 push하지 않았을 때만 사용할 수 있다. 다시 말해서 Clone한 사람이 여러 명 있을 때, 다른 사람이 Push한 후에 Push하려고 하면 Push할 수 없다. 먼저 다른 사람이 작업한 것을 가져와서 머지한 후에 Push할 수 있다.



#### 리모트 저장소 이름 변경

```cmd
git remote rename <저장소이름> <변경할이름>
```



#### 리모트 저장소 삭제

```cmd
git remote rm <저장소이름>
```

