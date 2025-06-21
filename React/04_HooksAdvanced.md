# 🧠 Advanced Hooks in React

Now that you're fluent in `useState`, it’s time to explore the powerful companion Hooks that let you control lifecycle timing, mutable values, and DOM access with surgical precision.

---

## 🔁 useEffect – Handling Side Effects

`useEffect` lets your component **run side effects** — like fetching data, subscribing to events, or updating the DOM after render.

### 🔹 Basic Example

```jsx
useEffect(() => {
	console.log("Component mounted or updated");
});
```

Runs after every render.

---

### 🔍 With Dependency Array

```jsx
useEffect(() => {
	console.log("Runs only on mount");
}, []);
```

```jsx
useEffect(() => {
	console.log("Runs when `count` changes");
}, [count]);
```

---

### 🧼 Cleanup Logic

```jsx
useEffect(() => {
	const id = setInterval(() => console.log("Tick"), 1000);

	return () => clearInterval(id); // Cleanup on unmount or re-run
}, []);
```

Use this for removing listeners, clearing timers, or canceling fetches.

---

## 🔍 useRef – Persist Values Without Re-Renders

`useRef` gives you a **mutable object** that doesn’t trigger a re-render. Useful for storing DOM references or persisting values between renders.

### 🔹 DOM Access

```jsx
const inputRef = useRef();

function focusInput() {
	inputRef.current.focus();
}

return <input ref={inputRef} />;
```

### 🔹 Persisting a Value

```jsx
const renderCount = useRef(0);
renderCount.current += 1;
```

You can think of `useRef()` as a “box” that holds a value — and doesn’t reset on rerenders.

---

## 📉 useMemo – Cache Expensive Calculations

```jsx
const sortedItems = useMemo(() => {
	return items.sort(compareFn); // Only re-sorts when `items` changes
}, [items]);
```

Use it to **optimize performance**, especially inside components that re-render often.

---

## ⚙️ useCallback – Memoize Functions

```jsx
const handleClick = useCallback(() => {
	doSomething();
}, [dependency]);
```

Prevents the function from being recreated unless its dependencies change — useful for passing stable callbacks to children or useEffect.

---

## 🧠 Summary: Which Hook When?

| Hook          | Use for...                                         |
| ------------- | -------------------------------------------------- |
| `useEffect`   | Running side effects (data fetching, timers, etc.) |
| `useRef`      | Accessing DOM or storing mutable values            |
| `useMemo`     | Avoiding unnecessary recalculations                |
| `useCallback` | Avoiding unnecessary function re-creations         |

> Coming up: practical patterns for `useEffect`, debouncing with `useRef`, and when to skip `useMemo` entirely. Or let me know if you'd like to go deeper into any one of these first.

<br>

# 🧪 useEffect Patterns and Best Practices

---

## 🧭 1. Fetching Data on Mount

```jsx
useEffect(() => {
	async function fetchData() {
		const res = await fetch("/api/data");
		const data = await res.json();
		setData(data);
	}

	fetchData();
}, []);
```

Runs once when the component mounts. You can also cancel fetches using `AbortController` for cleanup.

---

## 🕰️ 2. Debounced Input with useEffect + useRef

Avoid making requests on _every keystroke_ — delay the effect using a timer:

```jsx
function Search() {
	const [query, setQuery] = useState("");
	const timeoutRef = useRef();

	useEffect(() => {
		clearTimeout(timeoutRef.current);
		timeoutRef.current = setTimeout(() => {
			if (query) {
				console.log("Searching for", query);
			}
		}, 500);
		return () => clearTimeout(timeoutRef.current);
	}, [query]);

	return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
}
```

💡 This pattern is great for autocomplete, filters, or live searches.

---

## 🎯 3. Triggering on Prop or State Change

Use the dependency array to limit when an effect runs:

```jsx
useEffect(() => {
	console.log("User ID changed:", userId);
}, [userId]);
```

Only runs when `userId` updates — not when unrelated state changes.

---

## 🔄 4. Re-running Effects on Intervals

Example: polling every 5 seconds

```jsx
useEffect(() => {
	const id = setInterval(() => {
		console.log("Polling data...");
	}, 5000);

	return () => clearInterval(id); // Cleanup
}, []);
```

You can combine this with a `paused` state to enable/disable it dynamically.

---

## 🧹 5. Handling Component Unmount or Cleanup

Clean up timers, listeners, or subscriptions:

```jsx
useEffect(() => {
	const handleScroll = () => console.log("Scrolled!");
	window.addEventListener("scroll", handleScroll);

	return () => {
		window.removeEventListener("scroll", handleScroll);
	};
}, []);
```

Skipping cleanup can lead to memory leaks and bugs in complex components.

---

## 🚨 Common Pitfalls

- ❌ Forgetting the dependency array = runs every render
- ❌ Including unstable dependencies (like inline functions) in the array
- ❌ Triggering infinite loops by updating state inside a careless effect

---

> With these patterns under your belt, `useEffect` becomes a powerful tool — and now you’re ready to build your own **custom Hooks** that wrap these patterns into reusable modules.
