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

