# 📘 Tutorial 3 — Mark Todos Complete

> Continue the React + Firestore Todo series by adding completion status.

---

## ✅ What You Will Build

A todo list where each item can be marked complete.

Completed todos will:

* Save `completed: true` in Firestore
* Show a visual style (like strikethrough text)

---

## 🎯 Learning Goals

By completing this tutorial you will:

* Update existing Firestore documents
* Toggle boolean values in React UI
* Use conditional styling in JSX

---

## 🧰 Before You Start

You should already have:

* [Tutorial 2](2-delete-todos.md) completed
* A working Firestore `todos` collection

---

# Part 1 — Save `completed` When Adding Todos

Open:

```text
src/components/Todo.js
```

Find `addDoc(...)` and change it from:

```javascript
await addDoc(collection(db, "todos"), { todo });
```

To:

```javascript
await addDoc(collection(db, "todos"), {
  todo,
  completed: false,
});
```

---

# Part 2 — Update Imports

Change Firestore imports from:

```javascript
import { collection, addDoc, getDocs } from "firebase/firestore";
```

To:

```javascript
import { collection, addDoc, getDocs, updateDoc, doc } from "firebase/firestore";
```

---

# Part 3 — Add Toggle Handler

Add this function inside the `Todo` component:

```javascript
const toggleComplete = async (id, completed) => {
  await updateDoc(doc(db, "todos", id), {
    completed: !completed,
  });

  fetchTodos();
};
```

---

# Part 4 — Update UI Rendering

Replace your map block with:

```jsx
{todos.map((t) => (
  <div key={t.id} className="todo-row">
    <p className={t.completed ? "done" : ""}>{t.todo}</p>

    <button className="btn" onClick={() => toggleComplete(t.id, t.completed)}>
      {t.completed ? "Undo" : "Complete"}
    </button>
  </div>
))}
```

---

# Part 5 — Add Styling

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

.done {
  text-decoration: line-through;
  opacity: 0.65;
}
```

---

# Part 6 — Test It

1. Add a new todo
2. Click **Complete**
3. Confirm text style changes
4. Refresh page and verify state stays saved

You should see:

✅ Completed state saved in Firestore
✅ Button toggles between **Complete** and **Undo**

---

## 🛠️ Common Errors

### ❌ All old todos show weird behavior

Older docs may not have `completed` yet. You can:

* delete old docs and re-add them, or
* treat missing values as false.

### ❌ `updateDoc is not defined`

Import `updateDoc` and `doc` from Firestore.

---

## 📚 Docs

* [Update data (Firestore)](https://firebase.google.com/docs/firestore/manage-data/add-data#update-data)
* [Conditional rendering (React)](https://react.dev/learn/conditional-rendering)

---

➡️ Next: [Tutorial 4 — Add Timestamps](4-add-timestamps.md)
