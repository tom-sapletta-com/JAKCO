# JAKCO






#dowod #metoda #JAK


```
JAK: Admin chce dodać nowego pracownika do systemu

Scenariusz:
1. Admin klika "Dodaj pracownika" 
2. Wypełnia formularz: imię, email, dział
3. Klika "Zapisz" 
4. Widzi komunikat "Pracownik dodany" i wraca do listy
5. Nowy pracownik pojawia się na liście z możliwością edycji

CO teraz potrzebuję:
- Formularz z walidacją
- Endpoint POST /users  
- Zapis do bazy
- Lista użytkowników z refresh

Zrób to najprostszym sposobem. Potem dodamy więcej.
```

[Rezultat chatgpt](JAK.md)
```
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
