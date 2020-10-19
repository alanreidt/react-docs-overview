# React documentation overview

## JSX
Consider this variable declaration:
```jsx
const element = <h1>Hello, world!</h1>;
```

This funny tag syntax is neither a string nor HTML.
It is called JSX, and it is a syntax extension to JavaScript. We recommend using it with React to describe what the UI should look like.

### Why JSX?
React embraces the fact that rendering logic is inherently coupled with other UI logic: how events are handled, how the state changes over time, and how the data is prepared for display.

Instead of artificially separating technologies by putting markup and logic in separate files, React separates concerns with loosely coupled units called “components” that contain both.

### Essence of JSX
Fundamentally, JSX just provides syntactic sugar for the `React.createElement(component, props, ...children)` function.

The JSX code:
```jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```

Compiles into:
```jsx
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```

### Embedding Expressions in JSX
You can put any valid JavaScript expression inside the curly braces in JSX.
```jsx
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;
```

But you cannot use statements inside JSX:
```jsx
<div>
  // Will throw a Error
  {if (showHeader) <Header />}
  <Content />
</div>
```

### Specifying Attributes with JSX
You may use quotes to specify string literals as attributes:
```jsx
const element = <div tabIndex="0"></div>;
```

You may also use curly braces to embed a JavaScript expression in an attribute:
```jsx
const element = <img src={user.avatarUrl}></img>;
```

When you pass a string literal, its value is HTML-unescaped. So these two JSX expressions are equivalent:
```jsx
<MyComponent message="&lt;3" />
<MyComponent message={'<3'} />
```

> **Warning:**
>
> Since JSX is closer to JavaScript than to HTML, React DOM uses `camelCase` property naming convention instead of HTML attribute names.
>
> For example, `class` becomes `className` in JSX, and `tabindex` becomes `tabIndex`.

### JSX Prevents Injection Attacks
By default, React DOM escapes any values embedded in JSX before rendering them. Thus it ensures that you can never inject anything that’s not explicitly written in your application. Everything is converted to a string before being rendered.

### Props Default to “True”
If you pass no value for a prop, it defaults to true. These two JSX expressions are equivalent:
```jsx
<MyTextBox autocomplete />
<MyTextBox autocomplete={true} />
```

In general, we don’t recommend not passing a value for a prop, because it can be confused with the ES6 object shorthand `{foo}` which is short for `{foo: foo}` rather than `{foo: true}`. This behavior is just there so that it matches the behavior of HTML.

### Spread Attributes
If you already have props as an object, and you want to pass it in JSX, you can use `...` as a “spread” operator to pass the whole props object. These two components are equivalent:
```jsx
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};

  return <Greeting {...props} />;
}
```

### Children in JSX
In JSX expressions that contain both an opening tag and a closing tag, the content between those tags is passed as a special prop: `props.children`.

There are several different ways to pass children:

- You can use a string as props.children:
  ```jsx
  <MyComponent>Hello world!</MyComponent>
  ```

- JSX components:
  ```jsx
  <MyContainer>
    <MyFirstComponent />
    <MySecondComponent />
  </MyContainer>
  ```

- Or mix of both:
  ```jsx
  <div>
    Here is a list:
    <ul>
      <li>Item 1</li>
      <li>Item 2</li>
    </ul>
  </div>
  ```

A React component can also return an array of elements:
```jsx
render() {
  // No need to wrap list items in an extra element!
  return [
    // Don't forget the keys :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

### JavaScript Expressions as Children
You can pass any JavaScript expression as children, by enclosing it within `{}`.
This is often useful for rendering a list of JSX expressions of arbitrary length.

For example, this renders an HTML list:
```jsx
function Item(props) {
  return <li>{props.message}</li>;
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];

  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  );
}
```

### Functions as Children
Normally, JavaScript expressions inserted in JSX will evaluate to a string, a React element, or a list of those things. However, props.children works just like any other prop in that it can pass any sort of data, not just the sorts that React knows how to render.

For example, if you have a custom component, you could have it take a callback as `props.children`:
```jsx
// Calls the children callback numTimes to produce a repeated component
function Repeat(props) {
  let items = [];

  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }

  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
```

Children passed to a custom component can be anything, as long as that component transforms them into something React can understand before rendering. This usage is not common, but it works if you want to stretch what JSX is capable of.

### Booleans, Null, and Undefined Are Ignored
`false`, `null`, `undefined`, and `true` are valid children. They simply don’t render.

This can be useful to conditionally render React elements:
```jsx
<div>
  {showHeader && <Header />}
  <Content />
</div>
```

One caveat is that some “falsy” values, such as the `0` number, are still rendered by React:
```jsx
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```

To fix this, make sure that the expression before `&&` is always boolean:
```jsx
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```

### JSX is an Expression Too
After compilation, JSX expressions become regular JavaScript function calls and evaluate to JavaScript objects.

### JSX Represents Objects
Babel compiles JSX down to `React.createElement()` calls.

These two examples are identical:
```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);

const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()` performs a few checks to help you write bug-free code, but essentially it creates an object like this:
```jsx
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

These objects are called “React elements”. You can think of them as descriptions of what you want to see on the screen. React reads these objects and uses them to construct the DOM and keep it up to date.
