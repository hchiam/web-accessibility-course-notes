# Web Accessibility (a11y) Course Notes

Just one of the things I'm learning. <https://github.com/hchiam/learning>

These are my notes for [Google's Udacity course](https://www.udacity.com/course/web-accessibility--ud891): <https://classroom.udacity.com/courses/ud891> (and its companion [GitHub repo](https://github.com/udacity/ud891)), plus other some helpful supplemental resources/notes I found.

For other notes, I also have a [repo hchiam/learning-a11y](https://github.com/hchiam/learning-a11y) with a URL that's easier to type and remember.

## Key things I personally focus on most:

1. Use [WAVE](https://chrome.google.com/webstore/detail/wave-evaluation-tool/jbbplnpkjmmeebjpijfedlgcdilocofh) or some other automated a11y checker.
2. Use a [screen reader](https://www.youtube.com/watch?v=5R-6WvAihms&list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g&index=7).
3. Tab. Shift+Tab. Enter.

## Click the following to expand:

<details>
<summary>Resources</summary>

### Automated Testing

aXe Chrome Extension or Node module with 0 false positives:

- Quick setup for `axe-cli`: <https://github.com/hchiam/learning-axe-cli#learning-axe-cli>
- Fuller reference: <https://developers.google.com/web/fundamentals/accessibility/a11y-for-teams#automated_testing>
- You can also re-run the axe devtools without rebuilding the page: <https://css-tricks.com/video-screencasts/204-using-the-axe-devtools-web-accessibility-testing-browser-plugin> (and focus on re-running the most critical issues)

Integrate Lighthouse into your CI (e.g. GitHub Travis CI):

- <https://github.com/hchiam/learning-lighthouse-ci#learning-lighthouse-ci-from-scratch-quickstart>

### Web Accessibility VSCode Extension (live coded linter)

<https://marketplace.visualstudio.com/items?itemName=MaxvanderSchee.web-accessibility>

### Practical Solutions, Considerations, Pattern Library

After (or before!) you find a11y problems via the automated tools and UX testing, here are ways to fix them:

<https://inclusive-components.design>

### WebAIM's WCAG 2 Checklist

<https://webaim.org/standards/wcag/checklist>

### Chrome Web Server

<https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb>

### NoCoffee (to Simulate Vision Deficiencies)

<https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl>

### High Contrast (check if content still visible)

<https://chrome.google.com/webstore/detail/high-contrast/djcfdncoelnlbldjfhinnjlhdjlikmph>

### ARIA design patterns and links to **_live examples_**

<https://www.w3.org/TR/wai-aria-practices-1.1/#aria_ex>

### VisBug

(Hover items to see contrast levels, or move items around like an artboard.)

<https://chrome.google.com/webstore/detail/visbug/cdockenadnadldjbbgcallicgledbeoc>

</details>

<details>
<summary>Motivation</summary>

Design for everyone. Making your website accessible helps everyone. Disability is more broad than what you might typically think of as disability: aging, temporary disability, and situational disability even for healthy individuals. Different devices and different situations (e.g. outdoors screen with low contrast). This goes beyond permanent disability and can affect everyone.

Aside: I found [an article on Medium.com that gives more examples](https://medium.com/swlh/why-web-accessibility-is-far-more-important-than-you-think-831d9bfdf9af) and [another one](https://medium.com/valtech-design/inclusive-design-dd4e03f82094).

You don't have to choose between a delightful website and checking some "accessibility checkbox".

Tip: Start with the most frequently-used pieces of UI.

A11y = make sure all of your users can use your content.

**Good a11y -> good UX.** _(Example: clearing out clutter in UI helps screen readers get to content, but also improves the UI for everyone in general. Good contrast helps for low-contrast projectors and outdoor displays.)_

</details>

<details>
<summary>Focus</summary>

- DOM order = tab order -> so: place in logical order in the DOM, and avoid CSS that positions elements visually in a different order
- `tabindex="-1"` = not in tab order but can be focused with focus() in js = great for modals (`document.querySelector('#modal').focus()`)
- `tabindex="0"` = added to tab order (and can also be focused with focus() in js = useful for custom elements (e.g. custom `div` dropdown)
- tabindex > 0 is **NOT** recommended. Instead, aim to [put elements in the logical sequence instead](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex).
- typically do **NOT** have to use tabindex for non-interative elements like headers (screen readers can read them)
  - exception: [SPA](https://en.wikipedia.org/wiki/Single-page_application)-like interaction menu anchor clicks -> good case for `tabindex="-1"` and `focus()` on a header that "appears" on the page
- skip links help switch device users: hidden links to jump to page content (for an example, visit <https://github.com/> and hit tab)
  - example: `<a href="#main-content-id" class="skip-link">Skip to main content</a>` and `.skip-link { position: absolute; top: -40px; } .skip-link:focus { top: 0; }`
- Helpful easy test: tab through page and see if things make sense, e.g. the focus order and focused item is shown.
  - In your browser's Console: `document.activeElement` gives you currently-focused item.
- [Lighthouse](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk?hl=en) Chrome extension -> unselect all except Accessibility to get an audit of the current page.
- Keyboard traps are usually bad but can be temporarily good: while a modal is open, and then return focus to last element when modal is closed. `firstTabStop.focus()`, `lastTabStop.focus()`, `focusedElementBeforeModal.focus()`

  - ```js
    var focusableElementsString =
      'a[href], area[href], input:not([disabled]), select:not([disabled]), textarea:not([disabled]), button:not([disabled]), iframe, object, embed, [tabindex="0"], [contentediteable]';
    var focusableElements = modal.querySelectorAll(focusableElementsString);
    // convert NodeList to Array
    focusableElements = Array.prototype.slice.call(focusableElements);
    var firstTabStop = focusableElements[0];
    var lastTabStop = focusableElements[focusableElements.length - 1];
    ```

    - (And example usage of that ↑ is here: <https://classroom.udacity.com/courses/ud891/lessons/7962031279/concepts/79621414230923>)

</details>

<details>
<summary>Semantics</summary>

- You can address diverse assistive technologies <- by expressing semantics programmatically <- by understand affordances.
- Affordances are like common conventions that users are used to. At the very least, they minimize training time to get used to.
- To express semantics programmatically: (as per AIM WCAG checklist)
  - **role** (example: "combo box")
  - **label/name** (example: "preferred seat type")
  - **value** (example: "no preference")
  - **state** (example: "collapsed")
  - (These _are_ the affordances!)
- Accessibility tree <- DOM tree:
  - Pretty much the same, except visuals removed and items "linearized" (to fit one dimension of speech over time).
  - Much of the DOM has implicit semantic meaning. (Example: `button` instead of `div`)
- `alt="description of the image in its context to provide the same experience"`. Tricky example: header logo is also link to home. Instead of `"Home"`, just do `"<Page name as shown in logo image>"`.
- `alt=""` = good if a description would be redundant in the image's context, but we also don't want the screen reader to read out the file name either. Tricky example: magnifying glass next to search field that already gets read out as a searchbox.
- Include meaningful headers in your web page! They give users of screen readers an easy way to quickly navigate your page.
- Don't go overboard with screen-reader-only headers.
- Make link text usable for screen reader shortcut lists:
  - Bad: "Learn more." (About what? Unclear in a list of link texts.)
  - OK: "Learn more about responsive layouts."
  - Better: "Responsive Layouts" (Turn the title into a link.)
- Example semantic HTML elements: `main`, `header`, `footer`, `nav`, `section` (usually has a h1/h2/... header in it), `article`, and `aside`.
  - You can simplify CSS to refer to `header` instead of `.header`, while also making the HTML more semantic for assistive tech users.
- Meaningful headings and link text, and good page structure.
- Don't try to control the experience a screen reader would have, since that can confuse users. E.g.: the tool has ways work around odd names, like spelling them out.

</details>

<details>
<summary>Semantics: ARIA</summary>

- Built-in HTML Semantics Sometimes Isn't Enough
  - Dropdowns: currently no standard HTML element.
  - Another example: urgent user notification (`<div role="alert">Could not connect!</div>` -> `aria-live`)
- Example: `role="checkbox"` and then ALSO add `aria-checked="true"` or `aria-checked="false"` (ARIA HTML properties must be explicitly indicated. Good for custom elements.)
  - But you have to take care of a lot more.
  - Example: `this.el.setAttribute('role', 'checkbox');`
  - Example: `if (this.el.hasAttribute('checked')) { this.el.setAttribute('aria-checked', 'true'); }`
- Example: expandable tree: `role="tree"`, `role="group"`, `role="treeItem"`, `aria-expanded="true"`, `aria-expanded="false"`
- Example: `<button ... aria-label="Filter">`
- ARIA _only_ modifies the accessibility tree. It does not:
  - modify element appearance.
  - modify element behaviour.
  - add focusability.
  - add keyboard event handling.
- ARIA design patterns and links to **_live examples_**: <https://www.w3.org/TR/wai-aria-practices-1.1/#aria_ex>

- ```js
  // set ARIA role = radio group
  this.el.setAttribute("role", "radiogroup"); // role!!!

  var firstButton = true;
  for (var button of this.buttons) {
    if (firstButton) {
      button.tabIndex = "0";
      firstButton = false;
    } else {
      button.tabIndex = "-1";
    }

    // set ARIA role = radio
    button.setAttribute("role", "radio"); // role!!!
  }
  ```

- ```js
  RadioGroup.prototype.changeFocus = function () {
    // old button:
    this.focusedButton.tabIndex = -1; // tabindex!!!
    this.focusedButton.removeAttribute("checked");
    this.focusedButton.setAttribute("aria-checked", "false"); // ARIA!!!

    // new button:
    this.focusedButton = this.buttons[this.focusedIdx];
    this.focusedButton.focus();
    this.focusedButton.tabIndex = 0; // tabindex!!!
    this.focusedButton.setAttribute("checked", "");
    this.focusedButton.setAttribute("aria-checked", "true"); // ARIA!!!
  };
  ```

- `aria-label` example: name = "menu" (for screen-reader only):

  ```html
  <button aria-label="menu" class="hamburger-menu-icon"></button>
  ```

- `aria-label` example: name = "close", not "X" (for screen-reader only):

  ```html
  <button aria-label="close">X</button>
  ```

- `aria-labelledby` example: name/label = "Drink options" from another element (not whatever's in "..." below):

  ```html
  <span id="rg-label"> Drink options </span>
  <div role="radiogroup" aria-labelledby="rg-label">...</div>
  ```

  - This is an example of an ARIA relationship attribute (links 2 or more elements).

  - Notes: `aria-labelledby` can be put on any element, not just a `label` element, but it does not give you the nice label-clicking behaviour that `label` gives you. Also, you place `aria-labelledby="..."` on the element, which is opposite of putting `for="..."` on the `label`. `aria-labelledby` can take a list of elements to concatenate the name from (even from the element itself! and even from hidden elements!).

- In terms of precedence: `aria-labelledby` > `aria-label` > native `label`
- ARIA roles may be redundant in some cases: e.g.: `<input type="checkbox" role="checkbox">` or `<main role="main">`
  - but you might need this redundancy for wider browser support.

#### ARIA Relationship Attributes

- Example: `aria-labelledby="..."` = label/name (see earlier notes).
- `aria-owns="..."` = "treat ... as my **child** element" (even if separate in the DOM), like for submenus.
  - But why not just do so in DOM? Maybe for visual presentation or because of element reuse in different contexts.
  - `aria-owns` is a very common ARIA relationship attribute. Good to know!
- `aria-activedescendant="..."` = "present ... as the **apparent focused** element when I have page focus" (this is not actually moving the roving focus).
  - Example: typing in a textbox that has page focus, but while reading out an apparently-focused filtered option shown in a dropdown.
  - This basically can graft together different parts of the DOM onto this node in the Accessibility Tree.
- `aria-describedby="..."` = "use ... as my non-critical description" (NOT name/label) as extra info, like password requirements (vs. password characters typed). Even if that identified element is hidden from the DOM (just like aia-labelledby).
- `aria-posinset` and `aria-setsize` = "specify on this element its actual position in the set, and the actual number of items in its set", like when you don't know the size of the list when using lazy loading.

  - Example:

    ```html
    <div role="listbox">
      <!-- aria-posinset + aria-setsize on items not on container -->
      <!-- Use dynamic HTML techniques to let user explore up. -->
      <div role="option" aria-posinset="857" aria-setsize="1000">
        Item 857 shows up first, maybe due to lazy loading.
      </div>
      <div role="option" aria-posinset="858" aria-setsize="1000">
        Item 858 shows up last, maybe due to lazy loading.
      </div>
      <!-- Use dynamic HTML techniques to let user explore down. -->
    </div>
    ```

#### Hiding/Showing Only for Accessibility Tree (AT)

- Hide element for everyone:
  - Native explicit hiding: `visibility: hidden;`, `display:none`, or attribute `hidden`.
- Show label only in AT:
  - Make far off screen, e.g. `position: absolute; left: -10000px;`
  - Or: `aria-label="Some text that only screen-readers can access."`
  - Or: `aria-labelledby="some-hidden-element"`
  - Or: `aria-describedby="some-hidden-element-for-extra-info"`
- Hide element only in AT:
  - `aria-hidden="true"` (hides from AT all its descendants, except elements referred to by `aria-labelledby` or `aria-describedby`, which makes sense based on earlier notes).

#### Alerting the User

- Instead of waiting for user to get to the element in the DOM.
- `aria-live="..."`: `off` (default/fallback), `polite`, `assertive`.
  - "polite" = when you're done whatever you're doing. (waits)
  - "assertive" = you need to know this right now! (interrupts)
- **Troubleshooting tips when `aria-live` isn't speaking:**
  - Test on different platforms, since they can react differently.
  - Try including `aria-live` attributes in initial page load.
  - Try triggering style change on the element: hidden -> visible.
  - Try changing the content of the element to trigger speaking.
  - Try appending new element with `aria-live`.
- `aria-atomic` = say the whole contents each time.
- `aria-relevant` = say the changes of specified type(s):
  - `="text"` = when text changed.
  - `="additions"` = when element added.
  - `="removals"` = when element removed.
  - `="all"` = `="additions removals text"` = basically any change triggers (re-)announcing.
  - default/fallback is `="additions text"` (added elements or text changes trigger (re-)announcing).
- `aria-busy="true"` = ignore changes to element despite having `aria-live="polite"` for example.
  - Example (1/2): until everything's loaded, set this: `<div aria-live="polite" aria-busy="true">`.
  - Example (2/2): when ready, set this: `<div aria-live="polite" aria-busy="false">`.
  - Note: `="false"` by default, i.e. do not ignore by default. Makes sense.

</details>

<details>
<summary>Style</summary>

#### Overview

- Styles for focus.
- Styles for ARIA states.
- Responsive UIs (flexible device/zoom views).
- Colour choices/contrast.

#### Focus Styling

- Sometimes the focus ring is hard to see or doesn't look good.
- Give alternate indication instead of only clearing outline.

- ```css
  :focus {
    /* BAD */
    outline: 0;
  }
  ```

- ```css
  :focus {
    /* good */
    outline: 1px dotted #fff;
  }
  ```

- ```css
  :focus {
    /* better */
    outline: 0; /* browsers handle outline inconsistently */
    box-shadow: 0 0 8px 3px rgba(255, 255, 255, 0.8); /* consistent across browsers */
    text-decoration: underline; /* visual indicator that doesn't rely on colour */
  }
  ```

- ```css
  /* to improve style around radio options,
   * make the focus ring on radio buttons
   * only wrap around the radio icon */
  .radio:focus {
    outline: 0;
  }

  .radio:focus::before {
    box-shadow: 0 0 1px 2px #5b9dd9;
  }
  ```

- If you implement custom elements, you might get focus rings where you don't want them, so to differentiate between mouse clicks and keyboard tags, you might need to find a shim here: <https://github.com/alice/modality>
  - Right now, something like Firefox's `:-moz-focusring` is not implemented on all browsers.

#### ARIA States Styling

- You can clean up the selectors while also having a way to verify you're correctly updating ARIA states. So instead of this:

  ```css
  .toggle.pressed,
  .toggle[aria-pressed="true"] {
    ...;
  }
  ```

  you replace it with just this:

  ```css
  .toggle[aria-pressed="true"] {
    ...;
  }
  ```

#### Responsive UIs Styling (flexible device/zoom views)

- Include this in page `head`: `<meta name="viewport" content="width=device-width, initial-scale=1">`
  - (Stray observation: Easily generated with `!` snippet using Emmet in VSCode.)
- Favour relative units over `px`:
  - `%`, `em`, or `rem` respond to zoom and responsively move other items down the page.
  - `em` and `rem` are better than `px` for text since some browsers can zoom just the text on a page via user settings.
- Button size: **48dp minimum mobile touch target size** = 48 x 48 pixel area ~ 9 mm ~ finger pad.
  - You can achieve that with padding.
- Button margin: **32dp margin around touch target** (horizontally and vertically)

#### Colour Contrast

- For users with 20/40 vision, commonly ages 80+:
  - **4.5:1 minimum contrast** in text and images of text.
  - **3:1 minimum contrast** in large text > 14 point bold.
- For users with low vision impairments or colour vision deficiencies:
  - **7:1 minimum contrast** in text and images of text.
  - **4.5:1 minimum contrast**in large text > 14 point bold.
- Use the Chrome extension [Lighthouse](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk?hl=en) or Chrome Dev Tools > Audits > Accessibility > Run audits. (Note: right now both only work on http/https pages, not local html files.)
- 100s of millions, about 1 in 20 people have a colour vision deficiency, only expected to increase with aging populations.
  - -> Convey info with more than only colour differences! "Don't rely on colour alone."
- Use [NoCoffee](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US) Chrome extension to test your UI with different kinds of simulated vision impairments.
- Use [High Contrast](https://chrome.google.com/webstore/detail/high-contrast/djcfdncoelnlbldjfhinnjlhdjlikmph?hl=en) Chrome extension to test your content still shows up for users who have high contrast settings set up.

</details>

<details>
<summary>P.O.U.R.</summary>

My summary:

- P = Perceivable = can see/hear/feel (like captions)
- O = Operable = can use (like element focusability + keyboard + time + recovery)
- U = Understandable = can get meaning (like labels + layout familiarity + meaningful error messages)
- R = Robust = is flexible/cross-compatible (like mobile versus desktop)

</details>

<details>
<summary>Random Notes</summary>

- https://developer.mozilla.org/en-US/docs/Tools/Web_Console/The_command_line_interpreter#Helper_commands

  - In web dev console: `$('h1')` = `document.querySelector('h1')` (looks like jQuery!)
  - In web dev console: `$$('h1,h2,h3')` = `document.querySelectorAll('h1,h2,h3')` but returns an array instead of a NodeList. (Cool!)

- https://github.com/hchiam/toronto-js-workshop-a11y

- https://github.com/zeitspace/web-accessibility-session

  - Ontario: AODA: company > 50:
    - -> 2014: must have WCAG 2.0 Level A
    - -> 2021: must have WCAG 2.0 Level AA
  - WCAG **_"quick ref"_**: <https://www.w3.org/WAI/WCAG21/quickref>
  - practice/research later:
    - Perceivable:
      - -> web video add text: <https://www.w3schools.com/tags/tag_track.asp>
    - Operable:
      - -> skip links
    - Understandable
      - -> "Read more" link with screenreader-only audible extra "Read more about (...)"
    - Robust
      - -> research lighthouse compatibility IE checks
    - Write down: Don't just sketch! Sketch _semantic_ blocks! At the start!
    - Try out a11y-friendly drag and drop.

- https://github.com/hchiam/learning-extra-a11y-stuff (with example/demo)

- https://github.com/hchiam/learning-a11y/blob/main/udemy-creating-accessible-websites/README.md

- flying focus ring: https://github.com/hchiam/flying-focus

- keyboard focus trap: https://github.com/hchiam/keyboard-focus-trap

- map mouse positions to sounds (sonification): https://github.com/hchiam/_2dnote

- draw without a touchpad/stylus: https://github.com/hchiam/draw-with-mouse-and-spacebar

- how to phrase links to avoid the unhelpful "click here": https://www.smashingmagazine.com/2012/06/links-should-never-say-click-here

</details>
