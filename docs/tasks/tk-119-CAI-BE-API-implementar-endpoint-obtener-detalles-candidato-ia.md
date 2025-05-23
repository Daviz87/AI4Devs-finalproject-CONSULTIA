# Ticket: TK-119

## Título
CAI-BE-API: Implementar Endpoint API **Externa** Obtener Detalles CandidatoIA (incl. `candidaturas_ids`)

## Descripción
Crear y exponer un endpoint en la **capa API externa del monolito Core AI** que permita obtener los detalles de un `CandidatoIA` (incluyendo la lista completa de `candidaturas_ids` asociadas del ATS) a partir de un identificador (ej. email). Protegido por autenticación interna.

## User Story Relacionada
US-035: Consultar Historial de Aplicaciones Anteriores del Candidato (requiere esta API)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe un endpoint (ej. `GET /api/v1/ai/candidates/profiles?email={email}`) en Core AI, definido en TK-001.
2.  El endpoint está protegido por autenticación interna.
3.  Valida el parámetro `email`. Devuelve 400 si inválido.
4.  **Invoca el servicio interno** (TK-120) para buscar el `CandidatoIA`.
5.  Si se encuentra, devuelve 200 OK con los detalles del `CandidatoIA`, incluyendo el array `candidaturas_ids`.
6.  Si no se encuentra por email, devuelve 404 Not Found.
7.  Maneja errores de lógica de negocio (500).

## Solución Técnica Propuesta (Opcional)
Usar GET con query parameter para buscar por email.

## Dependencias Técnicas (Directas)
* TK-001 (Definición API Externa)
* TK-120 (Lógica de Negocio Interna Obtener CandidatoIA)
* Autenticación interna.

## Prioridad (Heredada/Ajustada)
Should Have (Heredada de US-035)

## Estimación Técnica Preliminar
[ 2 ] [horas] - [Creación ruta/controlador API externa, validación input, llamada a servicio interno]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, api, rest, candidate, profile, read, **monolith**, **external-api**

## Comentarios
Endpoint externo necesario para que el ATS consulte el perfil unificado y los IDs de historial.

## Enlaces o Referencias
[TK-001 - Especificación API](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)    