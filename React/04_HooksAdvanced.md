# ⚙️ useEffect – The Lifecycle Workhorse of React

`useEffect()` is React's way to tell a functional component:
**"Run some code after rendering."**.

It's one of the most versatile Hooks as it lets your component run side effects:

- Fetching data
- Subscribing to events (event listeners)
- Updating the DOM manually
- Triggering animations
- Working with timers
- Synchronizing with external systems (like localStorage or APIs)
- Writing to console

In a class component, you’d split this logic across `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.
But with `useEffect`, **you have one API to rule them all.**

---

## 🧠 What Is a Side Effect?

A **side effect** is any logic that affects something _outside the function scope_ — or relies on something external to compute a result.

```js
// Pure: always returns same output for same input
function square(x) {
	return x * x;
}

// Has side effects: does external work
function fetchData(url) {
	fetch(url).then(/* ... */);
}
```

React's render process must remain **pure** and predictable. So side effects must be handled _outside of rendering_ — with `useEffect()`.
So a side effect is anything that goes beyond computing and returning a value. It's a deviation from the function's pure logic.

---

## 🧪 Basic `useEffect()` Syntax

```jsx
useEffect(() => {
	// Side effect logic here (runs after render)
});
```

This effect will run **after every render** — including initial mount and on every update because it since it has no dependencies specified (no dependency array). In case of empty dependency array ([]), it runs only once after the initial render. <br>
This is the default behavior of `useEffect` — it runs _after_ the component renders, allowing you to perform side effects without blocking the UI.<br>
You can think of it as a **callback** that runs _after_ React has painted the UI.

---

## 🧳 useEffect with Dependencies: The Core Concept

You control _when_ your effect runs using the **second argument** — the **dependency array**:

```jsx
useEffect(() => {
	// Effect logic
}, [someValue]);
```

Here’s how it works:

| Dependency Array       | Effect Runs...                       |
| ---------------------- | ------------------------------------ |
| `undefined` (no array) | After _every_ render                 |
| `[]`                   | Once — after initial mount           |
| `[count]`              | After mount + when `count` changes   |
| `[a, b]`               | After mount + when `a` or `b` change |

---

## 🔹 Example 1: `useEffect` with No Dependencies

```jsx
useEffect(() => {
	console.log("Runs after every render");
});
```

Avoid this unless you **truly** need the effect on _every re-render_.
Most of the time, this creates performance issues or infinite loops if you're updating state.

---

## 🔹 Example 2: Empty Dependency Array (`[]`)

```jsx
useEffect(() => {
	console.log("Runs only once — when component mounts");
}, []);
```

This is great for:

- Fetching data on load
- Setting up subscriptions
- Initializing animations or timers

⚠️ Be careful not to reference unstable values inside the useEffect if not listed in dependency array — they won’t trigger re-runs if changed. Unstabe values include variables that can change between renders, like props, state, functions declred inside the component, or objects/arrays created inline (e.g. const obj = { a: 1 }) inside the conponent.<br>&nbsp; &nbsp; &nbsp; These are unstable because they can get a new reference or value every time your component re-renders, even if the content appears to be the same.

```jsx
useEffect(() => {
	// ❌ Unstable value 'someProp' is used here
	console.log(someProp);
}, []); // but it's not in the dependency array!
```

In the example above, because the dependency array is [], React thinks the effect has no dependencies — so it will not re-run even if someProp changes. This creates a bug: the useEffect uses an outdated value (printed to the console).

#### Correct usage:

- Add unstable values to the dependancy array to ensure the effect re-runs when they change:

```jsx
useEffect(() => {
	console.log(someProp);
}, [someProp]); // Effect will re-run when 'someProp' changes
```

- Or, if it truly only needs to run once (e.g., fetching something that doesn’t depend on props/state), make sure nothing dynamic is used inside:

```jsx
useEffect(() => {
  fetch("/api/data").then(...); // no props or state used — safe
}, []);

```

---

## 🔹 Example 3: Watching a Value

```jsx
useEffect(() => {
	console.log("Name changed:", name);
}, [name]);
```

This effect runs only when the `name` value changes.

---

## 🧼 Cleanup: Returning a Function

Sometimes you need to **clean up** after your side effect — like removing event listeners or stopping intervals.

```jsx
useEffect(() => {
	const id = setInterval(() => console.log("Tick"), 1000);

	return () => clearInterval(id); // Cleanup before next effect OR unmount
}, []);
```

React will:

1. Run the effect on mount
2. Run the cleanup before unmounting **or** before running this effect again (if dependencies change)

---

## 🔍 Real-World Use Cases for `useEffect`

### ✅ Data Fetching

```jsx
useEffect(() => {
	async function load() {
		const res = await fetch("/api/users");
		const users = await res.json();
		setUsers(users);
	}

	load();
}, []);
```

Mounts → fetches → updates state.

---

### ✅ Data Fetching with Error Handling (using arrow function)

```jsx
useEffect(() => {
	const loadData = async () => {
		try {
			const res = await fetch("/api/data");
			if (!res.ok) throw new Error("Network response was not ok"); // Check for HTTP errors (e.g., 404, 500)
			const data = await res.json();
			setData(data);
		} catch (error) {
			console.error("Fetch error:", error);
			setError(error);
		}
	};

	loadData();
}, []);
```

---

### ✅ Syncing with Local Storage

```jsx
useEffect(() => {
	const savedTheme = localStorage.getItem("theme");
	if (savedTheme) {
		setTheme(savedTheme);
	}
}, []);
```

---

### ✅ Responding to Props Change

```jsx
useEffect(() => {
	console.log("ID changed:", id);
}, [id]);
```

---

### ✅ Working with the DOM (scroll, title, etc.)

```jsx
useEffect(() => {
	document.title = `You clicked ${count} times`;
}, [count]);
```

---

### ✅ Event Listeners with Cleanup

```jsx
useEffect(() => {
	function onResize() {
		setWidth(window.innerWidth);
	}

	window.addEventListener("resize", onResize);
	return () => window.removeEventListener("resize", onResize);
}, []);
```

---

## 🚨 Common Pitfalls

- Updating state inside an effect **without proper dependencies** → leads to infinite loops.
- Missing dependencies → stale or incorrect values.
- Including unstable values (like objects or inline functions) → causes unnecessary re-renders.

Use the **React ESLint plugin** to warn about missing or unstable dependencies.

---

> Next up: best-practice patterns with `useEffect` — debouncing, polling, unmount triggers, and how to combine it with `useRef` for tight control. Let me know if you’d like to break that into its own file or continue the deep dive right here.

<br>

# 🧪 useEffect Patterns and Recipes

You’ve seen how `useEffect` runs _after render_, and how its **dependency array** controls execution. Now let’s make use of that power with some common and useful patterns.

---

## 1️⃣ Delayed Response: Debouncing Input

If the user is typing fast, you don’t want to make a network request on every keystroke. Instead, _wait until they pause typing_ — that’s called debouncing.

```jsx
function SearchBox() {
	const [query, setQuery] = useState("");
	const [results, setResults] = useState([]);
	const timeoutRef = useRef(null);

	useEffect(() => {
		clearTimeout(timeoutRef.current);

		timeoutRef.current = setTimeout(() => {
			if (query) {
				fetch(`/api/search?q=${query}`)
					.then((res) => res.json())
					.then(setResults);
			}
		}, 500); // waits 500ms after user stops typing

		return () => clearTimeout(timeoutRef.current);
	}, [query]);

	return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
}
```

💡 Use this to throttle calls to APIs, autocomplete services, etc.

---

## 2️⃣ Conditional Execution Based on a Flag

Sometimes you don’t want an effect to run **on the first render**, only when a value changes afterward.

```jsx
const isInitial = useRef(true);

useEffect(() => {
	if (isInitial.current) {
		isInitial.current = false;
		return;
	}

	console.log("Effect that skips the first render");
}, [someValue]);
```

Use this for things like sending analytics or triggering animations **only** after initial mount.

---

## 3️⃣ Polling at Intervals

You can fetch data every few seconds to keep the UI fresh:

```jsx
useEffect(() => {
	const interval = setInterval(() => {
		fetch("/api/status")
			.then((res) => res.json())
			.then(updateStatus);
	}, 5000); // every 5 seconds

	return () => clearInterval(interval); // stop on unmount
}, []);
```

You can also make polling conditional based on a `paused` state.

---

## 4️⃣ Listening for Window Events

Setting up a scroll or resize listener?

```jsx
useEffect(() => {
	function handleScroll() {
		console.log("Scrolled to", window.scrollY);
	}

	window.addEventListener("scroll", handleScroll);
	return () => window.removeEventListener("scroll", handleScroll);
}, []);
```

Always clean up global listeners to avoid memory leaks when components unmount.

---

## 5️⃣ Syncing State to an External System (Local Storage)

```jsx
useEffect(() => {
	localStorage.setItem("theme", theme);
}, [theme]);
```

Want to load from localStorage on mount?

```jsx
useEffect(() => {
	const saved = localStorage.getItem("theme");
	if (saved) setTheme(saved);
}, []);
```

---

## 🧠 Tips for Writing Clean Effects

- ✅ Use one `useEffect()` per concern — don’t lump everything together.
- ✅ Extract logic into functions outside the effect for readability.
- ✅ Include all used variables in the dependency array (or you'll get stale values).
- ✅ Rely on `useRef()` if you need to persist values across renders _without causing rerenders_.

---

> Ready to move on to `useRef()` next and see how it pairs beautifully with `useEffect`.

<br>

# 🧷 useRef – Persistent Values Without Re-Renders

`useRef()` creates a mutable container that:

- Holds a `.current` property
- **Persists across renders**
- **Does not cause** re-renders when updated

Think of it like a little box you can stash something in — and React will leave it untouched unless you say otherwise.

---

## 🔹 Basic Syntax

```jsx
const myRef = useRef(initialValue);
```

It returns an object: `{ current: initialValue }`. You can read and update `.current` whenever you want.

---

## 🔭 Accessing DOM Elements

The most well-known use case: targeting DOM nodes.

```jsx
function FocusInput() {
	const inputRef = useRef();

	function focusField() {
		inputRef.current.focus();
	}

	return (
		<>
			<input ref={inputRef} />
			<button onClick={focusField}>Focus the input</button>
		</>
	);
}
```

This is the React-safe way to grab an element — no `document.querySelector()` needed.

---

## 🗂️ Storing Mutable Values Across Renders

Need to persist a value that changes but shouldn’t re-render the component? `useRef` is your friend.

```jsx
function RenderCounter() {
	const countRef = useRef(0);
	countRef.current++;

	return <p>Renders: {countRef.current}</p>;
}
```

Each render, it holds and updates the count — but doesn’t cause another re-render itself.

---

## ⏮️ Storing Previous Values

Want to track what a value _was_ before it changed?

```jsx
const prevValue = useRef();

useEffect(() => {
	prevValue.current = value;
}, [value]);
```

This lets you compare the current and previous versions of state or props.

---

## 🔁 Combine with useEffect for Live Timers

```jsx
function Timer() {
	const [seconds, setSeconds] = useState(0);
	const intervalRef = useRef();

	function start() {
		if (!intervalRef.current) {
			intervalRef.current = setInterval(() => {
				setSeconds((s) => s + 1);
			}, 1000);
		}
	}

	function stop() {
		clearInterval(intervalRef.current);
		intervalRef.current = null;
	}

	useEffect(() => {
		return stop; // Clean up on unmount
	}, []);

	return (
		<>
			<p>{seconds}s</p>
			<button onClick={start}>Start</button>
			<button onClick={stop}>Stop</button>
		</>
	);
}
```

Here, `intervalRef` persists across renders without causing a loop.

---

## 🧠 When to Use useRef vs useState

| Purpose                      | useState | useRef |
| ---------------------------- | -------- | ------ |
| Triggers re-render on change | ✅ Yes   | ❌ No  |
| Persists between renders     | ✅ Yes   |

<br>

# 🧮 useMemo – Cache Expensive Computations

`useMemo()` lets you **memoize the result of a calculation** — caching it so that it only recomputes **when its dependencies change**.

Without it, every render would rerun your function — even if the inputs are the same.

---

## 🔹 Syntax

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(input), [input]);
```

- The function inside runs **only when `input` changes**
- Returns the **cached value** otherwise
- Think of it like a dynamic `const`

---

## 💡 When to Use useMemo

- Complex calculations or transforms (sorting, filtering, math-heavy tasks)
- Derived values that don't change often
- Preventing expensive work inside re-rendered components

---

## 🧪 Example: Filtering a List

```jsx
const visibleTodos = useMemo(() => {
	return todos.filter((todo) => !todo.completed);
}, [todos]);
```

Even if the component re-renders for unrelated reasons, the filter won’t re-run unless `todos` changes.

---

## 🎯 Example: Sorting Large Lists

```jsx
const sortedItems = useMemo(() => {
	return items.slice().sort((a, b) => a.name.localeCompare(b.name));
}, [items]);
```

Avoids running `.sort()` unless `items` itself is a different reference.

---

## 🧬 useMemo for Object Identity

Sometimes you want to avoid triggering `useEffect` or rerendering child components **unless a derived object truly changed**.

```jsx
const options = useMemo(
	() => ({
		limit: 10,
		sortBy: "date",
	}),
	[]
);

// Without useMemo, this object would be recreated on every render!
```

Passing stable memoized objects can help avoid re-renders in deeply nested components or prop chains.

---

## 🧪 Danger Zone: Don’t Overuse

Memoization has a cost. Storing the cached value and checking its dependencies takes time too — if the computation is **fast**, skip `useMemo()`.

✅ Use it **when rendering performance is noticeably affected**
❌ Don't wrap basic math or trivial expressions

---

## 🧠 Summary

| Benefit                           | When It Helps                                     |
| --------------------------------- | ------------------------------------------------- |
| Avoids unnecessary recalculations | Sorting, filtering, transforming lists            |
| Preserves reference identity      | Useful for `useEffect`, `React.memo`, `children`  |
| Improves performance              | Only when working with _non-trivial_ computations |

---

> Next up: `useCallback()` — a close cousin of `useMemo`, but for **functions** instead of values.

<br>

# 🔁 useCallback – Memoizing Functions for Stability

`useCallback()` returns a **memoized version of a callback function**, meaning the function reference stays the same across renders _unless_ one of its dependencies changes.

This is important because in React:

- Functions are **re-created on every render**
- Passing a _new_ function into a child component can cause **unnecessary re-renders**
- `useCallback()` helps avoid that

---

## 🔹 Syntax

```jsx
const memoizedFn = useCallback(() => {
	// function body
}, [dependencies]);
```

Works just like `useMemo()`, but for **functions** instead of values.

---

## 🧪 Example: Stable Callback to Prevent Child Rerenders

```jsx
const handleClick = useCallback(() => {
	console.log("Clicked!");
}, []);
```

Even though the parent component re-renders, this callback stays the same — which is key when passing it to `React.memo` children.

---

## ⚠️ The Problem: New Function Every Time

```jsx
// BAD – creates a brand-new function on every render
<MyButton onClick={() => console.log("clicked")} />
```

This causes `<MyButton />` to re-render even if nothing has changed — because `onClick` is a new reference.

---

## ✅ useCallback Fix

```jsx
const handleClick = useCallback(() => {
	console.log("clicked");
}, []);

<MyButton onClick={handleClick} />;
```

Now the function is **only re-created if dependencies change**, so `MyButton` can skip unnecessary renders (especially when memoized).

---

## 🎯 useCallback in Effects

Passing a memoized function to `useEffect()` helps avoid **stale closures** or **unwanted re-executions**.

```jsx
const fetchUser = useCallback(() => {
	fetch(`/api/user/${userId}`).then(/* ... */);
}, [userId]);

useEffect(() => {
	fetchUser();
}, [fetchUser]); // depends on the stable, memoized function
```

This ensures `useEffect` only runs when the actual `userId` dependency changes — not when a new function is created unnecessarily.

---

## 🧠 useCallback vs useMemo Recap

| Hook          | Purpose            | Returns         |
| ------------- | ------------------ | --------------- |
| `useMemo`     | Memoize _value_    | `computedValue` |
| `useCallback` | Memoize _function_ | `memoizedFn`    |

---

## ✅ When to Use useCallback

- When passing functions as props to **memoized** child components (`React.memo`)
- When using functions inside `useEffect` dependency arrays
- When function identity needs to be stable across renders

---

## ❌ When to Skip It

- If the function is _cheap_ and you’re not memoizing children — don’t worry about it
- Premature optimization adds clutter without benefit

---

## 💡 Pro Tip

- Prefer defining your callbacks **outside render scope** only when needed
- You can often memoize handlers that use updater functions like `setCount((c) => c + 1)` — these don’t depend on external values

---

> Next up: a full example combining all four hooks in one component

<br>

# 🧩 Full Example: Combining All Four Hooks

Here’s a real-world component that brings together:

- `useState` for handling input and results
- `useEffect` for triggering side effects
- `useRef` for debounce timing and render tracking
- `useCallback` for stabilizing a memoized function

```jsx
import { useState, useEffect, useRef, useCallback } from "react";

function DebouncedSearch() {
	const [query, setQuery] = useState("");
	const [results, setResults] = useState([]);
	const debounceRef = useRef(null);
	const renderCount = useRef(0);

	renderCount.current += 1; // Tracks how many times component has rendered

	const performSearch = useCallback(async () => {
		if (!query) {
			setResults([]);
			return;
		}

		const response = await fetch(`/api/search?q=${query}`);
		const data = await response.json();
		setResults(data);
	}, [query]);

	useEffect(() => {
		clearTimeout(debounceRef.current);

		debounceRef.current = setTimeout(() => {
			performSearch();
		}, 500);

		return () => clearTimeout(debounceRef.current);
	}, [performSearch]);

	return (
		<div style={{ fontFamily: "sans-serif" }}>
			<p>
				<strong>Render count:</strong> {renderCount.current}
			</p>

			<input
				type="text"
				placeholder="Search..."
				value={query}
				onChange={(e) => setQuery(e.target.value)}
				style={{ padding: "0.5rem", width: "200px" }}
			/>

			<ul>
				{results.map((item) => (
					<li key={item.id}>{item.title}</li>
				))}
			</ul>
		</div>
	);
}
```

---

## 🧠 How Each Hook Works Together

| Hook          | What It Does                                            |
| ------------- | ------------------------------------------------------- |
| `useState`    | Stores query and fetched results                        |
| `useEffect`   | Debounces query input and triggers the search           |
| `useRef`      | Tracks render count and debounce `setTimeout`           |
| `useCallback` | Memoizes `performSearch()` to avoid unnecessary re-runs |

---

This is a great snapshot of modern React in motion: smart re-renders, timed effects, stable dependencies, and responsive UI behavior (input, timers, remote data, and render control all work together).

```

```
