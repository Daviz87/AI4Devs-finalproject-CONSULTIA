# Ticket: TK-070

## Título
CAI-BE: Implementar Almacenamiento de Datos Parseados del CV **en BBDD Unificada**

## Descripción
Desarrollar la lógica dentro del **módulo de Evaluación del monolito Core AI** para tomar el objeto estructurado con los datos parseados del CV (generado en TK-069) y almacenarlo de forma persistente como parte de la entidad `EvaluacionCandidatoIA` asociada a la evaluación en curso, en la **BBDD unificada**.

## User Story Relacionada
US-020: Extraer Datos Estructurados del CV (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe una función/método `saveParsedData(evaluacionId, parsedData)` que recibe el ID de la evaluación y el objeto JSON con los datos parseados.
2.  Busca la entidad `EvaluacionCandidatoIA` correspondiente a `evaluacionId` **en la BBDD unificada**.
3.  Actualiza un campo específico en `EvaluacionCandidatoIA` (ej. `datos_extraidos_cv` de tipo JSONB) con el `parsedData` recibido.
4.  Guarda (UPDATE) la entidad `EvaluacionCandidatoIA` en la **BBDD unificada**.
5.  Maneja errores de base de datos.

## Solución Técnica Propuesta (Opcional)
Usar el ORM (Spring Data JPA) para actualizar el campo JSONB en la entidad `EvaluacionCandidatoIA` en la BBDD PostgreSQL unificada.

## Dependencias Técnicas (Directas)
* TK-069 (Provee los datos parseados).
* Lógica de creación/obtención de `EvaluacionCandidatoIA`.
* Esquema BBDD Core AI **unificado** con entidad `EvaluacionCandidatoIA` y campo `datos_extraidos_cv`.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-020)

## Estimación Técnica Preliminar
[ 2 ] [horas] - [Implementación lógica de actualización BBDD unificada, manejo JSONB]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, database, save, parsing, cv, json, **monolith**, **unified-db**

## Comentarios
Requiere definición del esquema unificado de `EvaluacionCandidatoIA`.

## Enlaces o Referencias
[Modelo de Datos Fase 5], [Documentación ORM/ODM sobre JSON/Document]