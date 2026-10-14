# Portal Financiero — Grupo X

> Materia: Diseño de Sistemas Web — Analista Funcional de Sistemas  
> Institución: Terciario Urquiza — Rosario  
> Docente: Pedernera Pablo  
> Cuatrimestre: 2.° 2026

## Integrantes

Ver [integrantes.md](integrantes.md)

## Descripción del proyecto

El Portal Financiero es una plataforma web corporativa para centralizar, automatizar y
auditar las operaciones del departamento de tesorería y contabilidad de un Holding. Conecta
el canal bancario (Interbanking) con el ERP corporativo (SAP Business One), cubriendo cuatro
módulos: generación de transferencias masivas (archivo TEF), sincronización de extractos
bancarios con impacto contable automático, sincronización de datos maestros de CBU contra el
maestro de socios de negocio de SAP, y seguridad/auditoría de todas las operaciones.

## Caso de estudio

El comitente es el departamento de tesorería y contabilidad de un Holding que hoy opera con
una desconexión manual entre Interbanking y SAP Business One: los pagos masivos y la
conciliación de extractos se transcriben a mano entre ambos sistemas, lo que genera errores
de carga, demoras en la conciliación diaria y riesgo de duplicar pagos o asientos contables.
El Portal Financiero busca unificar y validar esas operaciones antes de que lleguen al banco
o se impacten en el ERP.

## Entregas

| Entrega | Descripción | Fecha | Estado |
|---------|-------------|-------|--------|
| EP-01 | Presentación preliminar | | |
| EP-02 | | | |
| Final | Versión definitiva | | |

## Estructura del repositorio

```
/
├── README.md
├── integrantes.md
├── RECURSOS.md         ← leer antes de empezar: prerrequisitos, cheatsheet de git, recursos
├── docs/
│   ├── requisitos.md
│   ├── historias-de-usuario.md
│   ├── casos-de-uso.md
│   ├── er-modelo.md
│   ├── diseño-ui.md
│   └── stakeholders.md
├── diagramas/
│   ├── casos-de-uso.puml
│   ├── er.puml
│   └── wireframes/
└── cuestionario/
```

## Instrucciones operativas

- Un integrante del grupo es responsable de subir los cambios al repositorio.
- Completar `integrantes.md` antes de la primera entrega.
- Mantener los archivos en la carpeta correspondiente según la estructura indicada.
- Los diagramas deben entregarse en formato PlantUML (`.puml`). Se pueden visualizar en [plantuml.com](https://www.plantuml.com/plantuml/uml/).
