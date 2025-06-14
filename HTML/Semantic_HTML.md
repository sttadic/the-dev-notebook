## ✅ What is `Semantic HTML`?

**Semantic HTML** means using HTML _tags that clearly describe the meaning of the content inside them_.
It helps both **browsers and developers** (and assistive tech like screen readers) understand the structure and purpose of the webpage content.

## 📚 Common Semantic HTML Tags

Here are the most commonly used semantic tags in HTML5 and their typical purposes:

- `<header>` – Defines introductory content or a section's heading. Often contains logo, site name, or nav.
- `<nav>` – Represents navigation links (menus, table of contents, etc.).
- `<main>` – Specifies the primary content of the page (should be unique).
- `<section>` – Groups related content together (like a chapter or topic section).
- `<article>` – Represents self-contained content (e.g., blog post, news article).
- `<aside>` – Content that's indirectly related to the main content (e.g., sidebars, ads, tips).
- `<footer>` – Contains closing content for a page or section (e.g., contact info, copyright).
- `<h1>`–`<h6>` – Semantic headings that represent the structure of content (not just visual size).
- `<address>` – Contains contact information for the author or owner of the content.
- `<figure>` – Used to group media (images, charts, etc.) with a caption.
- `<figcaption>` – Caption for a `<figure>` element.
- `<time>` – Represents a specific time or date (can improve SEO and accessibility).
- `<mark>` – Highlights or emphasizes part of the text (usually for relevance).
- `<summary>` – Used inside `<details>` to provide a label for expandable content.
- `<details>` – A toggle-able container for content (like an FAQ dropdown).
- `<dialog>` – Represents a modal dialog box or interactive popup.
- `<blockquote>` – For quoting external content with semantic clarity.
- `<cite>` – Indicates a reference to a creative work (book, movie, research, etc.).
- `<code>` – Represents code snippets or inline code.
- `<pre>` – Preformatted text block (usually for code with preserved whitespace).

---

## 🛠️ How is it different from _non-semantic_ HTML?

In older (non-semantic) HTML, developers used generic tags like `<div>` and `<span>` for everything. These tags have **no meaning**—they’re just containers.

### Non-semantic Example:

```html
<div id="header">Welcome to my site</div>
<div id="nav">Home | About | Contact</div>
<div id="content">This is an article about HTML.</div>
```
