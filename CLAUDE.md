This repo is the only place the subject hub UI lives: `js/hub.js`,
`css/hub.css` and `img/`. Subject repos (habit-reader, and every repo made from
eckko/subject-reader-shell) hold encrypted data and a tiny `index.html` that
loads this UI from jsDelivr at the version named by `uiVersion` in its
`subject.json` (`"1"` = newest 1.x.x). Never put a book, a key,
a cover or any readable book text here. Keep `js/hub.js` plain ES5-style
browser code (no build step, no modules); keep `QUIZ_TEMPLATE_TAG` an exact
tag of eckko/recall-quiz-site-template (no `v`, never a branch). A released
tag is immutable: fix with a new tag. Minor and patch releases reach every
subject site at once, so they must stay backward compatible; anything breaking
is a new major (2.0.0). See README.md.
