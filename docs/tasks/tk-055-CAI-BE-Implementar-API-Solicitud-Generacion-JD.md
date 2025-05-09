# Ticket: TK-055

## Título
CAI-BE: Implementar Endpoint API **Externa** Core AI para Solicitud Generación JD

## Descripción
Crear y exponer un endpoint en la **capa API externa del monolito Core AI** que reciba las solicitudes del ATS MVP (iniciadas en TK-047) para generar una JD. Debe aceptar los datos básicos de la vacante como input y **orquestar la llamada al módulo interno** de generación (que incluye TK-056, TK-057, TK-058).

## User Story Relacionada
US-015: Generar Borrador de JD Usando IA (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  Existe un endpoint (ej. `POST /api/v1/ai/generate-jd`) en el monolito Core AI, definido según TK-001.
2.  El endpoint acepta un cuerpo JSON con datos básicos de la vacante (ej. titulo, requisitos_clave).
3.  El endpoint está protegido por el mecanismo de autenticación interna definido.
4.  Valida la entrada básica recibida del ATS MVP. Devuelve 400 si falla.
5.  **Invoca el servicio interno** del módulo de generación de JD (que contiene la lógica de TK-056, TK-057, TK-058).
6.  Devuelve la respuesta al ATS MVP (JD generada o error) con el código de estado apropiado (200 OK, 500 Error, etc.).

## Solución Técnica Propuesta (Opcional)
Implementar usando el framework backend de Core AI (Java/Spring Boot).

## Dependencias Técnicas (Directas)
* TK-001 (Definición API Externa)
* Lógica interna de generación (TK-056, TK-057, TK-058).

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-015)

## Estimación Técnica Preliminar
[ 3 ] [horas] - [Creación ruta/controlador API externa, validación básica, invocación servicio interno]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, api, rest, generate-jd, ai, **monolith**, **external-api**

## Comentarios
Punto de entrada externo para la funcionalidad de generación de JD en Core AI.

## Enlaces o Referencias
[TK-001 - Especificación API Externa](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)