# Ticket: TK-062

## Título
CAI-DB: Definir Schema `CandidatoIA` **en BBDD Unificada Core AI**

## Descripción
Definir la tabla/colección `CandidatoIA` como parte del **esquema PostgreSQL unificado** de Core AI para almacenar el perfil unificado del candidato. Debe incluir campos como `id` (PK), `email` (Unique Key), `nombre_completo`, `telefono`, `candidaturas_ids` (Array de UUIDs), `fecha_creacion`, `fecha_actualizacion`, y opcionalmente `tags_agregados`, `perfil_enriquecido`.

## User Story Relacionada
US-018: Gestionar Perfil Unificado de Candidato en IA (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe un script de migración de BBDD **único** (o definición de modelo ORM/ODM) que incluye la entidad `CandidatoIA`.
2.  La entidad incluye los campos: `id` (UUID, PK), `email` (String, Unique, Not Null), `nombre_completo` (String, Nullable), `telefono` (String, Nullable), `candidaturas_ids` (Array de UUIDs, Not Null), `fecha_creacion` (Timestamp), `fecha_actualizacion` (Timestamp).
3.  (Opcional Should/Could Have) Se incluyen campos `tags_agregados` (Array String), `perfil_enriquecido` (JSONB/Text).
4.  Se ha aplicado la definición/migración correctamente en el entorno de desarrollo de Core AI.
5.  El índice UNIQUE en `email` está creado.

## Solución Técnica Propuesta (Opcional)
Usar PostgreSQL con tipo `UUID[]` para `candidaturas_ids` y `JSONB` para `perfil_enriquecido`.

## Dependencias Técnicas (Directas)
* Base de Datos PostgreSQL Unificada para Core AI.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-018)

## Estimación Técnica Preliminar
[ 2 ] [horas] - [Definición modelo, script migración/definición unificada, aplicación inicial]

## Asignación Inicial
Equipo Backend (Core AI) / DBA

## Etiquetas
backend, core-ai, database, schema, migration, candidate, profile, **monolith**, **unified-db**

## Comentarios
Estructura central para la vista unificada del candidato en IA, dentro de la BBDD común.

## Enlaces o Referencias
[Modelo de Datos Fase 5], [Documentación ORM/ODM]