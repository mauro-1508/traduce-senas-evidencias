# Consolidación y verificación de enlaces de evidencias del proyecto formativo

## 1. Información general

- **Programa / ficha:** Análisis y Desarrollo de Software – ficha 3145556
- **Proyecto formativo:** Traduce Señas: traductor de Lengua de Señas Colombiana (LSC) a texto
- **Equipo de trabajo:** Traduce Señas
- **Integrantes:** Luis Alejandro Duarte Aldana, Juan Mauricio Suaza Solorzano, Juan Camilo Fierro Esquivel
- **Fecha de entrega:** 15 de octubre de 2026 (última verificación de enlaces: 15/10/2026)

## 2. Propósito

Reunir en un solo documento los enlaces a las evidencias del proyecto formativo para que el instructor y los evaluadores puedan abrir y verificar los productos documentales, técnicos y funcionales de Traduce Señas.

## 3. Dónde está cada cosa

| Ubicación | Contenido | Visibilidad |
|---|---|---|
| [Repositorio backend](https://github.com/mauro-1508/backendTS/tree/develop) | API Gateway, cinco microservicios, scripts SQL, pruebas y Docker Compose | Público |
| [Repositorio frontend](https://github.com/tadeo77789/frontendTS/tree/develop) | Aplicación móvil y web (Expo / React Native) | Público |
| [Repositorio de documentación](https://github.com/code-sena/trans-sl-docs) | Requisitos, dominio, arquitectura, datos, API, UML, calidad, DevOps, operación y manuales | Privado (organización code-sena) |
| Entregables en Word y PDF (carpeta `archivos`) | Informes 01 a 10 y manuales en español e inglés | Se entregan por el medio indicado por el instructor |

## 4. Matriz de evidencias y enlaces

| N.º | Evidencia | Contenido mínimo que debe poder verificarse | Enlace directo | Estado de acceso | Observaciones |
|---:|---|---|---|---|---|
| 1 | Informe de especificación de requisitos | Requisitos funcionales y no funcionales, historias de usuario, criterios de aceptación y trazabilidad. | [04-requirements](https://github.com/code-sena/trans-sl-docs/tree/main/04-requirements) | Pendiente de permisos | Incluye functional.md (79 RF), non-functional.md, user-stories.md y traceability-matrix.md. Documento formal: `01_Informe_de_Especificacion_de_Requisitos.pdf`. |
| 2 | Análisis del software | Vistas dinámicas, procesos, casos de uso u otros artefactos de análisis aplicables. | [02-domain](https://github.com/code-sena/trans-sl-docs/tree/main/02-domain) · [08-uml/diagrams](https://github.com/code-sena/trans-sl-docs/tree/main/08-uml/diagrams) | Pendiente de permisos | Mapa de dominio, eventos de dominio y diagramas de secuencia (login, traducción en vivo, grabación y entrenamiento). Documento formal: `02_Documentacion_de_Analisis_del_Software.pdf`. |
| 3 | Propuesta técnica del software | Alternativa seleccionada, tecnologías, justificación técnica, alcance y restricciones. | [05-architecture/decisions/records](https://github.com/code-sena/trans-sl-docs/tree/main/05-architecture/decisions/records) | Pendiente de permisos | Decisiones de arquitectura (ADR) con su justificación; alcance en [01-context/scope.md](https://github.com/code-sena/trans-sl-docs/blob/main/01-context/scope.md). Documento formal: `03_Propuesta_Tecnica_del_Software.pdf`. |
| 4 | Diseño del software | Interfaces gráficas, arquitectura, patrones de diseño, modelo de datos y diagramas aplicables. | [05-architecture](https://github.com/code-sena/trans-sl-docs/tree/main/05-architecture) · [06-data](https://github.com/code-sena/trans-sl-docs/tree/main/06-data) · [12-ux-ui/mockup](https://github.com/code-sena/trans-sl-docs/tree/main/12-ux-ui/mockup) | Pendiente de permisos | Arquitectura hexagonal por microservicio, modelo DBML, mockups de 18 de 21 pantallas y contratos en [07-api/contracts](https://github.com/code-sena/trans-sl-docs/tree/main/07-api/contracts). Documento formal: `04_Documentacion_de_Diseno_del_Software.pdf`. |
| 5 | Código fuente y artefactos de software | Repositorios de frontend web, aplicación móvil, backend, scripts y base de datos. Indicar las ramas relevantes. | [Backend – develop](https://github.com/mauro-1508/backendTS/tree/develop) · [Frontend – develop](https://github.com/tadeo77789/frontendTS/tree/develop) · [Frontend – feat/vps-api-url](https://github.com/tadeo77789/frontendTS/tree/feat/vps-api-url) | Público | Ramas: backend `develop` (código vigente; `main` tiene la versión del 07/10/2026); frontend `develop` (código; `main` solo tiene el README) y `feat/vps-api-url` (apunta al VPS y genera el APK). Scripts SQL en `services/<servicio>/db`. Documento formal: `05_Artefactos_de_Codigo.pdf`. |
| 6 | Documentación de pruebas | Plan o casos de prueba, resultados, evidencias de ejecución, defectos encontrados y correcciones. | [Pruebas por servicio (backend)](https://github.com/mauro-1508/backendTS/tree/develop/services) · [11-quality/testing-strategy.md](https://github.com/code-sena/trans-sl-docs/blob/main/11-quality/testing-strategy.md) | Público / Pendiente de permisos | Cada servicio tiene su carpeta `tests`; se ejecutan con `npm test` desde la raíz del backend. Resultados y defectos en `06_Documentacion_de_Pruebas.pdf`. |
| 7 | Procedimiento de implementación | Pasos reproducibles de instalación, configuración, despliegue y verificación del funcionamiento. | [README del backend](https://github.com/mauro-1508/backendTS/blob/develop/README.md) · [docker-compose.yml](https://github.com/mauro-1508/backendTS/blob/develop/docker-compose.yml) · [10-devops/local-setup.md](https://github.com/code-sena/trans-sl-docs/blob/main/10-devops/local-setup.md) | Público / Pendiente de permisos | Documentos formales: `07_Procedimiento_de_Implementacion.pdf` y `07_Plan_de_Despliegue.pdf` (despliegue en el VPS con Portainer, respaldos, pruebas de humo y reversión). |
| 8 | Acciones correctivas, preventivas y de mejora | Hallazgos, prioridad, responsable, acción aplicada y evidencia de cierre. | [15-project-control/risks.md](https://github.com/code-sena/trans-sl-docs/blob/main/15-project-control/risks.md) · [technical-backlog.md](https://github.com/code-sena/trans-sl-docs/blob/main/15-project-control/technical-backlog.md) | Pendiente de permisos | La evidencia de cierre son los PR del backend (por ejemplo [#24](https://github.com/mauro-1508/backendTS/pull/24)). Documento formal: `08_Acciones_Correctivas_Preventivas_y_de_Mejoramiento.pdf`. |
| 9 | Manual técnico, de instalación y de usuario | Requisitos previos, configuración, uso de funcionalidades y solución de problemas frecuentes. | [14-training/user-manual.md](https://github.com/code-sena/trans-sl-docs/blob/main/14-training/user-manual.md) · [14-training/admin-manual.md](https://github.com/code-sena/trans-sl-docs/blob/main/14-training/admin-manual.md) | Pendiente de permisos | Manuales formales en APA: `Manual_tecnico_APA.pdf`, `Manual_instalacion_APA.pdf` y `Manual_usuario_APA.pdf` (español e inglés). |
| 10 | Artículo del proyecto | Problema, objetivos, solución, metodología, resultados y conclusiones. | Sin enlace en línea | No disponible | El artículo está en formato IEEE como `10_Articulo_del_Proyecto_IEEE.pdf` y se entrega por el medio del instructor. Cuando se suba a la carpeta compartida se reemplaza esta celda por el enlace. |
| 11 | Aplicación desplegada o demostración | URL de acceso a la aplicación o entorno de demostración e instrucciones para verificar funcionalidades. | [Health check del gateway](http://179.236.77.215:8080/health) · [Alfabeto LSC (API)](http://179.236.77.215:8080/api/lexicon/alphabet) | Público | El backend está publicado en un VPS de Hostinger y responde 200. El 15/10/2026 el health check mostró profile-service caído (estado «degraded»). La interfaz se prueba con el APK o con `npx expo start` del repositorio frontend; la versión web pública queda pendiente del dominio con HTTPS. |
| 12 | Trazabilidad e integración de evidencias | Relación entre requisitos, historias de usuario, módulos implementados, pruebas y cambios o versiones. | [04-requirements/traceability-matrix.md](https://github.com/code-sena/trans-sl-docs/blob/main/04-requirements/traceability-matrix.md) | Pendiente de permisos | Cobertura declarada: 15/15 requisitos del SRS, 74/79 RF persistidos y 25/29 tablas. Los cambios por versión se siguen en el historial de PR de cada repositorio. |

> **Credenciales de prueba:** este documento no incluye contraseñas. El evaluador puede crear una cuenta desde la aplicación (registro con verificación por correo). Si necesita una cuenta con rol ADMIN, la solicita a Juan Mauricio Suaza Solorzano, quien le asigna el rol por la API de administración y le comparte la clave por un canal privado.

## 5. Lista de verificación de accesibilidad y calidad

- [x] Todos los enlaces abren el recurso correcto (verificado el 15/10/2026 con la cuenta del equipo).
- [ ] No hay errores 404, accesos denegados ni solicitudes de permiso sin resolver. *Pendiente: el repositorio de documentación es privado y devuelve 404 sin sesión; falta dar acceso de lectura al instructor.*
- [x] Los documentos se pueden leer y sus imágenes, diagramas y tablas son legibles.
- [x] El repositorio permite identificar el proyecto, sus ramas y la versión entregada.
- [x] La evidencia corresponde con el estado real del software.
- [x] No se presentan pantallas o resultados simulados como si fueran funcionalidades implementadas.
- [ ] La aplicación desplegada inicia y permite verificar las funcionalidades principales. *Parcial: el gateway responde, pero profile-service aparece caído y la web pública aún no está publicada.*
- [x] La documentación, el repositorio y la aplicación coinciden en nombres, funcionalidades y versión.
- [x] No se exponen credenciales, datos sensibles ni información privada.
- [x] El documento incluye el nombre del proyecto, equipo, fecha de actualización y enlaces directos.
- [ ] Los enlaces se verificaron con el nivel de acceso que se declara en la matriz. *Los públicos se probaron sin sesión; los del repositorio privado solo con la cuenta del equipo.*
- [x] El PDF exportado conserva los enlaces y su formato es legible.

## 6. Criterios de aceptación

| Criterio | Condición de cumplimiento | Resultado / observaciones |
|---|---|---|
| Completitud | Se registran todas las evidencias aplicables y se justifican las que no aplican. | Cumple. Las 12 evidencias aplican; el artículo (10) no tiene enlace en línea y se justifica. |
| Accesibilidad | Cada enlace se probó con el nivel de acceso declarado. | Parcial. Los enlaces públicos responden 200 sin sesión; el repositorio de documentación queda pendiente de permisos para el instructor. |
| Trazabilidad | Es posible relacionar los requisitos importantes con su implementación y sus pruebas. | Cumple con la matriz de trazabilidad (evidencia 12) y las carpetas `tests` de cada servicio. |
| Coherencia | Los documentos coinciden con el repositorio y el funcionamiento de la aplicación. | Cumple. Documentos y código describen la misma arquitectura de microservicios y la versión v1.0.0. |
| Seguridad | No se exponen secretos ni datos que no deban ser públicos. | Cumple. Los repositorios solo publican `.env.example` sin valores y aquí no hay contraseñas. |
| Presentación | El documento está organizado, es legible y contiene enlaces identificables. | Cumple. |

## 7. Pendientes antes de la evaluación

1. Dar acceso de lectura al instructor en `code-sena/trans-sl-docs` (Settings > Collaborators) y probar con su cuenta; luego cambiar el estado de las evidencias 1–4, 6–9 y 12 a «Acceso institucional verificado».
2. Subir los PDF de la carpeta `archivos` a la carpeta compartida del instructor y reemplazar el nombre del archivo por su enlace, empezando por el artículo (evidencia 10).
3. Recuperar profile-service en el VPS y repetir el health check hasta obtener «ok».

## 8. Recursos de consulta

- [GitHub Docs: Acerca de los repositorios](https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories)
- [GitHub Docs: Configurar la visibilidad de un repositorio](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)
