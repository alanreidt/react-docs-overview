React summary

JSX

Consider this variable declaration:
const element = <h1>Hello, world!</h1>;

This funny tag syntax is neither a string nor HTML.
It is called JSX, and it is a syntax extension to JavaScript. We recommend using it with React to describe what the UI should look like. JSX may remind you of a template language, but it comes with the full power of JavaScript.

Why JSX?
React embraces the fact that rendering logic is inherently coupled with other UI logic: how events are handled, how the state changes over time, and how the data is prepared for display.
Instead of artificially separating technologies by putting markup and logic in separate files, React separates concerns with loosely coupled units called “components” that contain both.

Essence of JSX
Fundamentally, JSX just provides syntactic sugar for the React.createElement(component, props, ...children) function.
The JSX code:
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
compiles into:
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)

Embedding Expressions in JSX
You can put any valid JavaScript expression inside the curly braces in JSX.
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

But you cannot use statement inside JSX:
<div>
  // Will throw a Error
  {if (showHeader) <Header />}
  <Content />
</div>

Specifying Attributes with JSX
You may use quotes to specify string literals as attributes:
const element = <div tabIndex="0"></div>;
You may also use curly braces to embed a JavaScript expression in an attribute:
const element = <img src={user.avatarUrl}></img>;

When you pass a string literal, its value is HTML-unescaped. So these two JSX expressions are equivalent:
<MyComponent message="&lt;3" />
<MyComponent message={'<3'} />

Warning:
Since JSX is closer to JavaScript than to HTML, React DOM uses camelCase property naming convention instead of HTML attribute names.
For example, class becomes className in JSX, and tabindex becomes tabIndex.

JSX Prevents Injection Attacks
By default, React DOM escapes any values embedded in JSX before rendering them. Thus it ensures that you can never inject anything that’s not explicitly written in your application. Everything is converted to a string before being rendered.

Props Default to “True”
If you pass no value for a prop, it defaults to true. These two JSX expressions are equivalent:
<MyTextBox autocomplete />
<MyTextBox autocomplete={true} />

In general, we don’t recommend not passing a value for a prop, because it can be confused with the ES6 object shorthand {foo} which is short for {foo: foo} rather than {foo: true}. This behavior is just there so that it matches the behavior of HTML.

Spread Attributes
If you already have props as an object, and you want to pass it in JSX, you can use ... as a “spread” operator to pass the whole props object. These two components are equivalent:
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}

Children in JSX
In JSX expressions that contain both an opening tag and a closing tag, the content between those tags is passed as a special prop: props.children. 

JSX is an Expression Too
After compilation, JSX expressions become regular JavaScript function calls and evaluate to JavaScript objects.

JSX Represents Objects
Babel compiles JSX down to React.createElement() calls.

These two examples are identical:
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

React.createElement() performs a few checks to help you write bug-free code but essentially it creates an object like this:
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};

These objects are called “React elements”. You can think of them as descriptions of what you want to see on the screen. React reads these objects and uses them to construct the DOM and keep it up to date.
