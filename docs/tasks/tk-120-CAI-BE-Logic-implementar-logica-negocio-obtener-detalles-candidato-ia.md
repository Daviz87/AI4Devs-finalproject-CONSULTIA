# Ticket: TK-120

## Título
CAI-BE-Logic: Implementar Lógica de Negocio Obtener Detalles CandidatoIA **en BBDD Unificada**

## Descripción
Desarrollar la lógica en el **módulo Perfil Candidato del monolito Core AI** para buscar y devolver un registro `CandidatoIA` por su email desde la **BBDD unificada**, incluyendo la lista `candidaturas_ids`.

## User Story Relacionada
US-035: Consultar Historial de Aplicaciones Anteriores del Candidato (requiere esta lógica)

## Criterios de Aceptación Técnicos (Verificables)
1.  La función/método de servicio recibe un `email`.
2.  Busca en la **BBDD unificada** de Core AI el registro `CandidatoIA` que coincida con el `email`.
3.  Si se encuentra, devuelve el objeto `CandidatoIA` completo (o los campos relevantes, incluyendo `candidaturas_ids`).
4.  Si no se encuentra, devuelve null o lanza una excepción NotFound.
5.  Maneja errores de BBDD.

## Solución Técnica Propuesta (Opcional)
Usar ORM/ODM para buscar por el campo `email` (que debe tener índice unique, TK-062) en la BBDD unificada.

## Dependencias Técnicas (Directas)
* TK-062 (Esquema BBDD Unificado `CandidatoIA`)
* TK-119 (API externa que invoca esta lógica)

## Prioridad (Heredada/Ajustada)
Should Have (Heredada de US-035)

## Estimación Técnica Preliminar
[ 1 ] [hora] - [Implementación lógica de servicio, consulta BBDD unificada por índice]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, logic, service, candidate, profile, read, database, **monolith**, **unified-db**

## Comentarios
Lógica de lectura simple sobre `CandidatoIA` en la BBDD común.

## Enlaces o Referencias
[Documentación ORM/ODM]