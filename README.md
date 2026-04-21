# Galería Demo

Plantilla full-stack para catálogos de indumentaria y accesorios. Diseñada para ser adaptada rápidamente a nuevos clientes: con un Firebase propio y editar `tienda/content.js`, tenés una demo personalizada en minutos.

## Características

**Tienda pública**
- Carruseles por categoría con scroll horizontal y botones de navegación
- Modal fullscreen con soporte de teclado (←/→/Esc), swipe táctil y precarga de imágenes
- CTA directos a WhatsApp con mensajes pre-armados por producto
- Skeleton loaders mientras carga el catálogo
- Paginación ("Ver más") para categorías con más de 12 productos
- Sincronización en tiempo real vía Firestore `onSnapshot()`
- Insignia SaaS opcional (configurable en `content.js`)

**Panel de administración** (`/admin/login.html`)
- Carga en lote: seleccioná múltiples imágenes, asignale nombre/precio/categoría a cada una y subí todo junto
- CRUD completo de productos: crear, editar (nombre, precio, categoría) y eliminar
- Gestión de categorías: crear, reordenar y eliminar (con borrado en cascada opcional)
- Filtrado de productos por categoría y selección masiva para borrado
- Notificaciones toast y diálogos de confirmación antes de acciones destructivas
- Sincronización en tiempo real del listado de productos

**Backend**
- Optimización automática de imágenes: Sharp convierte a WebP y redimensiona a 1200 px máximo
- Rate limiting diferenciado: 100 req/15 min general, 30 uploads/15 min, 10 intentos de auth/15 min
- CORS + validación de host por whitelist
- Paginación por cursor en `GET /api/productos`

## Stack

| Capa | Tecnología |
|------|-----------|
| Frontend | HTML5 · CSS3 · JavaScript (ES modules, vanilla) |
| Backend | Node.js 18 · Express 5 |
| Base de datos | Firebase Firestore (tiempo real) |
| Almacenamiento | Firebase Cloud Storage (imágenes WebP) |
| Procesamiento de imágenes | Sharp |
| Upload | Multer (memoria, 10 MB máx) |
| Seguridad | cors · express-rate-limit · dotenv |

## Estructura del proyecto

```
galeria-demo/
├── server.js               # Express: API REST + serve de estáticos
├── .env                    # Variables de entorno (no subir al repo)
├── serviceAccountKey.json  # Credenciales Firebase Admin (solo local)
│
├── tienda/                 # Tienda pública
│   ├── index.html          # Shell HTML estático
│   ├── content.js          # ← Primer archivo a editar por cliente
│   ├── scripts.js          # Lógica Firebase, carruseles, modal, WhatsApp
│   └── styles.css
│
└── admin/                  # Panel de administración
    ├── login.html          # Autenticación por ADMIN_KEY
    ├── admin.html          # Dashboard
    ├── admin.js            # Lógica CRUD, upload en lote, Firestore sync
    └── admin.css
```

## Modelo de datos (Firestore)

**Colección `productos`**
```
{ id, nombre, precio (string), categoria (string lowercase), imagen (URL firmada) }
```

**Colección `categorias`**
```
{ id, nombre (string lowercase), orden (number) }
```

## API

| Método | Ruta | Auth | Descripción |
|--------|------|------|-------------|
| GET | `/api/productos` | — | Listar (paginado, `?limit=50&startAfter=cursor`) |
| POST | `/api/productos` | ✓ | Crear con imagen (multipart/form-data) |
| PATCH | `/api/productos/:id` | ✓ | Editar nombre/precio/categoría |
| DELETE | `/api/productos/:id` | ✓ | Eliminar producto e imagen |
| GET | `/api/categorias` | — | Listar ordenadas por `orden` |
| POST | `/api/categorias` | ✓ | Crear categoría |
| PATCH | `/api/categorias/:id` | ✓ | Actualizar orden |
| DELETE | `/api/categorias/:id` | ✓ | Eliminar (`?cascade=true` borra productos) |
| GET | `/api/auth/verify` | ✓ | Verificar admin key |
| GET | `/healthz` | — | Health check rápido |
| GET | `/healthz/deep` | — | Health check completo (Firestore + Storage) |

Auth: header `x-admin-key: <ADMIN_KEY>`.

## Configuración rápida

```bash
git clone https://github.com/azael-p/galeria-demo.git
cd galeria-demo
npm install
```

1. Descargá tu cuenta de servicio de Firebase y guardala como `serviceAccountKey.json`.
2. Creá `.env`:
   ```env
   ADMIN_KEY=tu-clave-secreta
   NODE_ENV=development
   FIREBASE_STORAGE_BUCKET=gs://tu-proyecto.appspot.com
   ```
3. Reemplazá el objeto `firebaseConfig` en `tienda/scripts.js` y `admin/admin.js` con los datos de tu app web Firebase.
4. Editá `tienda/content.js` con el nombre de tienda, slogan, WhatsApp e Instagram.

```bash
npm start   # http://localhost:3000
```

- `/` → galería pública
- `/admin/login.html` → panel de administración

## Adaptar para un nuevo cliente

| Archivo | Qué cambiar |
|---------|-------------|
| `tienda/content.js` | Nombre, slogan, WhatsApp, Instagram, textos |
| `tienda/scripts.js` | `firebaseConfig` |
| `admin/admin.js` | `firebaseConfig` |
| `server.js` | `ALLOWED_ORIGINS` y `ALLOWED_HOSTS` |
| `.env` | `ADMIN_KEY`, `FIREBASE_STORAGE_BUCKET` |

## Deploy (Render / Railway)

1. Creá un Web Service apuntando a este repo (runtime: Node 18).
2. Configurá las variables de entorno:
   ```
   ADMIN_KEY=...
   NODE_ENV=production
   FIREBASE_STORAGE_BUCKET=gs://...
   FIREBASE_SERVICE_ACCOUNT=<JSON de cuenta de servicio en string o base64>
   ```
3. Agregá tu dominio de producción en `ALLOWED_ORIGINS` y `ALLOWED_HOSTS` de `server.js`.

## Requisitos previos

- Node.js 18+
- Proyecto Firebase con Firestore y Cloud Storage habilitados
- Cuenta de servicio con permisos de Firebase Admin SDK

## Licencia

Uso personal/educativo. Antes de publicar: reemplazá todas las claves, revisá las reglas de seguridad de Firestore y Storage, y asegurate de no subir `serviceAccountKey.json` al repositorio.
