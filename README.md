# API Testing con Postman & Newman — ReqRes Users API

Suite de pruebas automatizadas de API construida con **Postman** y ejecutada vía **Newman**, cubriendo el ciclo CRUD completo (Create, Read, Update, Delete) sobre la API pública de demostración [reqres.in](https://reqres.in).

Proyecto desarrollado como primer ejercicio práctico de transición de **QA Manual a QA Automation**.

---

## 🎯 Objetivo

Validar el comportamiento de una API REST combinando:
- Ejecución manual desde la interfaz de Postman (diseño y validación de casos)
- Automatización de assertions con JavaScript (`pm.test`)
- Ejecución automatizada desde línea de comandos con Newman
- Generación de reportes de ejecución en formato HTML

---

## 🛠️ Stack utilizado

| Herramienta | Uso |
|---|---|
| Postman | Diseño, ejecución y organización de requests |
| Newman | Ejecución de la suite desde CLI |
| newman-reporter-htmlextra | Generación de reporte visual de resultados |
| JavaScript (Chai / pm API) | Escritura de assertions |

---

## 📁 Estructura del repositorio

```
postman-tests/
├── ReqRes Users API.postman_collection.json   # Colección con las 5 requests y sus tests
├── QA.postman_environment.json                # Variables de entorno (base_url, api_key)
└── reporte.html                               # Último reporte de ejecución generado con Newman
```

---

## 🔄 Casos cubiertos

| # | Método | Endpoint | Qué valida |
|---|---|---|---|
| 1 | `POST` | `/api/users` | Creación de un usuario — status `201`, campos enviados en el body de respuesta, existencia de `id` generado |
| 2 | `GET` | `/api/users/2` | Lectura de un usuario existente — status `200`, email correcto, tiempo de respuesta < 1000ms |
| 3 | `PUT` | `/api/users/2` | Actualización completa del recurso — status `200`, campo actualizado, presencia de `updatedAt` |
| 4 | `PATCH` | `/api/users/2` | Actualización parcial del recurso — status `200`, solo el campo modificado cambia |
| 5 | `DELETE` | `/api/users/2` | Eliminación del recurso — status `204`, body vacío |

Cada request incluye sus propias validaciones automáticas (`pm.test`) además de la revisión manual del body y los headers de respuesta.

---

## ▶️ Cómo ejecutar la suite

### Requisitos previos
- [Node.js](https://nodejs.org/) instalado
- Newman instalado globalmente:
```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

### Ejecución

Desde la carpeta `postman-tests/`:

```bash
newman run "ReqRes Users API.postman_collection.json" -e "QA.postman_environment.json" -r cli,htmlextra --reporter-htmlextra-export ./reporte.html
```

Esto corre las 5 requests en orden, imprime el resultado de cada assertion en la consola y genera `reporte.html` con el detalle visual de la ejecución (requests, responses, tests pasados/fallados, tiempos).

---

## 🔍 Hallazgos y decisiones de diseño

Durante la construcción de esta suite surgieron dos aprendizajes concretos, propios de trabajar contra una API de demostración (no una API productiva real):

- **Los datos no persisten entre requests.** El endpoint `POST /api/users` de reqres.in simula la creación de un recurso (devuelve un `id` y un `201 Created`), pero no lo guarda realmente en el servidor. Por eso las requests de lectura/actualización/borrado apuntan a un usuario fijo existente en los datos de prueba (`id: 2`) en lugar de encadenar dinámicamente el `id` generado por el `POST` — una decisión de diseño para poder validar el comportamiento real de cada método HTTP contra un recurso que efectivamente existe.
- **`DELETE` no elimina el recurso de verdad.** Devuelve el status code correcto (`204`) pero, al tratarse de fixtures fijos, una lectura posterior seguiría devolviendo el mismo usuario. Por ese motivo la suite no incluye una verificación de "recurso inexistente" tras el `DELETE`, ya que no reflejaría el comportamiento real esperado en una API con persistencia.

Identificar y documentar este tipo de limitaciones del entorno de prueba —en lugar de forzar un resultado verde sin entenderlo— es parte del criterio de análisis que se aplicó durante el desarrollo de esta suite.

---

## 👤 Autora

**Cris (María Cristina Gaupmann)**
QA Analyst & Senior Systems Analyst — Buenos Aires, Argentina
[LinkedIn](#) · [GitHub](#)
