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
# or
open http://localhost:1313/ && hugo server -wD --navigateToChanged
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
`/layouts/_default/single.html`
```handlebars
{{ define "main" }}
{{ .Content }}
{{ end }}
```

## [Hugo Partials](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/hugo-partials/)

### Add a partial for navigation.
`/layouts/partials/nav.html`
```handlebars
<nav>
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about/">About</a></li>
  </ul>
</nav>
```

Update `baseof.html`
```handlebars
  {{ partial "nav.html" }}
```

### Extract head into partial.

Move `<head>` content  from `baseof.html` to `/layouts/partials/head.html`.

```handlebars
<meta charset="utf-8">
<title>{{ .Page.Title }}</title>
{{ $style := resources.Get "sass/main.scss" | resources.ToCSS | resources.Minify }}
<link rel="stylesheet" href="{{ $style.Permalink }}">
```

Update `baseof.html`. Pay attention to the `.`.  This passes the current page context to the partial.
```handlebars
{{ partial "head.html" . }}
```

## [Hugo Templating Basics](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/hugo-templating-basics/)

Output a string:
```
{{ "Hello" }}
```

Output from front matter:

```
{{ .Params.title }}
```

Output from site config:
```
{{ .Site.title }}
```

Conditions: 
```
{{ .Params.title | .Site.title }}
{{ if isset .Params "title" }}
  <title>{{ .Params.title }}</title>
{{ else }}
  <title>{{ .Site.title }}</title>
{{ end }}
```

Setting and evaluating variables:
```handlebars
{{ $favorite_food := "Pizza" }}
{{ $favorite_food }}
```

Looping:
```handlebars
{{ $friends := slice "bob" "alice" "bill" "ted" }}
<ul>
{{ range $friends }}
  <li>{{ . }}</li>
{{ end }}
</ul>
```

Nested Key Values:
- Content:
```yaml
---
title: Appearance
apperance:
  eyes: green
  snoot: boopable
  whiskers: true
  limbs:
    - claws: 5
      side: left
      position: front
    - claws: 4
      side: right
      position: front
    - claws: 3
      side: left
      position: back
    - claws: 5
      side: right
      position: back
---
```

Layout; use `with` to create namespace context for `.`:
```handlebars
{{ with .Params.appearance }}
<h3>My top appearance traits</h3>
<dl>
	<dt>Eyes</dt>
  <dd>{{ .eyes }}</dd>

  <dt>Snoot</dt>
  <dd>{{ .snoot }}</dd>

  <dt>Whiskers</dt>
  <dd>{{ .whiskers }}</dd>
	
	{{ with .limbs }}
    <dt>Claws</dt>
    <dd>
	    <ul>
      {{ range . }}
        <li>{{ .position }} {{ .side }} {{ .claws }</li>
      {{ end }}
      </ul>
    </dd>
  {{ end }}
</dl>
{{ end }}
```

See the [templating documentation](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/hugo-templating-basics/#:~:text=templating%20documentation) for more information.

### [Template Debugging](https://gohugo.io/templates/template-debugging/)

```handlebars
{{ printf "%#v" $.Site }}
```

## [Blogging in Hugo; Temporal Content and Listing](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/blogging-in-hugo/)

Create a section for notes.  `content/notes/_index.html`
```handlebars
---
title: Notes
---
```

Create a `list` template specifically for the notes section.

`layouts/notes/list.html`
```handlebars
{{ define "main" }}
  <h1>My Notes</h1>
  <ul>
    {{ range .Pages }}
      <li>
          <a href="{{ .Permalink }}">{{ .Title }}</a> - {{ .Date.Format "Jan 2, 2006" }}
      </li>
    {{ end }}
  </ul>
{{ end }}
```

A list page will have an array of all children pages accessible via the `.Pages` variable.

`.Date.Format "Jan 2, 2006"` is Go's quirky way of formatting dates.

`.Permalink` retrieves the actual URL for a page.

Add a few notes.  Set both `title` and `date` in the front matter.
```shell
content/notes/20230116-deeper-down-deep-deeper-down.md
content/notes/20230117-back-in-time.md
content/notes/20230118-my-first-note.md
```

Create a `single` template specifically for individual `notes` pages.

```layouts/notes/single.html```
```handlebars
{{ define "main" }}
  <h1>{{ .Params.Title }}</h1>
  <p>{{ .Date.Format "Jan 02, 2006" }}</p>
  {{ .Content }}
{{ end }}
```

Update the Nav bar to include the Notes section.
`layouts/partials/nav.html`
```handlebars
<nav>
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/notes/">Notes</a></li>
    <li><a href="/about/">About</a></li>
  </ul>
</nav>
```

## [Using Data with Hugo](https://cloudcannon.com/community/learn/hugo-beginner-tutorial/blogging-in-hugo/)

Todo
