# 🎨 Tailwind CSS – Overview

**Tailwind CSS** is a utility-first CSS framework that lets you build modern, responsive UIs directly in your HTML.
Instead of writing custom CSS, you apply pre-defined utility classes to elements.

---

## 🧱 Key Concepts

- **Utility-First:** Use small, single-purpose classes like `p-4`, `text-center`, `bg-blue-500` to style elements.
- **Composition > Customization:** Design is composed using class combinations, not writing new CSS rules.
- **Responsive by Default:** Built-in responsive design using mobile-first breakpoints (`sm:`, `md:`, etc.).
- **JIT Engine:** Just-In-Time compiler generates styles only for classes you actually use = super fast builds.
- **Customizable:** Tailwind’s config (`tailwind.config.js`) lets you define your own colors, spacing, fonts, etc.

---

## 🧩 Common Utility Classes (by category)

### 📏 Spacing

- `p-4` – Padding (all sides)
- `pt-2` / `px-6` – Padding top / horizontal
- `m-4` – Margin
- `space-x-4` – Horizontal spacing between children

### 🎨 Colors

- `bg-blue-500` – Background color
- `text-gray-700` – Text color
- `border-red-300` – Border color
- `hover:bg-blue-700` – Hover state background

### 📐 Layout & Flexbox

- `flex`, `flex-col`, `items-center`, `justify-between`
- `grid`, `grid-cols-3`, `gap-4`
- `w-full`, `max-w-md`, `h-screen`

### 📝 Typography

- `text-xl`, `font-bold`, `tracking-wide`
- `text-center`, `uppercase`, `leading-relaxed`

### 🖼️ Borders & Radius

- `border`, `border-2`, `border-gray-200`
- `rounded`, `rounded-lg`, `rounded-full`

### 🌓 Dark Mode

- `dark:bg-gray-800`, `dark:text-white`

### ⚡ Interactivity & Transitions

- `transition`, `duration-300`, `ease-in-out`
- `hover:scale-105`, `active:bg-blue-700`

---

## 🔧 Configuration Example

```js
// tailwind.config.js
module.exports = {
	theme: {
		extend: {
			colors: {
				primary: "#1D4ED8",
			},
		},
	},
	variants: {
		extend: {},
	},
	plugins: [],
};
```
