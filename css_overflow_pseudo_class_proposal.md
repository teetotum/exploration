# proposal for new css pseudo class :overflow (-top, -right, -bottom, -left)

There are valid use-cases for which the UI must react to an occurring oveflow (beyond just showing a scrollbar).
Currently those cases always require a javascript solution.
The required code to deal with such use-cases could be simplified drastically if the fact that an overflow occurred was accessible in the css.
A new css pseudo class `:overflow-<side>` would satisfy this need.

## proposed mechanism

The following css pseudo class selectors are to be added to the standard:

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

## use-case examples

### indicate content scrolled out-of-sight due to overflow

For text content for which it is important that the user is aware of potentially concealed parts of the text due to overflow we can show a shadow overlaying the content towards the edge where the overflow occurs.

- Link to example implementation, using javascript
- Link to example implementation, using proposed pseudo class

### show custom controls to enable scrolling when a scrollbar is undesired

When the design omits the default scrollbar but offers custom buttons to scroll the content we want to a) display the buttons only when there is actually an overflow and b) enable a button to receive clicks only when there is scrollable area in the respective direction.

- Link to example implementation, using javascript
- Link to example implementation, using proposed pseudo class

## roadblocks

- tbd
- explain problem of cyclic dependencies of styles causing overflow which causes new styles to be applied which affects overflow, potetially causing an infinite cycle
- link to related [discussion in wicg discourse](https://discourse.wicg.io/t/add-truncated-to-css-pseudo-selector-specification/621)

## alternatives

- tbd
- could be solved with CSS-only via experimental CSS features: @scroll-timeline / scroll-linked-animation
- but pseudo class would be more straight-forward

## potetial polyfill to allow tryout

- tbd
- a css polyfill could be created to allow experimentation and exploration of the proposed feature
