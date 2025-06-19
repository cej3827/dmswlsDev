# GitHub

## 초기화 (init)

원격 저장소를 만들고 로컬 저장소를 만들 폴더를 만든다. 그 경로에 깃 배시를 열어 git init 명령어로 로컬 저장소를 만든다.

```
git init
```

여기까지는 현재 로컬 저장소는 원격 저장소의 존재를 모른다. 둘이 상호작용하기 위해서는 원격 저장소를 로컬 저장소에 추가해야 한다. 원격 저장소를 origin이라는 이름으로 로컬 저장소에 추가하겠다.

```
git remote add origin <원격 저장소 경로>
```

이렇게 하면 추후 origin이라는 이름으로 원격 저장소와 상호작용 할 수 있다.

추가된 원격 저장소 목록은 git remote 명령으로 확인할 수 있다.

```
git remote -v // 원격 저장소의 이름과 경로를 함께 확인
```

```
git remote rename <기존 이름> <바꿀 이름> // 원격 저장소 이름을 바꾸는 명령어
```

```
git remote remove <원격 저장소 이름> // 원격 저장소 삭제
```

## 클론(clone) : 원격 저장소를 복제하기

클론하면 브랜치가 세 개가 보인다.

* main - 기본 브랜치
* origin/main - 원격 저장소 origin의 기본 브랜치
* origin/HEAD - 원격 저장소 origin의 HEAD

```
// 원격 저장소에 들어가서 code 버튼을 클릭한 다음 ssh를 복사한다. 
// 클론 받을 위치에서 깃 배시를 열고
git clone <복사한 경로>
```

### SSH : 안전하게 정보를 주고받을 수 있는 통신 방법

key를 두 개 생성한 후 (public, private) 통신하려는 대상(gitHub)에게 공개 키를 건네준다.

## 푸시 (push) : 원격 저장소에 밀어넣기

push란 원격 저장소에 로컬 저장소의 변경 사항을 밀어넣는 것이다.

원격 저장소에서 클론해온 뒤 로컬 저장소의 파일을 수정하고 커밋한다. 이 상태에서 원격 저장소를 로컬 저장소와 같게 만들 때 push 해야 한다.

처음 원격 저장소로 푸시하는 경우는 그대로 붙여넣는다.&#x20;

```
git remote add origin <경로> // 원격 저장소를 origin이라는 이름으로 추가한다.
git branch -m main // 현재 브랜치 이름을 mian으로 바꾼다.
git push -u origin main // 원격 저장소 origin으로 로컬 저장소 main 브랜치의 변경사항을 푸시
```

-u 옵션은 처음 푸시할 때 딱 한 번만 사용한다. 추후에는 git push(또는 git pull) 명령만으로도 origin의 main 브랜치로 push(혹은 pull) 할 수 있다.

## 패치(fetch) : 원격 저장소를 일단 가져만 오기

협업하여 개발하고 있는 상황을 가정해 보자. 다른 개발자가 언제든 변경 사항을 추가할 수 있기 때문에 파일은 시시각각 변할 수 있다. 다른 개발자가 푸시한 내용을 자신의 로컬로 가져오고 싶을 때 사용하는 것이 fetch이다.&#x20;

중요한 건 fetch 해도 원격 저장소의 내용이 로컬 저장소에 병합되는 건 아니다. 원격 저장소에 커밋이 하나 추가된 경우 git fetch하고 git status를 입력해 보면 'Your branch is behind 'origin/main' by 1 commit'이라는 메시지를 볼 수 있다. 현재 브랜치는 원격 저장소 origin/main 브랜치에 비해 커밋 하나가 뒤쳐진다는 의미이다. fetch는 원격 저장소의 변경 사항을 가져오는 것일 뿐 main 브랜치는 변함 없기 때문이다.&#x20;

main 브랜치를 origin/main 브랜치와 병합하기 위해서 main 브랜치에서 git merge origin/main 한다.

## 풀 (pull) : 원격 저장소를 가져와서 합치기

fetch가 일단 가져만 오는 것이라면 pull은 가져와서 합치는 방법이다. 즉 fetch와 병합을 동시에 하는 방법이다.

```
git pull <원격별칭> <브랜치> // 원격 저장소에서 브랜치 당겨오기
git pull --rebase <원격별칭> <브랜치> // 원격 저장소의 브랜치로 로컬 브랜치 덮어쓰기
```

***

## Pull Request : GitHub로 협업하기

한 원격 저장소에서 여러 개발자가 코드를 기여할 수 있다. 그렇다면 자신이 소유하지 않은 원격 저장소에도 푸시할 수 있을까? 일반적으로 그렇지 않다.

원격 저장소 소유자가 당신을 collaborator로 추가한 경우에는 자신이 소유하지 않은 계정의 원격 저장소에 푸시할 수 있다. 하지만 이러한 푸시 권한이 없는 원격 저장소는 pull request를 통해 가능하다.

{% stepper %}
{% step %}
### 기여하려는 저장소를 본인 계정으로 fork하기
{% endstep %}

{% step %}
### 저장소를 클론하기

```
git clone <원격 저장소 경로>
```
{% endstep %}

{% step %}
### 클론 받은 원격 저장소로 이동하기
{% endstep %}

{% step %}
### 브랜치 생성 후 생성된 브랜치에서 작업하기

```
git branch <브랜치 이름>
git checkout <브랜치 이름>
```
{% endstep %}

{% step %}
### 코드 수정 후 변경 사항 확인

```
git diff
```
{% endstep %}

{% step %}
### git add -> git commit
{% endstep %}

{% step %}
### 작업한 브랜치 푸시하기

```
git push origin <브랜치 이름>
```
{% endstep %}

{% step %}
### pull request 보내기

원격 저장소로 돌아가보면 Compare & pull request 버튼이 생성된다. 이를 클릭한다.
{% endstep %}
{% endstepper %}

***

## git <명령어> --help : 메뉴얼 페이지 보기

***

### 참고 문헌

* 강민철. 『모두의 깃 & 깃허브』. 길벗, 2022.
