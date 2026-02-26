# Galería Demo

Galería Demo es mi proyecto full-stack para mostrar catálogos de indumentaria y accesorios. Lo hice para aprender, practicar y tener una base reusable que pueda ofrecer a tiendas pequeñas como servicio.

## ¿Qué incluye?

- Sitio público con carruseles, modal de zoom y CTA directos a WhatsApp/Instagram.
- Panel de administración para cargar productos en lote, limpiar stock y gestionar categorías.
- Backend Express que conecta con Firebase (Firestore + Storage) para mantener todo sincronizado en tiempo real.

## Stack

- **Frontend**: HTML5 + CSS3 + JS vanilla (`tienda/`), animaciones y modal responsivo.
- **Panel admin**: HTML/CSS + Firebase Web SDK (`admin/`).
- **Backend**: Node 18, Express 5, Multer, firebase-admin, CORS selectivo.
- **Infra**: Proyecto Firebase propio (Firestore + Storage) y cualquier hosting Node (Render, Railway, VPS).

## Requisitos

1. Node.js 18+.
2. Proyecto Firebase con Firestore y Storage habilitados.
3. Cuenta de servicio (JSON) con permisos de Firebase Admin.
4. Clave `ADMIN_KEY` para proteger el panel y las APIs.

## Configuración rápida

```bash
git clone https://github.com/azael-p/galeria-demo.git
cd galeria-demo
npm install
```

1. Duplica `serviceAccountKey.json.example` (o descarga tu JSON) y renómbralo a `serviceAccountKey.json`.
2. Crea `.env` con:
   ```env
   ADMIN_KEY=tu-clave
   NODE_ENV=development
   FIREBASE_STORAGE_BUCKET=tu-bucket.appspot.com
   ```
   (Opcional: pega la cuenta de servicio en `FIREBASE_SERVICE_ACCOUNT` como JSON/base64).
3. Reemplaza `firebaseConfig` en `tienda/scripts.js` y `admin/admin.js` con los datos de tu app web de Firebase.
4. Personaliza textos y contacto editando `tienda/content.js`.

## Ejecutar localmente

```bash
npm start      # http://localhost:3000
```

- `/` → galería pública.
- `/admin/login.html` → panel (ingresá `ADMIN_KEY`). Desde ahí gestionás productos y categorías en tiempo real.

## Deploy sugerido

1. Crea un Web Service en Render/Railway (Node 18).
2. Configura las variables (`ADMIN_KEY`, `FIREBASE_STORAGE_BUCKET`, `FIREBASE_SERVICE_ACCOUNT`, `NODE_ENV=production`) y subí este repo.
3. Añadí tu dominio en `ALLOWED_ORIGINS`/`ALLOWED_HOSTS` de `server.js`.

## Por qué existe

Galería Demo nació como práctica personal y como base para ofrecer un catálogo online simple a emprendedoras que necesitan mostrar su stock sin plataformas complejas. Con solo apuntarlo a un nuevo Firebase y editar `content.js`, puedo armar demos personalizadas en minutos.

## Licencia

Uso personal/educativo. Si lo reutilizás, recordá reemplazar todas las claves y revisar las reglas de Firestore antes de publicar.
