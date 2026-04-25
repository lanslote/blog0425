---
title: "GitHub Actions로 Jekyll 블로그 자동 배포하기"
---

이번 글에서는 Jekyll 블로그를 GitHub Pages에 자동 배포한다. 목표는 단순하다. 로컬에서 글을 작성하고 `main` 브랜치에 push하면 GitHub Actions가 Jekyll 사이트를 빌드하고 GitHub Pages에 배포하도록 만든다.

GitHub Pages는 브랜치에 있는 파일을 그대로 배포하는 방식도 지원한다. 하지만 Jekyll 테마와 플러그인을 직접 관리하고 싶다면 GitHub Actions 배포 방식이 더 유연하다.

## 배포 방식 이해하기

GitHub Pages에는 크게 두 가지 배포 흐름이 있다.

첫 번째는 브랜치 배포다. 예를 들어 `main` 브랜치의 `/docs` 폴더나 `gh-pages` 브랜치를 Pages 소스로 지정하는 방식이다. 단순한 정적 파일에는 편하다.

두 번째는 GitHub Actions 배포다. 워크플로가 사이트를 빌드하고, 빌드 결과물을 Pages artifact로 업로드한 뒤, GitHub Pages에 배포한다. 이번 글에서는 이 방식을 사용한다.

## pages.yml 만들기

저장소 루트에 `.github/workflows/pages.yml` 파일을 만든다.

{% raw %}
```yml
name: Deploy Jekyll site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: "3.3"
          bundler-cache: true
          cache-version: 0

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build with Jekyll
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v4

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
{% endraw %}

이 워크플로는 `main` 브랜치에 push될 때 실행된다. `workflow_dispatch`가 있기 때문에 GitHub Actions 화면에서 수동으로 실행할 수도 있다.

## 워크플로가 하는 일

`actions/checkout`은 저장소 코드를 가져온다.

`ruby/setup-ruby`는 Ruby를 설치하고 Bundler 캐시를 준비한다. `bundler-cache: true`를 사용하면 `bundle install`을 자동으로 처리하고 캐시도 잡아준다.

`actions/configure-pages`는 GitHub Pages 배포에 필요한 정보를 가져온다. 여기서 repository name에 따른 base path도 계산된다.

`bundle exec jekyll build`는 Jekyll 사이트를 빌드한다. 빌드 결과는 기본적으로 `_site` 폴더에 만들어진다.

`actions/upload-pages-artifact`는 `_site` 결과물을 Pages 배포용 artifact로 업로드한다.

`actions/deploy-pages`는 업로드된 artifact를 실제 GitHub Pages에 배포한다.

## GitHub Pages 설정하기

워크플로 파일만 push한다고 끝나는 것은 아니다. GitHub 저장소에서 Pages 소스를 GitHub Actions로 설정해야 한다.

GitHub 저장소 화면에서 다음 경로로 이동한다.

```text
Settings > Pages > Build and deployment > Source
```

Source를 `GitHub Actions`로 선택한다.

이 설정은 중요하다. Pages가 아직 활성화되지 않았거나 GitHub Actions 배포 소스로 설정되지 않았다면 워크플로에서 다음과 같은 에러가 날 수 있다.

```text
Error: Get Pages site failed.
Please verify that the repository has Pages enabled and configured to build using GitHub Actions.
Error: HttpError: Not Found
```

이 에러는 Jekyll 빌드 실패가 아니라 GitHub Pages 설정 문제다. 저장소의 Pages 설정에서 Source를 GitHub Actions로 바꾸면 해결된다.

## 커밋하고 push하기

파일을 추가했다면 커밋하고 push한다.

```sh
git add .
git commit -m "Set up GitHub Pages deployment"
git push
```

push 후 GitHub 저장소의 `Actions` 탭으로 이동한다. `Deploy Jekyll site to Pages` 워크플로가 실행되는지 확인한다.

성공하면 deploy job의 출력이나 저장소의 Pages 설정 화면에서 배포 URL을 볼 수 있다.

## 배포 URL 확인하기

프로젝트 페이지의 기본 주소는 보통 다음 형식이다.

```text
https://사용자명.github.io/저장소명/
```

예를 들어 사용자명이 `lanslote`이고 저장소명이 `blog0425`라면 다음과 같다.

```text
https://lanslote.github.io/blog0425/
```

처음 배포는 몇 분 정도 걸릴 수 있다. Actions가 성공했는데도 페이지가 바로 보이지 않으면 잠시 기다린 뒤 새로고침한다.

## 자주 확인할 것

워크플로가 실패하면 먼저 Actions 로그에서 어느 단계가 실패했는지 본다.

`Setup Ruby`에서 실패하면 Ruby 버전이나 Bundler 문제일 수 있다.

`Build with Jekyll`에서 실패하면 `_config.yml`, Gemfile, Markdown front matter를 확인한다.

`Setup Pages`에서 `Get Pages site failed`가 나오면 GitHub Pages 설정에서 Source가 GitHub Actions인지 확인한다.

`Deploy to GitHub Pages`에서 권한 오류가 나오면 workflow의 permissions에 `pages: write`와 `id-token: write`가 있는지 확인한다.

## 마무리

이제 GitHub 저장소에 push하면 블로그가 자동으로 배포된다. 앞으로 글을 추가하는 흐름은 단순하다.

1. `_posts`에 Markdown 글을 작성한다.
2. 로컬에서 확인한다.
3. 커밋한다.
4. GitHub로 push한다.
5. GitHub Actions 배포 결과를 확인한다.

여기까지 오면 블로그 운영의 기본 뼈대는 완성이다. 이후에는 로고, 색상, 커스텀 도메인, 검색 엔진 등록, 댓글 기능 같은 요소를 필요에 따라 하나씩 더하면 된다.
