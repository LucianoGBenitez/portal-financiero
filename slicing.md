# Ejercicio: partir una épica en slices verticales

> **Ejemplo dejado por el docente.** Esta versión reemplaza la épica genérica de la
> billetera por una épica tomada de **su propia HU-01** (generación del archivo TEF). La
> Parte A está resuelta como ejemplo; lo que les toca a ustedes está marcado con
> **"Ahora ustedes"**.

## La épica

> Como personal administrativo contable, quiero generar el archivo de transferencias masivas
> para Interbanking a partir de la planilla de pagos interna, para pagarles a los proveedores
> del Holding sin transcribir CBU e importes a mano.

_Sale de: HU-01, CU-01, RF-01 a RF-05, RF-14 y RNF-02._

### ¿Por qué es una épica y no una historia?

En la validación INVEST de la HU-01 marcaron **"Pequeña: Sí"**. Pero esa historia abarca
cinco requisitos, tres formatos de archivo, detección automática de columnas, validaciones,
exportación en formato bancario, bitácora y notificación. No se puede estimar de una sola
vez ni terminar en una iteración: es una épica.

### Lo que ya está decidido (lo dicen sus propios documentos)

- Se aceptan planillas `.xls`, `.xlsx` y `.csv` (RF-01).
- Las columnas de CBU, importe, nombre y CUIT se detectan automáticamente (RF-02); si no se
  detectan, el usuario las mapea a mano (CU-01, E2).
- Se valida que el CBU tenga 22 dígitos, el formato del CUIT y los campos obligatorios
  (HU-01, criterio 3).
- Las filas con error se excluyen sin frenar el resto del lote (RF-04, CU-01 E1).
- El archivo sale en ancho fijo de 240 caracteres, con cabecera (línea U) y detalle (línea M)
  (RF-05).
- Cada archivo generado queda en la bitácora (RF-14) y se avisa por WhatsApp a la Jefatura
  de Administración con un PDF de control (RNF-02).

### Lo que nadie decidió todavía

- **¿Quién sube el archivo a Interbanking?** El CU-01 dice que el usuario lo descarga, pero
  la "Interbanking API Gateway" figura como actor secundario. ¿Lo envía el sistema o lo sube
  una persona?
- **¿Alcanza con que el CBU tenga formato válido?** Un CBU puede tener 22 dígitos y no ser
  del proveedor. ¿Se cruza con el maestro de SAP (Módulo 3)?
- **¿Qué pasa con las filas excluidas?** ¿Se pierden o quedan pendientes para el lote
  siguiente? ¿Quién se entera de que ese proveedor no cobró?
- **¿Genera y aprueba la misma persona?** En tesorería es común que una persona arme el lote
  y otra lo autorice.
- **¿Hay un tope?** Por transferencia, por lote o por día.

---

## Parte A — Historias verticales (ejemplo resuelto)

_Vertical significa que cada historia, sola, entrega algo usable de punta a punta. Fíjense
que la Historia 1 es la más chica que funciona de verdad: un archivo simple, sin errores, que
ya produce un TEF descargable. Cada historia siguiente le suma una capacidad a algo que ya
anda._

### Historia 1 — Generar el TEF desde un CSV simple

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero cargar una planilla `.csv` con las columnas CBU, Importe, Nombre y CUIT y descargar el archivo TEF, para dejar de transcribir los pagos a mano en Interbanking. |
| Requisitos | RF-01 (solo `.csv`), RF-05 |

**Criterios de aceptación**

1. Dada una planilla `.csv` con esas cuatro columnas y todas las filas correctas, el sistema
   genera un archivo de ancho fijo de 240 caracteres por registro, con la línea U de
   cabecera y una línea M por cada pago.
2. La suma de los importes del archivo coincide con la suma de los importes de la planilla.

---

### Historia 2 — Marcar y excluir las filas con errores

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero ver qué filas de la planilla tienen errores y por qué, para generar el TEF solo con los pagos válidos sin frenar todo el lote. |
| Requisitos | RF-03, RF-04 |

**Criterios de aceptación**

1. Una fila con CBU distinto de 22 dígitos, CUIT con formato inválido o algún campo
   obligatorio vacío aparece marcada en la previsualización, con el motivo del error.
2. El archivo generado incluye todas las filas válidas y ninguna de las marcadas.

---

### Historia 3 — Aceptar planillas de Excel

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero cargar la planilla directamente en `.xlsx` o `.xls`, para no tener que convertirla a `.csv` antes de cada pago. |
| Requisitos | RF-01 |

**Criterios de aceptación**

1. Una planilla `.xlsx` y una `.xls` con los mismos datos que un `.csv` generan exactamente
   el mismo archivo TEF.
2. Un archivo con otra extensión se rechaza con un mensaje que indica los formatos aceptados.

---

### Historia 4 — Reconocer columnas con otros nombres

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero que el sistema reconozca las columnas aunque en la planilla se llamen distinto ("Nro. CBU", "Monto", "Razón social"), para poder usar las planillas tal como las arma cada área. |
| Requisitos | RF-02, CU-01 E2 |

**Criterios de aceptación**

1. Si las columnas tienen nombres parecidos a CBU, Importe, Nombre y CUIT, el sistema las
   asocia solo y muestra qué columna usó para cada dato.
2. Si no logra reconocer alguna, le pide al usuario que elija la columna antes de continuar.

---

### Historia 5 — Resumen del lote y registro en la bitácora

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero ver un resumen de cada lote generado y que quede registrado, para poder controlar qué se pagó y responder una auditoría. |
| Requisitos | RF-14, HU-01 criterio 7 |

**Criterios de aceptación**

1. Al generar el archivo, el sistema muestra la cantidad de filas válidas, la cantidad de
   excluidas y el importe total.
2. Cada archivo generado queda en la bitácora con fecha, hora, usuario, cantidad de pagos e
   importe total.

---

### Historia 6 — Aviso a la Jefatura de Administración

| Campo | Detalle |
|-------|---------|
| Historia | Como Jefa de Administración, quiero recibir un aviso por WhatsApp con el PDF de control cada vez que se genera un lote, para enterarme de los pagos sin tener que entrar al portal. |
| Requisitos | RNF-02 |

**Criterios de aceptación**

1. Al generarse un archivo TEF, llega un mensaje de WhatsApp con el importe total, la
   cantidad de pagos y el PDF de control adjunto.
2. Si el envío del mensaje falla, el archivo igual queda generado y el fallo queda
   registrado (el aviso no puede frenar un pago).

---

### Historia 7 — Evitar generar dos veces el mismo lote

| Campo | Detalle |
|-------|---------|
| Historia | Como personal administrativo contable, quiero que el sistema me avise si estoy por generar un lote que ya generé, para no pagarle dos veces al mismo proveedor. |
| Requisitos | Ninguno todavía: sale de la Parte B, y depende de lo que decida el negocio |

**Criterios de aceptación**

1. Si se carga una planilla con los mismos pagos que un lote ya generado, el sistema lo
   advierte antes de generar el archivo.
2. _A definir con el negocio:_ ¿se bloquea, o se permite con una confirmación explícita?

---

### Lo que NO es vertical

Estas partes son necesarias, pero ninguna entrega algo usable por sí sola, así que no son
historias:

- "Implementar la búsqueda difusa de columnas" (queda adentro de la Historia 4).
- "Diseñar la pantalla de previsualización" (queda adentro de la Historia 2).
- "Programar el generador de ancho fijo" (queda adentro de la Historia 1).

---

## Parte B — Los caminos que no salen bien

_Elijan UNA de las historias del ejemplo de arriba. Las últimas tres preguntas son las
importantes: para cada una, indiquen qué debería hacer el sistema y quién tendría que
decidirlo. Las preguntas están adaptadas a su dominio (en un portal de tesorería no hay
"saldo insuficiente", pero sí hay pagos duplicados)._

**Historia elegida:** [Nombre / ID]

| Pregunta | Qué hace el sistema | Quién decide (analista / negocio / técnica) |
|----------|----------------------|-----------------------------------------------|
| ¿Qué pasa si una fila tiene un CBU con menos de 22 dígitos? | | |
| ¿Qué pasa si el CBU tiene formato válido pero no corresponde al CUIT del proveedor que figura en SAP? | | |
| ¿Qué pasa si el mismo lote se genera dos veces y los dos archivos llegan a Interbanking? | | |
| ¿Qué pasa si el sistema falla a mitad de armar el archivo? ¿Queda un archivo parcial que se puede descargar? | | |
| ¿Qué pasa si se corta la sesión justo después de confirmar, y el usuario no sabe si el lote se generó y lo vuelve a generar? | | |

_La primera ya la respondieron en su CU-01 (E1): parte del trabajo ya está hecho. Las
difíciles son las otras._

---

## Ahora ustedes — ¿Sus otras historias también son épicas?

_Hagan con cada una la misma pregunta que hicimos con la HU-01: ¿se puede terminar en una
iteración, o esconde varias historias adentro? Si esconde varias, pártanla en historias
verticales como en el ejemplo. Si creen que no hace falta partirla, justifiquen por qué._

### HU-02 — Sincronización de extractos bancarios

- ¿Es una épica? [Sí / No, y por qué]
- Historias verticales:

### HU-03 — Sincronización de datos maestros (CBU)

- ¿Es una épica? [Sí / No, y por qué]
- Historias verticales:

### HU-04 — Autenticación y auditoría del sistema

- ¿Es una épica? [Sí / No, y por qué]
- Historias verticales:

---

## Parte C — Defensa

_Se hace oral, en el plenario. No se documenta en este archivo._
