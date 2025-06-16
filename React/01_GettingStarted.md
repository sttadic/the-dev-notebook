# ⚛️ Introduction to React

**React** is a JavaScript library for building user interfaces — primarily for web applications. Developed by Facebook (now Meta), it's used to create **interactive**, **efficient**, and **reusable** UI components.

Think of React as the “view” layer in the classic **Model-View-Controller (MVC)** architecture. It doesn't handle backend logic or database access — it's all about what the user _sees_ and _interacts_ with.

## ✨ Why Use React?

- **Component-Based**: Break the UI into independent, reusable pieces.
- **Declarative**: Describe what the UI should look like for a given state, and React takes care of updating it.
- **Virtual DOM**: React optimizes rendering by updating only the parts of the DOM that need changes.
- **Strong Ecosystem**: Tools like Next.js, React Native, and thousands of libraries build on top of React.

## 👁️ Example Use Case

Imagine a shopping cart on an e-commerce site:

- Each product listing? A component.
- The cart summary? Another component.
- The product filter sidebar? Yet another component.

React lets developers build and manage these pieces independently, keeping code cleaner and more maintainable.

> Now that we know _what_ React is, let’s dive into its basic building block: the **component**.

<br>

# 🧱 What Is a Component in React?

A **component** in React is a reusable, self-contained piece of UI. Think of it like a LEGO brick: small on its own, but when combined with others, it forms complex interfaces.

In React, everything starts with components — they’re the core building blocks of any app.

## ⚙️ Two Types of Components

React supports two main ways to define components:

- **Functional components** (modern and preferred)
- Class components (older, still supported)

We'll focus on functional components, which are written as JavaScript functions.

---

## 👋 Basic Functional Component Example

```jsx
function HelloWorld() {
	return <h1>Hello, world!</h1>;
}
```

### 🔍 How It Works:

- `HelloWorld` is a regular JavaScript function.
- It returns **JSX**, which looks like HTML but is actually a JavaScript syntax extension.
- The returned JSX tells React what the UI should look like.
- This component can be used like a custom tag: `<HelloWorld />`.

---

## 🔁 Components Are Reusable

You can use the same component in multiple places:

```jsx
function App() {
	return (
		<div>
			<HelloWorld />
			<HelloWorld />
		</div>
	);
}
```

Each time you use `<HelloWorld />`, it renders the same UI.

---

## 🧱 Nesting and Composing Components

Components can include other components — just like nesting functions.

```jsx
function Header() {
	return <h1>My App</h1>;
}

function Footer() {
	return <p>&copy; 2025</p>;
}

function App() {
	return (
		<div>
			<Header />
			<p>Welcome to the app.</p>
			<Footer />
		</div>
	);
}
```

This approach keeps your UI modular and easier to manage.

---

## 🧠 Why Components Matter

- They **promote reuse**: Build once, use anywhere.
- They **organize your UI**: Separate layout, logic, and behavior.
- They **scale**: Small apps or large ones — components work for both.

> Now that we’ve explored how components, we can move onto JSX.

<br>

# 🧬 Understanding JSX in React

**JSX (JavaScript XML)** is a syntax extension for JavaScript that allows you to write HTML-like code _directly inside JavaScript_. It’s used to define what your UI should look like — in a way that’s intuitive and expressive.

Under the hood, JSX gets transformed into regular JavaScript. For example:

```jsx
const element = <h1>Hello, world!</h1>;
```

...gets turned into:

```js
const element = React.createElement("h1", null, "Hello, world!");
```

So JSX is just _syntactic sugar_ that makes writing UI code more readable.

## 🔤 JSX Syntax Rules You Should Know

Here are a few key rules and quirks when writing JSX:

- **One root element**: Each component must return a single top-level element.

  ✅ Correct:

  ```jsx
  return (
  	<div>
  		<h1>Hi</h1>
  		<p>Welcome</p>
  	</div>
  );
  ```

  ❌ Incorrect:

  ```jsx
  return <h1>Hi</h1><p>Welcome</p>; // This will throw an error
  ```

- **Use `className` instead of `class`**: Because `class` is a reserved word in JavaScript.

  ```jsx
  return <div className="container">Hello</div>;
  ```

- **JavaScript expressions inside `{}`**: You can inject dynamic values using curly braces.

  ```jsx
  const name = "Stjepan";
  return <p>Hello, {name}!</p>;
  ```

- **Attributes are camelCase**: For non-standard HTML attributes like `onClick`, use camelCase.

  ```jsx
  return <button onClick={handleClick}>Click me</button>;
  ```

## 🤔 Why Not Just Use HTML?

Because JSX lives inside JavaScript, you get the _full power of programming logic_ right in your markup — loops, conditionals, variables, and more — all without switching contexts.

JSX makes components **declarative**, meaning they describe _what_ the UI should look like for a given state.

> With JSX in our toolkit, we’re ready to start building components that respond to data and user interaction — which brings us to **props and state**, the twin engines of React dynamics.

<br>

# 🔁 Rendering Logic in JSX

JSX isn’t just static markup — it’s powered by JavaScript! That means you can embed logic for **conditionally rendering elements**, **looping through data**, and even **dynamically modifying styles or content**.

Let’s explore the key patterns 👇

---

## ✅ Conditional Rendering

You can control whether something should be rendered using `if` statements, ternary operators, or short-circuit logic.

### 🔹 Option 1: Ternary Operator (commonly used)

```jsx
function Welcome({ isLoggedIn }) {
	return <div>{isLoggedIn ? <p>Welcome back!</p> : <p>Please log in</p>}</div>;
}
```

### 🔹 Option 2: Short-Circuit AND (`&&`)

```jsx
function Notifications({ count }) {
	return <div>{count > 0 && <p>You have {count} new notifications.</p>}</div>;
}
```

---

## 🔁 Looping Over Data with `map()`

To render a list of items, you'll often use JavaScript’s `.map()` function:

```jsx
const todos = ["Buy milk", "Feed cat", "Learn React"];

function TodoList() {
	return (
		<ul>
			{todos.map((task, index) => (
				<li key={index}>{task}</li>
			))}
		</ul>
	);
}
```

### 🧠 Why use `key`?

React uses the `key` attribute to track elements and optimize rendering performance. The key should be a unique and stable identifier for each item.

---

## 🧩 Bonus: Inline Style or Dynamic Content

```jsx
function Status({ online }) {
	const statusColor = online ? "green" : "gray";
	return <span style={{ color: statusColor }}>{online ? "Online" : "Offline"}</span>;
}
```

JSX lets you mix JavaScript expressions, logic, and UI seamlessly — keeping your components flexible and declarative.

> With these tools in hand, you can now build components that adapt to data, user actions, or application state — which sets us up perfectly to explore **props and state** next.
