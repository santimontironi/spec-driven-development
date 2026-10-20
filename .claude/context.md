# context.md

## Proyecto
Panel de administración web para gestionar el catálogo de productos de un negocio chico.

## Objetivo
Que un administrador pueda iniciar sesión y, desde el navegador, ver, crear, editar y eliminar productos.

## Usuarios
- **Administrador:** es el único tipo de usuario. Todos los administradores tienen los mismos permisos.
- No hay usuarios públicos: sin iniciar sesión solo se ve la pantalla de login.

## Alcance
- Login con email y contraseña.
- Logout.
- La sesión se mantiene al recargar la página mientras no expire.
- Listado de productos.
- Alta, edición y baja de productos.

## Fuera de alcance
- Registro de usuarios (los administradores se crean con un script de seed).
- Recuperación de contraseña.
- Roles o permisos distintos entre usuarios.
- Tienda pública, carrito o pagos.
- Imágenes, categorías, búsqueda, filtros y paginación.
- Deploy.

## Supuestos
- El catálogo es chico (decenas de productos), así que no hace falta paginar.
- La aplicación está en español y los precios se manejan en pesos argentinos.