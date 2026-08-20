# Requisitos del sistema

## Descripción del sistema

_El Portal Financiero es una plataforma web corporativo diseñada para centralizar, automatizar y auditar las operaciones del departamento de tesorería y contabilidad del Holding. El sistema resuelve la desconexión crítica existente entre los canales bancarios tradicionales (Interbanking) y el entorno contable del ERP corporativo (SAP Business One). Mediante esta integración, la plataforma elimina los errores de transcripción manual de datos, reduce las demoras en la conciliación de extractos diarios y transferencias, y mitiga el riesgo transaccional al proporcionar un entorno unificado para validar lotes de pagos masivos antes de su procesamiento bancario definitivo._

## Requisitos funcionales

### Módulo 1 — [Generación de Transferencias Masivas (TEF)]

| ID | Requisito |
|----|-----------|
| RF-01 | El sistema debe permitir cargar planillas de pago internas en formatos .xls, .xlsx, o .csv.|
| RF-02 | El sistema debe auto-detectar las columnas necesarias en el archivo cargado mediante búsqueda difusa (fuzzy matching) para CBU, Importe, Nombre y CUIT del beneficiario.|
| RF-03 | El sistema debe desplegar una interfaz de previsualización que exponga alertas visuales claras si detecta fallas en los datos de las filas cargadas.|
| RF-04 | El sistema debe excluir automáticamente de la exportación final todas las filas que presenten errores validados en la previsualización.|
| RF-05 | El sistema debe compilar los datos válidos y generar un archivo de texto de ancho fijo (240 caracteres) estructurado bajo las normativas del estándar de Interbanking.|

### Módulo 2 — [Sincronización de Extractos Bancarios]

| ID | Requisito |
|----|-----------|
| RF-06 | El sistema debe conectarse a Interbanking de forma segura para descargar los movimientos diarios de una cuenta bancaria previamente mapeada. |
| RF-07 | El sistema debe proveer una interfaz para previsualizar los movimientos bancarios descargados antes de autorizar su envío al ERP. |
| RF-08 | El sistema debe impactar automáticamente los movimientos confirmados en el libro de bancos de SAP Business One en sus respectivas columnas de débito o crédito. |
| RF-09 | El sistema debe interrumpir el proceso de sincronización y anular el impacto si se detecta una pérdida de conexión con el ERP para evitar asientos duplicados parciales. |

### Módulo 3 — [Sincronización de Datos Maestros (CBU)] 

| ID | Requisito |
|----|-----------|
| RF-10 | El sistema debe contrastar la información de un padrón bancario externo contra los registros del maestro de socios de negocio de SAP. |
| RF-11 | El sistema debe categorizar los resultados del cruce de datos en tres grupos: actualizaciones inmediatas, sugerencias por nombre y registros únicos. |
| RF-12 | El sistema debe permitir la actualización masiva de los datos bancarios en el ERP de SAP desde la interfaz con un solo clic. |

### Módulo 4 — [Seguridad y Auditoría] 

| ID | Requisito |
|----|-----------|
| RF-13 | El sistema debe exigir autenticación obligatoria y validar el acceso contrastando el usuario con una lista blanca explícita de cuentas autorizadas. |
| RF-14 | El sistema debe registrar una bitácora centralizada detallada de auditoría en la base de datos por cada archivo TEF generado y cada impacto de extractos procesado. |
| RF-15 | El sistema debe guardar un registro del estado (Success/Error) y el mensaje técnico devuelto por el ERP por cada actualización de proveedor intentada en el maestro de SAP. |

## Requisitos no funcionales

### Rendimiento y disponibilidad

### RNF-01

**Procesamiento asíncrono de tareas pesadas.**
El procesamiento de generación de reportes y exportación de PDFs debe ocurrir en un proceso separado (CLI Background Process). El portal web debe liberar la conexión del navegador del usuario en menos de 100 milisegundos tras enviar la confirmación.

### RNF-02

**Notificaciones automáticas vía WhatsApp.**
Al completarse un impacto contable en SAP o la generación de un archivo TEF, el portal despachará de forma automática alertas en tiempo real al teléfono del Gerente de Finanzas, adjuntando el documento PDF de control.

### RNF-03

**Arquitectura modular desarrollada en PHP 8.3.**
La aplicación debe ser desarrollada de forma modular con PHP 8.3 puro, sin depender de frameworks monolíticos pesados, garantizando un despliegue inmediato en servidores Linux convencionales VPS.

### Seguridad y usabilidad

### RNF-04

**Sistema centralizado de logs.**
Todo error del sistema, fallo de las APIs o caída de la conexión de red del ERP debe ser capturado de forma obligatoria en un archivo físico local centralizado en la ruta storage/logs/worker.log para la auditoría de IT.
