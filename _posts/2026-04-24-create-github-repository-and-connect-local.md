---
title: "GitHub 저장소 만들고 로컬 프로젝트와 연결하기"
---

이번 글에서는 GitHub Pages 블로그가 올라갈 저장소를 만들고, 내 컴퓨터의 로컬 폴더와 연결한다. 이 단계가 끝나면 로컬에서 만든 파일을 GitHub 저장소로 push할 수 있는 상태가 된다.

블로그를 만들 때 가장 먼저 해야 할 일은 코드를 둘 장소를 정하는 것이다. GitHub Pages는 GitHub 저장소를 기반으로 동작하기 때문에, 저장소가 곧 블로그의 원본 저장 공간이 된다.

## 새 저장소 만들기

GitHub에 로그인한 뒤 오른쪽 위의 `+` 버튼을 누르고 `New repository`를 선택한다.

저장소 이름은 원하는 대로 정하면 된다. 예를 들어 `myblog`, `blog`, `blog0425`처럼 만들 수 있다. 저장소가 사용자 페이지가 아니라 프로젝트 페이지라면 최종 주소는 보통 다음 형태가 된다.

```text
https://사용자명.github.io/저장소명/
```

예를 들어 사용자명이 `lanslote`이고 저장소명이 `blog0425`라면 주소는 다음과 같다.

```text
https://lanslote.github.io/blog0425/
```

저장소 공개 범위는 public을 권장한다. GitHub Free 계정에서는 public 저장소가 GitHub Pages를 쓰기에 가장 단순하다. private 저장소에서도 Pages를 사용할 수 있는 경우가 있지만, 계정 플랜과 조직 설정에 따라 달라질 수 있다.

처음 연습할 때는 README, `.gitignore`, license를 자동 생성하지 않고 빈 저장소로 시작해도 좋다. 이렇게 하면 로컬에서 만든 첫 커밋을 그대로 올리기 쉽다.

## 로컬 폴더 만들기

이제 내 컴퓨터에서 블로그 작업 폴더를 만든다.

```sh
mkdir myblog
cd myblog
```

이미 폴더를 만들어두었다면 그 폴더로 이동하면 된다.

```sh
cd path/to/myblog
```

## Git 저장소 초기화

로컬 폴더를 Git 저장소로 만든다.

```sh
git init
```

현재 브랜치 이름이 `master`로 만들어지는 환경도 있다. GitHub Pages와 GitHub Actions 워크플로에서는 보통 `main` 브랜치를 기준으로 설명하는 경우가 많으므로, 브랜치 이름을 `main`으로 맞춰둔다.

```sh
git branch -M main
```

현재 브랜치를 확인하려면 다음 명령어를 사용한다.

```sh
git branch --show-current
```

출력이 `main`이면 준비가 된 것이다.

## 원격 저장소 연결

GitHub에서 만든 저장소 주소를 로컬 Git 저장소에 연결한다.

```sh
git remote add origin https://github.com/사용자명/저장소명.git
```

예시는 다음과 같다.

```sh
git remote add origin https://github.com/lanslote/blog0425.git
```

연결이 잘 되었는지 확인한다.

```sh
git remote -v
```

아래처럼 fetch와 push 주소가 보이면 된다.

```text
origin  https://github.com/lanslote/blog0425.git (fetch)
origin  https://github.com/lanslote/blog0425.git (push)
```

## 첫 파일 만들고 커밋하기

아직 아무 파일도 없으면 Git은 커밋할 내용이 없다고 말한다. 간단한 README 파일을 먼저 만들어 첫 커밋을 만든다.

```sh
echo "# My Blog" > README.md
git add README.md
git commit -m "Initial commit"
```

커밋은 현재 작업 상태를 하나의 스냅샷으로 기록하는 일이다. 앞으로 블로그 설정을 바꾸거나 글을 추가할 때마다 의미 있는 단위로 커밋을 남기면 된다.

## GitHub로 push하기

이제 로컬 커밋을 GitHub 저장소로 올린다.

```sh
git push -u origin main
```

처음 push할 때는 GitHub 로그인이나 토큰 인증을 요구할 수 있다. 인증이 끝나면 GitHub 저장소 페이지에서 방금 올린 파일을 확인할 수 있다.

`-u` 옵션은 현재 로컬 브랜치와 원격 브랜치를 연결해준다. 이후에는 보통 다음처럼 짧게 push할 수 있다.

```sh
git push
```

## 여기까지 확인할 것

이번 단계가 끝났다면 다음 항목을 확인한다.

1. GitHub에 새 저장소가 있다.
2. 로컬 폴더에서 `git status`를 실행할 수 있다.
3. `git remote -v`에 GitHub 저장소 주소가 나온다.
4. `main` 브랜치가 GitHub에 push되어 있다.

여기까지 되면 블로그를 올릴 장소가 준비된 것이다. 다음 글에서는 이 저장소 안에 Jekyll 블로그 기본 구조를 만든다.
