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
