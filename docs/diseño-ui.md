# Diseño UI

_Presentar al menos un wireframe por pantalla o módulo relevante._
_Los wireframes en imagen o PDF van en `diagramas/wireframes/`; acá se documenta la justificación de cada uno._

---

## Pantalla / Módulo 1 — Previsualización de planilla con errores (CU-01, paso 3 de 4)

**Wireframe:** [`diagramas/wireframes/cu01-previsualizacion-errores.txt`](../diagramas/wireframes/cu01-previsualizacion-errores.txt)

**Patrones de diseño utilizados:** Stepper / step-by-step, tarjetas de resumen (KPI cards), tabla con filtro y búsqueda, badges de estado (ícono + texto).

**Justificación:** El usuario de esta pantalla es el Personal Administrativo Contable, un
perfil no técnico que trabaja con planillas de hasta cientos de filas y está a punto de
confirmar un archivo que paga proveedores reales. El stepper ubica al usuario dentro de un
flujo con un paso condicional (el mapeo manual de columnas no siempre aparece). Las tarjetas
de resumen evitan que tenga que contar errores a ojo en un lote grande (RF-04, HU-01 criterio
7). La tabla con filtro "solo errores" y buscador permite aislar los problemas sin perder de
vista el lote completo. El estado de cada fila combina ícono y texto, nunca solo color, para
que la alerta sea "clara" (RF-03) también para usuarios con baja visión o daltonismo, y el
motivo del error se muestra en la celda visible (no en un tooltip), para que sea accesible
sin depender del mouse.

**Formulario (si aplica):**
- Cantidad de campos: no es un formulario de carga; es una pantalla de revisión con 2
  controles de filtro (checkbox "solo errores" y buscador) y 2 acciones (`Volver al mapeo`,
  `Confirmar y generar TEF`).
- Flujo: paso 3 de 4 (Carga → Mapeo condicional → Previsualización → Confirmación), según el
  diagrama de actividad de CU-01.
- Validaciones relevantes: cada fila se valida contra CBU (22 dígitos) y CUIT (RF-03); las
  filas inválidas se marcan pero no detienen el proceso general (CU-01, excepción E1) y
  quedan excluidas de la exportación final (RF-04).

### Supuestos tomados en esta pantalla (a validar con el equipo)

1. **Filas inválidas: 0 válidas.** El botón `Confirmar y generar TEF` se deshabilita si el
   lote no tiene ninguna fila válida, para evitar generar un archivo TEF vacío.
2. **La tabla es de solo lectura.** El usuario no corrige errores desde esta pantalla; debe
   corregir la planilla de origen y volver a cargarla. Se decidió así para no mezclar la
   función de "generar TEF" con la de "editar datos maestros", lo cual podría desincronizar
   la planilla con el origen real de los datos.
3. **Paginación en el cliente.** Dado que todavía no existe un RNF de rendimiento que defina
   un volumen límite, se asume paginación/filtrado en el cliente sobre los datos ya cargados.
   Si el volumen de filas crece de forma significativa, esta decisión debería revisarse junto
   con un RNF de tiempo de respuesta.

---

## Pantalla / Módulo 2 — Previsualización y confirmación de movimientos bancarios (CU-02, paso 2 de 3)

**Wireframe:** [`diagramas/wireframes/cu02-previsualizacion-movimientos.txt`](../diagramas/wireframes/cu02-previsualizacion-movimientos.txt)

**Patrones de diseño utilizados:** Tabla con selección múltiple (checkboxes), tarjetas de
resumen (KPI cards), banner de estado (éxito / error) en lugar de modal bloqueante.

**Justificación:** El usuario de esta pantalla es el Analista Contable. A diferencia de la
previsualización de CU-01 (donde solo se excluyen filas inválidas), acá el usuario elige
activamente cuáles movimientos confirmar (slicing, Historia 2.2), por eso la tabla necesita
selección múltiple y no solo badges de validación. El banner de resultado reemplaza a un
modal porque el analista necesita poder seguir viendo cuánto se procesó después de confirmar
(HU-02 criterio 7), algo que un modal bloqueante taparía. Los movimientos marcados como
"posible duplicado" se muestran desmarcados por defecto: es una decisión de seguridad, el
sistema nunca auto-confirma algo que sospecha que ya fue impactado antes.

**Formulario (si aplica):**
- Cantidad de campos: no es un formulario de carga; tiene 1 filtro ("solo posibles
  duplicados"), 2 acciones de selección masiva (`Seleccionar todos`/`Ninguno`), 1 checkbox
  por fila y 2 acciones finales (`Volver`, `Confirmar envío a SAP`).
- Flujo: paso 2 de 3 (Selección de sociedad/cuenta → Previsualización y selección →
  Resultado), según el diagrama de actividad de CU-02. El resultado se muestra como banner
  dentro de la misma pantalla (éxito) o la reemplaza (corte de conexión con SAP, RF-09).
- Validaciones relevantes: `Confirmar envío a SAP` se deshabilita con 0 movimientos
  seleccionados; la confirmación es atómica (todo o nada) — si se pierde la conexión con SAP
  a mitad del envío, no queda ningún movimiento del lote seleccionado impactado, para
  cumplir RF-09 también cuando la confirmación es parcial.

### Decisiones y pendientes de esta pantalla

1. **Confirmado — detección de duplicados:** se agregó el campo `numero_operacion_bancaria`
   a `MovimientoBancario` en el modelo ER (ver Decisión 5 en
   [er-modelo.md](er-modelo.md)), porque sin un identificador propio del movimiento no había
   forma de calcular la columna "posible duplicado" de este wireframe.
2. **Confirmado — atomicidad de la selección parcial:** la confirmación de los movimientos
   seleccionados es todo o nada. Si la conexión con SAP se corta a mitad de la operación, se
   revierte/cancela el lote completo de seleccionados, igual que se decidió para CU-01.
3. **Pendiente:** qué pasa con un movimiento deseleccionado por "posible duplicado" — si
   queda disponible para la próxima sincronización o se descarta para siempre. Todavía no
   está definido con el negocio.

---

## Pantalla / Módulo 3 — Revisión de categorías del cruce de CBU y confirmación (CU-03)

**Wireframe:** [`diagramas/wireframes/cu03-revision-categorias-cbu.txt`](../diagramas/wireframes/cu03-revision-categorias-cbu.txt)

**Patrones de diseño utilizados:** Tabs para categorías mutuamente excluyentes, tarjetas de
resumen (KPI cards), tabla con selección múltiple, banner de estado (éxito / error).

**Justificación:** RF-11 clasifica el cruce en tres categorías y HU-03 (criterio 3) exige
presentarlas agrupadas antes de aplicar cualquier cambio en SAP — un patrón de tabs resuelve
esto mejor que una sola tabla de 400+ filas con una columna "categoría", porque el usuario
(Personal Administrativo Contable) puede enfocarse en un grupo de riesgo a la vez. Dentro de
cada tab, la selección por fila varía según el riesgo de la categoría: "Actualización
inmediata" (coincidencia exacta de CUIT) viene pre-seleccionada porque el riesgo de error es
bajo; "Sugerencias por nombre" (coincidencia parcial) viene sin seleccionar, porque aplicar
una sugerencia equivocada significa pagarle a la cuenta incorrecta de un proveedor y requiere
juicio humano explícito. "Registros únicos" y "Excluidos" son solo informativos: no hay match
en SAP para aplicar un cambio automático.

**Formulario (si aplica):**
- Cantidad de campos: no es un formulario de carga; tiene 4 tabs, 1 checkbox por fila en las
  2 tabs accionables, y 2 acciones finales (`Volver`, `Confirmar actualización`).
- Flujo: una sola pantalla con navegación por tabs (no es step-by-step), según CU-03 y su
  diagrama de actividad: contrastar → clasificar → revisar → confirmar.
- Validaciones relevantes: `Confirmar actualización` actúa solo sobre las filas
  seleccionadas (de cualquier tab accionable); si se pierde la conexión con SAP durante la
  actualización masiva, no se aplica ningún cambio parcial (CU-03, excepción E1), igual
  criterio de atomicidad que en CU-01 y CU-02.

### Decisiones y mejoras futuras de esta pantalla

1. **Confirmado — pre-selección por riesgo:** "Actualización inmediata" pre-tildada,
   "Sugerencias por nombre" sin tildar por defecto.
2. **Mejora futura (fuera de alcance por ahora):** mostrar un porcentaje de similitud en
   "Sugerencias por nombre" para ayudar a priorizar la revisión. Ningún RF lo exige
   actualmente; se deja marcado para una futura iteración en vez de bloquear esta pantalla.

---

## Pantalla / Módulo 4 — Login y consulta de bitácora (CU-04)

**Wireframe:** [`diagramas/wireframes/cu04-login-bitacora.txt`](../diagramas/wireframes/cu04-login-bitacora.txt)

**Patrones de diseño utilizados:** Formulario simple (login), vistas separadas por pestaña
para historiales de distinto origen, tabla con filtros (rango de fechas, tipo, resultado).

**Justificación:** El login es un formulario mínimo (RF-13): usuario corporativo y
contraseña. El mensaje de error ante credenciales inválidas es **genérico** — no distingue
si el usuario no está en la lista blanca o si la contraseña es incorrecta — para evitar que
alguien infiera qué cuentas corporativas existen probando usuarios (enumeración de cuentas,
un riesgo de seguridad real en un sistema que mueve transferencias bancarias).

Para la consulta de bitácora (Administrador de Infraestructura / Analista IT), se evaluó una
vista unificada que mezclara `BitacoraAuditoria` (TEF/Extracto) y `ActualizacionMaestroCBU`
(CBU) en una sola tabla, pero se descartó: son dos entidades con columnas distintas (la
bitácora tiene `notificado`; las actualizaciones de CBU tienen `mensaje_tecnico` por socio) y
unificarlas requeriría una consulta combinada (`UNION`) con columnas que no siempre
coinciden. Se optó por **dos vistas separadas** (una pestaña por tipo), cada una mostrando
exactamente las columnas de su propia entidad, más simple de construir y igual de útil para
auditar.

**Formulario (si aplica):**
- Cantidad de campos (login): 2 (usuario, contraseña) + 1 acción (`Ingresar`).
- Flujo: todo en una sola pantalla para cada caso (login; consulta con pestañas).
- Validaciones relevantes: el login rechaza cualquier cuenta fuera de la lista blanca
  (RF-13) sin revelar el motivo específico; cada intento fallido queda registrado en la
  bitácora (CU-04, excepción E1).

---

## Consideraciones de accesibilidad

_Al menos una consideración concreta, relacionada con el sistema y sus usuarios reales
(no una mención genérica de "cumple con WCAG"). Ejemplos: contraste para usuarios con
baja visión, tamaño de tap targets para uso móvil, navegación por teclado, textos
alternativos en ícono-only buttons._

- En la previsualización de planilla (CU-01), el estado de cada fila combina ícono y texto
  del motivo del error (ej. "⚠️ CBU inválido (21 dígitos)"), nunca solo color rojo/verde, para
  que el Personal Administrativo Contable con daltonismo o baja visión pueda identificar y
  entender el error sin depender del color ni de un tooltip que requiera mouse.
- En la previsualización de movimientos (CU-02), "Seleccionar todos"/"Ninguno" existen como
  botones explícitos y no solo como un checkbox ambiguo en el encabezado de la tabla, para
  que el Analista Contable pueda operar la selección masiva navegando por teclado.

