# Ticket: TK-135

## Título
CAI-DB: Definir Schema `RegistroFeedbackIA` **en BBDD Unificada Core AI**

## Descripción
Definir la tabla/colección `RegistroFeedbackIA` como parte del **esquema PostgreSQL unificado** de Core AI. Debe incluir campos para identificar la evaluación asociada (`evaluacion_ia_id`), el usuario que dio el feedback (`usuario_ats_id`), el tipo de feedback (`tipo_feedback`), los datos específicos del feedback (`datos_feedback`), y la fecha (`fecha_feedback`).

## User Story Relacionada
US-039: Recibir y Almacenar Feedback de Usuario (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe un script de migración de BBDD **único** (o definición ORM/ODM) que incluye la entidad `RegistroFeedbackIA`.
2.  La entidad incluye campos como: `id` (PK, UUID), `evaluacion_ia_id` (FK/UUID, IDX), `candidatura_ats_id` (UUID, IDX, para contexto), `usuario_ats_id` (UUID), `tipo_feedback` (String/Enum, Not Null), `datos_feedback` (JSONB/Text, Nullable), `fecha_feedback` (Timestamp, Not Null).
3.  Se definen los posibles valores para `tipo_feedback`.
4.  El campo `datos_feedback` es flexible (JSONB/TEXT) para acomodar diferentes tipos de feedback.
5.  La definición se ha aplicado en el entorno de desarrollo de Core AI.
6.  Se consideran índices necesarios (ej. en `evaluacion_ia_id`).

## Solución Técnica Propuesta (Opcional)
Usar PostgreSQL con JSONB para `datos_feedback`.

## Dependencias Técnicas (Directas)
* Base de Datos PostgreSQL Unificada para Core AI.
* Definición de los tipos de feedback a almacenar.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-039)

## Estimación Técnica Preliminar
[ 2 ] [horas] - [Definición modelo/tabla, script migración/definición unificada, aplicación inicial]

## Asignación Inicial
Equipo Backend (Core AI) / DBA

## Etiquetas
backend, core-ai, database, schema, migration, feedback, learning, **monolith**, **unified-db**

## Comentarios
Define dónde y cómo se guarda el feedback en la BBDD común.

## Enlaces o Referencias
[Modelo de Datos Fase 5], [Documentación ORM/ODM]