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

> Up next: how to manage multiple form fields, validate inputs, and maybe explore checkboxes, radios, selects and textareas.

<br>

# 🚀 Advanced Form Patterns in React

Once you've got the hang of controlled inputs, you can start building more flexible and scalable forms. Here are some common patterns you’ll use often.

---

## 🧵 1. Handling Multiple Inputs with a Single State Object

Instead of tracking each input in separate state variables, use one state object for the whole form:

```jsx
function ContactForm() {
	const [formData, setFormData] = useState({
		name: "",
		email: "",
	});

	function handleChange(e) {
		const { name, value } = e.target;
		setFormData((prev) => ({
			...prev,
			[name]: value,
		}));
	}

	return (
		<form>
			<input name="name" value={formData.name} onChange={handleChange} placeholder="Name" />
			<input name="email" value={formData.email} onChange={handleChange} placeholder="Email" />
		</form>
	);
}
```

Using `name` as the key lets you reuse a single `handleChange` function across multiple fields.

---

## ✅ 2. Checkboxes (Boolean Values)

Checkboxes return a `.checked` value (not `.value`):

```jsx
function Preferences() {
	const [subscribe, setSubscribe] = useState(true);

	return (
		<label>
			<input type="checkbox" checked={subscribe} onChange={(e) => setSubscribe(e.target.checked)} />
			Subscribe to newsletter
		</label>
	);
}
```

---

## 🎚️ 3. Select Dropdowns

Use `value` like any other input — and handle changes the same way:

```jsx
function CountrySelector() {
	const [country, setCountry] = useState("Ireland");

	return (
		<select value={country} onChange={(e) => setCountry(e.target.value)}>
			<option value="Ireland">Ireland</option>
			<option value="Croatia">Croatia</option>
			<option value="Japan">Japan</option>
		</select>
	);
}
```

---

## 🚥 4. Basic Input Validation

You can validate as the user types or on submit:

```jsx
function EmailForm() {
	const [email, setEmail] = useState("");
	const [error, setError] = useState("");

	function handleSubmit(e) {
		e.preventDefault();
		if (!email.includes("@")) {
			setError("Invalid email");
		} else {
			setError("");
			console.log("Email submitted:", email);
		}
	}

	return (
		<form onSubmit={handleSubmit}>
			<input type="email" value={email} onChange={(e) => setEmail(e.target.value)} />
			{error && <p style={{ color: "red" }}>{error}</p>}
			<button type="submit">Send</button>
		</form>
	);
}
```

> You can expand this pattern with regex, libraries like Yup, or show validation messages per field.

---

## 🧠 Summary

| Pattern             | Use Case                           |
| ------------------- | ---------------------------------- |
| Single state object | Simpler handling of large forms    |
| Checkbox handling   | Booleans controlled by `.checked`  |
| Dropdowns           | Use `value` and `onChange`         |
| Inline validation   | Catch mistakes early in input flow |

> From here, you can evolve into larger tools like `react-hook-form` or integrate validation libraries — but mastering these basics gives you all the power you need for common form challenges.

<br>

# 🧱 More Advanced Form Techniques

---

## ➕ 5. Dynamic Form Fields (Add/Remove Inputs)

You may need to let users add or remove fields on the fly — e.g. multiple phone numbers or email addresses.

```jsx
function DynamicEmailList() {
	const [emails, setEmails] = useState([""]);

	function handleChange(index, value) {
		const updated = [...emails];
		updated[index] = value;
		setEmails(updated);
	}

	function addField() {
		setEmails((prev) => [...prev, ""]);
	}

	function removeField(index) {
		setEmails((prev) => prev.filter((_, i) => i !== index));
	}

	return (
		<div>
			{emails.map((email, i) => (
				<div key={i}>
					<input value={email} onChange={(e) => handleChange(i, e.target.value)} placeholder={`Email #${i + 1}`} />
					<button onClick={() => removeField(i)}>Remove</button>
				</div>
			))}
			<button onClick={addField}>Add Email</button>
		</div>
	);
}
```

Each field is tied to an index in an array of values — simple and scalable.

---

## 🎭 6. Input Formatting & Masking (Phone Numbers, Credit Cards)

For formatting inputs while typing (e.g. phone numbers), you can:

- Write your own formatter logic
- Use a library like `react-number-format`, `cleave.js`, or `react-input-mask`

### Example: Manual formatting

```jsx
function PhoneInput() {
	const [phone, setPhone] = useState("");

	function formatPhone(value) {
		return value
			.replace(/\D/g, "")
			.replace(/(\d{3})(\d{3})(\d{0,4})/, "($1) $2-$3")
			.trim();
	}

	function handleChange(e) {
		setPhone(formatPhone(e.target.value));
	}

	return <input value={phone} onChange={handleChange} placeholder="(123) 456-7890" />;
}
```

This keeps input formatted as the user types, improving UX.

---

## 🧪 7. Third-Party Form Libraries (Optional)

As forms grow more complex, libraries can help with state, validation, performance, and reusability.

### 📦 Popular Options:

- **`react-hook-form`**: Minimal re-renders, easy integration, controlled/uncontrolled hybrid.
- **`Formik`**: Built-in schema validation, works well with **Yup**.
- **`Yup`**: Schema-based validator for object shapes.

### Tiny taste of `react-hook-form`

```jsx
import { useForm } from "react-hook-form";

function SignUp() {
	const { register, handleSubmit } = useForm();

	function onSubmit(data) {
		console.log(data);
	}

	return (
		<form onSubmit={handleSubmit(onSubmit)}>
			<input {...register("username")} placeholder="Username" />
			<input type="password" {...register("password")} />
			<button type="submit">Submit</button>
		</form>
	);
}
```

Super concise — no `useState` or manual handlers needed!

---

## 🧠 Summary: Putting It All Together

| Feature/Pattern          | Use Case                                   |
| ------------------------ | ------------------------------------------ |
| Dynamic fields           | Variable input sets (emails, phones, tags) |
| Input formatting/masking | UX for phone, credit cards, etc.           |
| Third-party libraries    | Large forms, validation, performance       |

> Master these techniques and you’ll be able to build anything from login forms to survey builders to fully dynamic wizards.

<br>

# 🧩 More Useful Form Examples

---

## 🔘 8. Radio Buttons

Radios are used to choose **one option from a set**. All radios in a group must share the same `name` attribute.

```jsx
function ColorPicker() {
	const [color, setColor] = useState("red");

	return (
		<div>
			<label>
				<input
					type="radio"
					name="color"
					value="red"
					checked={color === "red"}
					onChange={(e) => setColor(e.target.value)}
				/>
				Red
			</label>

			<label>
				<input
					type="radio"
					name="color"
					value="blue"
					checked={color === "blue"}
					onChange={(e) => setColor(e.target.value)}
				/>
				Blue
			</label>

			<p>Selected: {color}</p>
		</div>
	);
}
```

---

## 📝 9. Textarea Input

Just like `<input>`, but for multi-line text. Controlled the same way.

```jsx
function BioForm() {
	const [bio, setBio] = useState("");

	return (
		<div>
			<textarea value={bio} onChange={(e) => setBio(e.target.value)} placeholder="Tell us about yourself" />
			<p>{bio.length} characters</p>
		</div>
	);
}
```

---

## 🔧 10. Form Reset (Clear All Fields)

You can reset form state to defaults manually:

```jsx
function ResettableForm() {
	const initial = { name: "", email: "" };
	const [data, setData] = useState(initial);

	function handleChange(e) {
		const { name, value } = e.target;
		setData((d) => ({ ...d, [name]: value }));
	}

	function resetForm() {
		setData(initial);
	}

	return (
		<form>
			<input name="name" value={data.name} onChange={handleChange} placeholder="Name" />
			<input name="email" value={data.email} onChange={handleChange} placeholder="Email" />
			<button type="button" onClick={resetForm}>
				Reset
			</button>
		</form>
	);
}
```

---

## 🧠 Tip: Disable Submit Until Valid

React makes it easy to disable a button until the form meets your criteria:

```jsx
<button type="submit" disabled={!email || !password}>
	Submit
</button>
```

---

> At this point, your React forms arsenal includes: dynamic fields, checkboxes, radios, selects, validation, formatting, custom logic, and third-party integrations. Whether you're building a feedback widget or a multi-step survey — you've got the chops.
