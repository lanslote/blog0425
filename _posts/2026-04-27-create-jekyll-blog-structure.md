---
title: "Jekyll 블로그 기본 파일 구성하기"
---

이번 글에서는 GitHub Pages에서 빌드할 수 있는 Jekyll 블로그의 기본 파일을 만든다. 아직 테마를 예쁘게 적용하기 전 단계다. 먼저 Jekyll이 읽을 수 있는 최소한의 구조를 잡고, 글이 어떻게 페이지로 변환되는지 확인하는 것이 목표다.

Jekyll 사이트는 몇 가지 약속된 파일과 폴더를 사용한다. 가장 중요한 파일은 `Gemfile`, `_config.yml`, `index.md`, `_posts` 폴더다.

## Ruby와 Bundler 확인하기

Jekyll은 Ruby 생태계의 도구다. 로컬에서 미리보기까지 하려면 Ruby와 Bundler가 필요하다.

```sh
ruby -v
bundle -v
```

둘 중 하나라도 명령어를 찾을 수 없다고 나오면 Ruby 설치가 먼저 필요하다. Windows에서는 RubyInstaller를 사용하는 방식이 가장 흔하고, macOS나 Linux에서는 rbenv, mise, asdf 같은 버전 관리 도구를 사용할 수 있다.

GitHub Actions에서만 빌드할 계획이라면 로컬 Ruby가 없어도 배포는 가능하다. 하지만 글을 쓰면서 바로 확인하려면 로컬 미리보기 환경을 갖추는 편이 좋다.

## Gemfile 만들기

`Gemfile`은 프로젝트에서 사용할 Ruby gem 목록을 적는 파일이다. Jekyll 자체와 필요한 플러그인을 여기에 선언한다.

```ruby
source "https://rubygems.org"

ruby "~> 3.3"

gem "jekyll", "~> 4.4"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.17"
end
```

아직 테마는 넣지 않았다. 테마는 다음 글에서 Just the Docs를 적용하면서 추가한다.

## _config.yml 만들기

`_config.yml`은 Jekyll 사이트의 중심 설정 파일이다.

```yml
title: My Blog
description: GitHub Pages로 운영하는 Jekyll 블로그입니다.

url: ""
permalink: pretty
timezone: Asia/Seoul

plugins:
  - jekyll-feed
```

`title`은 사이트 제목이다. `description`은 검색 엔진이나 메타 정보에 사용될 수 있다. `timezone`은 글 날짜 처리에 영향을 준다. 한국 시간 기준으로 운영한다면 `Asia/Seoul`로 설정한다.

`permalink: pretty`는 URL 끝에 `.html`이 보이지 않도록 깔끔한 주소를 만든다.

## 첫 페이지 만들기

사이트의 첫 화면이 될 `index.md` 파일을 만든다.

```md
---
layout: default
title: Home
---

# My Blog

GitHub Pages와 Jekyll로 운영하는 블로그입니다.
```

파일 맨 위의 `---`로 감싼 영역을 front matter라고 부른다. Jekyll은 front matter가 있는 파일을 변환 대상으로 인식한다.

`layout: default`는 이 페이지를 어떤 레이아웃으로 감쌀지 지정한다. 테마를 적용하기 전에는 기본 레이아웃이 없을 수 있으므로, 나중에 테마를 붙이면서 자연스럽게 해결된다.

## _posts 폴더와 첫 글 만들기

블로그 글은 `_posts` 폴더 안에 넣는다. 파일 이름은 반드시 날짜로 시작해야 한다.

```text
_posts/2026-04-25-first-post.md
```

내용은 다음처럼 작성할 수 있다.

```md
---
title: "첫 번째 글"
---

Jekyll 블로그의 첫 번째 글입니다.

Markdown으로 글을 쓰면 Jekyll이 HTML 페이지로 변환합니다.
```

글 파일에는 `layout`을 직접 적지 않아도 되도록 `_config.yml`의 defaults 설정을 나중에 추가할 수 있다.

```yml
defaults:
  - scope:
      path: ""
      type: "posts"
    values:
      layout: post
```

이 설정은 모든 posts 타입 문서에 기본으로 `post` 레이아웃을 적용한다.

## 글 목록 페이지 만들기

블로그라면 전체 글 목록 페이지도 필요하다. `posts.md` 파일을 만든다.

```md
---
title: Posts
permalink: /posts/
---

# Posts

{% raw %}{% for post in site.posts %}
## [{{ post.title }}]({{ post.url | relative_url }})

{{ post.date | date: "%Y-%m-%d" }}

{{ post.excerpt | strip_html | truncatewords: 40 }}
{% endfor %}{% endraw %}
```

여기서 `{% raw %}site.posts{% endraw %}`는 Jekyll이 알고 있는 블로그 글 목록이다. 새 글을 `_posts`에 추가하면 이 페이지에 자동으로 나타난다.

## 로컬 미리보기

Ruby와 Bundler가 설치되어 있다면 의존성을 설치하고 로컬 서버를 실행한다.

```sh
bundle install
bundle exec jekyll serve
```

기본 주소는 다음과 같다.

```text
http://localhost:4000
```

브라우저에서 접속했을 때 첫 페이지와 글 목록이 보이면 기본 구조는 성공이다.

## 자주 만나는 문제

`ruby` 또는 `bundle` 명령어를 찾을 수 없다는 메시지가 나오면 Ruby 설치나 PATH 설정을 확인해야 한다.

`Address already in use`가 나오면 이미 4000번 포트를 쓰는 프로그램이 있다는 뜻이다. 이때는 다른 포트를 지정할 수 있다.

```sh
bundle exec jekyll serve --port 4001
```

YAML 오류가 나오면 `_config.yml`이나 글의 front matter 들여쓰기를 확인한다. YAML은 들여쓰기에 민감하다.

## 다음 단계

이제 Jekyll이 읽을 수 있는 기본 구조가 생겼다. 하지만 아직 디자인과 탐색 기능은 부족하다. 다음 글에서는 Just the Docs 테마를 적용해 사이드바, 검색, 기본 스타일을 갖춘 블로그로 바꿔본다.
