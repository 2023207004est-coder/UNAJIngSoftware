# UNAJIngSoftware — Config Server y microservicio de catálogo

Práctica de la Escuela Profesional de Ingeniería de Software y Sistemas — UNAJ.

## Componentes

| Componente | Puerto | Rol |
|---|---|---|
| `catalogo-spring/ms-config-server` | 7070 | Lee la configuración desde GitHub (`config-data`) |
| `catalogo-spring/ms-catalogo-service` | 8081 | CRUD de Categoría + Swagger |
| PostgreSQL | 5432 | Base de datos `ms_catalogo` |

## Configuración

La carpeta `config-data` de este repositorio contiene el archivo que consume el Config Server:

```
config-data/
└── ms-catalogo-service-development.yml
```

El servidor de configuración lo lee desde:

```
https://github.com/2023207004est-coder/UNAJIngSoftware.git
rama: main
searchPaths: config-data
```

## Ejecución

1. Crear la base de datos:

```sql
CREATE DATABASE ms_catalogo;
```

2. Levantar el Config Server:

```powershell
cd catalogo-spring/ms-config-server
.\mvnw.cmd spring-boot:run
```

3. Verificar:

```
http://localhost:7070/ms-catalogo-service/development
```

Usuario: `root` — Clave: `123456`

4. Levantar el microservicio:

```powershell
cd catalogo-spring/ms-catalogo-service
.\mvnw.cmd spring-boot:run
```

5. Swagger:

```
http://localhost:8081/doc/swagger-ui.html
http://localhost:8081/v3/api-docs
```

## Endpoints

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
  "nombre": "Electrónica"
}
```
