# Consolidación y verificación de enlaces de evidencias del proyecto formativo

## 1. Información general

- **Programa / ficha:** Análisis y Desarrollo de Software – ficha 3145556
- **Proyecto formativo:** Traduce Señas: traductor de Lengua de Señas Colombiana (LSC) a texto
- **Equipo de trabajo:** Traduce Señas
- **Integrantes:** Luis Alejandro Duarte Aldana, Juan Mauricio Suaza Solorzano, Juan Camilo Fierro Esquivel
- **Fecha de entrega:** 15 de octubre de 2026 (última verificación de enlaces: 16/10/2026)

## 2. Propósito

Reunir en un solo documento los enlaces a las evidencias del proyecto formativo para que el instructor y los evaluadores puedan abrir y verificar los productos documentales, técnicos y funcionales de Traduce Señas.

## 3. Dónde está cada cosa

| Ubicación | Contenido | Visibilidad |
|---|---|---|
| [Repositorio de evidencias](https://github.com/mauro-1508/traduce-senas-evidencias) | Entregables formales 01 a 10 y manuales (Word, PDF y Markdown, en español e inglés) y, en la carpeta `docs/`, la documentación técnica del equipo | Público |
| [Repositorio backend](https://github.com/mauro-1508/backendTS/tree/develop) | API Gateway, cinco microservicios, scripts SQL, pruebas y Docker Compose | Público |
| [Repositorio frontend](https://github.com/tadeo77789/frontendTS/tree/develop) | Aplicación móvil y web (Expo / React Native) | Público |

## 4. Matriz de evidencias y enlaces

| N.º | Evidencia | Contenido mínimo que debe poder verificarse | Enlace directo | Estado de acceso | Observaciones |
|---:|---|---|---|---|---|
| 1 | Informe de especificación de requisitos | Requisitos funcionales y no funcionales, historias de usuario, criterios de aceptación y trazabilidad. | [Informe (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/01_INFORME_DE_ESPECIFICACION_DE_REQUISITOS/ES/PDF/01_Informe_de_Especificacion_de_Requisitos.pdf) · [docs/04-requirements](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/04-requirements) | Público | Fuente técnica: functional.md (79 RF), non-functional.md, user-stories.md y traceability-matrix.md. |
| 2 | Análisis del software | Vistas dinámicas, procesos, casos de uso u otros artefactos de análisis aplicables. | [Análisis (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/02_DOCUMENTACION_DE_ANALISIS_DEL_SOFTWARE/ES/PDF/02_Documentacion_de_Analisis_del_Software.pdf) · [docs/02-domain](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/02-domain) · [docs/08-uml](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/08-uml/diagrams) | Público | El PDF trae el diagrama de casos de uso y cinco diagramas de secuencia. En docs: mapa, entidades y eventos de dominio, y 11 diagramas de clases (PlantUML y SVG). |
| 3 | Propuesta técnica del software | Alternativa seleccionada, tecnologías, justificación técnica, alcance y restricciones. | [Propuesta técnica (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/03_PROPUESTA_TECNICA_DEL_SOFTWARE/ES/PDF/03_Propuesta_Tecnica_del_Software.pdf) · [docs/05-architecture/decisions](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/05-architecture/decisions/records) | Público | Decisiones de arquitectura (ADR) con su justificación; alcance en [docs/01-context/scope.md](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/docs/01-context/scope.md). |
| 4 | Diseño del software | Interfaces gráficas, arquitectura, patrones de diseño, modelo de datos y diagramas aplicables. | [Diseño (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/04_DOCUMENTACION_DE_DISENO_DEL_SOFTWARE/ES/PDF/04_Documentacion_de_Diseno_del_Software.pdf) · [docs/05-architecture](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/05-architecture) · [docs/06-data](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/06-data) · [docs/12-ux-ui/mockup](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/12-ux-ui/mockup) | Público | Arquitectura hexagonal por microservicio, modelo DBML, mockups de 18 de 21 pantallas y contratos OpenAPI en [docs/07-api/contracts](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/07-api/contracts). |
| 5 | Código fuente y artefactos de software | Repositorios de frontend web, aplicación móvil, backend, scripts y base de datos. Indicar las ramas relevantes. | [Backend – develop](https://github.com/mauro-1508/backendTS/tree/develop) · [Frontend – develop](https://github.com/tadeo77789/frontendTS/tree/develop) · [Frontend – feat/vps-api-url](https://github.com/tadeo77789/frontendTS/tree/feat/vps-api-url) · [Artefactos (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/05_ARTEFACTOS_DE_CODIGO/ES/PDF/05_Artefactos_de_Codigo.pdf) | Público | Ramas: backend `develop` (código vigente; `main` tiene la versión del 07/10/2026); frontend `develop` (código; `main` solo tiene el README) y `feat/vps-api-url` (apunta al VPS y genera el APK). Scripts SQL en `services/<servicio>/db`. |
| 6 | Documentación de pruebas | Plan o casos de prueba, resultados, evidencias de ejecución, defectos encontrados y correcciones. | [Pruebas (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/06_DOCUMENTACION_DE_PRUEBAS/ES/PDF/06_Documentacion_de_Pruebas.pdf) · [Pruebas por servicio (backend)](https://github.com/mauro-1508/backendTS/tree/develop/services) · [docs/11-quality](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/docs/11-quality/testing-strategy.md) | Público | Cada servicio tiene su carpeta `tests`; se ejecutan con `npm test` desde la raíz del backend. |
| 7 | Procedimiento de implementación | Pasos reproducibles de instalación, configuración, despliegue y verificación del funcionamiento. | [Procedimiento (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/07_PROCEDIMIENTO_DE_IMPLEMENTACION/ES/PDF/07_Procedimiento_de_Implementacion.pdf) · [Plan de despliegue (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/07_PROCEDIMIENTO_DE_IMPLEMENTACION/ES/PDF/07_Plan_de_Despliegue.pdf) · [docker-compose.yml](https://github.com/mauro-1508/backendTS/blob/develop/docker-compose.yml) | Público | El plan de despliegue cubre el VPS con Portainer, respaldos, pruebas de humo y reversión. Instalación local en [docs/10-devops/local-setup.md](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/docs/10-devops/local-setup.md). |
| 8 | Acciones correctivas, preventivas y de mejora | Hallazgos, prioridad, responsable, acción aplicada y evidencia de cierre. | [Acciones (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/08_ACCIONES_CORRECTIVAS_PREVENTIVAS_Y_DE_MEJORAMIENTO/ES/PDF/08_Acciones_Correctivas_Preventivas_y_de_Mejoramiento.pdf) · [docs/15-project-control](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/15-project-control) | Público | Riesgos y backlog técnico en docs; la evidencia de cierre son los PR del backend (por ejemplo [#24](https://github.com/mauro-1508/backendTS/pull/24)). |
| 9 | Manual técnico, de instalación y de usuario | Requisitos previos, configuración, uso de funcionalidades y solución de problemas frecuentes. | [Manual técnico](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/MANUALES/ES/PDF/Manual_tecnico_APA.pdf) · [Manual de instalación](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/MANUALES/ES/PDF/Manual_instalacion_APA.pdf) · [Manual de usuario](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/MANUALES/ES/PDF/Manual_usuario_APA.pdf) · [docs/14-training](https://github.com/mauro-1508/traduce-senas-evidencias/tree/main/docs/14-training) | Público | Manuales en APA, en español e inglés. En docs están además el manual de administrador y la guía técnica de entrada. |
| 10 | Artículo del proyecto | Problema, objetivos, solución, metodología, resultados y conclusiones. | [Artículo IEEE (PDF)](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/10_ARTICULO_DEL_PROYECTO/ES/PDF/10_Articulo_del_Proyecto_IEEE.pdf) | Público | Versión en inglés en la carpeta `EN` del mismo entregable. |
| 11 | Aplicación desplegada o demostración | URL de acceso a la aplicación o entorno de demostración e instrucciones para verificar funcionalidades. | [Health check del gateway](http://179.236.77.215:8080/health) · [Alfabeto LSC (API)](http://179.236.77.215:8080/api/lexicon/alphabet) | Público | El backend está publicado en un VPS de Hostinger y responde 200. El 16/10/2026 el health check respondió «ok» con los cinco servicios arriba. La interfaz se prueba con el APK o con `npx expo start` del repositorio frontend; la versión web pública queda pendiente del dominio con HTTPS. |
| 12 | Trazabilidad e integración de evidencias | Relación entre requisitos, historias de usuario, módulos implementados, pruebas y cambios o versiones. | [docs/04-requirements/traceability-matrix.md](https://github.com/mauro-1508/traduce-senas-evidencias/blob/main/docs/04-requirements/traceability-matrix.md) | Público | Cobertura declarada: 15/15 requisitos del SRS, 74/79 RF persistidos y 25/29 tablas. Los cambios por versión se siguen en el historial de PR de cada repositorio. |

> **Credenciales de prueba:** este documento no incluye contraseñas. El evaluador puede crear una cuenta desde la aplicación (registro con verificación por correo). Si necesita una cuenta con rol ADMIN, la solicita a Juan Mauricio Suaza Solorzano, quien le asigna el rol por la API de administración y le comparte la clave por un canal privado.

## 5. Lista de verificación de accesibilidad y calidad

- [x] Todos los enlaces abren el recurso correcto (verificado el 16/10/2026 sin iniciar sesión).
- [x] No hay errores 404, accesos denegados ni solicitudes de permiso sin resolver.
- [x] Los documentos se pueden leer y sus imágenes, diagramas y tablas son legibles.
- [x] El repositorio permite identificar el proyecto, sus ramas y la versión entregada.
- [x] La evidencia corresponde con el estado real del software.
- [x] No se presentan pantallas o resultados simulados como si fueran funcionalidades implementadas.
- [x] La aplicación desplegada inicia y permite verificar las funcionalidades principales. *El backend responde con los cinco servicios arriba; la interfaz se prueba con el APK, y la web pública queda para cuando haya dominio con HTTPS.*
- [x] La documentación, el repositorio y la aplicación coinciden en nombres, funcionalidades y versión.
- [x] No se exponen credenciales, datos sensibles ni información privada.
- [x] El documento incluye el nombre del proyecto, equipo, fecha de actualización y enlaces directos.
- [x] Los enlaces se verificaron con el nivel de acceso que se declara en la matriz (todos públicos, probados sin sesión).
- [x] El PDF exportado conserva los enlaces y su formato es legible.

## 6. Criterios de aceptación

| Criterio | Condición de cumplimiento | Resultado / observaciones |
|---|---|---|
| Completitud | Se registran todas las evidencias aplicables y se justifican las que no aplican. | Cumple. Las 12 evidencias aplican y todas tienen enlace. |
| Accesibilidad | Cada enlace se probó con el nivel de acceso declarado. | Cumple. Todos los enlaces son públicos y responden sin iniciar sesión. |
| Trazabilidad | Es posible relacionar los requisitos importantes con su implementación y sus pruebas. | Cumple con la matriz de trazabilidad (evidencia 12) y las carpetas `tests` de cada servicio. |
| Coherencia | Los documentos coinciden con el repositorio y el funcionamiento de la aplicación. | Cumple. Documentos y código describen la misma arquitectura de microservicios y la versión v1.0.0. |
| Seguridad | No se exponen secretos ni datos que no deban ser públicos. | Cumple. Los repositorios solo publican `.env.example` sin valores y aquí no hay contraseñas. |
| Presentación | El documento está organizado, es legible y contiene enlaces identificables. | Cumple. |

## 7. Pendientes antes de la evaluación

1. Publicar la versión web con dominio propio y HTTPS (hoy la interfaz se prueba con el APK).

## 8. Recursos de consulta

- [GitHub Docs: Acerca de los repositorios](https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories)
- [GitHub Docs: Configurar la visibilidad de un repositorio](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)
