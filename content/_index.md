---
title: Home
---

# Home

Hello, Home.


## [Getting Setup in Hugo](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/)

Install Hugo
```shell
brew install hugo
```

Initialize site
```shell
hugo new site hugo_tinker
cd hugo_tinker
git init
git add .
git commit -m "Initialized Hugo site."
```

Run dev server
```shell
hugo serve -wD
```

## [Layouts in Hugo](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/layouts-in-hugo/)

### Add a home page.

```/content/_index.md```

```markdown
---
title: Home
---

Hello, I'm a ferocious lion.

```

The page without a template results in a warning.
```
WARN 2023/01/17 17:26:15 found no layout file for "HTML" for kind "home": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
```

Not that the '[page kind](https://gohugo.io/templates/lookup-order/)' is "home".  "home" can use a 'list template'.

[Hugo's Lookup Order](https://gohugo.io/templates/lookup-order/)

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

### Add an About page.

```/content/about.md```
```handlebars
---
title: About
---
I'm learning Hugo, one step at a time.
```

Add a base template for pages.
```/layouts/_default/baseof.html```
```handlebars
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>{{ .Page.Title }}</title>
</head>

<body>
  {{ block "main" . }}
  {{ end }}
</body>
</html>
```

Update list to just fill in the base template.
```/layouts/_default/list.html```
```handlebars
{{ define "main" }}
  {{ .Content }}
{{ end }}
```

Mimic `list.html`
```/layouts/_default/single.html```
```handlebars
{{ define "main" }}
{{ .Content }}
{{ end }}
```

## [Hugo Partials](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/hugo-partials/)
