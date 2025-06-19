# 🖱️ Event Handling in React

React makes it easy to handle user interactions — like clicks, typing, and submitting forms — using familiar HTML-like syntax, but with JavaScript functions.

---

## 📌 Event Syntax in React

React uses **camelCase event names** and **JSX functions**, not strings:

```jsx
<button onClick={handleClick}>Click me</button>
```

Compare with HTML:

```html
<button onclick="handleClick()">Click me</button>
<!-- HTML style -->
```

In React, you're not assigning a string — you're passing a **function reference**.

---

## 🧪 Example: Click Event

```jsx
function AlertButton() {
	function handleClick() {
		alert("Button was clicked!");
	}

	return <button onClick={handleClick}>Click me</button>;
}
```

---

## 🧾 Common Events

| Event          | Trigger                         |
| -------------- | ------------------------------- |
| `onClick`      | Mouse click                     |
| `onChange`     | Input value change              |
| `onSubmit`     | Form submission                 |
| `onMouseEnter` | Mouse enters an element         |
| `onKeyDown`    | Key press                       |
| `onFocus`      | Element receives keyboard focus |

---

## 🎯 Passing Arguments to Event Handlers

You can pass values by wrapping your handler in an arrow function:

```jsx
function ActionButton({ label }) {
	function handleClick(message) {
		alert(message);
	}

	return <button onClick={() => handleClick(`Clicked ${label}`)}>{label}</button>;
}
```

> Avoid calling the function directly (`onClick={handleClick()}`) — that runs immediately instead of waiting for a click!

---

## 🌀 Synthetic Events

React wraps browser events in **SyntheticEvent**, providing consistent behavior across browsers.

You can still access native events if needed:

```jsx
function LogEvent(e) {
	console.log(e); // React synthetic event
	console.log(e.nativeEvent); // Native browser event
}
```

---

## 🧼 Preventing Default Behavior

Use `event.preventDefault()` to stop default actions (like a page reload on form submission):

```jsx
function Form() {
	function handleSubmit(e) {
		e.preventDefault();
		console.log("Form submitted!");
	}

	return (
		<form onSubmit={handleSubmit}>
			<input type="text" />
			<button type="submit">Submit</button>
		</form>
	);
}
```

---

## 🧠 Summary

- React uses camelCase event names and JavaScript functions.
- Use arrow functions to pass arguments without triggering the handler immediately.
- React wraps browser events in synthetic objects for consistency.
- Prevent default browser behaviors explicitly in event handlers.

> Up next: handling **forms and controlled components** — how user input drives state updates and interactivity.
