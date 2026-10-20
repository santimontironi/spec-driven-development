# rules.md

## Antes de escribir código
- Leer context.md y spec.md.
- Si algo es ambiguo, contradictorio o no está definido, preguntar antes de implementar. No suponer.
- Proponer un plan con los archivos a crear o modificar y esperar confirmación.

## Convenciones
- TypeScript en modo `strict`, tanto en el frontend como en el backend. No usar `any`.
- El código (variables, funciones, archivos y rutas) va en inglés. Los textos que ve el usuario van en español.
- `camelCase` para variables y funciones; `PascalCase` para componentes, tipos e interfaces.
- Los archivos del backend llevan el sufijo de su capa: `auth.service.ts`, `products.controller.ts`, `product.model.ts`.
- En React, un componente por archivo, con el mismo nombre que el componente (`ProductForm.tsx`).
- Usar `async/await` en lugar de `.then()` encadenados.

## Estructura
```
server/src/
  config/        # variables de entorno y conexión a MongoDB
  models/        # schemas de Mongoose
  schemas/       # schemas de validación (zod)
  routes/
  controllers/
  services/
  middlewares/   # requireAuth, validate, errorHandler
  utils/
  index.ts
client/src/
  api/           # todas las llamadas al backend
  pages/
  components/
  context/       # AuthContext
  routes/        # ProtectedRoute
```

## Cómo generar cada cosa
- **Controllers:** solo leen `req`, llaman al servicio y responden. La lógica va en el servicio, no en el controller.
- **Services:** tienen la lógica de negocio y el acceso a la base de datos con Mongoose.
- **Validación:** todo body se valida con un schema de zod y el middleware `validate` antes de llegar al controller.
- **Errores:** se lanzan con la clase `AppError(status, message)` y los responde el middleware `errorHandler`. No repetir try/catch en cada controller.
- **Usuarios en las respuestas:** siempre pasan por `toPublicUser(user)`, que devuelve solo `id`, `name` y `email`.
- **Rutas de productos:** `requireAuth` se aplica al router completo, no ruta por ruta.
- **Frontend:** los componentes no llaman a `fetch` directamente. Usan las funciones de `src/api`, que siempre envían `credentials: 'include'`.
- **Formularios:** validan en el cliente con los mismos límites que spec.md, pero el backend es la fuente de verdad.

## Prohibido
- Devolver `password` o `passwordHash` en cualquier respuesta, o escribirlos en los logs.
- Guardar el JWT en `localStorage` o `sessionStorage`, o leerlo desde JavaScript. Va solo en la cookie HttpOnly.
- Crear rutas de productos sin `requireAuth`.
- Agregar dependencias que no estén en spec.md sin preguntar antes.
- Agregar funcionalidades que context.md deja fuera de alcance (registro, roles, imágenes, paginación, etc.).
- Hardcodear secretos, contraseñas o URLs. Todo va en variables de entorno: se commitea `.env.example`, nunca `.env`.
- Usar datos reales de clientes en seeds, tests o ejemplos.
- Modificar context.md, spec.md o rules.md. Si hace falta un cambio, proponerlo y esperar confirmación.

## Al terminar
- Verificar el resultado contra los criterios de aceptación de spec.md.
- Listar los archivos que se crearon o modificaron y qué criterio cubre cada cambio.