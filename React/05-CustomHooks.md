# 🪝 Custom Hooks in React

A **custom Hook** is a function that lets you **extract reusable stateful logic** from components — combining the power of built-in Hooks (`useState`, `useEffect`, `useRef`, etc.) into your own tailored abstraction.

You can think of it as a tiny utility that’s:

- Easy to test
- Easy to reuse
- Keeps components clean and focused

---

## 🔧 How to Create a Custom Hook

- Must **start with `use`** (e.g. `useMousePosition`, `useForm`)
- Can use **other Hooks** inside (like `useState`, `useEffect`)
- Returns data or functions to be used in the component

---

## 🧪 Example: useWindowWidth

```jsx
import { useState, useEffect } from "react";

function useWindowWidth() {
	const [width, setWidth] = useState(window.innerWidth);

	useEffect(() => {
		function handleResize() {
			setWidth(window.innerWidth);
		}

		window.addEventListener("resize", handleResize);
		return () => window.removeEventListener("resize", handleResize);
	}, []);

	return width;
}
```

You can now use it like this:

```jsx
function Banner() {
	const width = useWindowWidth();

	return <p>Window width: {width}px</p>;
}
```

---

## 🎛️ Example: useToggle

```jsx
function useToggle(initial = false) {
	const [value, setValue] = useState(initial);
	const toggle = () => setValue((v) => !v);

	return [value, toggle];
}
```

Usage:

```jsx
const [isOpen, toggleOpen] = useToggle();
```

Perfect for modals, dropdowns, or toggling booleans.

---

## 🔗 Benefits of Custom Hooks

- 🧼 **Cleaner components**: your UI files are easier to scan and reason about
- ♻️ **Reusable logic**: use across multiple components and even projects
- 🧪 **Easier to test**: logic is isolated in small functions
- 🧩 **Composable**: custom hooks can call other custom hooks!

---

## 🚫 What Not to Do

- Don't call Hooks conditionally or in loops (follows same Rules of Hooks)
- Don’t mix UI and side effects in the hook — keep hooks **logic-focused**

---

> Next up: building your own `useForm()` hook with state and validation - You’re now building _React primitives_. 💡🛠️⚛️

<br>

# 📝 useForm – Managing Form State Easily

Forms often need you to:

- Track the value of multiple fields
- Update state on change
- Reset values on submit
- Field-level validation during typing or submission

Let’s build a `useForm` hook that does just that!

---

## 🔧 Custom Hook: `useForm`

```jsx
import { useState } from "react";

function useForm(initialValues, validate) {
	const [values, setValues] = useState(initialValues);
	const [errors, setErrors] = useState({});

	function handleChange(e) {
		const { name, value } = e.target;

		setValues((prev) => ({ ...prev, [name]: value }));

		if (validate) {
			const validationErrors = validate({ ...values, [name]: value });
			setErrors(validationErrors);
		}
	}

	function handleSubmit(callback) {
		return (e) => {
			e.preventDefault();
			const validationErrors = validate(values);
			setErrors(validationErrors);

			const isValid = Object.keys(validationErrors).length === 0;
			if (isValid) {
				callback(values);
			}
		};
	}

	function resetForm() {
		setValues(initialValues);
		setErrors({});
	}

	return { values, errors, handleChange, handleSubmit, resetForm };
}
```

---

## 📦 Usage Example: Contact Form with Validation

```jsx
function validateContact(values) {
	const errors = {};
	if (!values.name.trim()) errors.name = "Name is required";
	if (!values.email.includes("@")) errors.email = "Email is invalid";
	if (!values.message.trim()) errors.message = "Message cannot be empty";
	return errors;
}

function ContactForm() {
	const { values, errors, handleChange, handleSubmit, resetForm } = useForm(
		{ name: "", email: "", message: "" },
		validateContact
	);

	function onSubmit(data) {
		console.log("Submitted:", data);
		resetForm();
	}

	return (
		<form onSubmit={handleSubmit(onSubmit)}>
			<div>
				<input name="name" value={values.name} onChange={handleChange} placeholder="Name" />
				{errors.name && <p style={{ color: "red" }}>{errors.name}</p>}
			</div>

			<div>
				<input name="email" value={values.email} onChange={handleChange} placeholder="Email" />
				{errors.email && <p style={{ color: "red" }}>{errors.email}</p>}
			</div>

			<div>
				<textarea name="message" value={values.message} onChange={handleChange} placeholder="Message" />
				{errors.message && <p style={{ color: "red" }}>{errors.message}</p>}
			</div>

			<button type="submit">Send</button>
		</form>
	);
}
```

---

## 🔍 What This Custom Hook Does

- Tracks form `values` and `errors`
- Calls a `validate()` function on each change or submission
- Prevents submit if validation fails
- Supports external `onSubmit` handler
- Keeps your component code clean and declarative

---

<br>

# 🧠 useForm – With Blur-Based Validation and Real-Time Feedback

This version extends our form hook to include:

- `touched` tracking per field
- `handleBlur()` for marking a field as visited
- Revalidation on blur and input change
- Cleaner UX: no instant errors on first load

---

## 🔧 Hook: `useFormWithBlurValidation`

```jsx
import { useState } from "react";

function useForm(initialValues, validate) {
	const [values, setValues] = useState(initialValues);
	const [errors, setErrors] = useState({});
	const [touched, setTouched] = useState({});

	function handleChange(e) {
		const { name, value } = e.target;
		setValues((prev) => ({ ...prev, [name]: value }));

		if (touched[name]) {
			const validationErrors = validate({ ...values, [name]: value });
			setErrors(validationErrors);
		}
	}

	function handleBlur(e) {
		const { name } = e.target;
		setTouched((prev) => ({ ...prev, [name]: true }));

		const validationErrors = validate(values);
		setErrors(validationErrors);
	}

	function handleSubmit(callback) {
		return (e) => {
			e.preventDefault();
			const validationErrors = validate(values);
			setErrors(validationErrors);
			setTouched(
				Object.keys(values).reduce((acc, key) => {
					acc[key] = true;
					return acc;
				}, {})
			);

			if (Object.keys(validationErrors).length === 0) {
				callback(values);
			}
		};
	}

	function resetForm() {
		setValues(initialValues);
		setErrors({});
		setTouched({});
	}

	return {
		values,
		errors,
		touched,
		handleChange,
		handleBlur,
		handleSubmit,
		resetForm,
	};
}
```

---

## 📦 Usage Example

```jsx
function validate(values) {
	const errors = {};
	if (!values.name.trim()) errors.name = "Name is required";
	if (!values.email.includes("@")) errors.email = "Invalid email address";
	return errors;
}

function SignupForm() {
	const { values, errors, touched, handleChange, handleBlur, handleSubmit, resetForm } = useForm(
		{ name: "", email: "" },
		validate
	);

	function onSubmit(data) {
		console.log("Submitted:", data);
		resetForm();
	}

	return (
		<form onSubmit={handleSubmit(onSubmit)}>
			<div>
				<input name="name" placeholder="Name" value={values.name} onChange={handleChange} onBlur={handleBlur} />
				{touched.name && errors.name && <span style={{ color: "red" }}>{errors.name}</span>}
			</div>

			<div>
				<input name="email" placeholder="Email" value={values.email} onChange={handleChange} onBlur={handleBlur} />
				{touched.email && errors.email && <span style={{ color: "red" }}>{errors.email}</span>}
			</div>

			<button type="submit">Sign up</button>
		</form>
	);
}
```

---

## 🧠 Takeaways

| Feature          | Purpose                                   |
| ---------------- | ----------------------------------------- |
| `touched` state  | Avoids showing errors until user blurs    |
| `onBlur` handler | Triggers validation only when appropriate |
| `validate()`     | Keeps your form logic consistent & clean  |

---

> Want to push further with dynamic field arrays, async validation (e.g. checking username availability), or even Yup integration? We can evolve this into a real form engine whenever you're ready.

<br>

# 💾 useLocalStorage – Persist State Across Sessions

This custom Hook helps you:

- Read from and write to `localStorage`
- Keep React state synced with the storage
- Survive page reloads

---

## 🔧 Hook: `useLocalStorage(key, initialValue)`

```jsx
import { useState, useEffect } from "react";

function useLocalStorage(key, initialValue) {
	const [value, setValue] = useState(() => {
		const stored = localStorage.getItem(key);
		return stored !== null ? JSON.parse(stored) : initialValue;
	});

	useEffect(() => {
		localStorage.setItem(key, JSON.stringify(value));
	}, [key, value]);

	return [value, setValue];
}
```

- Reads from localStorage on initial render
- Writes to localStorage when the `value` changes
- Uses `JSON.stringify` / `JSON.parse` to support objects and arrays

---

## 📦 Usage Example: Persisted Dark Mode Toggle

```jsx
function DarkModeToggle() {
	const [isDark, setIsDark] = useLocalStorage("darkMode", false);

	return <button onClick={() => setIsDark((prev) => !prev)}>{isDark ? "🌙 Dark Mode" : "☀️ Light Mode"}</button>;
}
```

Even after refreshing the page, the dark mode preference is saved!

---

## 🧠 Pro Tips

- Works for strings, booleans, arrays, or objects
- Good for storing user preferences, last visited page, etc.
- You can wrap it further with a custom hook like `useDarkMode` or `useAuthStorage`

---

<br>

### Custom Hooks Recap

Custom Hooks are a powerful way to encapsulate and reuse logic in React applications. They allow you to:

- Create clean, reusable abstractions
- Manage complex stateful logic outside of components
- Enhance testability and maintainability
