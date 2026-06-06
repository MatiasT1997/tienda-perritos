# Tienda Perritos - Contenedorización y CI/CD

Aplicación de e-commerce para productos para perros, contenerizada con Docker y desplegada automáticamente en AWS EC2 mediante GitHub Actions.

## Estructura del Proyecto
tienda-perritos/
├── frontend/              # SPA con Nginx
│   ├── Dockerfile        # Multi-stage, usuario no-root
│   ├── app.js
│   ├── index.html
│   └── default.conf      # Config Nginx con proxy al backend
├── backend/              # API Node.js/Express
│   ├── Dockerfile        # Multi-stage, usuario no-root
│   ├── package.json
│   └── server.js
├── db/                   # Base de datos MySQL 8
│   ├── Dockerfile
│   └── init.sql
├── workflows/            # GitHub Actions CI/CD
│   ├── cicd-tienda-frontend.yml
│   ├── cicd-tienda-backend.yml
│   └── cicd-tienda-db.yml
├── docker-compose.yml    # Orquestación local
└── README.md

## Requisitos

- Docker Desktop (o Docker Engine + Docker Compose)
- AWS Academy Lab (para desplegar en EC2)
- GitHub (para CI/CD)

## Ejecutar Localmente

```bash
docker compose build
docker compose up -d
# Accede a http://localhost:8080
```

## Desplegar en AWS

1. **Crear 3 EC2s en VPC con subnets:**
   - Frontend: subred pública
   - Backend: subred privada
   - Data: subred privada

2. **Instalar Docker en cada EC2:**
```bash
   sudo yum install docker -y
   sudo systemctl start docker
```

3. **Configurar GitHub Secrets:**
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_SESSION_TOKEN`
   - `AWS_REGION`
   - `ECR_REGISTRY`
   - `ECR_REPO_URL_FRONTEND`
   - `ECR_REPO_URL_BACKEND`
   - `ECR_REPO_URL_DB`
   - `EC2_FRONTEND_INSTANCE_ID`
   - `EC2_BACKEND_INSTANCE_ID`
   - `EC2_DB_INSTANCE_ID`

4. **Push a rama `deploy` para activar CI/CD:**
```bash
   git checkout -b deploy
   git push origin deploy
```

## Decisiones Técnicas

### Multi-stage Dockerfile
- **Beneficio:** Reduce tamaño de imágenes (elimina herramientas de build del runtime)
- **Frontend:** Separa etapa de preparación de archivos estáticos de etapa Nginx
- **Backend:** Separa instalación de deps de ejecución

### Usuario No-Root
- **Beneficio:** Mayor seguridad — limita daños en caso de vulnerabilidad en la app
- Frontend: usuario `nginx_user` (UID 1001)
- Backend: usuario `nodeuser` (UID 1001)

### Volúmenes Docker
- **dbdata:** Persistencia de datos MySQL (no se pierden al reiniciar contenedor)
- **Type:** Named volume (mejor que bind mount para datos críticos)

### CI/CD con GitHub Actions
- **Trigger:** Push a rama `deploy`
- **Pipeline:** Build Docker → Push a ECR → Deploy en EC2 vía SSM
- **Ventaja:** Automatización completa, sin intervención manual

## Flujo de Datos
Frontend (Nginx en EC2 pública, puerto 80)
↓ (proxy /api/ hacia)
Backend (Node.js en EC2 privada, puerto 3001)
↓ (conexión a)
Database (MySQL en EC2 privada, puerto 3306)

## Autores
Matias Tudela, Eduardo Campos, Tiare Pérez

## Fecha
Junio 2026