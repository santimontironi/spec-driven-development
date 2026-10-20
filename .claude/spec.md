# spec.md

## Stack
- **Frontend:** React + TypeScript con Vite y React Router.
- **Backend:** Node.js + Express 5 + TypeScript.
- **Base de datos:** MongoDB con Mongoose.
- **Autenticación:** JWT guardado en una cookie HttpOnly.

## Dependencias
- **Backend:** express, mongoose, jsonwebtoken, bcrypt, cookie-parser, cors, zod, dotenv.
- **Frontend:** react, react-dom, react-router-dom. Las peticiones se hacen con `fetch` nativo.

## Variables de entorno (backend)
| Variable | Ejemplo |
|---|---|
| PORT | 3000 |
| MONGODB_URI | mongodb://localhost:27017/sdd-productos |
| JWT_SECRET | string largo y aleatorio |
| CLIENT_URL | http://localhost:5173 |
| ADMIN_EMAIL | admin@example.com |
| ADMIN_PASSWORD | solo se usa en el seed |

## Modelo de datos

### User
| Campo | Tipo | Reglas |
|---|---|---|
| name | String | Requerido |
| email | String | Requerido, único, se guarda en minúsculas |
| passwordHash | String | Requerido, hash con bcrypt (10 rondas) |
| createdAt / updatedAt | Date | Automáticos (timestamps) |

### Product
| Campo | Tipo | Reglas |
|---|---|---|
| name | String | Requerido, trim, de 2 a 100 caracteres |
| price | Number | Requerido, ≥ 0, hasta 2 decimales |
| stock | Number | Requerido, entero, ≥ 0 |
| createdAt / updatedAt | Date | Automáticos (timestamps) |

Las respuestas devuelven `id` en lugar de `_id` y no incluyen `__v`.

## Autenticación
1. `POST /auth/login` busca el usuario por email y compara la contraseña con bcrypt.
2. Si es correcta, firma un JWT con payload `{ sub: userId }` y expiración de 8 horas.
3. Lo envía en la cookie `token` con `httpOnly: true`, `sameSite: 'lax'`, `secure: true` en producción y `maxAge` de 8 horas.
4. El middleware `requireAuth` lee la cookie, verifica el JWT y guarda el id en `req.userId`. Si la cookie falta, es inválida o expiró, responde 401.
5. `POST /auth/logout` borra la cookie.
6. CORS se configura con `origin: CLIENT_URL` y `credentials: true`. El frontend hace todas las peticiones con `credentials: 'include'`.

## Endpoints

### Auth
| Método | Ruta | Requiere login | Body | Respuestas |
|---|---|---|---|---|
| POST | /auth/login | No | `{ email, password }` | 200 usuario público + cookie · 400 datos inválidos · 401 credenciales incorrectas |
| POST | /auth/logout | No | — | 204 |
| GET | /auth/me | Sí | — | 200 usuario público · 401 |

Usuario público: `{ id, name, email }`.

### Productos
Todas las rutas requieren login. Sin una sesión válida responden 401.

| Método | Ruta | Body | Respuestas |
|---|---|---|---|
| GET | /products | — | 200 lista ordenada del más nuevo al más viejo |
| GET | /products/:id | — | 200 · 404 |
| POST | /products | `{ name, price, stock }` | 201 producto creado · 400 |
| PUT | /products/:id | `{ name, price, stock }` | 200 producto actualizado · 400 · 404 |
| DELETE | /products/:id | — | 204 · 404 |

## Formato de respuestas

Producto:
```json
{
  "id": "6712f0c2a1b2c3d4e5f60789",
  "name": "Yerba mate 1 kg",
  "price": 4500,
  "stock": 20,
  "createdAt": "2026-10-20T13:00:00.000Z",
  "updatedAt": "2026-10-20T13:00:00.000Z"
}
```

Error:
```json
{ "message": "Credenciales incorrectas" }
```

Error de validación (400):
```json
{
  "message": "Datos inválidos",
  "errors": [{ "field": "price", "message": "El precio no puede ser negativo" }]
}
```

## Frontend
| Ruta | Acceso | Contenido |
|---|---|---|
| /login | Pública | Formulario de email y contraseña. Si ya hay sesión, redirige a /products. |
| /products | Protegida | Tabla con nombre, precio y stock; botones Editar y Eliminar; botón "Nuevo producto"; botón "Cerrar sesión". |
| /products/new | Protegida | Formulario de alta. |
| /products/:id/edit | Protegida | Formulario de edición con los datos ya cargados. |

- Al iniciar, la app llama a `GET /auth/me` para saber si hay sesión.
- Una ruta protegida sin sesión redirige a /login.
- Si cualquier petición devuelve 401 (salvo el login), se limpia la sesión y se redirige a /login.
- Eliminar pide confirmación antes de llamar a la API.
- Los precios se muestran con formato de moneda argentina (ej.: $ 4.500,00).
- Los errores de validación del backend se muestran debajo del campo que corresponde.

## Datos iniciales
`npm run seed` crea el administrador con `ADMIN_EMAIL` y `ADMIN_PASSWORD` si todavía no existe.

## Casos borde
- El email del login se normaliza a minúsculas: `Admin@Mail.com` y `admin@mail.com` son el mismo usuario.
- Si el email no existe o la contraseña es incorrecta, la respuesta es la misma (401 con el mismo mensaje), para no revelar qué dato falló.
- Un token expirado o modificado devuelve 401.
- Un `:id` que no es un ObjectId válido devuelve 404, igual que un producto inexistente.
- Editar o eliminar un producto que ya fue eliminado devuelve 404.
- Si `price` o `stock` llegan como texto (`"100"`), la respuesta es 400. El frontend convierte los valores del formulario a número antes de enviarlos.
- Los campos extra del body (`_id`, `createdAt`, etc.) se ignoran.
- Se permiten productos con el mismo nombre.
- Sin productos, `GET /products` devuelve `[]` y el frontend muestra "Todavía no hay productos".

## Criterios de aceptación
- [ ] Login con credenciales correctas → 200, se setea la cookie `token` HttpOnly y la respuesta no incluye `password` ni `passwordHash`.
- [ ] Login con credenciales incorrectas → 401 "Credenciales incorrectas".
- [ ] `GET /products` sin cookie → 401.
- [ ] `POST /products` con datos válidos → 201 y devuelve el producto creado.
- [ ] `POST /products` con `price: -5` → 400, con el error en el campo `price`.
- [ ] `DELETE /products/:id` de un producto existente → 204, y el producto deja de aparecer en el listado.
- [ ] Después de `POST /auth/logout`, `GET /auth/me` → 401.
- [ ] En el frontend, entrar a /products sin sesión redirige a /login.
- [ ] Recargar la página con la sesión activa no cierra la sesión.