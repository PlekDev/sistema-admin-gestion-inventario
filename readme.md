# Admin Panel — Sistema de Inventario y Ventas

Panel de administración interno: gestión de productos, categorías, inventario, usuarios y reporte de ventas, con una terminal de punto de venta (POS) integrada.

![Status](https://img.shields.io/badge/Status-Plantilla-blue)
![Node](https://img.shields.io/badge/Node-18+-green)
![Next.js](https://img.shields.io/badge/Next.js-14-black)
![PostgreSQL](https://img.shields.io/badge/DB-PostgreSQL-blue)

> Plantilla base neutra, lista para personalizar con tu propia marca, datos y despliegue.

---

## 📖 Tabla de Contenidos
- [¿Qué es este repo?](#-qué-es-este-repo)
- [Arquitectura](#-arquitectura)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Instalación y Uso](#-instalación-y-uso)
- [Módulos del Panel](#-módulos-del-panel)
- [Usuarios de prueba](#-usuarios-de-prueba)
- [Roadmap](#-roadmap)

---

## 🧭 ¿Qué es este repo?

Monorepo con dos aplicaciones:

| App | Descripción | Puerto |
|-----|-------------|--------|
| `apps/web` | Panel de administración + POS (Next.js / React) | `3001` |
| `apps/api` | API REST (Node.js / Express) sobre PostgreSQL | `3002` |

El frontend hace *proxy* de las rutas `/api/*` hacia la API mediante `rewrites` en `next.config.js`.

---

## 🏗 Arquitectura

```
┌─────────────────────────────────────────┐
│        Navegador (Panel + POS)          │
│           Next.js  ·  puerto 3001       │
└────────────────────┬────────────────────┘
                     │ /api/*  (rewrite)
┌────────────────────▼────────────────────┐
│        Node.js / Express  ·  3002       │
│                                         │
│  /api/auth        /api/products         │
│  /api/sales       /api/health           │
│  /api/dashboard   /api/inventory/*      │
└────────────────────┬────────────────────┘
                     │ pg (PostgreSQL)
┌────────────────────▼────────────────────┐
│         PostgreSQL (Neon o local)       │
│                                         │
│  usuarios · categorias · productos      │
│  ventas · detalle_venta                 │
│  movimientos_inventario                 │
└─────────────────────────────────────────┘
```

---

## 📂 Estructura del Proyecto

```text
admin-panel/
├── apps/
│   ├── api/
│   │   └── src/
│   │       ├── db/
│   │       │   └── index.js              # Pool de conexión a PostgreSQL
│   │       ├── modules/
│   │       │   ├── auth.js               # Login y usuarios
│   │       │   ├── products.js           # Productos e inventario
│   │       │   └── sales.js              # Registro y reporte de ventas
│   │       └── index.js                  # Servidor Express
│   │
│   └── web/
│       └── app/
│           ├── layout.tsx                # Layout raíz
│           ├── page.tsx                  # Panel de administración + POS
│           └── globals.css               # Estilos base
│
├── infra/
│   ├── docker-compose.yml                # Opcional: n8n para automatizaciones
│   ├── init-neon.js                      # Inicializa el schema en PostgreSQL
│   └── update_schema.sql                 # Migración / definición de tablas
│
├── iniciar.sh                            # Arranque rápido (Linux/Mac)
├── ejecutar.bat                          # Arranque rápido (Windows)
├── reparar_instalar.bat                  # Reinstalación limpia (Windows)
└── readme.md
```

---

## 🚀 Instalación y Uso

### Requisitos
- Node.js 18+
- Una base de datos PostgreSQL (local o en la nube, p. ej. Neon)

### Configuración

1. Crea `apps/api/.env` con tu cadena de conexión:

   ```env
   PORT=3002
   DATABASE_URL=postgresql://usuario:contraseña@host:5432/mi_base_de_datos?sslmode=require
   ```

2. Inicializa el schema (crea tablas y funciones):

   ```bash
   cd infra
   node init-neon.js
   ```

### Arranque rápido

**Linux / Mac:**
```bash
chmod +x iniciar.sh
./iniciar.sh
```

**Windows:**
```bat
ejecutar.bat
```

- Panel: `http://localhost:3001`
- API health: `http://localhost:3002/api/health`

---

## 🧩 Módulos del Panel

| Módulo | Descripción |
|--------|-------------|
| 📊 **Dashboard** | Métricas del día: ventas, ingresos, ganancia estimada, ticket promedio, top productos y alertas de stock bajo |
| 📦 **Inventario** | Alta, edición y baja de productos con código de barras, precio, costo, categoría y stock |
| 🛒 **Ventas (POS)** | Terminal de cobro con carrito, escáner de código de barras y métodos de pago |
| 🧾 **Reportes** | Historial de ventas filtrable por periodo |
| 🔔 **Alertas** | Monitoreo de productos con stock por debajo del mínimo |

---

## 👤 Usuarios de prueba

Los hashes de contraseña iniciales son *placeholders*, por lo que el login de desarrollo valida solo por email (ver `apps/api/src/modules/auth.js`). Reemplaza por `bcrypt` antes de producción.

| Correo | Rol |
|--------|-----|
| `admin@example.com` | Administrador |
| `cajero1@example.com` | Cajero |
| `cajero2@example.com` | Cajero |

---

## 🗺 Roadmap

- [x] POS base: cobro, carrito e inventario
- [x] Panel admin: CRUD de productos y reportes de ventas
- [ ] Autenticación con hash (bcrypt) y sesiones
- [ ] Control de inventario: entradas y salidas por ubicación
- [ ] Alertas de stock bajo y caducidades vía n8n
- [ ] Reportes consolidados multi-canal

---

## 📄 Licencia

Plantilla base — personaliza esta sección con la licencia y propiedad que corresponda.
