---
title: Home
---

Hello, Home.


[Getting Setup in Hugo](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/)

[Layouts in Hugo](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/layouts-in-hugo/)

```/content/_index.md```\
```markdown
---
title: Home
---

Hello, I'm a ferocious lion.

```

```/layouts/_default/list.html```
```handlebars
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>{{ .Page.Title }}</title>
</head>

<body>
  {{ .Content }}
</body>
</html>
```

