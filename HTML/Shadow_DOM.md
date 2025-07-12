# 🌑 Shadow DOM

## What Is Shadow DOM?

Shadow DOM is part of the **Web Components** standard. It allows encapsulating a portion of the DOM into a **separate, isolated subtree** attached to an element. This "shadow" subtree:

- Is **hidden** from the main document DOM.
- **Encapsulates** HTML, CSS, and JS behavior.
- Prevents **style and event leakage** in and out by default.

---

## 🧩 HTML or CSS Related?

### ✅ Primarily HTML/DOM:

- Shadow DOM is created using **JavaScript APIs**.
- It's a structural feature of the DOM tree.

```js
const shadow = element.attachShadow({ mode: "open" });
shadow.innerHTML = `
  <style>p { color: red; }</style>
  <p>I'm in the shadow!</p>
`;
```

---

### 🎯 CSS Implications:

Although it's a DOM feature, it **strongly affects CSS behavior**:

- Styles **inside** a shadow root:
  - Do **not leak out** to the main document.
- Styles **outside** the shadow root:
  - Do **not penetrate in**, unless specifically allowed.

This enables **style encapsulation** and avoids conflicts.

#### 🚫 Not Affected by Global CSS

```css
/* This won't affect elements inside a shadow root */
p {
	color: blue;
}
```

#### ✅ Scoped Styling Inside Shadow DOM

```html
<style>
	p {
		color: red; /* Only affects this shadow root */
	}
</style>
```

---

## 🔍 Real World Examples

Many built-in and custom elements use shadow DOM:

- `<input type="range">`
- `<video>`
- Framework components like `<my-button>`, `<custom-slider>`, etc.

These use shadow DOM to encapsulate internal structure and styles.

---

## 🛠️ Tips for Working with Shadow DOM

- Use browser DevTools → _"Show user agent shadow DOM"_ option to inspect.
- Use `::part` and `::slotted()` pseudo-elements to style internal parts **from outside**, if the component exposes them.

```css
/* Example with ::part exposed by a Web Component */
my-component::part(label) {
	color: blue;
}
```

---

## 🧠 Summary

| Aspect              | Shadow DOM Behavior                   |
| ------------------- | ------------------------------------- |
| DOM Access          | Via `.shadowRoot` in JS               |
| Style Encapsulation | Prevents CSS leaking in/out           |
| Reusability         | Ideal for custom, self-contained UI   |
| Isolation           | JavaScript, events, and styles scoped |

---

📌 **Bottom line:** Shadow DOM is an **HTML/JS feature** that gives you powerful **CSS encapsulation** capabilities. It's at the heart of modern Web Component architecture.

<br>

# ⚛️ Shadow DOM in React – Practical Overview

## ✅ Does React Use Shadow DOM?

No. React uses its own **Virtual DOM**, which is a **JavaScript-based abstraction** for efficient UI updates. Shadow DOM, on the other hand, is a **browser-native feature** for **DOM and CSS encapsulation**.

| Feature                | Shadow DOM               | Virtual DOM                               |
| ---------------------- | ------------------------ | ----------------------------------------- |
| Native browser feature | ✅ Yes                   | ❌ No (React internal abstraction)        |
| Purpose                | Encapsulation (HTML/CSS) | Efficient rendering (diffing/updating UI) |
| Used by React?         | ❌ Not by default        | ✅ Core of React rendering                |

---

## 🧩 Can React Use Shadow DOM?

Yes! While not built-in, React can work with Shadow DOM **manually**:

1. You create a shadow root on a DOM node.
2. Then render your React content into it using `ReactDOM.createRoot()`.

---

## 🎯 When to Use Shadow DOM in React?

Shadow DOM can be useful in React when you:

- Build **reusable or embedded components** (design systems, UI libraries).
- Avoid **global CSS pollution/conflicts**.
- Render React inside **non-React environments** (WordPress, CMS, micro-frontends).
- Integrate or wrap **Web Components**.

---

## 🔧 Example: Rendering React Inside a Shadow Root

```jsx
import { useEffect, useRef } from "react";
import { createRoot } from "react-dom/client";

const ShadowWrapper = ({ children }) => {
	const hostRef = useRef(null);
	const shadowRef = useRef(null);

	useEffect(() => {
		if (hostRef.current && !shadowRef.current) {
			// Create a shadow root
			const shadowRoot = hostRef.current.attachShadow({ mode: "open" });

			// Create a mount point inside the shadow root
			const mountPoint = document.createElement("div");
			shadowRoot.appendChild(mountPoint);

			// Render React content inside the shadow root
			createRoot(mountPoint).render(children);

			shadowRef.current = shadowRoot;
		}
	}, [children]);

	return <div ref={hostRef}></div>;
};
```

---

## ✅ Usage Example

```jsx
import ShadowWrapper from "./ShadowWrapper";

function App() {
	return (
		<div>
			<h1>Outside Shadow DOM</h1>

			<ShadowWrapper>
				<style>{`p { color: red; }`}</style>
				<p>This is inside Shadow DOM</p>
			</ShadowWrapper>
		</div>
	);
}
```

### Result:

- The red `p` style **only affects** the paragraph inside the shadow DOM.
- Global styles **do not leak in or out**.

---

## 🧱 Alternative: Web Components with Shadow DOM in React

You can also **define a Web Component** (e.g., with `customElements.define`) and then use it inside React just like any regular HTML tag:

```jsx
<MyShadowComponent some-prop="value" />
```

This is ideal for:

- Sharing components across frameworks.
- Full CSS/HTML/JS isolation.
- Public design systems.

---

## ⚠️ Considerations

- React Context and portals don’t cross Shadow DOM boundaries easily.
- Some styling libraries (like `styled-components`) don’t support Shadow DOM out of the box.
- You'll often need to use raw `<style>` tags or CSS-in-JS that manually injects into the shadow root.

---

## 🧠 Summary

| Feature                  | React + Shadow DOM                                      |
| ------------------------ | ------------------------------------------------------- |
| Native support           | ❌ No                                                   |
| Possible via manual code | ✅ Yes                                                  |
| Benefits                 | Encapsulation, style isolation                          |
| Use cases                | Design systems, embedded apps, conflict-free components |
| How to render            | `ReactDOM.createRoot()` inside `shadowRoot`             |

---

📌 **Bottom line**: Shadow DOM isn’t part of React by default, but you can absolutely integrate it when you need **encapsulated styling and DOM isolation**, especially for building scalable and reusable component libraries.
