---
title: Images
nav_order: 4
permalink: /images/
---

# Images

포스트에 이미지를 넣을 때는 `assets/images/posts/` 아래에 이미지 파일을 저장합니다.

추천 구조:

```text
assets/
└─ images/
   └─ posts/
      └─ post-name/
         └─ screenshot.png
```

Markdown에서 이미지를 넣는 예:

{% raw %}
```md
![이미지 설명]({{ "/assets/images/posts/post-name/screenshot.png" | relative_url }})
```
{% endraw %}

`relative_url`을 사용하면 GitHub Pages의 저장소 경로가 붙는 프로젝트 페이지에서도 안전하게 동작합니다.
