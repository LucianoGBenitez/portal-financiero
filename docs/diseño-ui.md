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

## Pantalla / Módulo 2 — [Nombre]

**Wireframe:** `diagramas/wireframes/[archivo]`

**Patrones de diseño utilizados:**

**Justificación:**

**Formulario (si aplica):**
- Cantidad de campos:
- Flujo (todo en una pantalla / por pasos):
- Validaciones relevantes:

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

