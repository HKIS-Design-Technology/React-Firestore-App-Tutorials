# Tutorial 3 — Connecting Comments

> Connect the comments to Firestore using a filtered query that finds only the comments belonging to the current post.

---

## What You Will Do

- Save new comments to a `comments` collection in Firestore, including a `postId` field
- Load only the comments that belong to the current post using `where()`

---

## Learning Goals

By the end of this tutorial you will be able to:

- Write a Firestore document that references another document via an ID field
- Use `query` and `where` to filter a collection
- Explain how two Firestore collections are linked through a shared ID

---

## Before You Start

Open `src/components/PostDetail.js` and find the `HARDCODED_COMMENTS` object near the top. Notice that it's a dictionary keyed by post ID:

```js
const HARDCODED_COMMENTS = {
  "1": [ { id: "c1", text: "...", authorName: "Sam", ... } ],
  "2": [ { id: "c4", text: "...", authorName: "Jordan", ... } ],
  ...
};
```

And the initial state uses the current post's ID to look up its comments:

```js
const [comments, setComments] = useState(HARDCODED_COMMENTS[post.id] || []);
```

In Firestore, you'll replace this lookup with a real query: *"give me all comments where `postId` equals the current post's ID"*.

---

# Part 1 — Add Imports

Open `src/components/PostDetail.js`.

Find the existing import:

```js
import { useState } from "react";
```

Replace it with:

```js
import { useState, useEffect } from "react";
import { collection, getDocs, addDoc, query, where } from "firebase/firestore";
import { db } from "../firebase";
```

Two new tools here compared to Tutorial 2:
- `query` — builds a filtered query
- `where` — adds a filter condition to a query (e.g., "where `postId` equals this value")

---

# Part 2 — Load Comments from Firestore

## Step 1 — Change the initial state

Find:

```js
const [comments, setComments] = useState(HARDCODED_COMMENTS[post.id] || []);
```

Replace it with:

```js
const [comments, setComments] = useState([]);
```

## Step 2 — Add `fetchComments` and `useEffect`

Below the `useState` lines, add:

```js
const fetchComments = async () => {
  const q = query(
    collection(db, "comments"),
    where("postId", "==", post.id)
  );
  const snapshot = await getDocs(q);
  const list = snapshot.docs.map((doc) => ({
    id: doc.id,
    ...doc.data(),
  }));
  setComments(list);
};

useEffect(() => {
  fetchComments();
}, [post.id]);
```

**Breaking this down:**

```js
const q = query(
  collection(db, "comments"),   // look in the "comments" collection
  where("postId", "==", post.id) // only return documents where postId matches
);
```

- `query(...)` builds a filtered query — it doesn't run it yet
- `where("postId", "==", post.id)` adds the filter: only fetch comments whose `postId` field equals the ID of the post currently being viewed
- `getDocs(q)` runs the query and returns the results

> Note: `useEffect` has `[post.id]` in its dependency array (not `[]`). This means it re-runs whenever the user opens a different post, loading the correct comments each time.

## Step 3 — Delete the hardcoded data

Delete the entire `HARDCODED_COMMENTS` object at the top of the file. You don't need it anymore.

---

# Part 3 — Save New Comments to Firestore

Find the `handleAddComment` function. Replace the TODO block with an `addDoc` call.

Find this code:

```js
// TODO (Tutorial 3): Replace the lines below with addDoc to save the comment to the "comments" collection in Firestore
// Important: the document must include postId: post.id so we can find this comment later
const newComment = {
  id: Date.now().toString(),
  text,
  authorName,
  createdAt: new Date().toLocaleDateString("en-US", {
    month: "long",
    day: "numeric",
    year: "numeric",
  }),
};
setComments([...comments, newComment]);
// TODO (Tutorial 3): After addDoc, call fetchComments() instead of updating state directly
```

Replace it with:

```js
await addDoc(collection(db, "comments"), {
  postId: post.id,
  text,
  authorName,
  createdAt: new Date().toLocaleDateString("en-US", {
    month: "long",
    day: "numeric",
    year: "numeric",
  }),
});
fetchComments();
```

**The critical line is `postId: post.id`.**

Every comment document saved to Firestore includes the ID of the post it belongs to. This is what makes the `where("postId", "==", post.id)` query work — without it, there would be no way to find a post's comments.

---

# Part 4 — Test It

Save your files.

1. Open the app and click on a post
2. Add a comment using the form
3. The comment should appear below
4. Go back, click a different post — its comments should be different
5. Refresh the page and re-open the post — your comment should still be there
6. Check Firebase Console → Firestore → `comments` collection — you should see documents with a `postId` field

✅ The bulletin board is now fully connected to Firestore.

---

## Common Errors

### ❌ All comments appear on every post

Check that `where("postId", "==", post.id)` is in your query and that `postId: post.id` is included in your `addDoc` call.

### ❌ `FirebaseError: The query requires an index`

Firestore sometimes needs an index for queries that combine `where` with `orderBy`. Since this tutorial doesn't use `orderBy`, you shouldn't hit this. If you do, follow the link in the error message — Firebase will create the index for you automatically.

### ❌ Comments from the old hardcoded data no longer show

That's expected. The hardcoded data was never in Firestore. You'll need to add real comments through the app.

### ❌ New comment appears and then disappears

Check that `fetchComments()` is being called after `addDoc`. If it runs before `addDoc` finishes, it may not find the new comment yet. Make sure `handleAddComment` is marked `async` and you're using `await` before `addDoc`.

---

## What You've Built

You now have a fully functional multi-screen app backed by Firestore:

| Feature | Firestore operation |
|---|---|
| Load all posts on startup | `getDocs(collection(db, "posts"))` |
| Add a new post | `addDoc(collection(db, "posts"), { ... })` |
| Load comments for a post | `getDocs(query(collection(db, "comments"), where("postId", "==", post.id)))` |
| Add a new comment | `addDoc(collection(db, "comments"), { postId: post.id, ... })` |

### The key pattern you've learned

Two collections. One shared ID. That's how Firestore connects data across collections — and it's the same pattern you'll use when designing the data model for your own client project.

When you read a client's mockups and identify the "things" the app needs to track, ask yourself:
- What are my entities? → those become collections
- How are they related? → store an ID field on the "child" entity
- What does each screen need to display? → that determines what queries you need

🐉 Torch is proud of you.
