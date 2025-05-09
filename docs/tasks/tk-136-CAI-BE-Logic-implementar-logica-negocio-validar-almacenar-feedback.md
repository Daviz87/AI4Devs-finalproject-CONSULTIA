# Ticket: TK-136

## Título
CAI-BE-Logic: Implementar Lógica para Validar y Almacenar Feedback Recibido **en BBDD Unificada**

## Descripción
Desarrollar la lógica en el **módulo de Feedback del monolito Core AI** que recibe los datos del feedback desde la API externa (TK-134), valida la información, crea una instancia de la entidad `RegistroFeedbackIA` y la guarda en la **base de datos PostgreSQL unificada** (usando TK-135).

## User Story Relacionada
US-039: Recibir y Almacenar Feedback de Usuario (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  La función/método de servicio recibe los datos del feedback (`evaluationId`, `userId`, `feedbackType`, `feedbackData`).
2.  Valida que `evaluationId` corresponda a una `EvaluacionCandidatoIA` existente en la BBDD unificada (opcional MVP).
3.  Valida que `feedbackType` sea uno de los valores permitidos.
4.  Crea una instancia de `RegistroFeedbackIA` con los datos recibidos y la fecha actual.
5.  Guarda la nueva entidad en la **BBDD unificada**.
6.  Maneja errores de validación (devolviendo error 400 a la API externa) y errores de base de datos (devolviendo error 500).
7.  Si tiene éxito, devuelve una confirmación a la capa API externa (que responderá a ATS).

## Solución Técnica Propuesta (Opcional)
Implementar en capa de Servicios (`@Service`). Usar ORM (Spring Data JPA) para la operación INSERT en la BBDD unificada.

## Dependencias Técnicas (Directas)
* TK-134 (API externa que invoca esta lógica)
* TK-135 (Esquema BBDD unificado `RegistroFeedbackIA`)
* (Opcional) Acceso a `EvaluacionCandidatoIA` para validación desde BBDD unificada.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-039)

## Estimación Técnica Preliminar
[ 3 ] [horas] - [Implementación lógica de servicio, validaciones básicas, operación INSERT BBDD unificada]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, logic, service, feedback, save, validation, database, **monolith**, **unified-db**

## Comentarios
Implementa el almacenamiento efectivo del feedback en la BBDD común.

## Enlaces o Referencias
[Documentación ORM/ODM]