# Subject site template

The **UI** of a subject site's hub page (the shelf): sign-in screen, book
covers, search, sort, filters, cards / list / compact views, the gear and the
app icons. It is **code only**: no books, no keys.

Subject sites (for example
[`habit-reader`](https://github.com/eckko/habit-reader), made from
[`subject-reader-shell`](https://github.com/eckko/subject-reader-shell)) hold
**encrypted data files only**. Their `index.html` is a tiny fixed loader that
reads the UI version from the subject's own `subject.json` and loads this UI
from jsDelivr. **Every change to the subject UI is made here**, then released
as a new tag. Subject sites follow a **major version** (`"uiVersion": "1"` =
the newest `1.x.x`), so minor and patch releases reach them without any change
in the subject repo; a new major version is adopted by hand, by editing
`subject.json`.

```
css/hub.css               all styles of the hub
js/hub.js                 the hub: markup, sign-in screen, shelf, gear, icons
img/icon.svg              browser favicon
img/apple-touch-icon.png  180x180 home-screen icon for iOS
```

## Use it in a subject repo

`subject.json` names the UI version:

```json
{ "title": "Habit", "templateTag": "3.2.4", "uiVersion": "1",
  "bookDetails": { "<book id>": { "authors": ["Name"], "edition": "2nd", "year": 2018 } } }
```

- `title`: the heading. `templateTag`: the `recall-quiz-site-template` tag whose
  tools check the site. `bookDetails`: optional (authors, edition, year).
- `uiVersion`: `"1"` (newest 1.x.x), `"1.2"` (newest 1.2.x) or `"1.2.3"`
  (exactly that release). Use `"1"` normally; pin an exact one to stop updates.

The shell's `index.html` (it never changes) has this loader at the end of
`<body>`; it adds the favicon and iOS icon itself through `js/hub.js`:

```html
<script>
(function () {
  "use strict";
  function fail(text) {
    var p = document.createElement("p"); p.textContent = text;
    document.body.appendChild(p);
  }
  fetch("subject.json", { cache: "no-cache" }).then(function (r) {
    if (!r.ok) { throw new Error("subject.json: HTTP " + r.status); }
    return r.json();
  }).then(function (subject) {
    var v = String(subject.uiVersion || "");
    if (!/^[0-9]+(\.[0-9]+){0,2}$/.test(v)) {
      throw new Error('subject.json needs "uiVersion" such as "1"');
    }
    var s = document.createElement("script");
    s.src = "https://cdn.jsdelivr.net/gh/eckko/subject-site-template@" + v +
      "/js/hub.js";
    s.onerror = function () { fail("Could not load the hub UI (" + v + ")."); };
    document.body.appendChild(s);
  }).catch(function (e) { fail("Could not start: " + e.message); });
})();
</script>
```

`hub.js` loads `css/hub.css` and the icons from the same version, then reads
`subject.json` and `books.json` next to the page. `books.json` is written by
the quiz template's `tools/site/build_hub.py`.

## Other templates this UI uses

`js/hub.js` loads sign-in (`js/unlock.js`), the gear (reading panel, themes)
and fonts from
[`eckko/recall-quiz-site-template`](https://github.com/eckko/recall-quiz-site-template)
at `QUIZ_TEMPLATE_TAG` (top of `js/hub.js`). The hub starts in the Classic
theme, light, at 16px; a reader's own gear choices win.

## Release a version

1. Change files here, open a PR, merge to `main`.
2. Create a release tag `MAJOR.MINOR.PATCH` (for example `1.1.0`: no `v`, never
   a branch). Subject sites load `@1`, so they get it without any change.
3. Subject sites on `"uiVersion": "1"` may take minutes to hours to see it: jsDelivr caches a
   version range. To refresh at once, open
   `https://purge.jsdelivr.net/gh/eckko/subject-site-template@1/js/hub.js`
   (and the same for `css/hub.css`).

**Versioning rules** (subject sites follow `@1`, so these matter):

- patch (`1.0.1`): a fix. Minor (`1.1.0`): a new feature. Both **must stay
  backward compatible**: a subject repo's `index.html`, `subject.json`,
  `books.json` and book folders keep working unchanged.
- major (`2.0.0`): anything that breaks them (a renamed `subject.json` key, a
  new required file, a changed book folder format). Subject repos move to it
  by hand: set `"uiVersion": "2"` in their `subject.json`.
- A tag is immutable. A bad release is fixed with a newer patch tag, never by
  moving a tag.

## Try it locally

Serve a subject repo with this folder standing in for the CDN (the test
setup routes `cdn.jsdelivr.net/gh/eckko/subject-site-template@<tag>/` to this
folder); sign in with the subject key. There is no build step.

## Checks

`.github/workflows/check.yml` runs `node --check js/hub.js` and verifies the
tags and files described above.
