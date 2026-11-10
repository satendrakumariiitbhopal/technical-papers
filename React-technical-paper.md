# React Fundamentals: A Technical Paper

**Author:** Satendra Kumar

## 2. JSX, Components and Props

### 1 JSX

JSX (JavaScript XML) is a syntax extension that lets you write markup-like code inside JavaScript. It is not HTML and it is not understood by browsers. It is a convenient notation that is later converted into ordinary JavaScript function calls.

```jsx
const name = "Asha";
const element = <h1 className="title">Hello, {name}!</h1>;
```

Key rules of JSX:

- Expressions go inside curly braces: `{name}`, `{price * 2}`, `{isLoggedIn ? "Logout" : "Login"}`.
- Attribute names follow camelCase and avoid reserved words: `className` instead of `class`, `htmlFor` instead of `for`, `onClick` instead of `onclick`.
- Every element must be closed, including self-closing ones such as `<img />` and `<input />`.
- A component must return a single root. When no wrapper element is wanted, a fragment (`<>...</>`) can be used.
- Because JSX escapes values before rendering them, it provides a built-in layer of protection against injecting unwanted markup.

### 2 Components

A component is a reusable piece of UI, written as a JavaScript function that returns JSX. Component names must start with a capital letter so that React can tell them apart from plain HTML tags.

```jsx
function Greeting() {
  return <p>Welcome to the dashboard.</p>;
}

function App() {
  return (
    <div>
      <Greeting />
      <Greeting />
    </div>
  );
}
```

Components can be nested, reused any number of times, and composed to form larger screens. This is how a complex page is reduced to many simple parts.

### 3 Props

Props (short for *properties*) are the inputs a component receives from its parent. They work like function arguments. Props are **read-only**: a component must never modify the props it receives.

```jsx
function ProductCard({ title, price }) {
  return (
    <div>
      <h3>{title}</h3>
      <p>Price: ₹{price}</p>
    </div>
  );
}

<ProductCard title="Keyboard" price={1499} />
```

Any JavaScript value can be a prop: strings, numbers, arrays, objects, functions and even other JSX. A special prop called `children` holds whatever is placed between a component's opening and closing tags, which makes wrapper components such as cards and layouts easy to build.

## 3. Babel and Its Role in React

Babel is a JavaScript compiler (more precisely, a transpiler). It reads source code and rewrites it into other JavaScript. In a React project it does two main jobs:

1. **Converts JSX into plain JavaScript.** Browsers cannot execute JSX, so it is transformed into function calls that create element descriptions.
2. **Converts modern JavaScript into compatible JavaScript.** Newer language features can be rewritten so that older browsers can run them.

For example, this JSX:

```jsx
const el = <button disabled>Save</button>;
```

is turned into something equivalent to:

```js
const el = jsx("button", { disabled: true, children: "Save" });
```

In older React versions the generated call was `React.createElement(...)`. Modern setups use an automatic runtime, so developers no longer need to import React in every file just to use JSX.

Babel works in three stages: it **parses** code into an abstract syntax tree (AST), **transforms** the tree using plugins, and **generates** new code from the changed tree. Presets such as the React preset bundle the required plugins. Today many build tools use faster compilers (for example SWC or esbuild) to perform the same JSX transformation, but the concept is the same: JSX must be compiled before the browser can run it.

## 4. Virtual DOM vs Real DOM

### 4.1 The Real DOM

The Document Object Model (DOM) is the browser's in-memory tree representation of a web page. JavaScript can read and change this tree, and the browser then recalculates layout and repaints. Directly changing the DOM many times is comparatively expensive because each change can trigger layout and paint work.

### 4.2 The Virtual DOM

The Virtual DOM is a lightweight JavaScript representation of the interface, kept in memory. It is a tree of plain objects describing what the UI should look like. It is not a browser feature; it is a technique React uses to decide what to change.

When state changes, React follows this cycle:

1. **Render:** components run again and produce a new Virtual DOM tree.
2. **Diff (reconciliation):** React compares the new tree with the previous one and works out the smallest set of differences.
3. **Commit:** only those differences are applied to the real DOM.

## 5. What is a Bundler?

A bundler is a build tool that takes a project's many source files and their dependencies and combines them into a small number of optimised files that a browser can load. A modern React app can consist of hundreds of modules plus third-party packages, and shipping them individually would be slow and difficult to manage.

A bundler typically performs these tasks:

- **Dependency resolution:** it follows `import` statements to build a graph of every file the app needs.
- **Bundling:** it merges modules into fewer files.
- **Transformation:** it processes JSX, TypeScript, CSS and static assets such as images.
- **Minification:** it removes whitespace and shortens names to reduce file size.
- **Tree shaking:** it removes exported code that is never used.
- **Code splitting:** it divides output into chunks so users download only what a page needs.
- **Development server:** it serves the app locally with fast refresh while coding.

Well-known bundlers include Webpack, Rollup, Parcel, esbuild and Vite (which uses a bundler internally for production builds).

## 6. Why Vite Instead of Create React App?

Create React App (CRA) was for years the standard way to start a React project. It hid a Webpack and Babel configuration behind a single command. However, it is no longer the recommended choice: the React team has deprecated it and now points developers to modern build tools and frameworks. Vite is a popular replacement for the following reasons.

1. **Fast development server.** Vite does not bundle the entire app before starting. It serves source files as native ES modules and transforms them on demand, so the server starts almost instantly even in large projects. CRA bundled everything with Webpack first, which grows slower as the app grows.
2. **Fast hot module replacement (HMR).** When a file changes, only that module is updated in the browser, usually in milliseconds.
3. **Efficient production builds.** Vite uses Rollup for production output, with tree shaking and code splitting enabled by default.
4. **Simple, flexible configuration.** A small `vite.config.js` file is enough and there is no "eject" step. CRA hid its configuration, and customising it often required ejecting or extra libraries.
5. **Active maintenance and ecosystem.** CRA is no longer actively developed, while Vite is regularly updated and supported by a large plugin ecosystem and many frameworks.
6. **Lighter dependencies.** A new Vite project installs fewer packages than a CRA project.

A new project is created with:

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

## 7. Folder Structure of a React (Vite) Boilerplate

A freshly generated Vite + React project looks roughly like this (exact files may vary slightly between versions):

```text
my-app/
├── node_modules/
├── public/
│   └── vite.svg
├── src/
│   ├── assets/
│   │   └── react.svg
│   ├── App.css
│   ├── App.jsx
│   ├── index.css
│   └── main.jsx
├── .gitignore
├── eslint.config.js
├── index.html
├── package.json
├── package-lock.json
├── README.md
└── vite.config.js
```

| Item | Purpose |
|---|---|
| `node_modules/` | Installed third-party packages. Generated by `npm install`; never committed to Git. |
| `public/` | Static files served as-is (favicon, robots.txt). They are not processed by the bundler. |
| `src/` | The application's source code. Almost all development happens here. |
| `src/main.jsx` | Entry point. It finds the root element in the HTML and mounts the React app into it. |
| `src/App.jsx` | The root component, from which the rest of the component tree grows. |
| `src/assets/` | Images and other files imported by components and processed by the bundler. |
| `src/*.css` | Global and component styles. |
| `index.html` | The single HTML page. It contains an empty `<div id="root">` and a script tag pointing to `main.jsx`. In Vite it sits at the project root. |
| `package.json` | Project metadata, dependencies and scripts such as `dev`, `build` and `preview`. |
| `package-lock.json` | Records exact installed dependency versions for reproducible installs. |
| `vite.config.js` | Vite configuration, including the React plugin. |
| `eslint.config.js` | Code-quality and linting rules. |

The entry point typically looks like this:

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import App from "./App.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

As a project grows, developers usually add folders inside `src/` such as `components/`, `pages/`, `hooks/`, `utils/` and `services/` to keep code organised.

## 8. Class Components vs Functional Components

React has two ways to define a component. Class components were the original approach. Functional components, combined with hooks, are now the recommended standard.

```jsx
// Class component
import { Component } from "react";

class Counter extends Component {
  state = { count: 0 };

  increment = () => this.setState({ count: this.state.count + 1 });

  render() {
    return <button onClick={this.increment}>Clicked {this.state.count}</button>;
  }
}
```

```jsx
// Functional component
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Clicked {count}</button>;
}
```

| Aspect | Class Component | Functional Component |
|---|---|---|
| Definition | ES6 class extending `Component` | Plain JavaScript function |
| State | `this.state` and `this.setState()` | `useState` hook |
| Side effects | Lifecycle methods (`componentDidMount`, `componentDidUpdate`, `componentWillUnmount`) | `useEffect` hook |
| `this` keyword | Required, and a common source of bugs | Not used |
| Boilerplate | More code | Less code |
| Reusing logic | Higher-order components or render props | Custom hooks |
| Rendering | `render()` method | Function body returns JSX |
| Current status | Still supported, but not recommended for new code | Recommended |

Functional components are shorter, easier to read and test, and allow related logic to be grouped together instead of being split across lifecycle methods. One exception remains: error boundaries can currently only be written as class components.

## 9. What are Hooks?

Hooks are special functions that let functional components use React features such as state and lifecycle behaviour. They are named with the prefix `use`.

Two rules apply to every hook:

1. **Call hooks only at the top level** of a component or custom hook, never inside loops, conditions or nested functions. React relies on the order of hook calls staying identical between renders.
2. **Call hooks only from React functions**: functional components or other custom hooks.

Built-in hooks include `useState`, `useEffect`, `useRef`, `useContext`, `useMemo`, `useCallback` and others. Developers can also write **custom hooks**, which are ordinary functions that call other hooks to share reusable logic between components.

## 10. Basic Hooks

### 10.1 `useState`

`useState` adds a piece of state to a component. It returns an array with two items: the current value and a function to update it. Calling the setter schedules a re-render with the new value.

```jsx
import { useState } from "react";

function NameInput() {
  const [name, setName] = useState("");

  return (
    <>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <p>Typed: {name}</p>
    </>
  );
}
```

Important points:

- The argument is the initial value and is used only on the first render.
- State should never be changed directly (`count = 5`). Always use the setter.
- When the new state depends on the previous one, use the functional form: `setCount((prev) => prev + 1)`. This guarantees the latest value is used even when several updates are batched.
- For objects and arrays, create a new copy instead of mutating the existing one: `setUser({ ...user, age: 30 })`. React detects changes by comparing references.

### 10.2 `useEffect`

`useEffect` lets a component run **side effects**, which are actions that reach outside rendering, such as fetching data, setting timers, subscribing to events or updating the document title. It runs after the component has rendered.

```jsx
import { useState, useEffect } from "react";

function Clock() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const id = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(id); // cleanup
  }, []); // dependency array

  return <p>{time.toLocaleTimeString()}</p>;
}
```

The **dependency array** controls when the effect runs:

| Dependency array | Behaviour |
|---|---|
| Omitted | Runs after every render |
| `[]` | Runs once, after the first render (on mount) |
| `[a, b]` | Runs after the first render and whenever `a` or `b` change |

The optional **cleanup function** returned from the effect runs before the effect re-runs and when the component unmounts. It prevents problems such as memory leaks, duplicate timers and stale subscriptions.

A typical data-fetching pattern:

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/users/${id}`, { signal: controller.signal })
    .then((res) => res.json())
    .then(setUser)
    .catch((err) => {
      if (err.name !== "AbortError") console.error(err);
    });

  return () => controller.abort();
}, [id]);
```

In development, `StrictMode` intentionally runs effects twice to reveal missing cleanup logic. This behaviour does not occur in production builds.

### 10.3 `useRef`

`useRef` returns a mutable object with a single property, `.current`, that persists for the full lifetime of the component. Changing `.current` does **not** trigger a re-render. It has two common uses.

**1. Accessing a DOM element directly:**

```jsx
import { useRef } from "react";

function SearchBox() {
  const inputRef = useRef(null);

  return (
    <>
      <input ref={inputRef} placeholder="Search..." />
      <button onClick={() => inputRef.current.focus()}>Focus</button>
    </>
  );
}
```

**2. Storing a value that must survive re-renders without causing one**, such as a timer ID, a previous value or a render counter.

| | `useState` | `useRef` |
|---|---|---|
| Persists across renders | Yes | Yes |
| Triggers re-render on change | Yes | No |
| Use for | Data shown on screen | DOM access and internal values not shown on screen |

## 11. Passing Data Between Components

The method depends on the relationship between the components.

### 11.1 Parent to child: props

This is the basic and most common pattern.

```jsx
function Parent() {
  return <Child message="Hello from parent" />;
}

function Child({ message }) {
  return <p>{message}</p>;
}
```

### 11.2 Child to parent: callback functions

Data does not flow upward on its own. The parent passes a function as a prop, and the child calls it with the data.

```jsx
function Parent() {
  const [reply, setReply] = useState("");
  return (
    <>
      <Child onReply={setReply} />
      <p>Child said: {reply}</p>
    </>
  );
}

function Child({ onReply }) {
  return <button onClick={() => onReply("Hi parent!")}>Reply</button>;
}
```

### 11.3 Between sibling components

Siblings cannot talk to each other directly. The data is moved to their closest common parent, which holds the state and passes it down to both (see Section 12).

### 11.4 Across many levels: Context API

Passing props through many intermediate components (called *prop drilling*) becomes tedious. The Context API lets a value be provided once near the top of the tree and read by any descendant using `useContext`.

```jsx
import { createContext, useContext } from "react";

const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const theme = useContext(ThemeContext);
  return <p>Current theme: {theme}</p>;
}
```

### 11.5 Large applications: state libraries

For complex global state, dedicated libraries such as Redux Toolkit, Zustand or Jotai are commonly used.

| Situation | Recommended approach |
|---|---|
| Parent → child | Props |
| Child → parent | Callback props |
| Siblings | Lift state to common parent |
| Many levels apart | Context API |
| App-wide, complex state | State management library |

## 12. Lifting State Up

**Lifting state up** means moving state from a child component to the nearest common ancestor of all components that need it. The ancestor becomes the single owner of the data and passes it down through props, along with functions that allow children to request changes.

Consider two components that must always show the same temperature value. If each keeps its own state, they will drift out of sync. The solution is to keep the value in the parent:

```jsx
function TemperatureApp() {
  const [celsius, setCelsius] = useState(0);

  return (
    <>
      <CelsiusInput value={celsius} onChange={setCelsius} />
      <FahrenheitDisplay value={celsius * 9 / 5 + 32} />
    </>
  );
}

function CelsiusInput({ value, onChange }) {
  return (
    <input
      type="number"
      value={value}
      onChange={(e) => onChange(Number(e.target.value))}
    />
  );
}

function FahrenheitDisplay({ value }) {
  return <p>{value.toFixed(1)} °F</p>;
}
```

Benefits of this pattern:

- **Single source of truth:** there is only one copy of the data, so components cannot disagree.
- **Predictable data flow:** values go down, change requests go up.
- **Derived values stay derived:** Fahrenheit is calculated from Celsius instead of being stored separately.

A practical rule is to keep state as close as possible to where it is used, and lift it only when another component needs access to it. If the value must be lifted very high and passed through many layers, Context becomes a better option.

## 13. Using Event Listeners in Components

React handles user interactions through **event handlers** attached directly in JSX. They are written in camelCase and receive a function, not a string.

```jsx
function LikeButton() {
  const [likes, setLikes] = useState(0);

  function handleClick() {
    setLikes(likes + 1);
  }

  return <button onClick={handleClick}>Likes: {likes}</button>;
}
```

Rules and details:

- **Pass the function, do not call it.** `onClick={handleClick}` is correct. `onClick={handleClick()}` runs the function during rendering. To pass an argument, wrap it: `onClick={() => deleteItem(id)}`.
- **Synthetic events.** React gives handlers a cross-browser wrapper around the native event with the familiar interface, including `e.target`, `e.preventDefault()` and `e.stopPropagation()`.
- **Common events:** `onClick`, `onChange`, `onSubmit`, `onKeyDown`, `onMouseEnter`, `onFocus`, `onBlur`.
- **Forms:** call `e.preventDefault()` in `onSubmit` to stop the browser's default page reload.

```jsx
function LoginForm() {
  const [email, setEmail] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    console.log("Submitting", email);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      <button type="submit">Login</button>
    </form>
  );
}
```

### Listeners on `window` or `document`

Some events, such as window resize, global key presses or scrolling, are not attached to a JSX element. These are registered inside `useEffect`, and **must be removed in the cleanup function**:

```jsx
function WindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const onResize = () => setWidth(window.innerWidth);
    window.addEventListener("resize", onResize);
    return () => window.removeEventListener("resize", onResize);
  }, []);

  return <p>Width: {width}px</p>;
}
```

Without the cleanup step, every time the component is mounted a new listener would be added and never removed, causing memory leaks and unexpected behaviour.

## 14. Conclusion

React simplifies interface development by treating the UI as a function of state, built from small reusable components and updated efficiently through the Virtual DOM. Supporting tools complete the picture: Babel (or a similar compiler) turns JSX into executable JavaScript, and a bundler such as Vite prepares the code for the browser, offering a much faster development experience than the retired Create React App workflow. Functional components with hooks are the modern standard, with `useState` for data, `useEffect` for side effects and `useRef` for DOM access and persistent values. Data flows down through props, changes flow up through callbacks, shared state is lifted to a common parent, and events are handled declaratively in JSX. Mastering these fundamentals provides a strong base for advanced topics such as routing, global state management, performance optimisation and server-side rendering.
