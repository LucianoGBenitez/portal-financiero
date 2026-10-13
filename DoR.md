# Definition of Ready (DoR)

_Antes de que una historia entre a desarrollo, tiene que pasar un filtro: el Definition of
Ready. Es un acuerdo del equipo sobre qué condiciones mínimas debe cumplir una historia para
considerarse "lista para trabajar". Si no las cumple, vuelve a refinamiento._

---

## Checklist del equipo

_Entre 6 y 10 ítems. Cada uno redactado como una condición verificable ("la historia tiene
criterios de aceptación escritos"), no como un deseo ("la historia está bien definida")._

| # | Ítem | Justificación (qué problema evita, máx. 3 renglones) |
|---|------|--------------------------------------------------------|
| 1 | **Formato estándar y rol de usuario específico**<br>La historia sigue el formato `Como [rol], quiero [acción] para [beneficio]`, con un rol identificado en la lista de stakeholders y un valor de negocio medible. | Evita construir funcionalidades sin justificación ni usuario real. Previene suposiciones erróneas sobre quién utilizará el módulo y qué beneficio aporta. |
| 2 | **Criterios de aceptación objetivos y medibles**<br>Posee al menos 3 criterios de aceptación redactados como condiciones de prueba objetivas (escenarios de éxito y fallo), sin limitarse a repetir el texto de la historia. | Evita ambigüedades al programar y discusiones subjetivas en la entrega sobre si la funcionalidad cumple con lo requerido ("Definition of Done"). |
| 3 | **Tamaño atómico y acotado (INVEST - Pequeña)**<br>La historia puede ser desarrollada, probada e integrada en una única iteración (sprint) por el equipo, sin abarcar múltiples módulos o flujos complejos (no es una épica encubierta). | Evita compromisos de entrega sobredimensionados, cuellos de botella en el sprint y funcionalidades que quedan a medio hacer al cierre del ciclo. |
| 4 | **Dependencias externas y técnicas definidas o simulables (mocks)**<br>Los contratos de interfaz, formatos de archivo (ej. ancho fijo 240 caracteres TEF) o conexiones con sistemas externos (Interbanking, SAP B1) están definidos o cuentan con mocks acordados. | Evita que un desarrollador comience la historia y quede inmediatamente bloqueado por falta de accesos, credenciales, especificaciones de protocolo o esquemas de datos. |
| 5 | **Comportamiento ante errores y casos borde especificado**<br>Se definen claramente los escenarios alternativos y de falla críticos (ej. registros duplicados, desconexión del ERP, archivos corruptos) y la respuesta esperada del sistema. | Evita diseñar pensando únicamente en el "camino feliz", previniendo inconsistencias graves en bases de datos o estados indeterminados en procesos financieros críticos. |
| 6 | **Interfaz de usuario / Wireframe referenciado (si aplica)**<br>Si la historia involucra interfaz gráfica, cuenta con un wireframe aprobado o una descripción precisa de interacción y componentes en la documentación de UI. | Evita retrabajo y discrepancias visuales a mitad del desarrollo causadas por improvisación en el maquetado y disposición de componentes. |
| 7 | **Datos de prueba representativos identificados**<br>Se dispone de muestras o archivos de prueba (ej. planillas de pago reales o sintéticas con casos válidos e inválidos) para validar el comportamiento antes de pasar a QA/producción. | Evita retrasos al verificar validaciones complejas (CBU, CUIT, importes) y asegura que las pruebas cubran casos reales del comitente. |

---

## Aplicación a tres historias propias

_Elijan TRES historias de usuario de su propio trabajo del primer semestre y pásenlas por su
propia checklist. Es esperable —y deseable— que alguna no pase._

### Historia 1 — HU-01: Generación de transferencias masivas TEF

| Ítem (según checklist) | ¿Pasa? | Qué le falta (si no pasa) |
|-------------------------|--------|-----------------------------|
| 1. Formato estándar y rol | **Sí** | Cumple el formato estándar y el rol "personal administrativo contable" está definido en stakeholders. |
| 2. Criterios de aceptación objetivos | **Sí** | Posee 7 criterios de aceptación detallados que cubren carga, validaciones y exportación en 240 caracteres. |
| 3. Tamaño atómico y acotado | **No** | Es una épica encubierta que agrupa 5 requisitos (RF-01 a RF-05). Como demostró el slicing vertical, abarca múltiples flujos y requiere dividirse en al menos 7 historias independientes. |
| 4. Dependencias técnicas definidas | **Sí** | El formato TEF de 240 caracteres de Interbanking está documentado y no requiere conexión API en línea (el usuario descarga el archivo). |
| 5. Comportamiento ante errores | **No** | Falta definir el comportamiento y alertas ante la carga de un lote de pagos duplicado (prevención de doble pago) y topes por lote/transferencia. |
| 6. Interfaz de usuario referenciada | **No** | Falta vincular el wireframe de la pantalla de carga y de la previsualización con detalle de registros inválidos (ausente en `diagramas/wireframes/`). |
| 7. Datos de prueba identificados | **Sí** | Se definieron los formatos admitidos (.xls, .xlsx, .csv) y la estructura de columnas requerida (CBU, CUIT, importe, beneficiario). |

> **Dictamen**: **NO PASA (Vuelve a refinamiento)**. Debe atravesar el slicing vertical para ingresar al sprint dividida en historias pequeñas y sumar los wireframes correspondientes.

---

### Historia 2 — HU-02: Sincronización de extractos bancarios

| Ítem (según checklist) | ¿Pasa? | Qué le falta (si no pasa) |
|-------------------------|--------|-----------------------------|
| 1. Formato estándar y rol | **Sí** | Cumple el formato estándar con el rol "analista contable". |
| 2. Criterios de aceptación objetivos | **No** | Varios criterios son vagos o repiten la historia ("el sistema descarga los movimientos"), sin especificar la estructura del reporte ni reglas contables de débito/crédito. |
| 3. Tamaño atómico y acotado | **No** | Es una épica que engloba descarga desde Interbanking, previsualización, impacto en libro de bancos de SAP B1, manejo transaccional de cortes y auditoría. |
| 4. Dependencias técnicas definidas | **No** | Faltan las especificaciones técnicas del API de Interbanking y del Service Layer / DI API de SAP Business One para el registro bancario. |
| 5. Comportamiento ante errores | **No** | Aunque contempla interrupción ante corte de SAP, no especifica cómo detectar y tratar movimientos bancarios ya impactados en conciliaciones previas (prevención de duplicados). |
| 6. Interfaz de usuario referenciada | **No** | Falta el wireframe de la previsualización de extractos bancarios y la confirmación previa al impacto en SAP. |
| 7. Datos de prueba identificados | **No** | No se dispone de un archivo de extracto simulado ni respuestas mock del API bancario para testing. |

> **Dictamen**: **NO PASA (Vuelve a refinamiento)**. Requiere slicing vertical, formalización de los contratos de interfaz con SAP/Interbanking y criterios de aceptación medibles.

---

### Historia 3 — HU-04: Autenticación y auditoría del sistema

| Ítem (según checklist) | ¿Pasa? | Qué le falta (si no pasa) |
|-------------------------|--------|-----------------------------|
| 1. Formato estándar y rol | **Sí** | Cumple el formato estándar con el rol "administrador de infraestructura / analista IT". |
| 2. Criterios de aceptación objetivos | **Sí** | Define criterios verificables sobre lista blanca de accesos, denegación a no autorizados y registro de eventos clave. |
| 3. Tamaño atómico y acotado | **Sí** | El alcance está acotado a control de acceso por lista blanca y registro centralizado de bitácora (sin roles complejos). Es abordable en una iteración. |
| 4. Dependencias técnicas definidas | **Sí** | Es una funcionalidad interna; no depende de APIs bancarias externas y utiliza la persistencia local de bitácora y lista autorizada. |
| 5. Comportamiento ante errores | **Sí** | Especifica el rechazo explícito ante accesos no autorizados y el registro de fallos técnicos del ERP en la bitácora. |
| 6. Interfaz de usuario referenciada | **No** | Falta vincular el wireframe de la pantalla de inicio de sesión y del visor de auditoría. |
| 7. Datos de prueba identificados | **Sí** | Se contemplan cuentas válidas, no autorizadas y eventos de prueba para validar el registro. |

> **Dictamen**: **NO PASA (Condicionada únicamente a la incorporación del wireframe UI)**. Es la historia más cercana a estar lista ("Ready"), requiriendo únicamente el artefacto visual de interfaz.
