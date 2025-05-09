# Ticket: TK-065

## Título
CAI-BE: Implementar Endpoint API **Externa** Core AI para Recibir Solicitud de Evaluación

## Descripción
Crear y exponer un endpoint en la **capa API externa del monolito Core AI** que reciba la solicitud del ATS MVP (enviada desde TK-045) para iniciar la evaluación de una nueva candidatura. Debe aceptar los identificadores necesarios (ID Candidatura ATS, ID Vacante ATS, ID/Referencia Archivo CV ATS).

## User Story Relacionada
US-019: Invocar Evaluación IA para Nueva Candidatura

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe un endpoint (ej. `POST /api/v1/ai/evaluations`) en Core AI, definido según TK-001.
2.  El endpoint acepta un cuerpo JSON con los IDs requeridos: `candidatura_ats_id`, `vacante_ats_id`, `archivo_cv_ats_id` (o referencia al archivo).
3.  El endpoint está protegido por el mecanismo de autenticación interna.
4.  Valida que los IDs proporcionados sean válidos (formato correcto, no nulos). Devuelve 400 si falla.
5.  **Invoca el servicio interno** (TK-066) para iniciar el proceso de evaluación.
6.  **Espera y devuelve la respuesta final** de la evaluación (definida en US-024), ya que se asume síncrono para MVP.
7.  Maneja errores de validación o de la lógica de inicio (TK-066) devolviendo códigos de error apropiados (400, 500).

## Solución Técnica Propuesta (Opcional)
Implementar usando el framework backend de Core AI. Definir claramente el contrato de entrada en TK-001.

## Dependencias Técnicas (Directas)
* TK-001 (Definición API Externa)
* TK-066 (Lógica de Negocio Interna que inicia la evaluación)
* Autenticación interna.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-019)

## Estimación Técnica Preliminar
[ 3 ] [horas] - [Creación ruta/controlador API externa, validación input, orquestación llamada a servicio interno]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, api, rest, evaluation, trigger, **monolith**, **external-api**

## Comentarios
Punto de entrada externo en Core AI para todo el flujo de evaluación.

## Enlaces o Referencias
[TK-001 - Especificación API Externa](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)