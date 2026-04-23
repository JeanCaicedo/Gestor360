# Gestor360

Monorepo para una aplicación de gestión orientada a administración residencial (usuarios, amenidades, reservas y visitas). Incluye una API en NestJS con Prisma y PostgreSQL, y un espacio reservado para un frontend web aún por implementar.

![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Prisma](https://img.shields.io/badge/Prisma-2D3748?style=for-the-badge&logo=prisma&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)

## Estado del proyecto

Proyecto en fase inicial. La API ya tiene esquema Prisma definido (User, Amenity, Reservation, Visit, VisitEvent), un endpoint de health y el cableado con PostgreSQL vía `@prisma/adapter-pg`. La carpeta `apps/web` está vacía: el frontend todavía no se ha iniciado.

## Tecnologías

- **API:** NestJS 11, TypeScript 5.7, Prisma 7, `@prisma/adapter-pg`, `pg`, dotenv
- **BD:** PostgreSQL 16 (vía Docker) con Prisma como ORM
- **Cache/colas:** Redis 7 (disponible en `docker-compose`, aún sin integración en el código)
- **Web:** pendiente (`apps/web` vacío)
- **Infraestructura:** npm workspaces (`apps/*`, `packages/*`), docker-compose para servicios de datos
- **Calidad:** ESLint 9, Prettier 3, Jest 30

## Estructura del monorepo

```
Gestor360/
├── apps/
│   ├── api/                 # API NestJS
│   │   ├── prisma/
│   │   │   ├── schema.prisma
│   │   │   ├── prisma.module.ts
│   │   │   ├── prisma.service.ts
│   │   │   └── migrations/
│   │   ├── src/
│   │   │   ├── main.ts
│   │   │   ├── app.module.ts
│   │   │   └── health.controller.ts
│   │   ├── prisma.config.ts
│   │   └── package.json
│   └── web/                 # Frontend (aún no iniciado)
├── docker-compose.yml       # PostgreSQL + Redis
├── package.json             # Workspaces + scripts raíz
└── package-lock.json
```

## Modelo de datos

El esquema Prisma define los siguientes modelos y enums:

- `User` (con roles `ADMIN`, `RESIDENT`, `SECURITY`, `MODERATOR`)
- `Amenity` (espacios comunes con capacidad y reglas en JSON)
- `Reservation` (estado `ACTIVE` o `CANCELLED`, con índices por amenidad y por usuario)
- `Visit` (estados `PENDING`, `CHECKED_IN`, `CHECKED_OUT`, `CANCELLED`, con token QR único)
- `VisitEvent` (registro de acciones `CHECK_IN` / `CHECK_OUT`)

## Requisitos previos

- Node.js 22 o superior (recomendado, acorde a `@types/node` ^22)
- npm 10+ (por los workspaces)
- Docker y Docker Compose (recomendado para la BD)
- PostgreSQL 16 local o accesible si no se usa Docker

## Instalación

```bash
git clone https://github.com/JeanCaicedo/Gestor360.git
cd Gestor360
npm install
```

## Levantar servicios con Docker

```bash
docker compose up -d
```

Esto arranca:

- PostgreSQL en `localhost:5432` (usuario/clave/db: `gestor360`)
- Redis en `localhost:6379`

## Variables de entorno

Crear `apps/api/.env` con, al menos:

```env
DATABASE_URL="postgresql://gestor360:gestor360@localhost:5432/gestor360"
PORT=3000
```

`PrismaService` lanza un error explícito si `DATABASE_URL` no está definida.

## Migraciones Prisma

```bash
cd apps/api
npx prisma migrate dev
npx prisma generate
```

Ya existe una migración inicial en `apps/api/prisma/migrations/20260114013445_init`.

## Ejecución

Desde la raíz del monorepo:

```bash
# API en modo desarrollo (watch)
npm run dev:api

# Frontend (aún no implementado; el script fallará hasta que exista apps/web)
npm run dev:web
```

O directamente dentro de la API:

```bash
cd apps/api
npm run start:dev
```

Endpoint disponible:

- `GET /health` — devuelve `{ ok: true, userCount: <n> }`, verificando a la vez la conexión a PostgreSQL.

## Scripts disponibles

### Raíz (`package.json`)

| Script     | Descripción                                         |
| ---------- | --------------------------------------------------- |
| `dev:web`  | Inicia el workspace `@gestor360/web` en desarrollo  |
| `dev:api`  | Inicia la API NestJS en modo watch                  |
| `lint`     | Ejecuta `lint` en todos los workspaces              |
| `test`     | Ejecuta `test` en todos los workspaces              |

### API (`apps/api/package.json`)

| Script        | Descripción                                 |
| ------------- | ------------------------------------------- |
| `start`       | Inicia la API con `nest start`              |
| `start:dev`   | Modo desarrollo con recarga                 |
| `start:debug` | Desarrollo con debugger                     |
| `start:prod`  | Ejecuta el build compilado (`dist/main`)    |
| `build`       | Compila con `nest build`                    |
| `format`      | Formatea `src/` y `test/` con Prettier      |
| `lint`        | Linting con ESLint y autofix                |
| `test`        | Tests unitarios con Jest                    |
| `test:watch`  | Jest en modo watch                          |
| `test:cov`    | Jest con reporte de cobertura               |
| `test:e2e`    | Tests end-to-end                            |

## Roadmap

- [ ] Iniciar el frontend en `apps/web` (la carpeta está vacía)
- [ ] Implementar módulos de dominio en la API: autenticación, usuarios, amenidades, reservas y visitas
- [ ] Integrar Redis (definido en `docker-compose.yml` pero sin uso en el código aún)
- [ ] Añadir `.env.example` y documentación de configuración
- [ ] Reemplazar el `README.md` por defecto de NestJS en `apps/api`
- [ ] Añadir LICENSE

## Autor

Jean Caicedo — [@JeanCaicedo](https://github.com/JeanCaicedo)
