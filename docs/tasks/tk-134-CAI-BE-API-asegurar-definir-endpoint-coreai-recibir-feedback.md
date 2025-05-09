# Ticket: TK-134

## Título
CAI-BE-API: Definir Endpoint API **Externa** Core AI para Recibir Feedback

## Descripción
Definir formalmente en el contrato API **externo** (TK-001) el endpoint en el **monolito Core AI** que recibirá el feedback enviado por el ATS MVP (llamado desde TK-132). La especificación debe incluir ruta, método (POST), formato del cuerpo esperado (`evaluation_ia_id`, `usuario_ats_id`, `tipo_feedback`, `datos_feedback`) y respuestas.

## User Story Relacionada
US-038: Enviar Feedback Capturado a Core AI (depende de la existencia de este endpoint) y US-039 (implementa el endpoint).

## Criterios de Aceptación Técnicos (Verificables)
1.  El archivo `openapi.yaml` (TK-001) incluye la definición del endpoint **externo** para recibir feedback (ej. `POST /api/v1/ai/feedback`).
2.  La definición especifica el método POST.
3.  La definición especifica el esquema JSON esperado para el cuerpo de la petición, incluyendo los campos requeridos (`evaluation_ia_id`, `usuario_ats_id`, `tipo_feedback`) y opcionales (`datos_feedback`).
4.  La definición especifica los códigos de respuesta esperados (ej. 200 OK/202 Accepted, 400 Bad Request, 500 Internal Server Error).
5.  La definición incluye los requerimientos de seguridad/autenticación interna.

## Solución Técnica Propuesta (Opcional)
Asegurar que TK-001 contenga esta definición de endpoint externo.

## Dependencias Técnicas (Directas)
* TK-001 (Contrato API Externa)
* Decisiones de diseño sobre la estructura de datos del feedback.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-038 y US-039)

## Estimación Técnica Preliminar
[ 1 ] [hora] - [Definición de un endpoint específico en OpenAPI externa]

## Asignación Inicial
Equipo Arquitectura / Tech Lead

## Etiquetas
api, contract, openapi, core-ai, feedback, definition, **monolith**, **external-api**

## Comentarios
Tarea de definición/documentación de la API externa. La implementación real del lado de Core AI es US-039.

## Enlaces o Referencias
[TK-001 - Especificación API](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)