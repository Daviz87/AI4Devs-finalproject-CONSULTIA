# Ticket: TK-071

## Título
CAI-BE: Recuperar Inputs Necesarios para Scoring **(Monolito Core AI)**

## Descripción
Implementar la lógica dentro del **módulo de Evaluación del monolito Core AI** para obtener toda la información necesaria antes de ejecutar el algoritmo de scoring (TK-072). Esto incluye: 1) Recuperar los datos estructurados del CV parseado (almacenados por TK-070 en `EvaluacionCandidatoIA` **en la BBDD unificada**). 2) Recuperar los requisitos relevantes de la vacante **invocando al servicio interno del módulo de Generación JD** para obtener la entidad `DescripcionPuestoGenerada` asociada (o leyendo directamente de la BBDD unificada si la modularidad lo permite).

## User Story Relacionada
US-021: Calcular Score de Idoneidad Candidato vs Vacante (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe una función/método que, dado un `evaluacionId`, puede recuperar los `datos_extraidos_cv` de la `EvaluacionCandidatoIA` correspondiente **desde la BBDD unificada**.
2.  Existe una función/método que, dado un `vacante_ats_id`, puede obtener los requisitos de la vacante:
    * **Opción 1 (Modular):** Llama al `GeneracionJDService.getVacancyConfig(vacante_ats_id)` para obtener la `DescripcionPuestoGenerada`.
    * **Opción 2 (Acceso Directo Repositorio):** Accede directamente al `DescPuestoGenRepository` para buscar por `vacante_ats_id`.
3.  Se extraen/interpretan los requisitos clave de la vacante desde `DescripcionPuestoGenerada` (ej. lista de skills, años de experiencia mínima, `evaluacion_corte`, etc.).
4.  Los datos del CV y los requisitos de la vacante están disponibles en un formato utilizable por el algoritmo de scoring (TK-072).
5.  Maneja errores si los datos del CV o los requisitos de la vacante no están disponibles o son incompletos.

## Solución Técnica Propuesta (Opcional)
Implementar como paso previo dentro del flujo de evaluación orquestado por TK-066. Usar ORM/ODM para leer de las entidades en la BBDD unificada y/o inyectar servicios de otros módulos.

## Dependencias Técnicas (Directas)
* TK-070 (Almacenamiento datos parseados CV en BBDD unificada)
* TK-060 (Almacenamiento params IA / `DescripcionPuestoGenerada` en BBDD unificada)
* Esquema BBDD **unificado** `EvaluacionCandidatoIA` y `DescripcionPuestoGenerada`.
* TK-072 (Consume estos inputs).
* (Si Opción 1) `GeneracionJDService` del módulo de Generación JD.

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-021)

## Estimación Técnica Preliminar
[ 3 ] [horas] - [Implementación lógica recuperación datos BBDD unificada y/o llamada a servicio interno, interpretación requisitos]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, logic, service, evaluation, scoring, data-retrieval, **monolith**, **unified-db**

## Comentarios
La interpretación de requisitos sigue siendo clave. La forma de acceder a la config de la vacante (Opción 1 vs 2) depende de cómo se estructure la modularidad interna.