# Despliegue Automatizado de WordPress con Docker, Git y Jenkins

# Ambiente de WordPress + MySQL desplegado con Docker Compose, versionado con Git y automatizado con Jenkins

## Requisitos

- **Docker Desktop** (Windows/Mac) o Docker Engine (Linux)
- **Git**
- **Puertos libres:** 8080 (WordPress), 8081 (Jenkins), 3306 (MySQL) y 50000 (agentes Jenkins)

## Estructura del proyecto

```text
Arquitectura-WordPress/
├── docker-compose.yml
├── docker-compose.jenkins.yml
├── config.env
├── jenkinsfile
└── README.md
```

> Jenkins se mantiene en un archivo aparte para que el pipeline pueda bajar y levantar la base de datos y WordPress sin apagarse a sí mismo.

## Pasos de ejecución

### 1. Clonar el repositorio

```bash
git clone https://github.com/LuisZamora24/WordPress.git
cd WordPress
```

### 2. Levantar WordPress y MySQL

```bash
docker compose up -d
```

### 3. Levantar Jenkins

```bash
docker compose -f docker-compose.jenkins.yml up -d
```

### 4. Verificar que el entorno arranque

```bash
docker ps
docker network ls
docker volume ls
```

### 5. Acceder a los servicios

- WordPress:http://localhost:8080
- Jenkins: http://localhost:8081

### 6. Ver la contraseña inicial de Jenkins

```bash
docker exec jenkins_server cat /var/jenkins_home/secrets/initialAdminPassword
```

### 7. Validar que WordPress y MySQL compartan la misma red

```bash
docker inspect wordpress
docker inspect db
```

### 8. Configurar el pipeline en Jenkins

- Nuevo Item → Pipeline
- Pipeline script from SCM → Git → URL del repositorio, rama main
- Script Path: jenkinsfile
- Ejecutar el build con Build Now

> El pipeline realiza: checkout del repositorio, docker compose down --volumes --remove-orphans, docker compose up -d, y verificación con docker ps, docker network ls y docker volume ls.

## Apagar el ambiente

### 1. Solo WordPress y MySQL (sin afectar Jenkins)

```bash
docker compose down
```

### 2. Todo, incluyendo Jenkins

```bash
docker compose down
docker compose -f docker-compose.jenkins.yml down
```
