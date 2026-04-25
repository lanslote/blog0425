---
title: "GitHub Pages와 Jekyll로 블로그를 만드는 전체 흐름"
---

GitHub Pages로 블로그를 만든다는 말은 처음 들으면 조금 추상적으로 느껴진다. 서버를 빌리는 것도 아니고, 관리자 화면에서 글을 쓰는 것도 아니고, Git 저장소에 파일을 올렸는데 웹사이트가 생긴다. 익숙해지면 단순하지만, 처음에는 각 도구가 어떤 역할을 하는지 먼저 잡아두는 편이 좋다.

이번 시리즈에서는 GitHub에 새 저장소를 만들고, 로컬 프로젝트와 연결한 뒤, Jekyll 기반 블로그를 구성하고, Just the Docs 테마를 적용하고, 마지막으로 GitHub Actions를 통해 자동 배포하는 흐름까지 진행한다.

## GitHub Pages란 무엇인가

GitHub Pages는 GitHub 저장소의 파일을 정적 웹사이트로 배포해주는 기능이다. HTML, CSS, JavaScript처럼 서버 실행 없이 브라우저에서 바로 열 수 있는 파일을 공개 웹사이트로 만들어준다.

블로그 관점에서 보면 GitHub Pages는 글과 설정 파일이 들어 있는 저장소를 읽고, 빌드된 결과물을 웹에 올려주는 호스팅 역할을 한다. 별도 서버를 관리하지 않아도 되고, Git으로 변경 이력이 남기 때문에 개발자에게 특히 편하다.

## Jekyll이 하는 일

Jekyll은 Markdown 파일과 설정 파일을 읽어서 정적 HTML 사이트로 바꿔주는 정적 사이트 생성기다.

예를 들어 `_posts/2026-04-25-first-post.md` 같은 Markdown 파일을 작성하면, Jekyll은 이 파일을 블로그 글 페이지로 변환한다. `_config.yml`에는 사이트 제목, 테마, 플러그인, URL 같은 설정을 적는다. 레이아웃 파일은 글이 어떤 HTML 구조로 감싸질지 결정한다.

정리하면 GitHub Pages는 배포를 맡고, Jekyll은 사이트 생성을 맡는다.

## 테마를 쓰는 이유

처음부터 HTML과 CSS를 모두 직접 만들 수도 있다. 하지만 블로그를 빠르게 시작하려면 검증된 테마를 사용하는 편이 훨씬 낫다. 테마는 기본 레이아웃, 글 목록, 네비게이션, 검색, 반응형 화면 같은 요소를 이미 갖추고 있다.

이번 시리즈에서는 [Just the Docs](https://github.com/just-the-docs/just-the-docs)를 사용한다. 원래 문서 사이트용 테마지만, 사이드바 탐색과 검색 기능이 좋아서 학습 기록이나 기술 블로그에도 잘 맞는다.

## 최종적으로 만들 구조

우리가 만들 블로그는 대략 이런 구조를 가진다.

```text
.
├─ .github/
│  └─ workflows/
│     └─ pages.yml
├─ _layouts/
│  └─ post.html
├─ _posts/
│  └─ 2026-04-25-example.md
├─ _config.yml
├─ Gemfile
├─ index.md
├─ posts.md
├─ about.md
└─ README.md
```

각 파일의 역할은 다음과 같다.

`Gemfile`은 Ruby gem 의존성을 관리한다. Jekyll과 Just the Docs 테마를 여기에 적는다.

`_config.yml`은 사이트 설정 파일이다. 사이트 이름, 설명, 테마, 플러그인, 기본 레이아웃 같은 값을 설정한다.

`_posts` 폴더에는 블로그 글을 넣는다. 파일 이름은 `YYYY-MM-DD-title.md` 형식을 따른다.

`.github/workflows/pages.yml`은 GitHub Actions 배포 자동화 파일이다. main 브랜치에 push하면 사이트를 빌드하고 GitHub Pages에 배포한다.

## 전체 흐름

이 시리즈는 다음 순서로 진행한다.

1. GitHub Pages, Jekyll, 테마의 역할 이해하기
2. GitHub 저장소 만들고 로컬 프로젝트와 연결하기
3. Jekyll 기본 파일 구성하기
4. Just the Docs 테마 적용하기
5. GitHub Actions로 자동 배포하기

첫 번째 단계에서 중요한 것은 모든 명령어를 외우는 것이 아니다. 어떤 파일이 어떤 역할을 하는지 감을 잡는 것이다. 이 그림이 잡혀 있으면, 뒤에서 에러가 나도 어디를 확인해야 하는지 훨씬 빨리 찾을 수 있다.

다음 글에서는 GitHub에서 새 저장소를 만들고, 로컬 폴더와 연결해 첫 커밋을 올리는 과정부터 시작한다.
