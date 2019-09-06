> #  Git의 내부구조

### Object

Git에서 **object**는 Git을 구성하는 데이터 파일이다. Git은 "Working Directory"의 파일정보를 object형식으로 변환하여 **Object Database**(Working Directory/.git/object)에 저장한다.

각각의 object파일은 Zlib을 이용하여 압축되며, 파일의 내용과 헤더를 40자의 SHA-1 해시값으로 저장한다.

Object의 종류에는 **<u>blob, tree, commit, tag</u>**가 있다.

![구조 다이어그램](http://wiki.tuestudy.org/aosabook/materials/git/git-object-hierarchy.png)

![구조](https://git-scm.com/book/en/v2/images/commit-and-tree.png)



## blob

Working Dirctory의 파일에 대응하여 내용이 저장되는 Object이다. 중요한 것은 Blob에는 **파일의 내용만 저장**이 된다. 이는 만약 이름이 다른 2개의 파일이 프로젝트 내에 있다해도, 그 내용이 같으면 Git은 blob을 한개만 저장한다는 것을 의미한다. 또한 `clone`이나 `fetch` 등을 할 때에도 파일이 한개만 전송 될 것이다.



## tree

Working Dirctory의 디렉토리에 대응하여 Git에 저장되는 Object이다. Tree 개체는 **해당 디렉토리 내부의 파일과 디렉토리 정보를 가지고 있다**. Git은 모든 것을 `Tree`와 `Blob`으로 저장한다. Tree 개체 하나는 항목을 여러 개 가질 수 있다. 그 항목에는 `blob`개체나 하위 `tree`를 가리키는 SHA-1 포인터, 파일 모드, 개체 타입, 파일 이름이 들어있다.

![예시1](https://git-scm.com/book/en/v2/images/data-model-1.png)



## commit

commit history를 저장하는 object이다. 파일의 내용을 살펴보면 "author"와 "committer", "commit message"를 포함하고 있다. 또한 tree 개체의 정보도 가지고 있는데, 이 commit의 스냅샷 최상단의 tree를 가리키는 포인터이다. 만약 commit을 한 번 더하게 되면 `parent`항목이 추가되고 이전 커밋(부모 커밋)을 가리키게 된다. 이 포인터를 통해 Git은 commit의 부모를 참조할 수 있다. 즉 필요하면 언제든지 스냅샷을 다시 만들 수 있다는 뜻이다.



## tag

Git에 특정 commit에 tag를 달면 tag object가 생성된다. 태그는 `Lightweight`와 `Annotated`태그로 나뉘는데, `Lightweight`태그는 단순히 특정 commit에 대한 포인터로 작동하는 반면, `Annotated`태그는 태그의 작성자, 이메일, 날짜, 메시지를 저장할 수 있다. 또한 보안을 위해 GPG로 서명할 수도 있다.



## references

Git의 object는 한번 생성되면 그 값이 변할 수 없다. 대응되는 파일이 수정되면 다시 새로운 object가 생성이 될 뿐이며 40자리 SHA-1 코드는 접근하기가 어렵다. 이러한 이유로  `reference`가 존재한다. reference는 특정 commit을 가리키는 포인터라는 점에서 tag object와 비슷하지만, object와 달리 값이 계속해서 바뀔 수 있다. 우리가 Git에서 사용하는 branch와 remote, HEAD 같은 요소들을 reference라고 한다.





[참고1]<http://wiki.tuestudy.org/aosabook/materials/git#object-database>

[참고2]<https://cyberx.tistory.com/81>

[참고3]<https://wayhome25.github.io/git/2017/04/02/git-01-core/>

[참고4]<https://m.blog.naver.com/imf4/220796681049>

[참고 5]<https://storycompiler.tistory.com/7>