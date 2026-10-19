# Integración con Coach / trajectory

Pregunta original: *¿se puede mergear la carrera con el sistema actual?*
**Sí.** No hay que elegir entre los dos sistemas; hay que decidir quién manda
en qué, y en qué momento. Este documento es el diseño, no la implementación.

## Reparto de responsabilidades

| Sistema | Guarda | No guarda |
| --- | --- | --- |
| **CCD** (este repo) | Programa oficial, fichas de materia, material de estudio, prácticas, diseño del laboratorio | Calendario, tareas, seguimiento semanal |
| **Coach / trajectory** | Programa → semestre → tracks → milestones → tareas, con horas objetivo, fechas y consistencia | Contenido de las materias |

La regla: **CCD es la fuente del contenido; Coach es el motor de ejecución.**
El plan de estudios en `docs/programa/plan-de-estudios.json` es la fuente de
verdad de las 37 materias, y de ahí se *deriva* lo que Coach necesite. No se
copian los datos a mano en los dos lados.

## Cómo encaja técnicamente (verificado en el código)

Coach no es una planilla: tiene modelo relacional y curriculum versionado.

- Tablas: `programs` → `semesters` → `tracks` (categoría `english` / `technical`
  / `career` / `networking` / `other`, con `weekly_target_minutes` y mínimo de
  sesiones) → `milestones` → `tasks` (con `target_minutes`, `minimum_minutes` y
  `minimum_action`).
- El currículum se declara en YAML (`curriculum/semester-1.yaml`), se valida
  (`validateCurriculumDocument`), se serializa canónico, se le calcula
  fingerprint y se aplica como **revisión** con procedencia
  (`curriculum_revisions` + `curriculum_item_provenance`).

Eso significa que la carrera entra por la puerta que ya existe: **un programa
nuevo con sus semestres, tracks y milestones**, aplicado como revisión. Sin
escribir SQL a mano, sin duplicar el esquema, con historial de cambios.

Estado actual medido: 1 programa (`ai-infrastructure-architect-program`),
1 semestre (2026-09-25 → 2027-03-11), 4 tracks, 16 h/semana contratadas
(English 480 min, AI Infrastructure 240, Executive Branding 120, US Networking
120), 20 milestones y 31 tareas.

## El solapamiento que hace que esto sume en vez de duplicar

| Track actual de Coach | Materia de la carrera que cubre |
| --- | --- |
| English for AI Architects (480 min/sem) | Inglés I (año 1) e Inglés II (año 2) |
| US Networking (120 min/sem) | Base de Infraestructura de Telecomunicaciones (materia 7) |

Esas dos no se suman: se **reutilizan**. El resto de la carrera (matemática,
SO, programación segura, forense, criptografía, marco legal) sí es contenido
nuevo y necesita tracks propios.

## Conflicto de capacidad (el problema real, y no es técnico)

| Escenario | Horas por semana |
| --- | --- |
| Hoy: programa de AI, autoestudio | 16 |
| Cursada de la carrera (clase ≈ 20 h + estudio 15–25 h) | 35–45 |
| Suma ingenua de ambos | **51–61** |

Sobre eso, el trabajo. La suma no cierra, y ninguna herramienta arregla eso.
La decisión se toma con números en la mano el **2027-02-15** (checkpoint ya
fijado en `docs/preparacion/plan-prep.md`), no ahora en caliente.

Recomendación a evaluar en ese checkpoint:

- **Se mantiene**: English (doble uso) y un track liviano de gestión de la
  carrera (trámites, calendario de parciales, laboratorio).
- **Se pausa**: los tracks técnicos del programa de AI. La carrera *es* el track
  técnico durante los 4 años; el programa de AI se retoma en los recesos.
- **No se pausa**: nada que ya sea requisito de la carrera.

## Fases de materialización

| Fase | Cuándo | Qué | Riesgo |
| --- | --- | --- | --- |
| 0 | ya | CCD con el plan de estudios estructurado. **Cero escrituras en Coach** | nulo |
| 1 | nov 2026, si se adopta el plan de prep | Revisión de currículum que agrega el track `ciberdefensa-prep` (4 h/semana) con un milestone por bloque | medio: escribe en la base de planificación |
| 2 | feb 2027 | Revisión con el semestre de cursada y la decisión de capacidad aplicada | alto: reestructura tracks activos |
| 3 | mar 2027 | Programa `licenciatura-ciberdefensa` como programa principal; AI en pausa | alto |

La fase 1 es un cambio de código en `trajectory` (YAML + validación + aplicar +
verificar conteos), no una edición manual de la base. Va por ODD **en ese repo**,
con su propio feature document y sus commits, porque toca datos persistidos de
tu planificación.

## Verificación previa a la fase 1

Antes de escribir una línea de YAML hay que confirmar dos cosas en
`trajectory`, que el código actual no responde por sí solo:

1. ¿El *apply* admite un **segundo programa** en la misma base, o asume un único
   currículum activo? El esquema lo permite (`programs` con `semesters` por
   `program_id`); la capa de aplicación puede no.
2. ¿Qué pasa con la consistencia semanal y las revisiones cuando hay dos
   programas activos a la vez? `weekly_reviews` y `daily_state` no tienen
   `program_id` en lo que se midió.

Si (1) es "un solo programa", la estrategia cambia: los semestres de la carrera
se agregan al programa existente como continuación, no como programa aparte.

## Lo que queda explícitamente afuera

- No se escribe directo en `coach.db`. Ni por conveniencia ni "porque es un
  solo INSERT".
- No se duplica el plan de estudios en los dos repos: CCD es la fuente.
- No se agrega el calendario de la carrera antes de tener el cronograma oficial
  de cursada (el PDF no lo trae).
