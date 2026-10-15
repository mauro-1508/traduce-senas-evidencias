**DOCUMENTACIÓN DE ACCIONES CORRECTIVAS, PREVENTIVAS Y DE MEJORAMIENTO DEL SOFTWARE**

**PROYECTO: Traduce Señas**

**INTEGRANTES:**

**Luis Alejandro Duarte Aldana**

**Juan Mauricio Suaza Solorzano**

**Juan Camilo Fierro Esquivel**

**SERVICIO NACIONAL DE APRENDIZAJE – SENA**

**Análisis y Desarrollo De Software**

2026

**Tabla de contenido**

# Introducción

Este documento registra las acciones que el equipo de Traduce Señas ha tomado y planea tomar para corregir los defectos encontrados, prevenir problemas futuros y mejorar continuamente el software. Corresponde a las actividades de mantenimiento correctivo, preventivo y perfectivo descritas en la norma ISO/IEC 14764 (ISO, 2006).

Las acciones provienen de cuatro fuentes: la auditoría de código y de seguridad realizada durante la migración a microservicios, los resultados de las pruebas, el registro de riesgos del proyecto y las retrospectivas de los sprints.

## Definiciones

**Tabla 1**

*Tipos de acción*

| **Tipo** | **Definición** | **Cuándo se aplica** |
|---|---|---|
| Correctiva | Elimina la causa de un defecto o falla ya ocurrido | Después de detectar un error en pruebas, auditoría o uso |
| Preventiva | Elimina la causa de un problema potencial antes de que ocurra | Ante un riesgo identificado o una debilidad detectada |
| De mejoramiento | Aumenta la calidad, el rendimiento o el valor del software sin que exista un defecto | Como resultado de retrospectivas, métricas o nuevas necesidades |

# Metodología

Cada hallazgo se gestiona con el ciclo planear-hacer-verificar-actuar:

- **Identificar:** registrar el hallazgo con su fuente, fecha y severidad.

- **Analizar la causa raíz:** preguntar «¿por qué?» hasta llegar a la causa que, si se elimina, evita que se repita.

- **Definir la acción:** responsable, fecha objetivo y criterio de cierre verificable.

- **Ejecutar** en una rama propia, con prueba automatizada que reproduzca el problema cuando sea posible.

- **Verificar** que la prueba pasa y que no aparecen regresiones (suite completa en verde).

- **Cerrar** y registrar la lección aprendida.

**Tabla 2**

*Escala de severidad*

| **Severidad** | **Criterio** | **Tiempo objetivo de atención** |
|---|---|---|
| Crítica | Expone datos o permite acceso no autorizado; el sistema no funciona | Inmediato, antes de cualquier despliegue |
| Alta | Una función principal falla o hay un riesgo de seguridad relevante | En el sprint en curso |
| Media | Falla parcial con alternativa disponible | En el siguiente sprint |
| Baja | Detalle cosmético o de mantenibilidad | Según capacidad del backlog |

# Acciones correctivas

La auditoría de código y de seguridad de la fase 4 de la migración a microservicios encontró 2 problemas críticos, 2 altos y 7 defectos. La tabla siguiente presenta las acciones tomadas.

**Tabla 3**

*Acciones correctivas ejecutadas*

| **ID** | **Hallazgo** | **Causa raíz** | **Acción** | **Severidad** | **Estado** |
|---|---|---|---|---|---|
| AC-01 | El servicio arrancaba aunque JWT_SECRET faltara o fuera débil | Valor por defecto permisivo en la configuración | JWT_SECRET obligatorio (32+ caracteres), HS256 con issuer y audience | Crítica | Cerrada |
| AC-02 | Bases de datos y broker expuestos con contraseñas por defecto (guest/guest, postgres/postgres) | docker-compose pensado solo para desarrollo | Puertos solo en 127.0.0.1; contraseñas obligatorias desde .env | Crítica | Cerrada |
| AC-03 | Código de recuperación predecible y sin límite de intentos | Uso de un generador no criptográfico | crypto.randomInt, contador de intentos y límite estricto en /api/auth | Alta | Cerrada |
| AC-04 | Cualquier usuario podía importar o borrar plantillas de señas | Ruta sin verificación de rol | Solo ADMIN (permiso models.manage) puede escribir plantillas | Alta | Cerrada |
| AC-05 | CORS abierto y rutas del gateway sin servicio que las atendiera | Configuración heredada del backend anterior a la separación en microservicios | Lista blanca CORS_ORIGINS; se eliminaron rutas huérfanas | Media | Cerrada |
| AC-06 | Eventos sin contrato: payloads inválidos podían romper consumidores | Cada servicio definía sus propios tipos | Contrato de eventos tipado y validadores en @traduce/shared | Media | Cerrada |
| AC-07 | Se perdían incrementos de logros con traducciones simultáneas | Lectura y escritura no atómicas | Actualización atómica con INSERT … ON CONFLICT DO UPDATE | Media | Cerrada |
| AC-08 | Nombre de usuario sin escapar en el HTML del correo | Interpolación directa en la plantilla | Función escapeHtml en las plantillas de correo | Alta | Cerrada |
| AC-09 | GET /api/profile/me fallaba si el perfil no existía | El perfil se crea por evento y podía llegar tarde | Creación del perfil al vuelo si no existe | Media | En verificación |
| AC-10 | El descargador del conjunto LSC-54 omitía videos sin avisar | Error de lectura por rangos de bytes | Corrección del cálculo de rangos y prueba con muestra real | Alta | Cerrada |
| AC-11 | La descarga se detenía con HTTP 429 | 16 conexiones simultáneas superaban el límite del servidor | 4 conexiones, bloques más grandes y espera ante el código 429 | Media | Cerrada |

*Nota.* Verificación: la suite automatizada del backend terminó con 494 pruebas aprobadas y 0 fallidas el 7 de octubre de 2026.

# Acciones preventivas

Las acciones preventivas atacan los riesgos del proyecto antes de que se conviertan en fallas.

**Tabla 4**

*Acciones preventivas*

| **ID** | **Riesgo que previene** | **Acción preventiva** | **Responsable** | **Indicador** |
|---|---|---|---|---|
| AP-01 | Datos de entrenamiento de LSC insuficientes (R-001) | Usar el conjunto público LSC-54 y capturar muestras propias con consentimiento | Líder técnico | ≥ 30 ejemplos por seña |
| AP-02 | Precisión del modelo degradada sin que nadie lo note (R-002) | Medir aciertos por seña antes de publicar plantillas; revisar mensualmente las señas no reconocidas | Líder técnico | Precisión ≥ 85 %; tasa de no reconocidas < 20 % |
| AP-03 | Confusión entre señas con la misma forma de mano | Evaluar con firmantes que no participaron en las plantillas; descartar las señas que chocan | Equipo | Matriz de confusión por seña |
| AP-04 | Fuga de datos personales y biométricos (R-006) | Consentimiento obligatorio por muestra; video procesado en el dispositivo; acceso solo ADMIN | Equipo | 0 muestras sin consentimiento |
| AP-05 | Servidor único sin respaldo (R-007) | Copias de seguridad diarias y prueba de restauración trimestral | Responsable de despliegue | Restauración < 4 horas |
| AP-06 | Regresiones al integrar cambios | Pruebas automatizadas obligatorias antes de cada pull request; revisión de código | Todos | Suite en verde en cada integración |
| AP-07 | Secretos en el repositorio | .env en .gitignore; .env.example sin valores reales; revisión en cada pull request | Todos | 0 secretos en el historial |
| AP-08 | Dependencias vulnerables | npm audit mensual y actualización controlada | Líder técnico | 0 vulnerabilidades críticas |
| AP-09 | Concentración de roles en una sola persona (R-004) | Documentación de cada servicio (README, runbook) y guía de incorporación técnica | Equipo | Cualquier integrante puede desplegar |

# Acciones de mejoramiento

**Tabla 5**

*Plan de mejoramiento*

| **ID** | **Mejora** | **Beneficio esperado** | **Prioridad** | **Plazo** |
|---|---|---|---|---|
| AM-01 | Registros estructurados con Pino e identificador de correlación entre servicios | Diagnóstico de incidentes mucho más rápido | Alta | Próximo sprint |
| AM-02 | Endpoint /health/ready que verifique bases de datos y broker | Despliegues que detectan fallas de dependencias | Alta | Próximo sprint |
| AM-03 | Métricas de latencia y errores con Prometheus | Medir el cumplimiento del RNF de rendimiento | Alta | Dos sprints |
| AM-04 | Persistir los errores clasificados en error_logs | Análisis de tendencias de fallas (RF10) | Media | Dos sprints |
| AM-05 | Motor de reconocimiento que considere ubicación y movimiento de la mano | Distinguir señas como «buenos días», «buenas tardes» y «buenas noches» | Alta | Siguiente versión |
| AM-06 | Reconocimiento de señas con dos manos | Cubrir la mayoría de las señas del conjunto LSC-54 | Media | Siguiente versión |
| AM-07 | Pruebas de carga con k6 en el pipeline | Detectar degradación antes de producción | Media | Dos sprints |
| AM-08 | Integración continua en GitHub Actions (build + pruebas) | Ningún cambio se integra con pruebas en rojo | Alta | Próximo sprint |
| AM-09 | Completar prototipos faltantes (notificaciones y otras 2 pantallas) | Diseño completo de las 21 pantallas | Baja | Dos sprints |
| AM-10 | Prueba piloto con usuarios sordos en una institución educativa | Validación real del objetivo específico 4 | Alta | Siguiente versión |

# Lecciones aprendidas

**Tabla 6**

*Lecciones aprendidas*

| **Situación** | **Lección** |
|---|---|
| El catálogo de requisitos reportaba una trazabilidad que no era real (17/17 y 77/79) | Toda métrica de cobertura debe recalcularse desde la fuente, no copiarse |
| Las copias «rep_N» del conjunto LSC-54 eran desplazamientos de la misma grabación | Antes de descargar datos masivos, estudiar su estructura con una muestra |
| La normalización por muñeca elimina la ubicación de la mano | Medir la distinguibilidad de las señas antes de prometer un vocabulario |
| Un agente de desarrollo se detuvo a mitad de la fase 5 por límite de uso | Dividir el trabajo en commits pequeños y dejar el estado documentado (ESTADO_TRABAJO.md) |
| Los presupuestos del proyecto tenían cuatro totales distintos | Un dato debe tener una sola fuente; las demás la referencian |

# Seguimiento

El estado de las acciones se revisa en cada retrospectiva de sprint. Una acción solo se cierra cuando existe evidencia verificable: una prueba automatizada que pasa, un commit referenciado o un indicador medido. Las acciones abiertas se mantienen en el backlog técnico del repositorio de documentación (15-project-control/technical-backlog.md).

# Conclusiones

Las acciones correctivas cerraron los dos problemas críticos y los dos altos detectados en la auditoría, y la suite de pruebas lo respalda. Las acciones preventivas se concentran en la calidad de los datos de entrenamiento y en la protección de los datos personales, que son los riesgos más altos del proyecto. El plan de mejoramiento prioriza la observabilidad y un motor de reconocimiento que tenga en cuenta el movimiento de la mano.

# Referencias

Congreso de la República de Colombia. (2012, 17 de octubre). *Ley Estatutaria 1581 de 2012, por la cual se dictan disposiciones generales para la protección de datos personales*. Diario Oficial No. 48.587.

International Organization for Standardization. (2006). *Software engineering — Software life cycle processes — Maintenance* (ISO/IEC 14764:2006). ISO.

International Organization for Standardization. (2011). *Systems and software engineering — Systems and software Quality Requirements and Evaluation (SQuaRE) — System and software quality models* (ISO/IEC 25010:2011). ISO.

Mora-Zarate, J. E., Garzón-Castro, C. L., & Castellanos Rivillas, J. A. (2025). LSC-54: A landmark-based dataset for Colombian Sign Language. *Data in Brief*. https://doi.org/10.1016/j.dib.2025.112145

OWASP Foundation. (2021). *OWASP Top 10: 2021*. https://owasp.org/Top10/

Pressman, R. S., & Maxim, B. R. (2020). *Software engineering: A practitioner's approach* (9.ª ed.). McGraw-Hill Education.

Sommerville, I. (2016). *Software engineering* (10.ª ed.). Pearson.
