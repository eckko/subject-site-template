# Subject site template

The **UI** of a subject site's hub page (the shelf): sign-in screen, book
covers, search, sort, filters, cards / list / compact views, the gear and the
app icons. It is **code only**: no books, no keys.

Subject sites (for example
[`habit-reader`](https://github.com/eckko/habit-reader), made from
[`subject-reader-shell`](https://github.com/eckko/subject-reader-shell)) hold
**encrypted data files only**. Their `index.html` is a tiny shell that loads
this UI from jsDelivr by an exact release tag. **Every change to the subject
UI is made here**, then released as a new tag; subject repos only move their
tag.

```
css/hub.css               all styles of the hub
js/hub.js                 the hub: markup, sign-in screen, shelf, gear
img/icon.svg              browser favicon
img/apple-touch-icon.png  180x180 home-screen icon for iOS
```

## Use it in a subject repo

The shell's `index.html` is only this (replace `1.0.0` with the release tag):

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Books</title>
  <link rel="icon" type="image/svg+xml" href="https://cdn.jsdelivr.net/gh/eckko/subject-site-template@1.0.0/img/icon.svg">
  <link rel="apple-touch-icon" sizes="180x180" href="https://cdn.jsdelivr.net/gh/eckko/subject-site-template@1.0.0/img/apple-touch-icon.png">
  <script src="https://cdn.jsdelivr.net/gh/eckko/subject-site-template@1.0.0/js/hub.js"></script>
</head>
<body><noscript>This page needs JavaScript.</noscript></body>
</html>
```

`hub.js` loads `css/hub.css` from the same tag, then reads the subject's own
`subject.json` and `books.json` next to the page.

### What the subject repo provides

- `subject.json`: `{ "title": "Habit", "templateTag": "3.2.4",
  "bookDetails": { "<book id>": { "authors": ["Name"], "edition": "2nd",
  "year": 2018 } } }`. `title` is the heading; `templateTag` is the
  `recall-quiz-site-template` tag whose tools check the site; `bookDetails`
  is optional (authors, edition, year shown on cards and rows).
- `books.json`: written by that template's `tools/site/build_hub.py`.
- One folder per book (encrypted), as before.

## Other templates this UI uses

`js/hub.js` loads sign-in (`js/unlock.js`), the gear (reading panel, themes)
and fonts from
[`eckko/recall-quiz-site-template`](https://github.com/eckko/recall-quiz-site-template)
at `QUIZ_TEMPLATE_TAG` (top of `js/hub.js`). The hub starts in the Classic
theme, light, at 16px; a reader's own gear choices win.

## Release a version

1. Change files here, open a PR, merge to `main`.
2. Create a release tag like `1.1.0` (exact `major.minor.patch`, no `v`,
   never a branch). **A tag is immutable**: jsDelivr caches it forever, so a
   fix is a new tag.
3. In each subject repo, change the three `@1.0.0` URLs in `index.html` to the
   new tag (see that repo's README).

Versioning: patch = fix, minor = new feature that needs nothing from the
subject repo, major = the subject repo's files or `subject.json` must change.

## Try it locally

Serve a subject repo with this folder standing in for the CDN (the test
setup routes `cdn.jsdelivr.net/gh/eckko/subject-site-template@<tag>/` to this
folder); sign in with the subject key. There is no build step.

## Checks

`.github/workflows/check.yml` runs `node --check js/hub.js` and verifies the
tags and files described above.
