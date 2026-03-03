# 📘 Tutorial 2 — Delete Todos

> Continue the React + Firestore Todo series by adding delete functionality.

---

## ✅ What You Will Build

A todo list where each item has a **Delete** button that:

* Removes the item from Firestore
* Immediately refreshes the list in your app

---

## 🎯 Learning Goals

By completing this tutorial you will:

* Delete a specific Firestore document by ID
* Pass arguments from UI buttons into handlers
* Keep React state in sync with Firestore data

---

## 🧰 Before You Start

You should already have:

* [Tutorial 1](1-react-firestore-todo-app.md) completed
* A working `todos` collection in Firestore

Run your app first to confirm your baseline works:

```bash
npm run start
```

---

# Part 1 — Update Imports in `Todo.js`

Open:

```text
src/components/Todo.js
```

Change your Firestore import from this:

```javascript
import { collection, addDoc, getDocs } from "firebase/firestore";
```

To this:

```javascript
import { collection, addDoc, getDocs, deleteDoc, doc } from "firebase/firestore";
```

---

# Part 2 — Add a Delete Handler

Inside your `Todo` component, add this function below `addTodo`:

```javascript
const deleteTodo = async (id) => {
  await deleteDoc(doc(db, "todos", id));
  fetchTodos();
};
```

✅ This targets one document in the `todos` collection using its `id`.

---

# Part 3 — Add Delete Buttons in the UI

Find your `.map()` render block.

Replace:

```jsx
{todos.map((t) => (
  <p key={t.id}>{t.todo}</p>
))}
```

With:

```jsx
{todos.map((t) => (
  <div key={t.id} className="todo-row">
    <p>{t.todo}</p>
    <button className="btn delete-btn" onClick={() => deleteTodo(t.id)}>
      Delete
    </button>
  </div>
))}
```

---

# Part 4 — Add Small Style Improvements

Open:

```text
src/App.css
```

Add:

```css
.todo-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
  margin-bottom: 0.75rem;
}

.delete-btn {
  background: #8b1e1e;
}
```

---

# Part 5 — Test It

1. Add 2–3 todos
2. Click **Delete** on one item
3. Refresh the page

You should see:

✅ Deleted item disappears immediately
✅ Deleted item does not come back after refresh

---

## 🛠️ Common Errors

### ❌ `deleteDoc is not defined`

You forgot to import `deleteDoc`.

### ❌ Wrong document deleted (or no deletion)

Make sure you call:

```javascript
deleteDoc(doc(db, "todos", id))
```

…and that `id` is `t.id` from the `.map()` item.

---

## 📚 Docs

* [Delete data (Firestore)](https://firebase.google.com/docs/firestore/manage-data/delete-data)
* [Rendering lists (React)](https://react.dev/learn/rendering-lists)

---

➡️ Next: [Tutorial 3 — Mark Todos Complete](3-mark-todos-complete.md)
