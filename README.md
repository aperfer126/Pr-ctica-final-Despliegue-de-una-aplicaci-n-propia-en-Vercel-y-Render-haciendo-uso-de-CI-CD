# Práctica Final: Despliegue en Vercel y Render con CI/CD

Alumno: Tu Nombre  
Asignatura: Despliegue de Aplicaciones Web  
Fecha: Marzo 2026

## Índice

1. Introducción
2. Clonación del repositorio
3. Configuración del entorno local
4. Ejecución local con Docker Compose
5. Verificación del funcionamiento local
6. Creación de la base de datos en Railway
7. Despliegue del Backend en Render
8. Despliegue del Frontend en Vercel
9. Configuración de CI/CD con GitHub Actions
10. Verificación del despliegue en producción
11. Resumen de URLs
12. Tecnologías utilizadas

## 1. Introducción

Esta práctica consiste en desplegar una aplicación web full-stack usando plataformas PaaS:

| Capa | Tecnología | Plataforma |
|---|---|---|
| Frontend | Vue 3 + Vite + Tailwind CSS | Vercel |
| Backend | FastAPI + SQLAlchemy | Render |
| Base de datos | MySQL | Railway |

La aplicación implementa operaciones CRUD sobre una lista de tareas.

Repositorio de la práctica:  
[Práctica final en GitHub](https://github.com/aperfer126/Pr-ctica-final-Despliegue-de-una-aplicaci-n-propia-en-Vercel-y-Render-haciendo-uso-de-CI-CD)

## 2. Clonación del repositorio

Clonamos el repositorio en local:

```bash
git clone https://github.com/aperfer126/Pr-ctica-final-Despliegue-de-una-aplicaci-n-propia-en-Vercel-y-Render-haciendo-uso-de-CI-CD.git
cd Pr-ctica-final-Despliegue-de-una-aplicaci-n-propia-en-Vercel-y-Render-haciendo-uso-de-CI-CD
```

![Clonación del repositorio y comprobación inicial](img/image.png)

Estructura principal del proyecto:

```text
.
├── .github/workflows/
│   ├── deploy-backend.yaml
│   └── deploy-frontend.yaml
├── backend/
│   ├── Dockerfile
│   ├── main.py
│   └── requirements.txt
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── src/
│   │   ├── App.vue
│   │   ├── main.ts
│   │   ├── style.css
│   │   └── services/api.ts
│   └── ...
├── compose.yaml
├── .env
└── README.md
```

## 3. Configuración del entorno local

Este proyecto utiliza un archivo `.env` con variables para frontend, backend y MySQL.

Contenido usado en la práctica:

```env
# Puertos
BACKEND_PORT=8000
FRONTEND_PORT=3000
MYSQL_PORT=3306

# Frontend
VITE_API_URL=http://localhost:8000

# MySQL (local)
MYSQL_DATABASE=app_db
MYSQL_USER=app
MYSQL_PASSWORD=app
MYSQL_ROOT_PASSWORD=root

# Backend DB (local)
DB_HOST=mysql
DB_PORT=3306
DB_NAME=app_db
DB_USER=app
DB_PASSWORD=app

# Producción (Railway)
DATABASE_URL=
```

![Archivo .env configurado](img/image2.png)

## 4. Ejecución local con Docker Compose

Levantamos todos los servicios:

```bash
docker compose up -d --build
```

Este comando:
- construye frontend y backend desde sus Dockerfile,
- descarga MySQL,
- y levanta los 3 contenedores.

![Ejecución de docker compose up --build](img/image3.png)

Comprobamos estado de contenedores:

```bash
docker compose ps
```

![Servicios en ejecución con docker compose ps](img/image4.png)

## 5. Verificación del funcionamiento local

### 5.1 Backend

Comprobamos endpoint raíz:

- http://localhost:8000

![Backend en local respondiendo online](img/image5.png)

Comprobamos documentación Swagger:

- http://localhost:8000/docs

![Swagger de la API en local](img/image6.png)

Comprobamos datos de la API:

- http://localhost:8000/api/items

![Endpoint /api/items con datos de MySQL local](img/image7.png)

### 5.2 Frontend

Abrimos la aplicación:

- http://localhost:3000

![Frontend local conectado con backend](img/image8.png)

## 6. Creación de la base de datos en Railway

### 6.1 Crear cuenta y proyecto

1. Accedemos a https://railway.com
2. Creamos un proyecto nuevo.

![Creación de nuevo proyecto en Railway](img/image9.png)

### 6.2 Añadir servicio MySQL

1. Seleccionamos Database.
2. Elegimos MySQL.

![Servicio MySQL creado en Railway](img/image10.png)

### 6.3 Obtener cadena de conexión

1. Entramos al servicio MySQL.
2. Abrimos la pestaña Variables.
3. Copiamos `MYSQL_PUBLIC_URL`.

![Variables del servicio MySQL en Railway](img/image11.png)

Nota: la URL suele venir como `mysql://...`; el backend la normaliza automáticamente a `mysql+pymysql://...`.

## 7. Despliegue del Backend en Render

### 7.1 Crear servicio

1. Accedemos a https://render.com
2. New + -> Web Service.
3. Conectamos el repositorio.

![Selección de Web Service en Render](img/image12.png)

### 7.2 Configurar servicio

Parámetros usados:

| Parámetro | Valor |
|---|---|
| Name | vercel-render-backend |
| Root Directory | ./backend |
| Runtime | Docker |
| Branch | main |
| Plan | Free |

![Configuración del backend en Render](img/image13.png)

### 7.3 Variables de entorno

Añadimos en Render:

| Variable | Valor |
|---|---|
| DATABASE_URL | URL de Railway |

![Render con DATABASE_URL configurada](img/image14.png)

### 7.4 Deploy y Deploy Hook

Realizamos el despliegue y revisamos logs:

![Logs del despliegue del backend en Render](img/image15.png)

Copiamos el Deploy Hook en Settings:

![Deploy Hook de Render](img/image16.png)

## 8. Despliegue del Frontend en Vercel

### 8.1 Importar proyecto

1. Accedemos a https://vercel.com
2. Add New -> Project.
3. Importamos el repositorio.

![Importación del repositorio en Vercel](img/image17.png)

### 8.2 Configurar build y variables

Parámetros habituales:

| Parámetro | Valor |
|---|---|
| Root Directory | frontend |
| Framework Preset | Vite |
| Build Command | npm run build |
| Output Directory | dist |

Variable requerida:

| Variable | Valor |
|---|---|
| VITE_API_URL | URL pública del backend en Render |

![Configuración del proyecto en Vercel](img/image18.png)

### 8.3 Deploy y token

Desplegamos proyecto:

![Despliegue completado en Vercel](img/image19.png)

Generamos token en Vercel Settings -> Tokens:

![Creación de token en Vercel](img/image20.png)

![Token generado en Vercel](img/image21.png)

## 9. Configuración de CI/CD con GitHub Actions

### 9.1 Secrets del repositorio

En GitHub -> Settings -> Secrets and variables -> Actions añadimos:

| Secret | Uso |
|---|---|
| RENDER_DEPLOY_HOOK | Disparar deploy backend en Render |
| VERCEL_TOKEN | Deploy frontend con Vercel CLI |

![Alta del secret RENDER_DEPLOY_HOOK](img/image22.png)

![Secrets de GitHub configurados](img/image23.png)

### 9.2 Workflows

- `.github/workflows/deploy-backend.yaml`: se ejecuta con cambios en `backend/**`.
- `.github/workflows/deploy-frontend.yaml`: se ejecuta con cambios en `frontend/**`.

### 9.3 Verificación de pipeline

Realizamos cambios y push a `main`.

```bash
git add .
git commit -m "ci: trigger frontend deploy workflow"
git push origin main
```

![Workflow ejecutado correctamente en GitHub Actions](img/image24.png)

## 10. Verificación del despliegue en producción

### 10.1 Backend en Render

Comprobamos endpoint principal y datos:

![Backend en producción online](img/image25.png)

![Endpoint /api/items en producción](img/image26.png)

### 10.2 Frontend en Vercel

Comprobamos la interfaz web consumiendo backend en producción:

![Frontend en producción funcionando](img/image27.png)

Resultado final general:

![Vista final de la práctica desplegada](img/image28.png)

## 11. Resumen de URLs

Completar con tus URLs reales:

| Servicio | URL |
|---|---|
| Frontend (Vercel) | https://TU-FRONTEND.vercel.app |
| Backend (Render) | https://TU-BACKEND.onrender.com |
| Base de datos (Railway) | Servicio MySQL en Railway |
| Repositorio GitHub | https://github.com/aperfer126/Pr-ctica-final-Despliegue-de-una-aplicaci-n-propia-en-Vercel-y-Render-haciendo-uso-de-CI-CD |

## 12. Tecnologías utilizadas

| Componente | Stack |
|---|---|
| Frontend | Vue 3, Vite, Tailwind CSS, TypeScript |
| Backend | FastAPI, SQLAlchemy, PyMySQL |
| Base de datos | MySQL 8.3 |
| Contenedores | Docker, Docker Compose |
| CI/CD | GitHub Actions |
| Hosting frontend | Vercel |
| Hosting backend | Render |
| Hosting base de datos | Railway |
