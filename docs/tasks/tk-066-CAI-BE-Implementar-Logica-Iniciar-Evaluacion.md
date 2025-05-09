# Ticket: TK-066

## Título
CAI-BE: Implementar Lógica **Orquestadora** Core AI para Iniciar Proceso de Evaluación

## Descripción
Desarrollar la lógica de negocio inicial dentro del **módulo** de Evaluación del **monolito Core AI** que se activa al recibir una solicitud válida (desde API Externa TK-065). Debe validar la existencia/accesibilidad de los recursos referenciados (ej. ¿puede acceder al CV?) y luego **orquestar las llamadas internas a los servicios/componentes** responsables de los pasos subsiguientes: parsing (TK-067/069), scoring (TK-071/072), etc., hasta obtener la respuesta final (US-024).

## User Story Relacionada
US-019: Invocar Evaluación IA para Nueva Candidatura (y desencadena US-020)

## Criterios de Aceptación Técnicos (Verificables)
1.  La función/método de servicio recibe los IDs validados (`candidatura_ats_id`, `vacante_ats_id`, `archivo_cv_ats_id`/referencia).
2.  Valida que puede acceder al archivo CV usando la referencia proporcionada (invocando TK-067). Si no, lanza error 404/400.
3.  Invoca **directamente el servicio/método interno** responsable del parsing del CV (TK-069), pasando la referencia/contenido del archivo y los IDs relevantes.
4.  Recibe el resultado del parsing.
5.  Invoca **directamente el servicio/método interno** responsable del scoring (TK-072), pasando los datos parseados y los requisitos de la vacante (obtenidos vía TK-071).
6.  Recibe el resultado del scoring.
7.  Invoca **directamente la lógica interna** de comparación (TK-074) y determinación de etapa (TK-075).
8.  Invoca **directamente la lógica interna** (opcional) de generación de resumen (TK-078).
9.  Recibe todos los resultados y los pasa a la lógica de formateo de respuesta (TK-076).
10. Maneja excepciones provenientes de las llamadas internas y las propaga adecuadamente para la respuesta final de la API externa (TK-065).

## Solución Técnica Propuesta (Opcional)
Implementar como un método principal anotado con `@Service` en el módulo de Evaluación, que inyecta (`@Autowired`) las dependencias de otros servicios/componentes internos (ParsingService, ScoringService, etc.).

## Dependencias Técnicas (Directas)
* TK-065 (Endpoint API Externa que invoca esta lógica)
* TK-067 (Lógica Obtención CV)
* TK-069 (Lógica Parsing CV)
* TK-072 (Lógica Scoring)
* TK-074 (Lógica Comparación)
* TK-075 (Lógica Sugerencia Etapa)
* TK-078 (Lógica Generación Resumen - Opcional)
* TK-076 (Lógica Formateo Respuesta Final)

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-019)

## Estimación Técnica Preliminar
[ 1 ] [hora] - *(Estimación reducida)* - [Implementación orquestación de llamadas internas, manejo de excepciones internas. Se elimina complejidad de llamadas API internas]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, logic, service, evaluation, orchestrator, pipeline, **monolith**

## Comentarios
Ticket clave para el flujo de evaluación dentro de Core AI. Ahora simplificado al usar llamadas internas.

## Enlaces o Referencias
[TK-001 - Especificación API Externa](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)