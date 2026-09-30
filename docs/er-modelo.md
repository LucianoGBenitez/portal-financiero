# Modelo Entidad-Relación

## Diagrama

Código PlantUML disponible en [er.puml](../diagramas/er.puml).
Visualizar en [plantuml.com](https://www.plantuml.com/plantuml/uml/).

## Entidades

| Entidad | Descripción | Relaciones clave |
|---------|-------------|-------------------|
| `Usuario` | Cuenta interna autorizada para operar el portal (RF-13). | 1:N con `LotePago`, `SincronizacionExtracto`, `ActualizacionMaestroCBU` y `BitacoraAuditoria`. |
| `CuentaBancaria` | Cuenta bancaria de una empresa del Holding, mapeada contra su cuenta contable en SAP (RF-06). | 1:N con `SincronizacionExtracto`. |
| `LotePago` | Planilla de pagos cargada por un usuario para generar un archivo TEF (RF-01). | 1:N con `DetallePago`; 1:1 con `ArchivoTEF`. |
| `DetallePago` | Fila individual de un lote de pagos, con su propio resultado de validación (RF-02, RF-03, RF-04). | N:1 con `LotePago`. |
| `ArchivoTEF` | Archivo plano de 240 caracteres generado a partir de los registros válidos de un lote (RF-05). | 1:1 con `LotePago`; 1:1 (opcional) con `BitacoraAuditoria`. |
| `SincronizacionExtracto` | Ejecución de una descarga de movimientos bancarios desde Interbanking para una cuenta (RF-06, RF-09). | N:1 con `CuentaBancaria` y `Usuario`; 1:N con `MovimientoBancario`; 1:1 (opcional) con `BitacoraAuditoria`. |
| `MovimientoBancario` | Movimiento bancario individual descargado y, si se confirma, impactado en el libro de bancos de SAP (RF-07, RF-08). | N:1 con `SincronizacionExtracto`. |
| `SocioNegocio` | Reflejo del maestro de socios de negocio de SAP, usado para contrastar el padrón bancario externo (RF-10). | 1:N con `ActualizacionMaestroCBU`. |
| `ActualizacionMaestroCBU` | Intento individual de actualización del CBU de un socio de negocio, con el resultado devuelto por SAP (RF-11, RF-12, RF-15). | N:1 con `SocioNegocio` y `Usuario`. |
| `BitacoraAuditoria` | Registro centralizado de auditoría por cada archivo TEF generado o cada sincronización de extracto procesada (RF-14). | N:1 con `Usuario`; 1:1 (opcional) con `ArchivoTEF` o con `SincronizacionExtracto`. |

## Descripción de atributos principales

### `Usuario`

- `id_usuario` (PK): identificador interno de la cuenta.
- `nombre`, `email`: datos de identificación de la persona.
- `rol`: perfil simple del usuario (administrativo contable, analista contable, jefatura de administración, admin IT).
- `autorizado`: bandera que implementa la lista blanca exigida por RF-13.
- `fecha_alta`: marca temporal de creación de la cuenta.

### `CuentaBancaria`

- `id_cuenta` (PK): identificador interno de la cuenta bancaria.
- `cbu`, `banco`, `empresa`: datos identificatorios de la cuenta y de a qué empresa del Holding pertenece.
- `cuenta_contable_sap`: cuenta del libro de bancos de SAP donde se impactan sus movimientos (RF-08).

### `LotePago`

- `id_lote` (PK): identificador del lote/planilla cargada.
- `id_usuario_carga` (FK → `Usuario`): quién cargó la planilla.
- `nombre_archivo_original`: nombre del archivo `.xls`/`.xlsx`/`.csv` subido (RF-01).
- `fecha_carga`: marca temporal de la carga.
- `estado_general`: resultado global del lote (procesado / con errores).

### `DetallePago`

- `id_detalle` (PK): identificador de la fila.
- `id_lote` (FK → `LotePago`): lote al que pertenece.
- `cbu`, `cuit`, `nombre_beneficiario`, `importe`: datos detectados por búsqueda difusa (RF-02).
- `estado_validacion`: válido / inválido, resultado de validar CBU y CUIT (RF-03).
- `motivo_error`: detalle del error para la previsualización visual (RF-03), nulo si es válido.

### `ArchivoTEF`

- `id_archivo_tef` (PK): identificador del archivo generado.
- `id_lote` (FK → `LotePago`, única): lote que lo originó.
- `fecha_generacion`: marca temporal de generación.
- `cantidad_registros`: cantidad de registros válidos compilados (RF-04).
- `ruta_archivo`: ubicación del archivo de 240 caracteres (RF-05).

### `SincronizacionExtracto`

- `id_sincronizacion` (PK): identificador de la corrida de sincronización.
- `id_cuenta` (FK → `CuentaBancaria`): cuenta sincronizada.
- `id_usuario` (FK → `Usuario`): quién ejecutó la sincronización.
- `fecha_ejecucion`: marca temporal de la corrida.
- `estado`: completa / interrumpida / error — refleja el corte ante pérdida de conexión (RF-09).
- `cantidad_movimientos_procesados`: cantidad de movimientos descargados en la corrida.

### `MovimientoBancario`

- `id_movimiento` (PK): identificador del movimiento.
- `id_sincronizacion` (FK → `SincronizacionExtracto`): corrida que lo descargó.
- `fecha_movimiento`, `importe`, `tipo` (débito/crédito): datos del movimiento bancario.
- `estado`: descargado / confirmado / impactado / rechazado (RF-07, RF-08).
- `fecha_impacto_sap`: marca temporal del impacto en SAP, nula hasta que se confirma.

### `SocioNegocio`

- `id_socio` (PK): identificador interno, reflejo del socio de negocio de SAP.
- `cuit`, `cbu_actual`, `nombre`: datos del maestro de proveedores (RF-10).
- `fecha_ultima_sincronizacion`: marca temporal del último contraste contra el padrón externo.

### `ActualizacionMaestroCBU`

- `id_actualizacion` (PK): identificador del intento de actualización.
- `id_socio` (FK → `SocioNegocio`): socio actualizado.
- `id_usuario` (FK → `Usuario`): quién ejecutó la actualización masiva (RF-12).
- `categoria`: actualización inmediata / sugerencia por nombre / registro único (RF-11).
- `estado`, `mensaje_tecnico`: resultado (Success/Error) y respuesta técnica de SAP (RF-15).
- `fecha`: marca temporal del intento.

### `BitacoraAuditoria`

- `id_bitacora` (PK): identificador del registro de auditoría.
- `id_usuario` (FK → `Usuario`): quién generó la operación auditada.
- `id_archivo_tef` (FK → `ArchivoTEF`, nulleable) / `id_sincronizacion` (FK → `SincronizacionExtracto`, nulleable): cada fila completa solo una de las dos.
- `tipo_operacion`: TEF / EXTRACTO, indica cuál de las dos FK está completa.
- `resultado`, `detalle`: resultado general y detalle técnico de la operación (RF-14).
- `notificado`, `fecha_notificacion`: si se despachó y cuándo la alerta de WhatsApp asociada (RNF-02).
- `fecha`: marca temporal del registro.

## Decisiones de diseño

### Decisión 1 — Autorización simple en lugar de un modelo de roles granular

Se evaluó crear una entidad `Rol` con permisos diferenciados por módulo, pero se descartó porque RF-13 solo exige "una lista blanca explícita de cuentas autorizadas", sin pedir permisos distintos por funcionalidad. Un campo `rol` simple en `Usuario` (más una bandera `autorizado`) cubre el requisito real sin agregar una tabla y una capa de permisos que ningún RF sustenta.

### Decisión 2 — `DetallePago` desacoplado del maestro `SocioNegocio`

Se consideró vincular cada fila de una planilla de pago contra `SocioNegocio` para reutilizar CBUs ya validados en el maestro de SAP. Se descartó porque el Módulo 1 (RF-01 a RF-05) y el Módulo 3 (RF-10 a RF-12) son procesos independientes en los requisitos: el primero valida el formato de CBU/CUIT de una planilla puntual, el segundo sincroniza un maestro persistente de proveedores. Acoplarlos obligaría a resolver casos no contemplados (ej. un beneficiario de pago que todavía no existe como socio de negocio) sin que ningún requisito lo pida.

### Decisión 3 — `BitacoraAuditoria` con FKs nulleables en lugar de una referencia polimórfica

Para que una misma fila de auditoría pueda representar tanto un archivo TEF como una sincronización de extracto, se evaluó usar una referencia polimórfica genérica (`tipo_operacion` + `id_referencia` apuntando a "la tabla que corresponda"). Se descartó porque ningún motor de base de datos puede crear una Foreign Key real hacia una tabla variable: la integridad referencial quedaría completamente a cargo del código de aplicación, justo en la entidad cuyo propósito es garantizar confiabilidad. Se optó por dos columnas FK nulleables (`id_archivo_tef`, `id_sincronizacion`), donde cada fila completa una sola de las dos y la base de datos valida siempre contra un registro existente. Por el mismo motivo se descartó una tercera FK hacia `ActualizacionMaestroCBU`: RF-14 solo exige bitácora para archivos TEF y extractos, mientras que RF-15 ya exige guardar `estado` y `mensaje_tecnico` directamente en `ActualizacionMaestroCBU`, por lo que una tercera referencia sería redundante.

### Decisión 4 — `SincronizacionExtracto` como entidad de sesión

RF-09 exige poder interrumpir "el proceso de sincronización" ante una pérdida de conexión con el ERP, evitando asientos duplicados parciales. Esa es una propiedad de una corrida completa de descarga, no de un movimiento individual: sin una entidad que agrupe la sesión, no habría dónde registrar que una sincronización quedó interrumpida. Por eso se incorporó `SincronizacionExtracto`, que cumple para el Módulo 2 el mismo rol que `LotePago` cumple para el Módulo 1: agrupar varios `MovimientoBancario` bajo una sola ejecución con estado propio (completa / interrumpida / error).
