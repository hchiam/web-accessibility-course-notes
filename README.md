# web-accessibility-course-notes
https://classroom.udacity.com/courses/ud891/

## WebAIM's WCAG 2 Checklist
https://webaim.org/standards/wcag/checklist

## Stray notes for now
* DOM order = tab order -> so: place in logical order in the DOM, and avoid CSS that positions elements visually in a different order
* `tabindex="-1"` = not in tab order but can be focused with focus() in js = great for modals (`document.querySelector('#modal').focus()`)
* `tabindex="0"` = added to tab order (and can also be focused with focus() in js = useful for custom elements (e.g. custom `div` dropdown)
* tabindex > 0 is **NOT** recommended
* typically do **NOT** have to use tabindex for non-interative elements like headers (screen readers can read them)
  * exception: [SPA](https://en.wikipedia.org/wiki/Single-page_application)-like interaction menu anchor clicks -> good case for `tabindex="-1"` and `focus()` on a header that "appears" on the page
