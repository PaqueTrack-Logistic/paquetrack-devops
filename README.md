# paquetrack-devops
 Estructura del Proyecto
text
paquetrack-devops/
├── authentication-service/     # Servicio de autenticación (Spring Boot)
├── gateway-service/            # API Gateway (Spring Cloud Gateway)
├── shipment-service/           # Servicio de envíos (Spring Boot)
├── tracking-service/           # Servicio de tracking (Spring Boot)
├── ui-service/                 # Frontend (React/Vite)
├── docker-compose.yml          # Orquestación de servicios
├── .env                        # Variables de entorno (NO subir)
├── .gitignore                  # Archivos ignorados por git
├── k8s/                        # Manifiestos de Kubernetes
│   ├── namespace.yaml
│   ├── configmap.yaml
│   ├── secrets.yaml
│   ├── *-deployment.yaml
│   ├── *-service.yaml
│   └── ingress.yaml
└── README.md                   # Este archivo


🔧 Configuración Inicial
1. Clonar los repositorios

# Clonar el repositorio principal
git clone https://github.com/paquetrack-logistic/paquetrack-devops.git
cd paquetrack-devops
2. Configurar variables de entorno
Crea un archivo .env en la raíz del proyecto basado en este ejemplo:

.env
# === Authentication Service (Neon Database) ===
AUTH_DB_URL=jdbc:postgresql://ep-patient-band-ambkd3g2.c-5.us-east-1.aws.neon.tech:5432/neondb?sslmode=require
AUTH_DB_USER=neondb_owner
AUTH_DB_PASSWORD=tu_contraseña_auth

# === Shipment Service (Neon Database) ===
SHIPMENT_DB_URL=jdbc:postgresql://ep-quiet-wind-aq1c3sor-pooler.c-8.us-east-1.aws.neon.tech/neondb?sslmode=require
SHIPMENT_DB_USER=neondb_owner
SHIPMENT_DB_PASSWORD=tu_contraseña_shipment

# === Tracking Service (Render Database) ===
TRACKING_DB_URL=jdbc:postgresql://dpg-d7t6b3l0lvsc73cge01g-a.ohio-postgres.render.com/tracking_db_8fau
TRACKING_DB_USER=trackingadmin
TRACKING_DB_PASSWORD=tu_contraseña_tracking

# === RabbitMQ (Mensajería) ===
RABBITMQ_HOST=shrimp.rmq.cloudamqp.com
RABBITMQ_PORT=5671
RABBITMQ_USER=edjhxutz
RABBITMQ_PASSWORD=tu_contraseña_rabbitmq
RABBITMQ_VHOST=edjhxutz

# === JWT Security ===
JWT_SECRET=owJ4IAPe62UOCIZ0ldW1l54m23KTB2/mzC6fG4tBlPw=
⚠️ IMPORTANTE: El archivo .env NO debe subirse a git. Está incluido en .gitignore.

3. Verificar Dockerfiles
Asegúrate que cada microservicio tenga su Dockerfile:

bash
# Verificar existencia de Dockerfiles
ls authentication-service/Dockerfile
ls gateway-service/Dockerfile
ls shipment-service/Dockerfile
ls tracking-service/Dockerfile
ls ui-service/Dockerfile

🐳 Ejecutar con Docker Compose
Levantar todos los servicios

# Construir imágenes y levantar contenedores
docker compose up --build
Ejecutar en segundo plano

# Modo detach (background)
docker compose up -d --build

# Ver logs
docker compose logs -f
Verificar que todo funciona

# Listar contenedores activos
docker ps

# Verificar salud de los servicios
docker compose ps

🌐 Endpoints de los Servicios
Servicio	URL Local	Propósito
UI Web	http://localhost:3000	Interfaz de usuario
UI Tracking	http://localhost:3000/tracking	Seguimiento de envíos
UI Shipments	http://localhost:3000/shipments	Gestión de envíos
API Gateway	http://localhost:8080	Punto de entrada API
Auth Service	http://localhost:8081	Autenticación JWT
Auth Swagger	http://localhost:8081/swagger-ui.html	Documentación API Auth
Shipment Service	http://localhost:8082	Gestión de envíos
Tracking Service	http://localhost:8083	SeguimientoConfiguración de despliegue para los microservicios de PaqueTrack usando Docker y Kubernetes.

Variables requeridas por cada servicio
Servicio	Variables	Descripción
Authentication	AUTH_DB_URL, AUTH_DB_USER, AUTH_DB_PASSWORD, JWT_SECRET	BD Neon + JWT
Shipment	SHIPMENT_DB_URL, SHIPMENT_DB_USER, SHIPMENT_DB_PASSWORD, RABBITMQ_*	BD Neon + RabbitMQ
Tracking	TRACKING_DB_URL, TRACKING_DB_USER, TRACKING_DB_PASSWORD, RABBITMQ_*	BD Render + RabbitMQ
Gateway	JWT_SECRET_PROD	Configuración rutas
UI	VITE_API_GATEWAY_URL	URL del gateway