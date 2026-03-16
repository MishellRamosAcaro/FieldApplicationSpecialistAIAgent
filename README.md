# Documentación Docker Compose — FASEnvDev

Este documento describe la configuración y el uso del entorno Docker del proyecto FASEnvDev (frontend Website3.0, API Atlas, PostgreSQL y CloudBeaver).

## Resumen de servicios

| Servicio   | Descripción                    | Puerto | Imagen/Build              |
|-----------|--------------------------------|--------|---------------------------|
| **nginx** | Frontend Vue.js (Website3.0)   | 8080   | Build desde `./Website3.0` |
| **fastapi** | API backend (Atlas)          | 8000   | Build desde `./Atlas`     |
| **postgres** | Base de datos PostgreSQL 16  | 5432   | `postgres:16.10-alpine`   |
| **dbeaver** | CloudBeaver (gestor BD)      | 8978   | `dbeaver/cloudbeaver:25.2.0` |

## Requisitos previos

- [Docker](https://docs.docker.com/get-docker/) y [Docker Compose](https://docs.docker.com/compose/install/)
- Archivo `.env` en la raíz del proyecto (ver sección [Variables de entorno](#variables-de-entorno))

## Variables de entorno

Copia o crea un `.env` en la raíz del proyecto. Ejemplo:

```env
# DBeaver / CloudBeaver
DBEAVER_SERVER_NAME=atlas
DBEAVER_ADMIN_NAME=admin
DBEAVER_ADMIN_PASSWORD=admin

# PostgreSQL
POSTGRES_PASSWORD=postgres
POSTGRES_USER=postgres
POSTGRES_DB=atlas

# Zona horaria (común a todos los servicios)
TZ=Europe/Madrid

# Frontend: URL base de la API (usada en build del frontend)
VITE_API_BASE_URL=http://localhost:8000
```

- **POSTGRES_***: credenciales y nombre de la base de datos usada por Atlas.
- **DBEAVER_***: usuario y contraseña de administrador de CloudBeaver.
- **VITE_API_BASE_URL**: URL a la que el frontend llama para la API (importante en build; por defecto `http://localhost:8000`).

## Uso básico

Desde la raíz del proyecto (donde está `docker-compose.yml`):

```bash
# Construir y levantar todos los servicios
docker compose up -d --build

# Ver logs (todos o de un servicio)
docker compose logs -f
docker compose logs -f fastapi

# Parar todos los servicios
docker compose down

# Parar y eliminar volúmenes (¡borra datos de Postgres y CloudBeaver!)
docker compose down -v
```

## URLs de acceso

Tras `docker compose up -d`:

| Aplicación   | URL                    |
|-------------|------------------------|
| Frontend    | http://localhost:8080  |
| API (Atlas) | http://localhost:8000  |
| CloudBeaver | http://localhost:8978  |

PostgreSQL queda expuesto en `localhost:5432` para clientes externos (por ejemplo, DBeaver desktop) si lo necesitas.

## Volúmenes y persistencia

Los datos se guardan en carpetas locales para que sobrevivan a `docker compose down` (sin `-v`):

| Servicio   | Ruta local                         | Contenido                    |
|-----------|------------------------------------|------------------------------|
| **postgres** | `./ddbb/postgresql`             | Datos de PostgreSQL          |
| **dbeaver**  | `./ddbb/cloudbeaver/workspace` | Workspace de CloudBeaver     |
| **fastapi**  | `./data`                        | Datos de la aplicación Atlas |

Conviene tener `./ddbb` y `./data` en `.gitignore` si no quieres versionar datos ni configuraciones locales de BD.

## Orden de arranque y dependencias

- **postgres**: no depende de nadie; arranca primero.
- **fastapi**: `depends_on: postgres`; espera a que Postgres esté disponible.
- **dbeaver**: `depends_on: postgres`; idem.
- **nginx**: puede arrancar en paralelo; el frontend llama a la API por `VITE_API_BASE_URL`.

Postgres incluye un **healthcheck** (`pg_isready`) cada 5 s; los demás servicios pueden usarlo implícitamente al depender de `postgres`.

## Comandos útiles

```bash
# Reconstruir solo un servicio (por ejemplo tras cambiar código)
docker compose up -d --build fastapi
docker compose up -d --build nginx

# Entrar al contenedor de la API
docker compose exec fastapi sh

# Entrar a Postgres con psql
docker compose exec postgres psql -U postgres -d atlas

# Ver estado de los contenedores
docker compose ps
```

## Notas

- **Zona horaria**: Todos los servicios usan `TZ` del `.env` (p. ej. `Europe/Madrid`).
- **Frontend y API**: Si cambias `VITE_API_BASE_URL`, hay que volver a construir la imagen del frontend: `docker compose build --no-cache nginx` y luego `docker compose up -d nginx`.
- **CloudBeaver**: La primera vez, entra en http://localhost:8978 y configura el workspace; los datos quedarán en `./ddbb/cloudbeaver/workspace`.
