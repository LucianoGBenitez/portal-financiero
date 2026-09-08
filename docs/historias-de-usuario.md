## HU-01 — Generación de transferencias masivas TEF

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero cargar una planilla de pagos y previsualizar sus validaciones, para generar un archivo TEF válido para Interbanking sin incluir registros erróneos. |
| Módulo | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados | RF-01, RF-02, RF-03, RF-04, RF-05 |

### Criterios de aceptación

1. El sistema permite cargar archivos de pago en formato `.xls`, `.xlsx` o `.csv`.
2. El sistema identifica las columnas necesarias mediante búsqueda flexible para CBU, importe, nombre y CUIT del beneficiario.
3. El sistema valida automáticamente que la CBU y el CUIT tengan un formato válido, y que los campos obligatorios estén completos.
4. La previsualización identifica visualmente cada registro inválido y muestra el motivo del error.
5. El sistema excluye automáticamente de la exportación los registros que tengan errores de validación, manteniendo los registros válidos.
6. El sistema genera un archivo de texto de ancho fijo de 240 caracteres por registro, conforme al estándar requerido por Interbanking.
7. El sistema informa al usuario el resultado de la generación, incluyendo la cantidad de registros válidos, excluidos y los errores detectados.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | Puede desarrollarse sin depender del módulo de extractos bancarios. |
| Negociable | Sí | El formato de la previsualización y los mensajes de error pueden definirse con el equipo contable. |
| Valiosa | Sí | Reduce errores de carga y permite obtener un archivo TEF listo para Interbanking. |
| Estimable | Sí | El alcance está limitado a carga, validación, previsualización y exportación. |
| Pequeña | Sí | Se limita a una generación de archivo por lote de pagos. |
| Verificable | Sí | Puede probarse con archivos válidos, inválidos y con distintas extensiones. |

---

## HU-02 — Sincronización de extractos bancarios

| Campo | Detalle |
|-------|---------|
| Historia | Como analista contable, quiero descargar y previsualizar los movimientos bancarios de una cuenta desde Interbanking antes de enviarlos a SAP Business One, para registrar los movimientos confirmados en el libro de bancos de forma segura y auditable. |
| Módulo | Sincronización de Extractos Bancarios |
| Requisitos relacionados | RF-06, RF-07, RF-08, RF-09, RF-14 |

### Criterios de aceptación

1. El sistema verifica que el usuario esté autenticado y autorizado para realizar la sincronización.
2. El sistema establece una conexión segura con Interbanking y descarga los movimientos de una cuenta bancaria previamente configurada.
3. El sistema permite previsualizar los movimientos descargados antes de enviarlos al ERP.
4. El usuario puede confirmar el envío de los movimientos previamente visualizados.
5. El sistema registra los movimientos confirmados en el libro de bancos de SAP Business One, clasificándolos en las columnas de débito o crédito correspondientes.
6. Si se pierde la conexión con SAP Business One durante el proceso, el sistema interrumpe la sincronización y evita o revierte cualquier impacto parcial para prevenir duplicaciones.
7. El sistema genera un reporte final con la cantidad de movimientos descargados, confirmados, rechazados y procesados con error.
8. El sistema registra en la bitácora la fecha, usuario, cuenta procesada, resultado de la operación y los mensajes técnicos devueltos por SAP o Interbanking.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | Es funcionalmente independiente de la generación de archivos TEF. |
| Negociable | Sí | El formato de la previsualización y del reporte puede acordarse con contabilidad. |
| Valiosa | Sí | Reduce la carga manual de extractos y mejora la trazabilidad de los movimientos. |
| Estimable | Sí | El alcance se limita a una cuenta bancaria y a un proceso de sincronización. |
| Pequeña | Sí, con alcance acotado | Debe implementarse inicialmente para una cuenta y un tipo de extracto definido. |
| Verificable | Sí | Puede probarse con movimientos válidos, errores de conexión y respuestas fallidas de SAP. |



# Historias de usuario

_Presentar al menos una historia de usuario representativa por módulo._
_Cada historia debe incluir formato clásico, criterios de aceptación y validación INVEST._

---

## HU-00 — [Nombre de la historia]

| Campo | Detalle |
|-------|---------|
| Historia | Como [rol], quiero [acción], para [objetivo]. |
| Módulo | |
| Requisitos relacionados | RF-XX, RF-XX |

### Criterios de aceptación

1. 
2. 
3. 

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | | |
| Negociable | | |
| Valiosa | | |
| Estimable | | |
| Pequeña | | |
| Verificable | | |

---

