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

## HU-03 — Sincronización de datos maestros (CBU)

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero contrastar el padrón bancario externo contra el maestro de socios de negocio de SAP y actualizar los datos bancarios detectados con un solo clic, para mantener los CBU de proveedores y clientes correctos sin cargarlos manualmente uno por uno. |
| Módulo | Sincronización de Datos Maestros (CBU) |
| Requisitos relacionados | RF-10, RF-11, RF-12 |

### Criterios de aceptación

1. El sistema contrasta automáticamente los registros del padrón bancario externo contra el maestro de socios de negocio de SAP.
2. El sistema clasifica cada resultado del cruce en una de tres categorías: actualización inmediata (coincidencia exacta), sugerencia por nombre (coincidencia parcial) o registro único (sin coincidencia).
3. El sistema presenta los resultados agrupados por categoría antes de aplicar cualquier cambio en SAP.
4. El personal administrativo contable puede confirmar la actualización masiva de los datos bancarios en SAP con una sola acción.
5. El sistema informa la cantidad de registros actualizados, sugeridos y no encontrados al finalizar el proceso.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | No depende de la generación de TEF ni de la sincronización de extractos. |
| Negociable | Sí | Los criterios de coincidencia (exacta/por nombre) pueden ajustarse con contabilidad. |
| Valiosa | Sí | Evita la actualización manual de CBU uno por uno en SAP. |
| Estimable | Sí | El alcance se limita a comparar, clasificar y actualizar el maestro. |
| Pequeña | Sí | Se limita a un cruce y una actualización masiva por ejecución. |
| Verificable | Sí | Puede probarse con coincidencias exactas, parciales y registros sin match. |

---

## HU-04 — Autenticación y auditoría del sistema

| Campo | Detalle |
|-------|---------|
| Historia | Como administrador de infraestructura / analista IT, quiero que el sistema exija autenticación contra una lista blanca de cuentas autorizadas y registre una bitácora centralizada de cada operación crítica, para garantizar que solo personal autorizado opere el sistema y que cada acción quede trazada. |
| Módulo | Seguridad y Auditoría |
| Requisitos relacionados | RF-13, RF-14, RF-15 |

### Criterios de aceptación

1. El sistema exige autenticación antes de permitir el acceso a cualquier módulo funcional.
2. El sistema valida al usuario autenticado contra una lista blanca explícita de cuentas autorizadas y rechaza el acceso a cualquier cuenta no incluida.
3. El sistema registra en una bitácora centralizada cada archivo TEF generado y cada impacto de extracto procesado, incluyendo usuario, fecha/hora y resultado.
4. El sistema registra el estado (Success/Error) y el mensaje técnico devuelto por SAP en cada actualización del maestro de proveedores.
5. El administrador de infraestructura / analista IT puede consultar el historial de operaciones registradas en la bitácora.

### Validación INVEST

| Criterio | ¿Se cumple? | Observación |
|----------|-------------|-------------|
| Independiente | Sí | Es transversal, pero puede desarrollarse y probarse por separado de los demás módulos. |
| Negociable | Sí | El detalle de los campos de la bitácora puede ajustarse con IT. |
| Valiosa | Sí | Protege el acceso al sistema y da trazabilidad ante auditorías. |
| Estimable | Sí | El alcance se limita a autenticación, lista blanca y registro de bitácora. |
| Pequeña | Sí | No incluye gestión de roles ni permisos granulares, solo acceso/no acceso. |
| Verificable | Sí | Puede probarse con usuarios autorizados, no autorizados y fallos del ERP. |
