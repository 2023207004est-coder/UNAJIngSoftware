# UNAJIngSoftware — Config Server, Eureka, Gateway y microservicio de catálogo

Práctica de la Escuela Profesional de Ingeniería de Software y Sistemas — UNAJ.

Guía 1: Config Server y microservicio de catálogo con PostgreSQL y Swagger.
Guía 2: Registro y descubrimiento con Eureka Server y Gateway Server.

## Componentes y puertos

| Componente | Puerto | Rol |
|---|---|---|
| `catalogo-spring/ms-config-server` | 7070 | Configuración centralizada desde GitHub (`config-data`) |
| `catalogo-spring/ms-eureka-server` | 8761 | Registro y descubrimiento de servicios |
| `catalogo-spring/ms-catalogo-service` | 8081 | CRUD de Categoría + Swagger, se registra en Eureka |
| `catalogo-spring/ms-gateway-server` | 8080 | Único punto de entrada, enruta con `lb://` |
| PostgreSQL | 5432 | Base de datos `ms_catalogo` |

## Configuración

La carpeta `config-data` de este repositorio contiene los archivos que consume el Config Server:

```
config-data/
├── ms-catalogo-service-development.yml
├── ms-eureka-server-development.yml
└── ms-gateway-server-development.yml
```

El servidor de configuración los lee desde:

```
https://github.com/2023207004est-coder/UNAJIngSoftware.git
rama: main
searchPaths: config-data
```

## Ejecución (en este orden)

1. Levantar PostgreSQL y crear la base de datos:

```sql
CREATE DATABASE ms_catalogo;
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

5. Gateway (8080):

```powershell
cd catalogo-spring/ms-gateway-server
.\mvnw.cmd spring-boot:run
```

## Rutas a través del Gateway

| Petición | Llega a |
|---|---|
| `http://localhost:8080/categoria` | `ms-catalogo-service` / `categoria` (ruta declarada) |
| `http://localhost:8080/ms-catalogo-service/categoria` | `ms-catalogo-service` / `categoria` (discovery locator) |

## Endpoints del catálogo

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
