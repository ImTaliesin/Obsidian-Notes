[[React JS]]

1. **Use Refs When:**
    
    - **Managing Focus, Text Selection, or Media Control**: You need to explicitly focus an input, select text, or control media playback.
    - **Integrating with Third-Party DOM Libraries**: Libraries that operate directly on the DOM, like jQuery plugins or D3.js, often need direct access to DOM elements.
    - **Performing Animations**: If you're doing complex animations that are difficult to achieve with CSS transitions or React state.
    - **Measuring or Reading Values from the DOM**: When you need to read dimensions, positions, or check the state of an element (like scroll position).
    - **Imperative Actions**: Actions that are not tied to React's state or lifecycle methods, like triggering focus or clearing a form field.
2. **Use Props When:**
    
    - **Passing Data to Components**: Anytime you need to pass data down the component tree.
    - **Handling Events**: For event handling, such as click, hover, or change events.
    - **Controlling Components**: When you need to control what is rendered, such as conditional rendering or toggling component visibility.
    - **Sharing State Between Components**: Props are ideal for sharing state and functions between parent and child components.
3. **Key Differences to Remember:**
    
    - **Props are for React's Declarative Nature**: They fit into React's design of declarative and component-based programming, allowing for more predictable and easier-to-debug code.
    - **Refs are for Imperative Escape Hatches**: They provide a way to escape React's declarative paradigm and interact with DOM elements directly, which is occasionally necessary but should be used sparingly.