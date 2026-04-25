---
title: "Just the Docs 테마를 Jekyll 블로그에 적용하기"
---

이번 글에서는 Jekyll 블로그에 Just the Docs 테마를 적용한다. Just the Docs는 원래 문서 사이트를 만들기 좋은 테마지만, 개발 기록이나 학습 노트를 정리하는 블로그에도 잘 어울린다. 사이드바 탐색, 검색, 반응형 레이아웃이 기본으로 제공되기 때문이다.

테마를 적용한다는 것은 단순히 색을 바꾸는 일이 아니다. Jekyll이 페이지를 어떤 레이아웃으로 감싸고, 어떤 스타일과 스크립트를 사용할지 정하는 일이다.

## Gemfile에 테마 추가하기

먼저 `Gemfile`에 Just the Docs를 추가한다.

```ruby
source "https://rubygems.org"

ruby "~> 3.3"

gem "jekyll", "~> 4.4"
gem "just-the-docs", "~> 0.12.0"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.17"
end
```

`just-the-docs` 버전을 명시해두면 나중에 테마가 갑자기 바뀌어 사이트가 예상과 다르게 보이는 일을 줄일 수 있다.

의존성을 설치한다.

```sh
bundle install
```

## _config.yml에 테마 설정하기

이제 `_config.yml`에서 테마를 지정한다.

```yml
title: My Blog
description: A GitHub Pages blog powered by Just the Docs.
theme: just-the-docs

url: ""
permalink: pretty
timezone: Asia/Seoul

plugins:
  - jekyll-feed

search_enabled: true
heading_anchors: true

nav_enabled: true
color_scheme: light
```

핵심은 `theme: just-the-docs`다. 이 설정으로 Jekyll은 Just the Docs 테마의 레이아웃과 스타일을 사용한다.

`search_enabled: true`는 사이트 검색 기능을 켠다. `heading_anchors: true`는 제목 옆에 바로가기 링크를 사용할 수 있게 해준다.

## 기본 레이아웃 설정하기

페이지마다 `layout`을 반복해서 쓰지 않으려면 defaults를 설정한다.

```yml
defaults:
  - scope:
      path: ""
      type: "pages"
    values:
      layout: default
  - scope:
      path: ""
      type: "posts"
    values:
      layout: post
```

일반 페이지는 Just the Docs의 `default` 레이아웃을 사용하고, 블로그 글은 우리가 만들 `post` 레이아웃을 사용하게 한다.

## 글 레이아웃 만들기

Just the Docs는 문서 페이지에 강하지만, 블로그 글 날짜를 표시하는 전용 레이아웃은 직접 만드는 편이 좋다. `_layouts/post.html` 파일을 만든다.

{% raw %}
```html
---
layout: default
---

<article class="post">
  <h1>{{ page.title }}</h1>
  <p class="text-grey-dk-000 fs-3">{{ page.date | date: "%Y-%m-%d" }}</p>

  {{ content }}
</article>
```
{% endraw %}

이 레이아웃은 Just the Docs의 `default` 레이아웃 위에 블로그 글 제목과 날짜를 얹는다. 글 본문은 `{% raw %}{{ content }}{% endraw %}` 자리에 들어간다.

## Home 페이지 구성하기

`index.md`는 블로그 첫 화면이다. 최신 글 몇 개를 보여주도록 구성한다.

{% raw %}
```md
---
layout: home
title: Home
nav_order: 1
---

# My Blog

개발과 배운 것들을 정리하는 GitHub Pages 블로그입니다.

## Latest Posts

{% assign recent_posts = site.posts | slice: 0, 5 %}
{% for post in recent_posts %}
### [{{ post.title }}]({{ post.url | relative_url }})

{{ post.date | date: "%Y-%m-%d" }}

{{ post.excerpt | strip_html | truncatewords: 35 }}
{% endfor %}
```
{% endraw %}

`nav_order`는 사이드바에 표시되는 순서를 정한다. 숫자가 작을수록 위에 나온다.

## Posts 페이지 구성하기

전체 글 목록 페이지도 만든다.

{% raw %}
```md
---
title: Posts
nav_order: 2
permalink: /posts/
---

# Posts

{% for post in site.posts %}
## [{{ post.title }}]({{ post.url | relative_url }})

{{ post.date | date: "%Y-%m-%d" }}

{{ post.excerpt | strip_html | truncatewords: 45 }}
{% endfor %}
```
{% endraw %}

새 글을 `_posts` 폴더에 추가하면 이 목록에 자동으로 표시된다.

## About 페이지 만들기

블로그 소개 페이지도 하나 두면 좋다.

```md
---
title: About
nav_order: 3
permalink: /about/
---

# About

이 공간은 블로그 소개와 운영 방향을 적어두는 페이지입니다.
```

Just the Docs는 front matter의 `title`, `nav_order`, `permalink`를 이용해 사이드바 탐색을 구성한다.

## 로컬에서 확인하기

테마 적용 후 로컬 서버를 다시 실행한다.

```sh
bundle exec jekyll serve
```

브라우저에서 `http://localhost:4000`에 접속해 다음을 확인한다.

1. Just the Docs 스타일이 적용되어 있는가
2. 사이드바에 Home, Posts, About이 보이는가
3. 검색 입력창이 보이는가
4. 블로그 글에 제목과 날짜가 표시되는가

## 다음 단계

이제 블로그의 모양과 기본 페이지는 갖춰졌다. 마지막으로 이 사이트를 GitHub Pages에 자동 배포해야 한다. 다음 글에서는 GitHub Actions 워크플로를 작성하고, push할 때마다 블로그가 빌드되어 배포되도록 설정한다.
