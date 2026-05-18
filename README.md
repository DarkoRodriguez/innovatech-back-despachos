# Backend - Microservicio de Despachos (Innovatech Chile)

Este repositorio contiene el microservicio de **Despachos** para Innovatech Chile, construido en **Java 17 con Spring Boot**. Su responsabilidad principal es tomar las órdenes de compra (ventas) registradas y gestionar la logística de envíos y sus intentos de entrega.

## 🚀 Tecnologías Utilizadas
* **Framework:** Spring Boot 3
* **Lenguaje:** Java 17
* **ORM:** Hibernate / Spring Data JPA
* **Base de Datos:** MySQL
* **Documentación:** Swagger / OpenAPI
* **Contenedorización:** Docker

## 🏗️ Arquitectura y Contenedorización
Al igual que el resto de microservicios, se empaca utilizando un **Dockerfile Multi-Stage**:
1. **Build Stage:** Compilación mediante Maven en una imagen pesada para generar el artefacto `.jar`.
2. **Run Stage:** Ejecución en un entorno de Java Runtime Environment (JRE) Alpine muy liviano.

**Buenas prácticas de Seguridad y Eficiencia:**
* Ejecución del proceso Java con el usuario seguro `innovatech` en lugar del usuario `root`.
* Aislamiento de red: Este contenedor expone el puerto `8081` solo a nivel de red de Docker; no recibe tráfico público directamente, sino que todo el tráfico pasa por el proxy del Frontend, respetando los *Security Groups*.

## ⚙️ Configuración y Ejecución Local

1. Requiere de una base de datos MySQL operativa.
2. Ejecuta el servicio mediante Docker Compose:
   ```bash
   docker compose up -d --build backend-despachos
   ```
3. La API estará operativa en el puerto interno `8081`.
4. Documentación de endpoints (Swagger) disponible en: `http://localhost:8081/swagger-ui.html`

### Conexión a Base de Datos
El proyecto implementa la propiedad `createDatabaseIfNotExist=true` en el driver JDBC y `spring.jpa.hibernate.ddl-auto=update` para automatizar la generación y migración de tablas, reduciendo el trabajo manual en el servidor.

## 🔄 Pipeline CI/CD
Posee su propio flujo en **GitHub Actions** asociado a la rama `deploy`. Se compila la imagen, se almacena en el repositorio remoto y se despliega directamente en el clúster EC2 backend de manera transparente y automatizada utilizando **AWS SSM** para la ejecución remota de comandos.

## 🔄 Pipeline CI/CD
El flujo de CI/CD posee su propio flujo en **GitHub Actions** asociado a la rama `deploy`. Se compila la imagen, se almacena en el repositorio remoto y se despliega directamente en el clúster EC2 backend de manera transparente y automatizada utilizando **AWS SSM** para la ejecución remota de comandos.

## 📡 Comunicación entre aplicaciones
- **Cómo se conectan:** El frontend se comunica con los microservicios de ventas y despachos a través de HTTP/REST. En producción se recomienda enrutar el tráfico por un proxy (NGINX o API Gateway) y usar la red interna de Docker para llamadas entre contenedores.
- **Desde Backend Ventas → Despachos:** El microservicio de `despachos` obtiene las órdenes registradas por `ventas` ya sea realizando peticiones HTTP a los endpoints de `ventas` o consumiendo eventos desde un bus (si se habilita). Ambos enfoques son compatibles; si se usa la base de datos compartida o colas/eventos, documentarlo y configurarlo explícitamente.
- **Variables de entorno importantes:** `VENTAS_HOST`, `DESPACHOS_HOST`, `DB_*` (host/port/name/user/password). En Docker Compose use los nombres de servicio como host (ej. `ventas:8080`).

## 🔌 Endpoints de ejemplo
- `GET /api/v1/despachos` — Listar despachos.
- `POST /api/v1/despachos` — Crear una orden de despacho.
- `GET /api/v1/despachos/{id}` — Consultar estado del despacho.

## 🧭 Ejecutar en el Monorepo (Docker Compose)
1. Desde la raíz del repositorio con todos los servicios (monorepo) ejecutar:
   ```bash
   docker compose up -d --build
   ```
2. Esto levantará `backend-ventas`, `backend-despachos`, `front-despacho` y la base de datos según el `docker-compose.yml` de la raíz.

## 📝 Buenas prácticas de integración
- Configure siempre los `HOST`/`PORT` de los servicios como variables de entorno para facilitar despliegues.
- En entornos distribuidos, use TLS entre servicios y autenticación (tokens o mTLS) para asegurar las llamadas internas.
