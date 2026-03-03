# 📘 Tutorial 4 — Add Timestamps

> Continue the React + Firestore Todo series by storing creation time for each task.

---

## ✅ What You Will Build

Each todo document will include a Firestore server timestamp:

* `createdAt: serverTimestamp()`

You will also load todos in newest-first order.

---

## 🎯 Learning Goals

By completing this tutorial you will:

* Use Firestore server-side timestamps
* Query data with `orderBy`
* Display formatted date/time in React

---

## 🧰 Before You Start

You should already have:

* [Tutorial 3](3-mark-todos-complete.md) completed
* A working `Todo.js` component loading todos from Firestore

---

# Part 1 — Update Firestore Imports

Open:

```text
src/components/Todo.js
```

Change Firestore imports from:

```javascript
import { collection, addDoc, getDocs } from "firebase/firestore";
```

To:

```javascript
import {
  collection,
  addDoc,
  getDocs,
  serverTimestamp,
  query,
  orderBy,
} from "firebase/firestore";
```

---

# Part 2 — Save Timestamp on Create

Find `addDoc(...)` and update it to:

```javascript
await addDoc(collection(db, "todos"), {
  todo,
  createdAt: serverTimestamp(),
});
```

---

# Part 3 — Query Todos by Time

Replace your `fetchTodos` function with:

```javascript
const fetchTodos = async () => {
  const q = query(collection(db, "todos"), orderBy("createdAt", "desc"));
  const snapshot = await getDocs(q);

  const list = snapshot.docs.map((doc) => ({
    id: doc.id,
    ...doc.data(),
  }));

  setTodos(list);
};
```

---

# Part 4 — Show Timestamp in the UI

Update map output to include a readable date:

```jsx
{todos.map((t) => (
  <div key={t.id} className="todo-row">
    <p>{t.todo}</p>

    <small className="time">
      {t.createdAt?.toDate
        ? t.createdAt.toDate().toLocaleString()
        : "Saving timestamp..."}
    </small>
  </div>
))}
```

---

# Part 5 — Add Styles

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

.time {
  color: #666;
  font-size: 0.8rem;
}
```

---

# Part 6 — Test It

1. Add several todos
2. Confirm new ones appear first
3. Confirm each displays date/time

You should see:

✅ `createdAt` field in Firestore
✅ Newest items first
✅ Human-readable timestamps in UI

---

## 🛠️ Common Errors

### ❌ `orderBy` requires an index

Firestore may prompt for an index in advanced queries. Follow the console link if needed.

### ❌ Timestamp shows “Saving timestamp...” forever

Refresh once. Server timestamps can be null briefly right after write.

---

## 📚 Docs

* [Server timestamps (Firestore)](https://firebase.google.com/docs/firestore/manage-data/add-data#server_timestamp)
* [Order and limit data (Firestore)](https://firebase.google.com/docs/firestore/query-data/order-limit-data)

---

➡️ Next: [Tutorial 5 — Realtime Listeners](5-realtime-listeners.md)
