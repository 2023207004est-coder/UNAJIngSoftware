# UNAJIngSoftware — Config Server, Eureka, Gateway y microservicios de catálogo y producto

Práctica de la Escuela Profesional de Ingeniería de Software y Sistemas — UNAJ.

Guía 1: Config Server y microservicio de catálogo con PostgreSQL y Swagger.
Guía 2: Registro y descubrimiento con Eureka Server y Gateway Server.
Tarea: microservicio de producto con MySQL integrado a Eureka y Gateway.

## Componentes y puertos

| Componente | Puerto | Rol |
|---|---|---|
| `catalogo-spring/ms-config-server` | 7070 | Configuración centralizada desde GitHub (`config-data`) |
| `catalogo-spring/ms-eureka-server` | 8761 | Registro y descubrimiento de servicios |
| `catalogo-spring/ms-catalogo-service` | 8081 | CRUD de Categoría + Swagger, PostgreSQL |
| `catalogo-spring/ms-producto-service` | 8082 | CRUD de Producto + Swagger, MySQL |
| `catalogo-spring/ms-gateway-server` | 8080 | Único punto de entrada, enruta con `lb://` |
| PostgreSQL | 5432 | Base de datos `ms_catalogo` |
| MySQL | 3306 | Base de datos `ms_producto` |

## Configuración

La carpeta `config-data` de este repositorio contiene los archivos que consume el Config Server:

```
config-data/
├── ms-catalogo-service-development.yml
├── ms-eureka-server-development.yml
├── ms-gateway-server-development.yml
└── ms-producto-service-development.yml
```

El servidor de configuración los lee desde:

```
https://github.com/2023207004est-coder/UNAJIngSoftware.git
rama: main
searchPaths: config-data
```

## Ejecución (en este orden)

1. Levantar PostgreSQL y MySQL, y crear las bases de datos:

```sql
CREATE DATABASE ms_catalogo;
```

```sql
CREATE DATABASE ms_producto;
```

2. Config Server (7070):

```powershell
cd catalogo-spring/ms-config-server
.\mvnw.cmd spring-boot:run
```

Verificar: `http://localhost:7070/ms-catalogo-service/development` (usuario `root`, clave `123456`).

3. Eureka Server (8761):

```powershell
cd catalogo-spring/ms-eureka-server
.\mvnw.cmd spring-boot:run
```

Panel: `http://localhost:8761`

4. Microservicio de catálogo (8081):

```powershell
cd catalogo-spring/ms-catalogo-service
.\mvnw.cmd spring-boot:run
```

Swagger: `http://localhost:8081/doc/swagger-ui.html`

5. Microservicio de producto (8082, MySQL):

```powershell
cd catalogo-spring/ms-producto-service
.\mvnw.cmd spring-boot:run
```

Swagger: `http://localhost:8082/doc/swagger-ui.html`

6. Gateway (8080):

```powershell
cd catalogo-spring/ms-gateway-server
.\mvnw.cmd spring-boot:run
```

## Rutas a través del Gateway

| Petición | Llega a |
|---|---|
| `http://localhost:8080/categoria` | `ms-catalogo-service` / `categoria` (ruta declarada) |
| `http://localhost:8080/ms-catalogo-service/categoria` | `ms-catalogo-service` / `categoria` (discovery locator) |
| `http://localhost:8080/producto` | `ms-producto-service` / `producto` (ruta declarada) |
| `http://localhost:8080/ms-producto-service/producto` | `ms-producto-service` / `producto` (discovery locator) |

## Endpoints de catálogo

| Método | Ruta | Descripción |
|---|---|---|
| GET | `/categoria` | Lista categorías |
| GET | `/categoria/{id}` | Busca una categoría |
| POST | `/categoria` | Crea una categoría |
| PUT | `/categoria/{id}` | Actualiza una categoría |
| DELETE | `/categoria/{id}` | Elimina una categoría |

Ejemplo de creación:

```json
{
  "nombre": "Línea blanca"
}
```

## Endpoints de producto

| Método | Ruta | Descripción |
|---|---|---|
| GET | `/producto` | Lista productos |
| GET | `/producto/{id}` | Busca un producto |
| POST | `/producto` | Crea un producto |
| PUT | `/producto/{id}` | Actualiza un producto |
| DELETE | `/producto/{id}` | Elimina un producto |

Ejemplo de creación:

```json
{
  "nombre": "Refrigeradora",
  "descripcion": "Refrigeradora 300 L",
  "precio": 1899.90,
  "stock": 5
}
```
