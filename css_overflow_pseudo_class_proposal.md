# Proposal for new CSS pseudo class :overflow (-top, -right, -bottom, -left)

There are valid use-cases for which the UI must react to an occurring oveflow (beyond just showing a scrollbar).
Currently those cases always require a javascript solution.
The required code to deal with such use-cases could be simplified if the fact that an overflow occurred was accessible in the CSS.
A new CSS pseudo class `:overflow-<side>` would satisfy this need.

## Proposed Mechanism

The following CSS pseudo class selectors are added to the standard:

- `:overflow-top`
  A selector that matches any HTML element wich has content overflowing its top boundary.
  For this the element must be a scroll container i.e. the content must exceed the available size
  and the computed style for `overflow` in this direction must be `visible`, `hidden`, `scroll`, or `auto`; it must not be `clip`.

- `:overflow-right`
  analogous to `:overflow-top` but concerned with the right boundary

- `:overflow-bottom`
  analogous to `:overflow-top` but concerned with the bottom boundary

- `:overflow-left`
  analogous to `:overflow-top` but concerned with the left boundary

Further shorthand selectors could be added:

- `:overflow-x`
  matches an element if it matches at least one of `:overflow-left` or `:overflow-right`

- `:overflow-y`
  matches an element if it matches at least one of `:overflow-top` or `:overflow-bottom`

## use-case Examples

### indicate content scrolled out-of-sight due to overflow

For text content for which it is important that the user is aware of potentially concealed parts of the text due to overflow we can show a shadow overlaying the content towards the edge where the overflow occurs.

![example without scroll shadow](https://github.com/teetotum/exploration/blob/master/scroll_shadow/example_without_scroll_shadow.png)

![example with scroll shadow](https://github.com/teetotum/exploration/blob/master/scroll_shadow/example_with_scroll_shadow.png)

- [Link](https://gleaming-peppered-lifeboat.glitch.me/) to live example implementation using javascript
- [Link](https://glitch.com/edit/#!/gleaming-peppered-lifeboat) to code

A pure CSS solution using the proposed pseudo class would work with the following CSS:

```css
.scroll-container {
  /* inset | offset-x | offset-y | blur-radius | spread-radius | color */
  --top-shadow: inset 0 80px 48px -64px #00000021;
  --bottom-shadow: inset 0 -80px 48px -64px #00000021;
}

.scroll-container:overflow-top {
  box-shadow: var(--top-shadow);
}

.scroll-container:overflow-bottom {
  box-shadow: var(--bottom-shadow);
}

.scroll-container:overflow-top:overflow-bottom {
  box-shadow: var(--top-shadow), var(--bottom-shadow);
}
```

### show custom controls to enable scrolling when a scrollbar is undesired

When the design omits the default scrollbar but offers custom buttons to scroll the content we want to _a)_ display the buttons only when there is actually an overflow and _b)_ enable a button to receive clicks only when there is scrollable area in the respective direction.

![custom scroll buttons example](https://github.com/teetotum/exploration/blob/master/custom_scroll_buttons/custom_scroll_buttons_example.png)

- [Link](https://humorous-glass-file.glitch.me/) to live example implementation using javascript
- [Link](https://glitch.com/edit/#!/humorous-glass-file) to code

A pure CSS solution using the proposed pseudo class would work with the following CSS:

```css
/* .scroll-left disabled */
.scroll-container:not(:overflow-left) + .scroll-buttons .scroll-left {
  cursor: default;
  pointer-events: none;
  opacity: 0.3;
}

/* .scroll-right disabled */
.scroll-container:not(:overflow-right) + .scroll-buttons .scroll-right {
  cursor: default;
  pointer-events: none;
  opacity: 0.3;
}
```

## Roadblocks

The proposed feature does not fit well into how browsers currently render a document.
Selectors are matched, resulting in styles being applied, resulting in layout, causing overflow, which would now need to trigger matching of `:overflow` selectors and applying corresponding styles, which could cause more overflow or undo an oveflow; potentially causing an infinite cycle.
The same concern has been discussed [here](https://discourse.wicg.io/t/add-truncated-to-css-pseudo-selector-specification/621) without a feasible solution.

## Alternatives

Some use-cases could be solved with a pure CSS solution, using other proposed CSS features:
As of writing this proposal `scroll-linked-animations` are still an experimental feature.
However, it has progressed quite far; it seems the spec for version 1 is nearing a stable state.
At the moment a [polyfill](https://github.com/flackr/scroll-timeline) is available.
An [alternative implementation](https://github.com/teetotum/exploration/blob/master/scroll_shadow/css_solution_with_scrolltimeline.html) of the first use-case using `scroll-linked-animations` would look like this:

```css
@keyframes adjust-shadow {
  0% {
    box-shadow: var(--hidden-top-shadow), var(--visible-bottom-shadow);
  }
  1%,
  99% {
    box-shadow: var(--visible-top-shadow), var(--visible-bottom-shadow);
  }
  100% {
    box-shadow: var(--visible-top-shadow), var(--hidden-bottom-shadow);
  }
}

.scroll-container.with-shadow {
  --visible-top-shadow: inset 0 80px 48px -64px #00000021;
  --visible-bottom-shadow: inset 0 -80px 48px -64px #00000021;
  --hidden-top-shadow: inset 0 80px 48px -64px #00000000;
  --hidden-bottom-shadow: inset 0 -80px 48px -64px #00000000;
  animation: 1s linear both adjust-shadow;
  animation-timeline: scroll();
}
```

## Potential polyfill to allow experimentation

An implementation of a polyfill could be attempted to allow experimentation and exploration of the proposed feature, verifying whether it simplyfies dealing with overflow.
