# AutoMarket – App Web con Arquitectura de Microservicios, Patrón CQRS, Docker y Kafka


Este proyecto es una aplicación web interactiva diseñada para mejorar la compra y venta de vehículos usados mediante dashboards avanzados y una infraestructura segura, que implementa una arquitectura de microservicios para el sistema de gestión automotriz **AutoMarket**, exponiendo **APIs REST** para la comunicación entre servicios y el frontend, utilizando Docker Compose, MySQL, MongoDB, Apache Kafka, Kafka Connect y un frontend web, siguiendo un enfoque **CQRS (Command Query Responsibility Segregation)**.

---

## Propósito

El objetivo del proyecto es construir un sistema distribuido basado en microservicios que permita gestionar usuarios, vehículos, contratos y ventas, integrando comunicación por eventos mediante Kafka y sincronización de datos entre bases de datos relacionales y NoSQL.

---

## Arquitectura General

La solución está compuesta por:


- Microservicios de negocio (Usuarios, Vehículos, Contratos, Ventas)

- Bases de datos MySQL y MongoDB aisladas por servicio

- Kafka + Zookeeper como bus de eventos

- Kafka Connect (Debezium + Mongo Sink) para integración y replicación de datos

- Kafka UI

- Frontend web (Nginx)

- Red Docker compartida para comunicación interna


---

## Microservicios

### Usuarios
- Servicio: `usuarios-ms`
- Puerto: `3000 -> 4001`
- Base de datos: MySQL
- Inicialización automática con `init.sql`

---

### Vehículos (CQRS)

#### Commands (Escritura)
- Servicio: `vehiculos-commands-ms`
- Puerto: `3002 -> 4006`
- Base de datos: MySQL
- Publica eventos en Kafka

#### Queries (Lectura)
- Servicio: `vehiculos-queries-ms`
- Puerto: `3001 -> 4005`
- Base de datos: MongoDB

---

### Contratos
- Servicio: `contratos-ms`
- Puerto: `3003 -> 4003`
- Base de datos: MySQL

---

### Ventas
- Servicio: `ventas-ms`
- Puerto: `3004 -> 4004`
- Base de datos: MySQL

---

### Frontend Web
- Servicio: `automarketweb`
- Puerto: `80`
- Servido mediante Nginx

---

## Bases de Datos

### MySQL
Utilizado por los microservicios de:
- Usuarios
- Vehículos (Commands)
- Contratos
- Ventas

Cada servicio tiene su propia base de datos y volumen persistente.

### MongoDB
- Utilizado por `vehiculos-queries-ms`
- Almacena el modelo de lectura del dominio de vehículos

---

## Kafka y Streaming

### Zookeeper
- Puerto: `2181`

### Kafka
- Puerto externo: `9092`
- Puerto interno: `29092`

Kafka se utiliza para la comunicación basada en eventos entre microservicios y para la replicación de datos mediante Kafka Connect.

---

## Kafka Connect

### kafka-connect
- Puerto: `8083`
- Incluye:
  - Debezium MySQL Connector
  - MongoDB Sink Connector

### init-connectors
Contenedor que registra automáticamente los conectores al iniciar el sistema.

---

## Kafka UI

- Servicio: `kafka-ui`
- Puerto: `8080`

Permite visualizar:
- Topics
- Consumidores
- Productores
- Estado de Kafka Connect

---

## Persistencia de Datos

El proyecto utiliza volúmenes Docker para mantener los datos:

- mysql-usuarios-data
- mysql-vehiculos-data
- mysql-contratos-data
- mysql-ventas-data
- mongo-vehiculos-data

---

## Red Docker

Todos los servicios están conectados a la red:

`microservices-net`

Esto permite comunicación interna por nombre de servicio.


---


## Ejecución del Proyecto


### Levantar el entorno completo:

```bash
docker compose up --build -d
```

### Detener los servicios:

```bash
docker compose down
```