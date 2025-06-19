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

<br>

# 📝 Controlled Components and Forms

In React, inputs don't manage their own state like in plain HTML. Instead, we keep them in sync with **component state** — this pattern is known as a **controlled component**.

---

## 🎛️ What Is a Controlled Component?

A controlled component is a form element (like `<input>` or `<textarea>`) whose value is **driven by React state**.

### 🔹 Example: Controlled Text Input

```jsx
function NameForm() {
	const [name, setName] = useState("");

	return (
		<div>
			<label>
				Name:
				<input type="text" value={name} onChange={(e) => setName(e.target.value)} />
			</label>
			<p>Hello, {name || "friend"}!</p>
		</div>
	);
}
```

### ⚙️ Key Parts:

- `value={name}` binds the input’s value to React state.
- `onChange={...}` updates the state whenever the user types.

---

## 🧪 Controlled Form Example

```jsx
function SignupForm() {
	const [email, setEmail] = useState("");
	const [password, setPassword] = useState("");

	function handleSubmit(e) {
		e.preventDefault();
		console.log("Email:", email);
		console.log("Password:", password);
	}

	return (
		<form onSubmit={handleSubmit}>
			<input type="email" value={email} onChange={(e) => setEmail(e.target.value)} placeholder="Email" />
			<input type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="Password" />
			<button type="submit">Sign up</button>
		</form>
	);
}
```

All input values are controlled via state. The form won’t submit unless you call `e.preventDefault()` and manage what happens manually.

---

## 🧠 Benefits of Controlled Forms

- Instant access to user input (no need to query the DOM)
- Real-time validation and formatting
- Ensures **single source of truth** for input state

---

## 🎮 When to Use Uncontrolled Inputs?

Uncontrolled components (using `ref`) are useful when:

- You don’t need to validate or manipulate input during typing
- You’re integrating with non-React libraries
- You need very simple input without rerenders

But for most React workflows — **controlled inputs are the standard**.

> Up next: how to manage multiple form fields, validate inputs, and maybe explore checkboxes, radios, selects and textareas — or we can move on to the next React topic if you're happy with event handling!
