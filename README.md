# web-accessibility-course-notes

<https://classroom.udacity.com/courses/ud891>

## Resources

### WebAIM's WCAG 2 Checklist

<https://webaim.org/standards/wcag/checklist>

### Chrome Web Server

<https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb?hl=en>

## Notes

### Focus

* DOM order = tab order -> so: place in logical order in the DOM, and avoid CSS that positions elements visually in a different order
* `tabindex="-1"` = not in tab order but can be focused with focus() in js = great for modals (`document.querySelector('#modal').focus()`)
* `tabindex="0"` = added to tab order (and can also be focused with focus() in js = useful for custom elements (e.g. custom `div` dropdown)
* tabindex > 0 is **NOT** recommended
* typically do **NOT** have to use tabindex for non-interative elements like headers (screen readers can read them)
  * exception: [SPA](https://en.wikipedia.org/wiki/Single-page_application)-like interaction menu anchor clicks -> good case for `tabindex="-1"` and `focus()` on a header that "appears" on the page
* skip links help switch device users: hidden links to jump to page content (for an example, visit <https://github.com/> and hit tab)
  * example: `<a href="#main-content-id" class="skip-link">Skip to main content</a>` and `.skip-link { position: absolute; top: -40px; } .skip-link:focus { top: 0; }`
* Helpful easy test: tab through page and see if things make sense, e.g. the focus order and focused item is shown.
  * In your browser's Console: `document.activeElement` gives you currently-focused item.
* [Lighthouse](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk?hl=en) Chrome extension -> unselect all except Accessibility to get an audit of the current page.
* Keyboard traps are usually bad but can be temporarily good: while a modal is open, and then return focus to last element when modal is closed. `firstTabStop.focus()`, `lastTabStop.focus()`, `focusedElementBeforeModal.focus()`

  * ```js
    var focusableElementsString = 'a[href], area[href], input:not([disabled]), select:not([disabled]), textarea:not([disabled]), button:not([disabled]), iframe, object, embed, [tabindex="0"], [contentediteable]';
    var focusableElements = modal.querySelectorAll(focusableElementsString);
    // convert NodeList to Array
    focusableElements = Array.prototype.slice.call(focusableElements);
    var firstTabStop = focusableElements[0];
    var lastTabStop = focusableElements[focusableElements.length - 1];
    ```

  * <https://classroom.udacity.com/courses/ud891/lessons/7962031279/concepts/79621414230923>

### Semantics

* You can address diverse assistive technologies <- by expressing semantics programmatically <- by understand affordances.
* Affordances are like common conventions that users are used to. At the very least, they minimize training time to get used to.
* To express semantics programmatically: (as per AIM WCAG checklist)
  * **role** (example: "combo box")
  * **label/name** (example: "preferred seat type")
  * **value** (example: "no preference")
  * **state** (example: "collapsed")
  * (These *are* the affordances!)
* Accessibility tree <- DOM tree:
  * Pretty much the same.
  * Much of the DOM has implicit semantic meaning. (Example: `button` instead of `div`)
* `alt="description of the image in its context to provide the same experience"`. Tricky example: header logo is also link to home. Instead of `"Home"`, just do `"<Page name as shown in logo image>"`.
* `alt=""` = good if a description would be redundant in the image's context, but we also don't want the screen reader to read out the file name either. Tricky example: magnifying glass next to search field that already gets read out as a searchbox.
