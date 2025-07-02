# 🧭 React Router – Routing for Modern SPAs

React Router lets you build **multi-page experiences** in a single-page app (SPA). Instead of full page reloads, React handles navigation using the browser's history API, giving you lightning-fast transitions and dynamic layouts.

---

## 🚀 Why Use React Router?

- Navigate between views without reloading the page
- Handle dynamic URLs like `/user/:id`
- Enable nested layouts (shared headers, sidebars)
- Manage auth-protected routes
- Respond to browser history, back/forward, and deep links

---

## 📦 Installation

Make sure you’re using **React Router v6 or later**:

```bash
npm install react-router-dom
```

---

## 📁 Suggested File Structure

```
src/
├── App.jsx
├── index.js
├── pages/
│   ├── Home.jsx
│   ├── About.jsx
│   └── Profile.jsx
└── routes/
    └── AppRoutes.jsx
```

We’ll use `AppRoutes.jsx` to centralize all routes.

---

## 🧭 Basic Setup: Define Routes

```jsx
// src/routes/AppRoutes.jsx
import { Routes, Route } from "react-router-dom";
import Home from "../pages/Home";
import About from "../pages/About";
import Profile from "../pages/Profile";

export default function AppRoutes() {
	return (
		<Routes>
			<Route path="/" element={<Home />} />
			<Route path="/about" element={<About />} />
			<Route path="/profile/:userId" element={<Profile />} />
		</Routes>
	);
}
```

---

## 🧱 App Entry Point

```jsx
// src/App.jsx
import { BrowserRouter } from "react-router-dom";
import AppRoutes from "./routes/AppRoutes";

function App() {
	return (
		<BrowserRouter>
			<AppRoutes />
		</BrowserRouter>
	);
}

export default App;
```

---

## 🔗 Navigate Between Pages

```jsx
import { Link } from "react-router-dom";

<Link to="/">Home</Link>
<Link to="/about">About</Link>
<Link to="/profile/42">My Profile</Link>
```

Use `<Link />` instead of `<a href="...">` to prevent full page reloads.

---

## 🧬 Access Route Parameters

```jsx
// src/pages/Profile.jsx
import { useParams } from "react-router-dom";

function Profile() {
	const { userId } = useParams();
	return <h2>Viewing profile for user {userId}</h2>;
}
```

---

## 🧩 Nested Routes & Layouts

You can nest routes using a shared layout:

```jsx
<Route path="/" element={<Layout />}>
	<Route index element={<Home />} />
	<Route path="about" element={<About />} />
	<Route path="profile/:userId" element={<Profile />} />
</Route>
```

Use `<Outlet />` inside `Layout` to render child routes.

---

> Coming up next: navigation hooks (`useNavigate`), route guards (auth redirects), and code splitting with lazy routes. Let’s make your pages truly dynamic!

<br>

# 🔄 Programmatic Navigation in React Router

Sometimes you need to navigate **in code**, not via links — for example:

- After a form submission
- After login/logout
- On click of a button that conditionally routes elsewhere

That’s where the `useNavigate()` hook comes in.

---

## 🧭 useNavigate Hook

```jsx
import { useNavigate } from "react-router-dom";

function Dashboard() {
	const navigate = useNavigate();

	function handleLogout() {
		logout(); // your logic
		navigate("/login"); // redirect manually
	}

	return <button onClick={handleLogout}>Log out</button>;
}
```

`navigate(path)` pushes a new location to the history stack.
Use `navigate(-1)` to go back.

---

## 📦 Redirect After Action

```jsx
function Form() {
	const navigate = useNavigate();

	function handleSubmit() {
		// submit logic
		navigate("/thank-you", { replace: true }); // replace = no back button return
	}

	return <button onClick={handleSubmit}>Submit</button>;
}
```

---

## 🔒 Private Routes (Auth Guard)

Sometimes, you want to **prevent access to a route** unless the user is authenticated.

Here’s how to create a “guarded” route:

### 1. Create a wrapper component

```jsx
import { Navigate } from "react-router-dom";

function ProtectedRoute({ children }) {
	const isAuthenticated = useAuth(); // your custom hook or context

	if (!isAuthenticated) {
		return <Navigate to="/login" replace />;
	}

	return children;
}
```

### 2. Use it inside routes

```jsx
<Route
	path="/dashboard"
	element={
		<ProtectedRoute>
			<Dashboard />
		</ProtectedRoute>
	}
/>
```

---

## 🧩 Dynamic Routes with Params

To link to a user profile:

```jsx
<Link to={`/profile/${user.id}`}>Visit Profile</Link>
```

Access the dynamic portion in the route with `useParams()`:

```jsx
const { userId } = useParams();
```

---

## 🧱 Nested Layouts and Outlets

Create a layout component:

```jsx
function Layout() {
	return (
		<>
			<Header />
			<main>
				<Outlet />
			</main>
			<Footer />
		</>
	);
}
```

Wrap your nested routes:

```jsx
<Route path="/" element={<Layout />}>
	<Route index element={<Home />} />
	<Route path="about" element={<About />} />
	<Route path="profile/:userId" element={<Profile />} />
</Route>
```

---

## 🧠 Summary: React Router Patterns

| Feature         | Hook/Component          | Purpose                              |
| --------------- | ----------------------- | ------------------------------------ |
| Page navigation | `<Link>`, `useNavigate` | Declarative & programmatic routing   |
| Dynamic params  | `useParams()`           | Access URL segments like `/user/:id` |
| Guarded access  | `<ProtectedRoute>`      | Prevent access based on auth logic   |
| Shared layouts  | `<Outlet />`            | Compose nested routes cleanly        |

---

> Up next: we’ll add global shared state using the **Context API** — perfect for wiring up your app-wide auth logic, themes, and user info across all pages. Shall we dive into that next?
