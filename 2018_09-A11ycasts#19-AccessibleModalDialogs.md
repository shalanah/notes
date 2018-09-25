# Accessible Modal Dialogs
Notes for video [A11ycasts#19](https://www.youtube.com/watch?v=JS68faEUduk)

Before starting looks at:
- [ARIA Authoring Practices: Dialog](https://goo.gl/ibNKWw)
- [eBay MIND Patterns: Dialog](https://goo.gl/FI5NHa) - Will be using a lot of the code and patterns from this doc

## Behaviors
### Opening
- Focus is moved to first focusable element in dialog
### Closing
- On click of btns
- On ESC
- On background mask
### After closing
- Return previous active element to active state

## Tasks & Code
### Previous active element
- Keep reference to this before mounting modal so we can return to this when modal is closed 
- `document.activeElement`
### Role & Aria
Top Level Div
- `role="dialog"`
- `aria-labelledby="{{IDHERE}}"` That matches the title text
### Make everything but the dialog inert
- Don't want any actions on anything on the page to steal focus from the dialog
- Markup: make the modal a top level sibling, immediate child of `<body>`. Trying to make sure we cannot lose focus to the rest of the page when in a modal.
```js
Array.from(document.body.children).forEach(child => {
  if (child !== dialog) child.inert = true
})
```
- `inert` is a proposed concept, not a real property
- [Inert Polyfill](https://goo.gl/nXMS1V)
  - Seems to apply `user-select: none` and `pointer-events: none` via css prop `[inert]`
### Apply focus to first button in dialog
```js
dialog.querySelector('button').focus()
```
### Tips
- Create / destroy modals or `display: none`. Do not hide off screen because screen readers will still have access
- Good idea to do `position: fixed` especially if page can be scrolled and do an `overflow: hidden` to prevent scrolling.
- Suggest allowing user to tab in the modal and up to url (not just trapped in modal)
## Future
- `inert`
- `dialog` html element only available in Chrome and FF when enabled
