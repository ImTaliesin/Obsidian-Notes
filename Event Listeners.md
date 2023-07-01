Here is a list of some commonly used jQuery event handling methods with a short description:

1. `click()`: Binds a function to be executed when the click event is fired.
2. `dblclick()`: Binds a function to be executed when the double click event is fired.
3. `mouseenter()`: Binds a function to be executed when the mouse enters the element.
4. `mouseleave()`: Binds a function to be executed when the mouse leaves the element.
5. `hover()`: Takes two functions and is a shorthand for `mouseenter()` and `mouseleave()`.
6. `mousedown()`: Binds a function to be executed when the mouse button is pressed down within the element.
7. `mouseup()`: Binds a function to be executed when the mouse button is released within the element.
8. `mousemove()`: Binds a function to be executed when the mouse is moved within the element.
9. `keydown()`: Binds a function to be executed when a key is pressed down.
10. `keyup()`: Binds a function to be executed when a key is released.
11. `keypress()`: Binds a function to be executed when a key is pressed and released.
12. `submit()`: Binds a function to be executed when the form is submitted.
13. `change()`: Binds a function to be executed when the element changes.
14. `focus()`: Binds a function to be executed when the element gets focus.
15. `blur()`: Binds a function to be executed when the element loses focus.
16. `on()`: Attaches one or more event handlers for specified events to selected elements.
17. `off()`: Removes one or more event handlers set with the `.on()` method.
18. `bind()`: Attach a handler to an event for the elements. (Note: as of jQuery 3.0, `bind()` has been deprecated. It is recommended to use `on()`)
19. `unbind()`: Remove a previously-attached event handler from the elements. (Note: as of jQuery 3.0, `unbind()` has been deprecated. It is recommended to use `off()`)
20. `trigger()`: Execute all handlers and behaviors attached to the matched elements for the given event type.
21. `one()`: Attach a handler to an event for the elements. The handler is executed at most once per element per event type.

This list isn't exhaustive. There are many more event methods available in jQuery, including shortcuts for combining certain events and methods for dealing with custom events.