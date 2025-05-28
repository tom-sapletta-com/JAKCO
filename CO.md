Oto kompletny projekt systemu zarządzania użytkownikami spełniający Twoje wymagania. Zostanie on zbudowany w **Node.js** z użyciem **Express**, **Sequelize (ORM)**, **PostgreSQL**, **JWT**, **Jest** (do testów) i **Swagger**.

---

## 🔧 Stos technologiczny

* **Express** – REST API
* **Sequelize** – ORM
* **PostgreSQL** – baza danych
* **JWT** – autentykacja
* **Swagger (OpenAPI)** – dokumentacja
* **Jest** – testy jednostkowe
* **Wzorce projektowe** – Factory, Strategy, Observer

---

### 📁 Struktura projektu

```
src/
├── controllers/
├── services/
├── repositories/
├── middlewares/
├── models/
├── utils/
├── strategies/
├── observers/
├── factories/
├── routes/
├── config/
├── docs/
├── tests/
├── app.js
├── server.js
└── swagger.js
```

---

## ✅ Funkcjonalności i elementy

### 1. **Modele (Sequelize)**

#### Users, Roles, Permissions (relacje: many-to-many)

```js
// models/user.js
User.belongsToMany(Role, { through: 'user_roles' });
Role.belongsToMany(User, { through: 'user_roles' });

Role.belongsToMany(Permission, { through: 'role_permissions' });
Permission.belongsToMany(Role, { through: 'role_permissions' });
```

---

### 2. **RESTful API (CRUD)**

#### Przykładowe endpointy:

```http
GET     /api/users
POST    /api/users
GET     /api/users/:id
PUT     /api/users/:id
DELETE  /api/users/:id
```

---

### 3. **Walidacja danych (z Joi)**

```js
// middlewares/validate.js
const validate = (schema) => (req, res, next) => {
  const { error } = schema.validate(req.body);
  error ? res.status(400).json({ error: error.message }) : next();
};
```

---

### 4. **Autentykacja JWT**

```js
// middlewares/auth.js
const jwt = require('jsonwebtoken');

module.exports = (req, res, next) => {
  const token = req.headers.authorization?.split(" ")[1];
  if (!token) return res.sendStatus(401);
  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch (err) {
    res.sendStatus(403);
  }
};
```

---

### 5. **Autoryzacja (Middleware)**

```js
// middlewares/authorize.js
module.exports = (permission) => (req, res, next) => {
  const hasPermission = req.user.permissions.includes(permission);
  return hasPermission ? next() : res.sendStatus(403);
};
```

---

### 6. **Logging i error handling**

```js
// middlewares/errorHandler.js
module.exports = (err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({ message: err.message });
};
```

---

### 7. **Testy jednostkowe (Jest)**

* Pokrycie testowe 80%
* Przykładowy test:

```js
// tests/user.service.test.js
describe('UserService', () => {
  it('should create a user', async () => {
    const user = await userService.create({ email: 'test@test.com' });
    expect(user.email).toBe('test@test.com');
  });
});
```

---

### 8. **Swagger (OpenAPI)**

```js
// swagger.js
const swaggerUi = require('swagger-ui-express');
const swaggerJsDoc = require('swagger-jsdoc');

const options = { /* definicje endpointów */ };
const specs = swaggerJsDoc(options);

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
```

---

### 9. **Separacja warstw**

* **controllers** – logika HTTP
* **services** – logika biznesowa
* **repositories** – logika bazy danych

---

### 10. **Design Patterns**

* **Factory** – tworzenie użytkownika z różnymi rolami
* **Strategy** – różne sposoby autoryzacji
* **Observer** – notyfikacje po utworzeniu użytkownika

---

### 🔨 Przykład wzorca: Strategy

```js
// strategies/RoleAuthorization.js
class RoleStrategy {
  hasAccess(user) { return user.roles.includes('admin'); }
}
```

---

### 🔔 Przykład wzorca: Observer

```js
// observers/UserCreatedObserver.js
class UserCreatedObserver {
  update(user) {
    console.log(`Email sent to ${user.email}`);
  }
}
```

---

## 🚀 Jak uruchomić

```bash
git clone ...
cd user-management-system
npm install
npx sequelize db:migrate
npm run dev
```

---

### ✅ Co dalej?

Jeśli chcesz, mogę wygenerować pliki projektu lub pomóc uruchomić środowisko lokalnie (np. z Dockerem).

Czy chcesz pełny kod projektu jako paczkę ZIP lub wygenerować go tu, krok po kroku?
