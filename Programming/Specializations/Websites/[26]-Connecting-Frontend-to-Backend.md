[Previous](./[25]-Authentication-and-Sessions.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[27]-Environment-Variables.md)

*Full-Stack Concepts*

# Lesson 26 - Connecting Front-End to Back-End (API Integration)

## 26.1 Putting the Pieces Together

Earlier lessons covered fetching data (Lesson 5), building a REST API (Lesson 22), and authentication (Lesson 25) separately. A real application wires all of these together: a front end that calls a back end's API, handles loading and error states, and keeps the UI in sync with server data.

---

## 26.2 A Typical Data-Fetching Component

```jsx
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("/api/users")
      .then(res => {
        if (!res.ok) throw new Error("Failed to load users");
        return res.json();
      })
      .then(setUsers)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

Handling all three states — loading, error, and success — is what separates a robust integration from one that only works on the happy path.

---

## 26.3 Sending Authenticated Requests

When an API requires authentication (Lesson 25), the client must attach credentials to every relevant request — a token in a header, or rely on cookies being sent automatically:

```js
fetch("/api/profile", {
  headers: { Authorization: `Bearer ${token}` }
});
```

---

## 26.4 The Same-Origin vs Cross-Origin Question

If the front end and back end are served from the same domain (common when a back end also serves the built front-end files), requests are simple same-origin calls. If they're on different domains — a common setup during development, or when using a separate API and static host — CORS (Lesson 5, expanded in Lesson 36) must be configured on the server to permit the front end's origin.

---

## 26.5 Keeping UI and Server Data in Sync

After a mutation (creating, editing, deleting something), the UI needs to reflect the change — either by updating local state directly with the server's response, or by re-fetching the affected data. Libraries like TanStack Query (mentioned in Lesson 20) automate much of this synchronization, including caching and automatic refetching, which becomes valuable once an app has many components reading the same server data.

---

[Previous](./[25]-Authentication-and-Sessions.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[27]-Environment-Variables.md)
