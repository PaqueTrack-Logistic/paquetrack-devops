# 📦 PaqueTrack DevOps

Configuración de despliegue y orquestación de los microservicios de **PaqueTrack** utilizando **Docker Compose** y **Kubernetes**.

---

## 🏗️ Arquitectura del Proyecto

```text
paquetrack-devops/
├── authentication-service/     # Servicio de autenticación
├── gateway-service/            # API Gateway
├── shipment-service/           # Gestión de envíos
├── tracking-service/           # Seguimiento de envíos
├── ui-service/                 # Frontend React/Vite
│
├── docker-compose.yml          # Orquestación local
├── .env                        # Variables de entorno (no versionar)
├── .gitignore
│
├── k8s/
│   ├── namespace.yaml
│   ├── configmap.yaml
│   ├── secret-template.yaml
│   ├── auth-deployment.yaml
│   ├── shipment-deployment.yaml
│   ├── tracking-deployment.yaml
│   ├── gateway-deployment.yaml
│   ├── ui-deployment.yaml
│   ├── *-service.yaml
│   └── ingress.yaml
│
└── README.md
```

---

# 🚀 Inicio Rápido

## 1. Clonar el repositorio

```bash
git clone https://github.com/paquetrack-logistic/paquetrack-devops.git
cd paquetrack-devops
```

---

## 2. Configurar variables de entorno

Crear un archivo `.env` en la raíz del proyecto.

### Base de datos Authentication

```env
AUTH_DB_URL=
AUTH_DB_USER=
AUTH_DB_PASSWORD=
```

### Base de datos Shipment

```env
SHIPMENT_DB_URL=
SHIPMENT_DB_USER=
SHIPMENT_DB_PASSWORD=
```

### Base de datos Tracking

```env
TRACKING_DB_URL=
TRACKING_DB_USER=
TRACKING_DB_PASSWORD=
```

### RabbitMQ

```env
RABBITMQ_HOST=
RABBITMQ_PORT=
RABBITMQ_USER=
RABBITMQ_PASSWORD=
RABBITMQ_VHOST=
```

### JWT

```env
JWT_SECRET=
```

> ⚠️ El archivo `.env` contiene información sensible y **no debe subirse al repositorio**.

---

## 3. Verificar Dockerfiles

Todos los servicios deben contener un Dockerfile:

```bash
ls authentication-service/Dockerfile
ls gateway-service/Dockerfile
ls shipment-service/Dockerfile
ls tracking-service/Dockerfile
ls ui-service/Dockerfile
```

---

# 🐳 Ejecución con Docker Compose

## Construir y levantar servicios

```bash
docker compose up --build
```

## Ejecutar en segundo plano

```bash
docker compose up -d --build
```

## Ver logs

```bash
docker compose logs -f
```

## Verificar estado

```bash
docker ps

docker compose ps
```

---

# 🌐 Servicios Disponibles

| Servicio         | URL                                   | Descripción        |
| ---------------- | ------------------------------------- | ------------------ |
| Frontend         | http://localhost:3000                 | Interfaz principal |
| Tracking UI      | http://localhost:3000/tracking        | Consulta de envíos |
| Shipment UI      | http://localhost:3000/shipments       | Gestión de envíos  |
| API Gateway      | http://localhost:8080                 | Punto de entrada   |
| Auth Service     | http://localhost:8081                 | Autenticación      |
| Swagger Auth     | http://localhost:8081/swagger-ui.html | Documentación API  |
| Shipment Service | http://localhost:8082                 | Gestión de envíos  |
| Tracking Service | http://localhost:8083                 | Seguimiento        |

---

# ☸️ Kubernetes

Los manifiestos se encuentran en:

```text
k8s/
```

### Recursos incluidos

* Namespace
* ConfigMap
* Secrets
* Deployments
* Services
* Ingress

### Aplicar configuración

```bash
kubectl apply -f k8s/
```

### Verificar despliegue

```bash
kubectl get pods -n paquetrack

kubectl get svc -n paquetrack

kubectl get ingress -n paquetrack
```

---

# 🔐 Variables de Entorno por Servicio

| Servicio       | Variables                                                           |
| -------------- | ------------------------------------------------------------------- |
| Authentication | AUTH_DB_URL, AUTH_DB_USER, AUTH_DB_PASSWORD, JWT_SECRET             |
| Shipment       | SHIPMENT_DB_URL, SHIPMENT_DB_USER, SHIPMENT_DB_PASSWORD, RABBITMQ_* |
| Tracking       | TRACKING_DB_URL, TRACKING_DB_USER, TRACKING_DB_PASSWORD, RABBITMQ_* |
| Gateway        | JWT_SECRET_PROD                                                     |
| UI             | VITE_API_GATEWAY_URL                                                |

---

# 🔒 Seguridad

* No subir archivos `.env`.
* No subir `secret.yaml` con credenciales reales.
* Utilizar `secret-template.yaml` como referencia.
* Mantener JWT y credenciales fuera del repositorio.

---

# 🛠️ Tecnologías Utilizadas

* Java 21
* Spring Boot
* Spring Cloud Gateway
* React + Vite
* PostgreSQL
* RabbitMQ
* Docker
* Docker Compose
* Kubernetes
* NGINX Ingress

---

# 👥 Equipo

Proyecto académico desarrollado para la gestión logística de envíos y seguimiento de paquetes mediante arquitectura de microservicios.
