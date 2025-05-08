# Ticket: TK-073

## Título
CAI-BE: Implementar Almacenamiento de Scores Calculados **en BBDD Unificada**

## Descripción
Desarrollar la lógica dentro del **módulo de Evaluación del monolito Core AI** para tomar los scores calculados por el algoritmo (TK-072) y almacenarlos de forma persistente en la entidad `EvaluacionCandidatoIA` asociada a la evaluación en curso, **en la BBDD unificada**.

## User Story Relacionada
US-021: Calcular Score de Idoneidad Candidato vs Vacante (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe una función/método `saveScoreData(evaluacionId, scoreData)` que recibe el ID de la evaluación y el objeto con los scores (`valor_general`, `scores_parciales` opcional).
2.  Busca la entidad `EvaluacionCandidatoIA` correspondiente a `evaluacionId` **en la BBDD unificada**.
3.  Actualiza los campos correspondientes en `EvaluacionCandidatoIA` (ej. un subdocumento/JSON `score` con los valores calculados).
4.  Guarda (UPDATE) la entidad `EvaluacionCandidatoIA` en la **BBDD unificada**.
5.  Maneja errores de base de datos.

## Solución Técnica Propuesta (Opcional)
Usar el ORM (Spring Data JPA) para actualizar los campos de score en la entidad `EvaluacionCandidatoIA` en la BBDD PostgreSQL unificada.

## Dependencias Técnicas (Directas)
* TK-072 (Provee los scores calculados).
* Lógica de creación/obtención de `EvaluacionCandidatoIA`.
* Esquema BBDD Core AI **unificado** con entidad `EvaluacionCandidatoIA` y campos/estructura para almacenar el score.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-021)

## Estimación Técnica Preliminar
[ 2 ] [horas] - [Implementación lógica de actualización BBDD unificada para los campos de score]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, database, save, evaluation, scoring, **monolith**, **unified-db**

## Comentarios
Requiere definición del esquema unificado de `EvaluacionCandidatoIA` para el score.

## Enlaces o Referencias
[Modelo de Datos Fase 5], [Documentación ORM/ODM]