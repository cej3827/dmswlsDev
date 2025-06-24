# 배포 자동화 (GitHub Actions, AWS EC2, Node.js)

## 도입 배경

CI/CD, 배포 자동화... 말로만 들어봤지 왜 필요한지 제대로 느껴본 적은 없었다. 그러나 이번 프로젝트를 통해 EC2에 Node.js 서버를 배포하게 되었고 반복되는 수동 작업 속에서 배포 자동화의 필요성을 온몸으로 체감하게 됐다.

기존 방식은 main branch에 배포할 코드를 merge하면 EC2 인스턴스 SSH로 접속하여 main branch를 pull 하는 방식으로 진행했고, 수정 사항이 있을 때마다 서버를 종료하고 git pull 하고 서버를 다시 시작하는 과정이 매우 번거롭게 느껴졌다.

## pm2로 Node.js 서버 백그라운드 실행하기

### pm2란

Node.js는 기본적으로 Foreground 방식이라 EC2 인스턴스에 접속하여 프로젝트를 실행시키면 터미널을 종료했을 때 프로젝트도 함께 종료된다. 그렇다고 해서 계속 컴퓨터를 켜두고 있을 수도 없는 일.. 백그라운드에서 실행시키기 위해 pm2를 사용해야 한다.

### pm2 설치 및 실행

```sh
# pm2 설치
npm install -g pm2

# 서버 시작
pm2 start server.js

# 서버 상태 확인
pm2 status

# 서버 로그 확인
pm2 logs
```

## GitHub Actions

GitHub Actions는 GtiHub에서 제공하는 CI/CD 플랫폼으로, 레포지토리에서 발생하는 다양한 이벤트(코드 푸시, PR 생성)에 따라 빌드, 테스트, 배포 등 반복적인 작업을 자동화할 수 있는 서비스이다.

### yml 파일 작성

레포지토리에 .github/workflows 폴더를 생성하고 yml 파일을 작성한다. 내가 작성한 코드는 다음과 같다.

```yaml
name: Deploy to EC2

on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    
  steps:
    - name: Connect & Deploy to EC2
      uses: appleboy/ssh-action@v1.2.2
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.KEY }}
        port: ${{ secrets.PORT }}
        script: |
          cd /home/ec2-user/--
          git pull origin main
          npm install
          pm2 restart server
```

* 이 워크플로우는 **main 브랜치에 push가 발생할 때** 자동으로 실행된다.
* **deploy라는 이름의 job**을 정의한다. 이 job은 GitHub Actions의 ubuntu-latest 가상 머신에서 실행된다.
* steps는 job 안에서 실행할 작업을 정의한다.
* [appleboy/ssh-action](https://github.com/appleboy/ssh-action) 이라는 GitHub 액션을 사용해서 EC2에 SSH로 접속해 명령어를 실행한다
* with: SSH 접속에 필요한 정보와 실행할 스크립트를 지정한다.&#x20;
* script:
  * EC2 서버의 프로젝트 폴더로 이동한다.
  * main 브랜치의 최신 코드를 받아온다.
  * Node.js 패키지를 설치한다.
  * PM2로 관리하는 server라는 이름의 프로세스를 재시작한다.

{% hint style="info" %}
$\{{ secrets.변수 \}} 는 github 레포에서 settings - security 탭에서 secrets and varaibles - actions - new repository secret 버튼을 누르면 등록할 수 있다.

![](<../../.gitbook/assets/스크린샷 2025-06-24 오후 4.36.56 (1).png>)
{% endhint %}

***

## 결과
