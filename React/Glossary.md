# 🧠 React Glossary

A quick reference to common React terms and what they really mean — no fluff.

---

## ⚛️ React

A JavaScript library for building user interfaces, focused on building reusable UI components.

---

## 🧱 Component

A self-contained piece of UI. Functional components are just JavaScript functions that return JSX.

---

## 🧬 JSX (JavaScript XML)

A syntax extension that allows HTML-like markup in JavaScript. It’s not required, but heavily used in React to describe UI.

---

## 🎁 Props

Short for "properties" — inputs passed into components to customize behavior or display. Read-only from the child’s perspective.

---

## 🧠 State

Internal memory of a component. State changes trigger re-renders and can reflect user input or time-based updates.

---

## 🪞 Virtual DOM

A lightweight JavaScript representation of the real DOM. React uses it to figure out the minimal set of changes needed and applies only those — for optimal performance.

---

## 🔁 Reconciliation

React’s process of comparing the current virtual DOM with the new one (after a state or props change) and applying the smallest possible updates to the real DOM.

---

## 🚦 Controlled Component

A form input (like `<input>` or `<textarea>`) whose value is controlled by React via state:

```jsx
<input value={text} onChange={(e) => setText(e.target.value)} />
```

The source of truth is React state.

---

## 🎮 Uncontrolled Component

The opposite of controlled: input maintains its own internal state and is accessed via a ref when needed.

```jsx
<input ref={inputRef} />
```

Used when you don’t need real-time updates or full control over the input.

---

## 🎣 Hook

Special functions that let you “hook into” React features from functional components. `useState`, `useEffect`, and `useRef` are common examples.

---

## 🪝 Custom Hook

A function starting with `use` that combines one or more built-in hooks to encapsulate reusable logic.

---

## 🔝 Lifting State

Moving state up to the nearest common parent so that multiple child components can access and share it.

---

## 🌀 Effect (useEffect)

Code that runs as a **side effect** of rendering — like fetching data, setting up subscriptions, timers, etc.

---

## 🔄 React Lifecycle

The **React lifecycle** refers to the sequence of phases a component goes through from creation to removal. In functional components, we simulate lifecycle events using **Hooks** (like `useEffect`), whereas class components use dedicated lifecycle methods.

---

### 🧪 Mounting (Component Initialization)

Occurs when a component is inserted into the DOM.

- In functional components:
  Use `useEffect(() => { ... }, [])`
  (runs once after the first render — similar to `componentDidMount` in class components)

```jsx
useEffect(() => {
	console.log("Component mounted");
}, []);
```

---

### 🔁 Updating (Re-render Due to Props/State)

Happens when props or state change.

- Use `useEffect(() => { ... }, [value])`
  (runs after the first render and whenever `value` changes)

```jsx
useEffect(() => {
	console.log("Value changed:", value);
}, [value]);
```

---

### ❌ Unmounting (Cleanup Before Component Is Removed)

Happens when a component is removed from the DOM.

- Use a **cleanup function** inside `useEffect`

```jsx
useEffect(() => {
	// Setup
	return () => {
		console.log("Component unmounted");
	};
}, []);
```

This mimics `componentWillUnmount` from class components.

---

### 🧠 Full Analogy (Functional vs Class Components)

| Phase   | Class Method           | Functional Hook Equivalent           |
| ------- | ---------------------- | ------------------------------------ |
| Mount   | `componentDidMount`    | `useEffect(() => {}, [])`            |
| Update  | `componentDidUpdate`   | `useEffect(() => {}, [dep])`         |
| Unmount | `componentWillUnmount` | `return () => { ... }` inside effect |

---

Functional components don’t have lifecycle _methods_, but you can think of **`useEffect` as the window into the lifecycle world**.

```jsx
useEffect(() => {
	console.log("Mounted or updated");

	return () => {
		console.log("Cleanup before unmount or re-run");
	};
});
```

The behavior changes based on the **dependency array**:

- `[]` → runs once on mount
- `[dep]` → runs when `dep` changes
- _(no array)_ → runs on every render

---

> Understanding the lifecycle helps you manage side effects, timers, subscriptions, and cleanups effectively — all while keeping components predictable.

## 🧼 Cleanup Function

Returned from a `useEffect` to clean up after side effects (like removing event listeners or stopping timers).

```jsx
useEffect(() => {
	// setup
	return () => {
		// cleanup
	};
}, []);
```

---

## 📦 Context

A way to share data between components **without** passing props at every level. Often used for themes, auth, or app-wide settings.

---

## 🔄 Re-render

What happens when a component's state or props change — React recalculates the JSX and updates the DOM accordingly.

---

## 🧪 Key Prop

A special prop used in lists to help React identify which items changed. Should be unique and stable.

---

## ⚙️ useRef

A Hook used to hold a mutable value or directly reference a DOM element — without triggering re-renders.

---

## 🧩 Higher-Order Component (HOC)

A function that takes a component and returns a **new component with enhanced behavior**. Used to reuse logic like logging, authentication, or styling.

```jsx
function withLogger(Component) {
	return function (props) {
		console.log("Rendering", Component.name);
		return <Component {...props} />;
	};
}
```

Used like: `const Enhanced = withLogger(MyComponent);`

---

## ⚛️ ReactDOM vs React

- **React**: The core library for defining components, state, and JSX.
- **ReactDOM**: Responsible for rendering React components to the browser DOM.

You often import them separately:

```js
import React from "react";
import ReactDOM from "react-dom/client";
```

---

## 🔀 Fragment (`<></>`)

A shorthand wrapper for grouping multiple JSX elements **without** adding an extra DOM node:

```jsx
return (
	<>
		<h1>Hello</h1>
		<p>This is grouped</p>
	</>
);
```

Equivalent to `<React.Fragment>`.

---

## 🧃 Controlled vs Uncontrolled Component

- **Controlled**: Form data is handled by React via state.
- **Uncontrolled**: DOM handles its own state, accessed via `ref`.

Use controlled components when you need full control or validation.

---

## 🐣 useEffect Dependency Array

Determines **when** an effect runs:

- `[]`: Runs only on mount.
- `[value]`: Runs when `value` changes.
- Omitted: Runs after every render (rarely desired).

Keeps side effects predictable and performance-friendly.

---

Let me know if you'd like a reference entry on `useMemo`, `useCallback`, or `refs` next — or we can flow right into the event handling chapter. You’re really shaping this up like a pro! 📘⚛️

---
