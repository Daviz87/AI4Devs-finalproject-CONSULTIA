# Ticket: TK-063

## Título
CAI-BE: Implementar Endpoint API **Externa** Core AI para Crear/Actualizar `CandidatoIA`

## Descripción
Crear y exponer un endpoint en la **capa API externa del monolito Core AI** que reciba la solicitud del ATS MVP (desencadenada en TK-045) para crear o actualizar el perfil `CandidatoIA`. Debe aceptar el email del candidato y el ID de la nueva candidatura ATS.

## User Story Relacionada
US-018: Gestionar Perfil Unificado de Candidato en IA (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe un endpoint (ej. `POST /api/v1/ai/candidates/profiles`) en Core AI, definido según TK-001.
2.  El endpoint acepta un cuerpo JSON con `{ "email": "...", "candidatura_ats_id": "..." }`.
3.  El endpoint está protegido por el mecanismo de autenticación interna definido.
4.  Valida la entrada (email válido, ID candidatura presente). Devuelve 400 si falla.
5.  **Invoca el servicio interno** (TK-064) para buscar/crear/actualizar el `CandidatoIA`.
6.  Devuelve una respuesta de éxito (ej. 200 OK o 201 Created) o error (500) al ATS MVP.

## Solución Técnica Propuesta (Opcional)
Usar POST o PUT. La lógica de "upsert" estará en el servicio interno (TK-064).

## Dependencias Técnicas (Directas)
* TK-001 (Definición API Externa)
* TK-064 (Lógica de Negocio Interna Upsert CandidatoIA)
* Autenticación interna entre ATS y Core AI.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-018)

## Estimación Técnica Preliminar
[ 3 ] [horas] - [Creación ruta/controlador API externa, validación input, llamada a servicio interno]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, api, rest, candidate, profile, upsert, **monolith**, **external-api**

## Comentarios
Punto de entrada externo para sincronizar perfiles desde ATS.

## Enlaces o Referencias
[TK-001 - Especificación API]