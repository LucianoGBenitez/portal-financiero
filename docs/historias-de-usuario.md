## Épica HU-01 — Generación de transferencias masivas TEF

### HU-01.1 — Carga de planilla de pagos

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero cargar una planilla de pagos en formato `.xls`, `.xlsx` o `.csv`, para que el sistema la reciba y la tenga disponible para procesar. |
| Módulo                   | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados  | RF-01 |

#### Criterios de aceptación
1. El sistema permite cargar archivos de pago en formato `.xls`, `.xlsx` o `.csv`.
2. El sistema confirma la carga exitosa del archivo antes de continuar con el proceso.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | No depende de la detección de columnas ni de la generación del archivo. |
| Negociable    | Sí | Los formatos de archivo aceptados pueden ampliarse a futuro. |
| Valiosa       | Sí | Es el punto de entrada obligatorio de todo el proceso de TEF. |
| Estimable     | Sí | Alcance acotado a la carga del archivo. |
| Pequeña       | Sí | Una sola acción: subir el archivo. |
| Verificable   | Sí | Puede probarse con archivos válidos e inválidos en su formato. |

---

### HU-01.2 — Detección automática de columnas

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero que el sistema identifique automáticamente (búsqueda flexible) las columnas de CBU, importe, nombre y CUIT, para no tener que indicarlas manualmente. |
| Módulo                   | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados  | RF-02 |

#### Criterios de aceptación
1. El sistema identifica mediante búsqueda flexible las columnas de CBU, importe, nombre y CUIT del beneficiario.
2. Si el sistema no logra identificar automáticamente una columna, permite asignarla manualmente antes de continuar.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende únicamente de que exista un archivo cargado (HU-01.1). |
| Negociable    | Sí | El algoritmo de búsqueda flexible puede ajustarse con el equipo contable. |
| Valiosa       | Sí | Evita el mapeo manual de columnas en cada carga. |
| Estimable     | Sí | Alcance limitado a la detección/mapeo de 4 columnas. |
| Pequeña       | Sí | Una sola funcionalidad concreta. |
| Verificable   | Sí | Puede probarse con planillas con encabezados estándar y no estándar. |

---

### HU-01.3 — Validación y previsualización de CBU/CUIT

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero previsualizar la planilla con los registros inválidos marcados visualmente, para detectar errores de CBU, CUIT o campos obligatorios antes de generar el archivo. |
| Módulo                   | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados  | RF-03 |

#### Criterios de aceptación
1. El sistema valida automáticamente que la CBU y el CUIT tengan un formato válido, y que los campos obligatorios estén completos.
2. La previsualización marca visualmente cada registro inválido y muestra el motivo del error.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Requiere las columnas ya detectadas (HU-01.2), pero no la generación del archivo. |
| Negociable    | Sí | Las reglas de formato de CBU/CUIT pueden ajustarse con contabilidad. |
| Valiosa       | Sí | Permite corregir errores antes de generar un archivo inválido para el banco. |
| Estimable     | Sí | Alcance limitado a validación y marcado visual. |
| Pequeña       | Sí | No incluye la exclusión ni la generación del archivo. |
| Verificable   | Sí | Puede probarse con CBU/CUIT válidos, inválidos e incompletos. |

---

### HU-01.4 — Exclusión automática de registros inválidos

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero que los registros inválidos se excluyan automáticamente de la exportación, para que un error puntual no impida generar el archivo con los registros válidos. |
| Módulo                   | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados  | RF-03 |

#### Criterios de aceptación
1. El sistema excluye automáticamente de la exportación los registros con errores de validación.
2. Los registros válidos se mantienen disponibles para la exportación sin intervención manual.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de la validación (HU-01.3), pero es una acción separada de ella. |
| Negociable    | Sí | Se podría negociar si la exclusión es automática o requiere confirmación del usuario. |
| Valiosa       | Sí | Evita que un solo error bloquee todo el lote de pagos. |
| Estimable     | Sí | Alcance limitado al filtrado de registros ya validados. |
| Pequeña       | Sí | Una sola regla de negocio: excluir lo inválido. |
| Verificable   | Sí | Puede probarse con lotes mixtos (válidos e inválidos). |

---

### HU-01.5 — Generación del archivo TEF

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero generar un archivo de texto de ancho fijo de 240 caracteres por registro, para obtener un archivo TEF válido y listo para subir a Interbanking. |
| Módulo                   | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados  | RF-04 |

#### Criterios de aceptación
1. El sistema genera un archivo de texto de ancho fijo de 240 caracteres por registro, conforme al estándar requerido por Interbanking.
2. El archivo generado incluye únicamente los registros válidos (ya filtrados en HU-01.4).
3. El usuario puede descargar el archivo generado.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de tener registros ya validados y filtrados. |
| Negociable    | No | El formato de 240 caracteres lo define el estándar de Interbanking, no es negociable. |
| Valiosa       | Sí | Es el entregable final del proceso: el archivo listo para el banco. |
| Estimable     | Sí | Alcance limitado a la construcción del archivo según el estándar. |
| Pequeña       | Sí | Una sola acción: generar y poner a disposición el archivo. |
| Verificable   | Sí | Puede probarse comparando el archivo generado contra el estándar de Interbanking. |

---

### HU-01.6 — Informe de resultado de la generación

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero recibir un informe con la cantidad de registros válidos, excluidos y los errores detectados, para conocer el resultado completo del proceso de generación. |
| Módulo                   | Generación de Transferencias Masivas (TEF) |
| Requisitos relacionados  | RF-05 |

#### Criterios de aceptación
1. El sistema informa la cantidad de registros válidos, excluidos y con error al finalizar el proceso.
2. El informe detalla el motivo de exclusión de cada registro con error.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Puede desarrollarse en paralelo a la generación del archivo, solo necesita los resultados del proceso. |
| Negociable    | Sí | El formato del informe (en pantalla, PDF, etc.) puede definirse con el equipo. |
| Valiosa       | Sí | Da visibilidad y confianza sobre el resultado del proceso. |
| Estimable     | Sí | Alcance limitado a mostrar un resumen de resultados. |
| Pequeña       | Sí | Una sola pantalla/mensaje de resumen. |
| Verificable   | Sí | Puede probarse comparando el informe contra el detalle real del lote procesado. |

---

## Épica HU-02 — Sincronización de extractos bancarios

### HU-02.1 — Descarga de movimientos desde Interbanking

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como analista contable, quiero que el sistema descargue los movimientos bancarios de una cuenta configurada desde Interbanking, para tener el extracto actualizado antes de enviarlo a SAP. |
| Módulo                   | Sincronización de Extractos Bancarios |
| Requisitos relacionados  | RF-06, RF-07 |

#### Criterios de aceptación
1. El sistema verifica que el usuario esté autenticado y autorizado antes de iniciar la sincronización.
2. El sistema establece una conexión segura con Interbanking y descarga los movimientos de la cuenta bancaria previamente configurada.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | No depende del envío ni del registro en SAP. |
| Negociable    | Sí | La frecuencia y el rango de fechas de la descarga pueden ajustarse. |
| Valiosa       | Sí | Es el punto de entrada del proceso de conciliación. |
| Estimable     | Sí | Alcance limitado a autenticación y descarga. |
| Pequeña       | Sí | Una cuenta, una descarga. |
| Verificable   | Sí | Puede probarse con usuarios autorizados/no autorizados y con la API disponible/caída. |

---

### HU-02.2 — Previsualización y confirmación de movimientos

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como analista contable, quiero previsualizar los movimientos descargados y confirmar cuáles enviar, para controlar qué se registra en SAP antes de impactarlo. |
| Módulo                   | Sincronización de Extractos Bancarios |
| Requisitos relacionados  | RF-07, RF-08 |

#### Criterios de aceptación
1. El sistema permite previsualizar los movimientos descargados antes de enviarlos al ERP.
2. El usuario puede confirmar el envío de los movimientos previamente visualizados.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de tener movimientos ya descargados (HU-02.1). |
| Negociable    | Sí | Se puede definir si la confirmación es por movimiento o por lote completo. |
| Valiosa       | Sí | Agrega un control humano antes de impactar en el ERP. |
| Estimable     | Sí | Alcance limitado a mostrar y confirmar una lista de movimientos. |
| Pequeña       | Sí | Una sola pantalla de revisión y confirmación. |
| Verificable   | Sí | Puede probarse confirmando parcial o totalmente un lote de movimientos. |

---

### HU-02.3 — Registro de movimientos en el libro de bancos de SAP

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como analista contable, quiero que los movimientos confirmados se registren automáticamente en el libro de bancos de SAP Business One, para no tener que cargarlos manualmente. |
| Módulo                   | Sincronización de Extractos Bancarios |
| Requisitos relacionados  | RF-09 |

#### Criterios de aceptación
1. El sistema registra los movimientos confirmados en el libro de bancos de SAP Business One.
2. El sistema clasifica cada movimiento en las columnas de débito o crédito correspondientes.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de tener movimientos ya confirmados (HU-02.2). |
| Negociable    | Sí | El mapeo de cuentas contables puede ajustarse con contabilidad. |
| Valiosa       | Sí | Es el impacto contable real, el objetivo final del proceso. |
| Estimable     | Sí | Alcance limitado al registro del asiento en SAP. |
| Pequeña       | Sí | Una sola acción: registrar el movimiento confirmado. |
| Verificable   | Sí | Puede probarse verificando el asiento generado en SAP. |

---

### HU-02.4 — Manejo de error de conexión con SAP durante la sincronización

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como analista contable, quiero que el sistema interrumpa la sincronización y evite impactos parciales si se pierde la conexión con SAP, para que no se dupliquen registros contables. |
| Módulo                   | Sincronización de Extractos Bancarios |
| Requisitos relacionados  | RF-09 |

#### Criterios de aceptación
1. Si se pierde la conexión con SAP Business One durante el proceso, el sistema interrumpe la sincronización.
2. El sistema evita o revierte cualquier impacto parcial para prevenir duplicaciones.
3. El sistema informa el error al analista contable.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Es un caso alternativo del registro (HU-02.3), pero se puede probar y desarrollar por separado. |
| Negociable    | Sí | La política de reintento (automática o manual) puede definirse con el equipo. |
| Valiosa       | Sí | Evita duplicación de asientos contables, un riesgo crítico. |
| Estimable     | Sí | Alcance limitado al manejo de esta excepción puntual. |
| Pequeña       | Sí | Un solo caso de error a resolver. |
| Verificable   | Sí | Puede probarse simulando la caída de conexión con SAP en distintos momentos del proceso. |

---

### HU-02.5 — Reporte final de la sincronización

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como analista contable, quiero recibir un reporte con la cantidad de movimientos descargados, confirmados, rechazados y procesados con error, para conocer el resultado completo de la sincronización. |
| Módulo                   | Sincronización de Extractos Bancarios |
| Requisitos relacionados  | RF-08 |

#### Criterios de aceptación
1. El sistema genera un reporte final con la cantidad de movimientos descargados, confirmados, rechazados y procesados con error.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Solo necesita los resultados del proceso, no el proceso en sí. |
| Negociable    | Sí | El formato del reporte puede definirse con el equipo contable. |
| Valiosa       | Sí | Da visibilidad del resultado sin tener que revisar SAP directamente. |
| Estimable     | Sí | Alcance limitado a un resumen de resultados. |
| Pequeña       | Sí | Una sola pantalla/mensaje de resumen. |
| Verificable   | Sí | Puede probarse comparando el reporte contra el detalle real procesado. |

---

### HU-02.6 — Registro en bitácora de la sincronización

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como analista contable, quiero que cada sincronización quede registrada en la bitácora con fecha, usuario, cuenta y resultado, para tener trazabilidad de lo procesado. |
| Módulo                   | Sincronización de Extractos Bancarios |
| Requisitos relacionados  | RF-14 |

#### Criterios de aceptación
1. El sistema registra en la bitácora la fecha, usuario, cuenta procesada y resultado de la operación.
2. El sistema registra los mensajes técnicos devueltos por SAP o Interbanking.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Comparte el requisito de bitácora con HU-04.3, pero puede probarse de forma aislada. |
| Negociable    | Sí | Los campos exactos de la bitácora pueden ajustarse con IT. |
| Valiosa       | Sí | Da trazabilidad ante auditorías o reclamos. |
| Estimable     | Sí | Alcance limitado al registro de un evento por sincronización. |
| Pequeña       | Sí | Una sola escritura en bitácora por ejecución. |
| Verificable   | Sí | Puede probarse revisando que cada sincronización deje su registro correspondiente. |

---

## Épica HU-03 — Sincronización de datos maestros (CBU)

### HU-03.1 — Contraste del padrón bancario contra el maestro de SAP

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero que el sistema contraste el padrón bancario externo contra el maestro de socios de negocio de SAP, para detectar qué CBU están desactualizados. |
| Módulo                   | Sincronización de Datos Maestros (CBU) |
| Requisitos relacionados  | RF-10 |

#### Criterios de aceptación
1. El sistema contrasta automáticamente los registros del padrón bancario externo contra el maestro de socios de negocio de SAP.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | No depende de la clasificación ni de la actualización posterior. |
| Negociable    | Sí | La fuente y frecuencia del padrón externo pueden ajustarse. |
| Valiosa       | Sí | Es la base para detectar CBU desactualizados sin revisar manualmente. |
| Estimable     | Sí | Alcance limitado al cruce de dos fuentes de datos. |
| Pequeña       | Sí | Una sola acción: comparar. |
| Verificable   | Sí | Puede probarse con datos coincidentes y no coincidentes conocidos. |

---

### HU-03.2 — Clasificación y presentación de resultados del cruce

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero ver los resultados del cruce agrupados en actualización inmediata, sugerencia por nombre o registro único, para revisar cada caso antes de aplicar cambios en SAP. |
| Módulo                   | Sincronización de Datos Maestros (CBU) |
| Requisitos relacionados  | RF-11 |

#### Criterios de aceptación
1. El sistema clasifica cada resultado del cruce en una de tres categorías: actualización inmediata (coincidencia exacta), sugerencia por nombre (coincidencia parcial) o registro único (sin coincidencia).
2. El sistema presenta los resultados agrupados por categoría antes de aplicar cualquier cambio en SAP.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende del cruce ya realizado (HU-03.1), pero es una funcionalidad propia. |
| Negociable    | Sí | Los criterios de coincidencia exacta/parcial pueden ajustarse con contabilidad. |
| Valiosa       | Sí | Permite decidir con criterio antes de tocar el maestro de SAP. |
| Estimable     | Sí | Alcance limitado a clasificar y mostrar resultados. |
| Pequeña       | Sí | Una sola pantalla de resultados agrupados. |
| Verificable   | Sí | Puede probarse con coincidencias exactas, parciales y sin match. |

---

### HU-03.3 — Confirmación y actualización masiva en SAP

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero confirmar la actualización masiva de los datos bancarios en SAP con una sola acción, para no cargar los CBU uno por uno manualmente. |
| Módulo                   | Sincronización de Datos Maestros (CBU) |
| Requisitos relacionados  | RF-11, RF-12 |

#### Criterios de aceptación
1. El personal administrativo contable puede confirmar la actualización masiva de los datos bancarios en SAP con una sola acción.
2. El sistema aplica la actualización únicamente a los registros confirmados por el usuario.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de tener resultados ya clasificados (HU-03.2). |
| Negociable    | Sí | Se puede definir si la confirmación es global o por categoría. |
| Valiosa       | Sí | Es el impacto real: evita cargar CBU manualmente uno por uno. |
| Estimable     | Sí | Alcance limitado a la actualización masiva en SAP. |
| Pequeña       | Sí | Una sola acción de confirmación y escritura. |
| Verificable   | Sí | Puede probarse verificando los CBU actualizados en el maestro de SAP. |

---

### HU-03.4 — Informe final de la sincronización de CBU

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como personal administrativo contable, quiero conocer la cantidad de registros actualizados, sugeridos y no encontrados al finalizar el proceso, para tener control del resultado de la sincronización de CBU. |
| Módulo                   | Sincronización de Datos Maestros (CBU) |
| Requisitos relacionados  | RF-12 |

#### Criterios de aceptación
1. El sistema informa la cantidad de registros actualizados, sugeridos y no encontrados al finalizar el proceso.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Solo necesita los resultados del proceso de actualización. |
| Negociable    | Sí | El formato del informe puede definirse con el equipo contable. |
| Valiosa       | Sí | Da visibilidad del resultado sin revisar SAP registro por registro. |
| Estimable     | Sí | Alcance limitado a un resumen de resultados. |
| Pequeña       | Sí | Una sola pantalla/mensaje de resumen. |
| Verificable   | Sí | Puede probarse comparando el informe contra el detalle real procesado. |

---

## Épica HU-04 — Autenticación y auditoría del sistema

### HU-04.1 — Autenticación obligatoria para acceder al sistema

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como administrador de infraestructura / analista IT, quiero que el sistema exija autenticación antes de permitir el acceso a cualquier módulo, para garantizar que nadie opere el sistema sin identificarse. |
| Módulo                   | Seguridad y Auditoría |
| Requisitos relacionados  | RF-13 |

#### Criterios de aceptación
1. El sistema exige autenticación antes de permitir el acceso a cualquier módulo funcional.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | No depende de la validación contra lista blanca (HU-04.2), aunque suelen implementarse juntas. |
| Negociable    | Sí | El proveedor de autenticación (Google OAuth u otro) puede ajustarse. |
| Valiosa       | Sí | Es la barrera mínima de seguridad de todo el sistema. |
| Estimable     | Sí | Alcance limitado a exigir login. |
| Pequeña       | Sí | Una sola regla: sin login, no hay acceso. |
| Verificable   | Sí | Puede probarse intentando acceder sin autenticarse. |

---

### HU-04.2 — Validación contra lista blanca de cuentas autorizadas

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como administrador de infraestructura / analista IT, quiero que el sistema valide al usuario autenticado contra una lista blanca de cuentas autorizadas, para que solo personal autorizado pueda operar el sistema. |
| Módulo                   | Seguridad y Auditoría |
| Requisitos relacionados  | RF-13 |

#### Criterios de aceptación
1. El sistema valida al usuario autenticado contra una lista blanca explícita de cuentas autorizadas.
2. El sistema rechaza el acceso a cualquier cuenta no incluida en la lista blanca.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de que exista autenticación (HU-04.1), pero es una regla de negocio aparte. |
| Negociable    | Sí | El mecanismo de gestión de la lista blanca (manual, por dominio, etc.) puede ajustarse. |
| Valiosa       | Sí | Restringe el acceso a personal específicamente autorizado. |
| Estimable     | Sí | Alcance limitado a la validación contra la lista. |
| Pequeña       | Sí | Una sola regla de autorización. |
| Verificable   | Sí | Puede probarse con cuentas incluidas y no incluidas en la lista. |

---

### HU-04.3 — Registro en bitácora de operaciones críticas

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como administrador de infraestructura / analista IT, quiero que el sistema registre en una bitácora centralizada cada TEF generado, extracto procesado y actualización del maestro, para tener trazabilidad de cada operación crítica. |
| Módulo                   | Seguridad y Auditoría |
| Requisitos relacionados  | RF-14 |

#### Criterios de aceptación
1. El sistema registra en una bitácora centralizada cada archivo TEF generado y cada impacto de extracto procesado, incluyendo usuario, fecha/hora y resultado.
2. El sistema registra el estado (Success/Error) y el mensaje técnico devuelto por SAP en cada actualización del maestro de proveedores.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Es transversal a los demás módulos, pero puede desarrollarse y probarse por separado. |
| Negociable    | Sí | El detalle de los campos de la bitácora puede ajustarse con IT. |
| Valiosa       | Sí | Da trazabilidad ante auditorías de todas las operaciones críticas. |
| Estimable     | Sí | Alcance limitado al registro de eventos ya definidos. |
| Pequeña       | Sí | Una sola responsabilidad: escribir el evento en la bitácora. |
| Verificable   | Sí | Puede probarse verificando que cada operación crítica deje su registro. |

---

### HU-04.4 — Consulta del historial de bitácora

| Campo                   | Detalle |
| ------------------------ | ------- |
| Historia                 | Como administrador de infraestructura / analista IT, quiero poder consultar el historial de operaciones registradas en la bitácora, para revisar lo ocurrido ante una duda o auditoría. |
| Módulo                   | Seguridad y Auditoría |
| Requisitos relacionados  | RF-15 |

#### Criterios de aceptación
1. El administrador de infraestructura / analista IT puede consultar el historial de operaciones registradas en la bitácora.

#### Validación INVEST

| Criterio      | ¿Se cumple? | Observación |
| ------------- | ----------- | ----------- |
| Independiente | Sí | Depende de que existan registros previos (HU-04.3), pero la consulta es una funcionalidad propia. |
| Negociable    | Sí | Los filtros de búsqueda (por fecha, usuario, módulo) pueden definirse con el equipo. |
| Valiosa       | Sí | Permite auditar el sistema sin acceder directamente a la base de datos. |
| Estimable     | Sí | Alcance limitado a una pantalla de consulta. |
| Pequeña       | Sí | Una sola pantalla de listado/consulta. |
| Verificable   | Sí | Puede probarse verificando que los eventos registrados aparezcan en la consulta. |