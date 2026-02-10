<!-- # 📋 Trabajo de Docker: Sistema de Gestión de Tareas

## Objetivo General

Crear una aplicación web completa usando Docker Compose que permita gestionar una lista de tareas (To-Do List). La aplicación constará de un **frontend** (HTML + JavaScript), un **backend** (Node.js + Express) y una **base de datos PostgreSQL**, todos orquestados mediante Docker Compose.

---

##  Requisitos Técnicos

### 1. Arquitectura de Servicios (3 contenedores)

Debes implementar exactamente **tres servicios** independientes:

- **Frontend**: Servidor web Nginx que sirva archivos estáticos (HTML, CSS, JavaScript)
- **Backend**: API REST en Node.js con Express que gestione las operaciones CRUD
- **Base de datos**: PostgreSQL para almacenar las tareas de forma persistente

### 2. Gestión de Volúmenes

Implementa dos tipos de volúmenes:

- **Volumen gestionado por Docker** (`postgres-data`): Para persistir los datos de PostgreSQL
  - Debe estar montado en `/var/lib/postgresql/data` dentro del contenedor
  - Los datos deben persistir incluso si se elimina el contenedor
  
- **Bind mount** (punto de montaje): Para el frontend
  - Monta el directorio local `./frontend` en `/usr/share/nginx/html`
  - Debe ser de solo lectura (`ro`)
  - Todo esto permite desarrollo en tiempo real sin reconstruir

### 3. Red Personalizada

Crea una red bridge personalizada llamada `todo-network`:

- El **backend** debe estar conectado a esta red
- La **base de datos** debe estar conectada a esta red
- El **frontend NO debe estar** en la red interna 

### 4. Gestión de Credenciales

Implementa un sistema seguro de gestión de credenciales:

- Crear un archivo `.env` en la raíz del proyecto con todas las credenciales
- Crear un archivo `.env.example` como plantilla (sin credenciales reales)
- Crear un archivo `.gitignore` para evitar subir el `.env` a Git
- Configurar `docker-compose.yml` para usar variables de entorno desde `.env`

**Variables requeridas en `.env`**:
```
POSTGRES_USER=todouser
POSTGRES_PASSWORD=todopass
POSTGRES_DB=tododb
DB_HOST=database
DB_PORT=5432
BACKEND_PORT=4000
```

### 5. Funcionalidad de la Aplicación

La aplicación permite las siguientes operaciones:

-  **Ver todas las tareas**: Listar todas las tareas guardadas en la BD
-  **Añadir nuevas tareas**: Crear una tarea con un título
-  **Marcar tareas como completadas**: Cambiar el estado de una tarea
-  **Eliminar tareas**: Remover una tarea de la BD
-  **Persistencia**: Los datos deben guardarse en PostgreSQL

### 6. ENTREGA (README.md)

El README debe incluir:

- Estructura del proyecto
- Explicación de cada servicio (qué hace, puertos, variables de entorno)
- Cómo funcionan los volúmenes (gestionado vs bind mount)
- Cómo funciona la red personalizada
- Tabla de puertos expuestos
- Instrucciones de configuración del `.env`

**Formato de entrega**: Repositorio Git

### Comandos Útiles

```bash
# Ver estado de los contenedores
docker compose ps

# Ver logs de un servicio
docker compose logs backend

# Acceder a la base de datos
docker compose exec database psql -U todouser -d tododb

# Parar los servicios
docker compose down

# Parar y eliminar volúmenes (borra datos)
docker compose down -v

# Ver configuración final (con variables resueltas)
docker compose config
```

---

## Consideraciones de Seguridad

-  Las credenciales deben estar en `.env` (nunca en `docker-compose.yml`)
-  El archivo `.env` debe estar en `.gitignore`
-  Proporcionar `.env.example` como plantilla
-  El frontend no debe tener acceso a la red interna
-  PostgreSQL solo acepta conexiones desde servicios autorizados
-  En producción, usar Docker Secrets o gestores de secretos

--- -->
#  Sistema de Gestión de Tareas  con Docker

## Descripción General

Este proyecto consiste en una aplicación web de gestión de tareas  desarrollada con una arquitectura de **tres servicios**, hacemos mediante **Docker Compose**:

- **Frontend**: Servidor Nginx que sirve archivos estáticos (HTML + JavaScript)
- **Backend**: API REST desarrollada en Node.js con Express
- **Base de Datos**: PostgreSQL para la persistencia de las tareas

El objetivo principal es demostrar el uso de contenedores, redes personalizadas, volúmenes y gestión segura de credenciales con Docker.

---

##  Estructura del Proyecto

```text
todo-docker/
│
├── backend/
│   ├── Dockerfile
│   ├── index.js
│   └── package.json
│
├── frontend/
│   ├── index.html
│   └── script.js
│
├── docker-compose.yml
├── .env
├── .env.example
├── .gitignore
└── README.md


---

##  Servicios

###  Frontend (Nginx)

- Imagen: `nginx:alpine`
- Función: Servir la interfaz web estática
- Puerto expuesto: `8080`
- Volumen:
  - Bind mount del directorio local `./frontend`
  - Montado en `/usr/share/nginx/html`
  - Modo solo lectura (`ro`)

---

###  Backend (Node.js + Express)

- Construido mediante un `Dockerfile`
- Función: Proveer una API REST para gestionar tareas (CRUD)
- Puerto expuesto: `4000`
- Conectado a la red personalizada `todo-network`
- Usa variables de entorno definidas en `.env`

puntos de final principales:
- `GET /tasks` → Obtener todas las tareas
- `POST /tasks` → Crear una nueva tarea
- `PUT /tasks/:id` → Marcar tarea como completada
- `DELETE /tasks/:id` → Eliminar tarea

---

###  Base de Datos (PostgreSQL)

- Imagen: `postgres:15`
- Función: Almacenar las tareas de forma persistente
- Conectada a la red `todo-network`
- Volumen gestionado por Docker para persistencia de datos

---

##  Volúmenes

###  Volumen gestionado por Docker

- Nombre: `postgres-data`
- Uso: Persistencia de datos de PostgreSQL
- Ruta interna:- Los datos se conservan incluso si el contenedor se elimina

---



```md
###  Tabla de Puertos

| Servicio     | Puerto Interno | Puerto Externo | Descripción |
|-------------|----------------|----------------|-------------|
| Frontend    | 80             | 8080           | Interfaz web (Nginx) |
| Backend     | 4000           | 4000           | API REST (Node.js + Express) |
| PostgreSQL  | 5432           | —              | Base de datos (red interna) |

---
### Bind Mount

- Uso: Frontend
- Montaje:
   1. primero tendras que configurar las variables del entorno
      (copia el archivo .env.example y crea el .env con tus propias credenciales)
   2. ejecuta con el docker abierto docker compose up --build
   3. Acceder a la aplicación desde los puertos asignados front(http://localhost:8080) back (http://localhost:4000/tasks)


