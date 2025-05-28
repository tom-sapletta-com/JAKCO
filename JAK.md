Zrobimy najprostsze możliwe rozwiązanie przy użyciu:

* **Frontend**: React + prosty formularz z walidacją (bez bibliotek).
* **Backend**: Node.js + Express
* **Baza**: SQLite (lekka, prosta w setupie)

Zacznijmy od kompletnego **MVP**.

---

## 📁 Struktura folderów

```
project/
│
├── backend/
│   ├── server.js
│   └── db.sqlite (utworzy się automatycznie)
│
└── frontend/
    └── (tu będzie React)
```

---

## 📦 Backend (Node.js + Express + SQLite)

**backend/server.js**

```js
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const cors = require('cors');

const app = express();
const db = new sqlite3.Database('./db.sqlite');

app.use(cors());
app.use(express.json());

// Tworzenie tabeli jeśli nie istnieje
db.run(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT NOT NULL,
    department TEXT NOT NULL
  )
`);

// Dodawanie użytkownika
app.post('/users', (req, res) => {
  const { name, email, department } = req.body;
  if (!name || !email || !department) {
    return res.status(400).json({ error: 'All fields are required' });
  }

  db.run(
    'INSERT INTO users (name, email, department) VALUES (?, ?, ?)',
    [name, email, department],
    function (err) {
      if (err) return res.status(500).json({ error: err.message });
      res.status(201).json({ id: this.lastID });
    }
  );
});

// Lista użytkowników
app.get('/users', (req, res) => {
  db.all('SELECT * FROM users', (err, rows) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(rows);
  });
});

app.listen(3001, () => console.log('Backend działa na http://localhost:3001'));
```

---

## 🎨 Frontend (React)

**frontend/src/App.js**

```jsx
import { useState, useEffect } from 'react';

export default function App() {
  const [users, setUsers] = useState([]);
  const [form, setForm] = useState({ name: '', email: '', department: '' });
  const [message, setMessage] = useState('');

  const fetchUsers = () => {
    fetch('http://localhost:3001/users')
      .then(res => res.json())
      .then(data => setUsers(data));
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  const handleChange = (e) => {
    setForm({ ...form, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!form.name || !form.email || !form.department) {
      setMessage('Wszystkie pola są wymagane');
      return;
    }

    fetch('http://localhost:3001/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form)
    })
      .then(res => {
        if (!res.ok) throw new Error('Błąd podczas dodawania');
        return res.json();
      })
      .then(() => {
        setMessage('Pracownik dodany');
        setForm({ name: '', email: '', department: '' });
        fetchUsers();
      })
      .catch(() => setMessage('Błąd'));
  };

  return (
    <div style={{ padding: 20 }}>
      <h2>Dodaj pracownika</h2>
      <form onSubmit={handleSubmit}>
        <input name="name" placeholder="Imię" value={form.name} onChange={handleChange} /><br />
        <input name="email" placeholder="Email" value={form.email} onChange={handleChange} /><br />
        <input name="department" placeholder="Dział" value={form.department} onChange={handleChange} /><br />
        <button type="submit">Zapisz</button>
      </form>
      <p>{message}</p>

      <h2>Lista pracowników</h2>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            {user.name} ({user.email}) - {user.department}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

**frontend/index.html** + konfiguracja przez Vite / CRA lub inny sposób, zależnie od preferencji.

---

## ✅ Co działa:

* ✔️ Walidacja frontendowa
* ✔️ Endpoint `POST /users`
* ✔️ Zapis do SQLite
* ✔️ Lista aktualizuje się po dodaniu pracownika

---

## 🔜 Co dalej?

* Edycja / usuwanie
* Lepsza walidacja (regex na email)
* Stylizacja (np. Tailwind / MUI)
* Logowanie admina

Chcesz, żebym przygotował plik `package.json` i komendy startowe?
