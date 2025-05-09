# Ticket: TK-079

## Título
CAI-BE: Implementar Almacenamiento Resumen Generado **en BBDD Unificada**

## Descripción
Desarrollar la lógica dentro del **módulo de Evaluación del monolito Core AI** para tomar el texto del resumen generado (por TK-078) y almacenarlo de forma persistente en la entidad `EvaluacionCandidatoIA` asociada, **en la BBDD unificada**.

## User Story Relacionada
US-025: Generar Resumen Ejecutivo del Candidato vs Vacante (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe una función/método `saveSummary(evaluacionId, summaryText)` que recibe el ID de la evaluación y el texto del resumen.
2.  Busca la entidad `EvaluacionCandidatoIA` correspondiente a `evaluacionId` **en la BBDD unificada**.
3.  Actualiza el campo `resumen_generado` (Text) en `EvaluacionCandidatoIA`.
4.  Guarda (UPDATE) la entidad en la **BBDD unificada**.
5.  Maneja errores de base de datos.

## Solución Técnica Propuesta (Opcional)
Usar el ORM para actualizar el campo de texto en `EvaluacionCandidatoIA`.

## Dependencias Técnicas (Directas)
* TK-078 (Provee el texto del resumen).
* Lógica de creación/obtención de `EvaluacionCandidatoIA`.
* Esquema BBDD Core AI **unificado** con entidad `EvaluacionCandidatoIA` y campo `resumen_generado`.

## Prioridad (Heredada/Ajustada)
Should Have (Heredada de US-025)

## Estimación Técnica Preliminar
[ 2 ] [horas] - [Implementación lógica de actualización BBDD unificada para campo resumen]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, database, save, evaluation, summary, **monolith**, **unified-db**

## Comentarios
Requiere definición del esquema unificado de `EvaluacionCandidatoIA`.

## Enlaces o Referencias
[Modelo de Datos Fase 5], [Documentación ORM]