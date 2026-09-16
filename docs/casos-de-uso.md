# Casos de uso

## Diagrama general

*Incluir el código PlantUML en `diagramas/casos-de-uso.puml`.*
*Visualizar en [plantuml.com](https://www.plantuml.com/plantuml/uml/).*

**Actores principales**

- **Operador de Tesorería**: genera las transferencias masivas (CU-01).
- **Analista Contable / Conciliador**: concilia extractos e impacta movimientos en el ERP (CU-02).

**Actores secundarios / externos**

- **Interbanking API Gateway**: provee los movimientos bancarios y recibe los archivos de transferencia.
- **SAP Business One Service Layer**: recibe los impactos contables y expone el maestro de proveedores/cuentas.
- **Gerente de Finanzas**: recibe las notificaciones de control (relación `«extend»` desde CU-02).
- **Administrador IT**: mantiene la infraestructura, las credenciales y el monitoreo del sistema.

**Relaciones principales**: CU-01 y CU-02 `«include»` la autenticación mediante Google OAuth 2.0 y la validación contra la lista blanca corporativa. CU-02 `«extend»` con el envío de la notificación por WhatsApp al Gerente de Finanzas cuando el impacto contable finaliza correctamente.

---

## CU-01 — Generación de Transferencias Masivas (Archivo TEF)

| Campo            | Detalle                                                                                                                                                                                                          |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identificador    | CU-01                                                                                                                                                                                                            |
| Nombre           | Generación de Transferencias Masivas (Archivo TEF)                                                                                                                                                              |
| Descripción      | El Operador de Tesorería carga una planilla de pagos; el sistema detecta y valida automáticamente los datos bancarios (CBU, Importe, Nombre, CUIT) y genera el archivo TEF de ancho fijo (240 caracteres) para ejecutar las transferencias mediante Interbanking. |
| Actores          | Principal: Operador de Tesorería / Secundario: Administrador IT                                                                                                                                                 |
| Precondiciones   | Usuario autenticado con cuenta corporativa autorizada; planilla de pagos disponible en formato .xlsx, .csv o .txt                                                                                               |
| Postcondiciones  | Éxito: archivo TEF generado y disponible para su descarga y envío a Interbanking / Fallo: carga rechazada o filas con error excluidas de la exportación final                                                  |

### Secuencia normal

| #   | Acción (actor)                                              | Reacción (sistema)                                                                                                                                    |
| --- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | El Operador de Tesorería carga la planilla de pagos (.xlsx/.csv/.txt) | El sistema detecta automáticamente las columnas de CBU, Importe, Nombre y CUIT mediante búsqueda difusa (fuzzy matching)                                |
| 2   | El Operador revisa la previsualización de los datos           | El sistema muestra alertas visuales de errores (ej. CBU sin 22 dígitos) y excluye automáticamente las filas inválidas                                    |
| 3   | El Operador confirma la generación del archivo                | El sistema compila el archivo de texto de ancho fijo (240 caracteres), estructurando la cabecera (Línea U) y el detalle (Línea M) según Interbanking     |
| 4   | El Operador descarga el archivo generado                      | El sistema pone a disposición el archivo TEF para su envío a Interbanking                                                                                |

### Excepciones

| #   | Situación                                                     | Respuesta del sistema                                                                                    |
| --- | -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| E1  | Una fila de la planilla contiene un CBU sin 22 dígitos numéricos | El sistema marca la fila con error visual y la excluye de la exportación final, sin detener el proceso general |
| E2  | El sistema no logra detectar automáticamente alguna columna requerida | El sistema solicita al Operador mapear manualmente la columna correspondiente antes de continuar          |

| Campo       | Detalle                                                                 |
| ----------- | ------------------------------------------------------------------------ |
| Rendimiento | Alto: elimina la transcripción manual del CBU y reduce errores de carga |
| Frecuencia  | Mensual (carga de planillas de pago)                                     |
| Importancia | Alta                                                                      |
| Urgencia    | Media                                                                     |

---

## CU-02 — Conciliación de Extractos e Impacto Contable Directo en ERP

| Campo            | Detalle                                                                                                                                                                                          |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identificador    | CU-02                                                                                                                                                                                            |
| Nombre           | Conciliación de Extractos e Impacto Contable Directo en ERP                                                                                                                                     |
| Descripción      | El Analista Contable sincroniza los movimientos bancarios de una cuenta desde Interbanking; el sistema los impacta automáticamente en el libro de bancos de SAP Business One, deja registro en la base de datos y notifica el resultado. |
| Actores          | Principal: Analista Contable / Conciliador / Secundario: Interbanking API Gateway, SAP Business One Service Layer, Gerente de Finanzas                                                          |
| Precondiciones   | Usuario autenticado; cuenta bancaria configurada; CBU asociado correctamente                                                                                                                    |
| Postcondiciones  | Éxito: movimientos impactados en SAP, registro guardado en MySQL y PDF de control enviado por WhatsApp al Gerente de Finanzas / Fallo: proceso cancelado, sin duplicación de registros y con el error informado al Analista Contable |

### Secuencia normal

| #   | Acción (actor)                                                    | Reacción (sistema)                                                                                              |
| --- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| 1   | El Analista Contable selecciona la sociedad y la cuenta a conciliar | El sistema consulta los movimientos de esa cuenta en Interbanking                                                  |
| 2   | —                                                                    | El sistema verifica la conexión con SAP Business One                                                               |
| 3   | —                                                                    | El sistema mapea la cuenta bancaria con la cuenta contable correspondiente en SAP                                  |
| 4   | —                                                                    | El sistema impacta el movimiento en el libro de bancos (asiento contable) de SAP y registra el resultado en MySQL |
| 5   | —                                                                    | El sistema genera un PDF de control y lo envía automáticamente por WhatsApp al Gerente de Finanzas                 |

### Excepciones

| #   | Situación                                            | Respuesta del sistema                                                                          |
| --- | ------------------------------------------------------ | --------------------------------------------------------------------------------------------------- |
| E1  | SAP Business One no responde durante el impacto contable | El sistema cancela el proceso, evita la duplicación de registros e informa el error al Analista Contable |

| Campo       | Detalle                                                                     |
| ----------- | ------------------------------------------------------------------------------ |
| Rendimiento | Alto: elimina la conciliación manual diaria y reduce inconsistencias contables |
| Frecuencia  | Diaria                                                                          |
| Importancia | Alta                                                                            |
| Urgencia    | Alta                                                                            |

## CU-03 — Sincronización de Datos Maestros (CBU)

| Campo            | Detalle                                                                                                                                                                        |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identificador    | CU-03                                                                                                                                                                          |
| Nombre           | Sincronización de Datos Maestros (CBU)                                                                                                                                        |
| Descripción      | El Analista Contable contrasta un padrón bancario externo contra el maestro de socios de negocio de SAP; el sistema clasifica los resultados y permite actualizar masivamente los datos bancarios en SAP. |
| Actores          | Principal: Analista Contable / Secundario: SAP Business One Service Layer                                                                                                     |
| Precondiciones   | Usuario autenticado; padrón bancario externo disponible; conexión activa con SAP                                                                                              |
| Postcondiciones  | Éxito: datos bancarios actualizados en el maestro de socios de negocio de SAP / Fallo: cruce cancelado sin modificar el maestro                                              |

### Secuencia normal

| #   | Acción (actor)                                              | Reacción (sistema)                                                                                          |
| --- | ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| 1   | El Analista Contable carga o selecciona el padrón bancario externo | El sistema obtiene el maestro de socios de negocio desde SAP y contrasta ambos registros                     |
| 2   | El Analista revisa las tres categorías generadas               | El sistema clasifica los resultados en actualizaciones inmediatas, sugerencias por nombre y registros únicos |
| 3   | El Analista confirma la actualización masiva                   | El sistema aplica los cambios en el maestro de SAP y registra el resultado de cada actualización              |

### Excepciones

| #   | Situación                                                | Respuesta del sistema                                                              |
| --- | ----------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| E1  | SAP no responde durante la actualización masiva              | El sistema cancela la operación, no aplica cambios parciales e informa el error         |
| E2  | El padrón bancario contiene registros con CBU inválido        | El sistema excluye esos registros de la comparación y los reporta por separado          |

| Campo       | Detalle                                                                 |
| ----------- | ------------------------------------------------------------------------ |
| Rendimiento | Alto: elimina la actualización manual de CBU uno por uno en SAP        |
| Frecuencia  | Mensual o quincenal (según ciclo de actualización del padrón bancario) |
| Importancia | Alta                                                                      |
| Urgencia    | Media                                                                     |

---

## CU-04 — Autenticación y Auditoría del Sistema

| Campo            | Detalle                                                                                                                                                           |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identificador    | CU-04                                                                                                                                                             |
| Nombre           | Autenticación y Auditoría del Sistema                                                                                                                            |
| Descripción      | El sistema valida el acceso de cualquier usuario contra una lista blanca de cuentas autorizadas y registra en una bitácora centralizada cada operación crítica ejecutada, permitiendo su consulta posterior por el Administrador IT. |
| Actores          | Principal: Todos los usuarios del sistema (transversal) / Secundario: Administrador IT                                                                          |
| Precondiciones   | El usuario posee credenciales corporativas                                                                                                                       |
| Postcondiciones  | Éxito: acceso concedido y toda operación crítica registrada en la bitácora / Fallo: acceso denegado y evento registrado                                          |

### Secuencia normal

| #   | Acción (actor)                                              | Reacción (sistema)                                                                                          |
| --- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| 1   | El usuario intenta acceder al sistema                         | El sistema solicita autenticación                                                                             |
| 2   | —                                                             | El sistema valida las credenciales contra la lista blanca de cuentas autorizadas                              |
| 3   | El usuario ejecuta una operación crítica (TEF, extracto, actualización de maestro) | El sistema registra la operación en la bitácora centralizada con usuario, fecha/hora, resultado y mensaje técnico |
| 4   | El Administrador IT consulta la bitácora                       | El sistema muestra el historial de operaciones registradas                                                    |

### Excepciones

| #   | Situación                                     | Respuesta del sistema                                                     |
| --- | ------------------------------------------------ | ------------------------------------------------------------------------------ |
| E1  | El usuario no figura en la lista blanca            | El sistema deniega el acceso y registra el intento en la bitácora              |
| E2  | El ERP devuelve un error técnico durante una operación | El sistema registra el estado Error junto con el mensaje técnico devuelto |

| Campo       | Detalle                                                                     |
| ----------- | ------------------------------------------------------------------------------ |
| Rendimiento | Alto: evita accesos no autorizados y pérdida de trazabilidad                  |
| Frecuencia  | Continua (se ejecuta en cada acceso y en cada operación crítica del sistema) |
| Importancia | Alta                                                                            |
| Urgencia    | Alta                                                                            |