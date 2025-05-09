# Ticket: TK-064

## Título
CAI-BE: Implementar Lógica Core AI para Find-Or-Create/Update `CandidatoIA` **en BBDD Unificada**

## Descripción
Desarrollar la lógica de negocio dentro del **módulo Perfil Candidato del monolito Core AI** que maneja la creación o actualización de la entidad `CandidatoIA`. Debe buscar un perfil existente por email; si no existe, crea uno nuevo; si existe, añade el nuevo ID de candidatura ATS a la lista `candidaturas_ids` (evitando duplicados). Persiste los cambios en la **BBDD PostgreSQL unificada** de Core AI (TK-062).

## User Story Relacionada
US-018: Gestionar Perfil Unificado de Candidato en IA (Capacidad Core AI)

## Criterios de Aceptación Técnicos (Verificables)
1.  La función/método de servicio recibe `email` y `candidatura_ats_id`.
2.  Busca en la **BBDD unificada** (`CandidatoIA`) un registro con el `email` proporcionado.
3.  Si NO existe:
    * Crea una nueva instancia de `CandidatoIA`.
    * Establece el `email`.
    * Inicializa `candidaturas_ids` como un array con el `candidatura_ats_id` recibido.
    * Establece `fecha_creacion` y `fecha_actualizacion`.
    * Guarda (INSERT) el nuevo registro en la **BBDD unificada**.
4.  Si SÍ existe:
    * Verifica si `candidatura_ats_id` ya está presente en el array `candidaturas_ids`.
    * Si no está presente, añade el `candidatura_ats_id` al array.
    * Actualiza `fecha_actualizacion`.
    * Guarda (UPDATE) el registro existente en la **BBDD unificada**.
5.  La operación de búsqueda y actualización/inserción debe ser lo más atómica posible (gestionada por `@Transactional` si usa Spring).
6.  Maneja errores de base de datos y devuelve éxito o error al controlador (TK-063).

## Solución Técnica Propuesta (Opcional)
Implementar en capa de Servicios (`@Service`). Usar ORM (Spring Data JPA) para buscar por índice `email` y para operaciones de update/insert en la BBDD unificada.

## Dependencias Técnicas (Directas)
* TK-063 (Endpoint API externa que invoca esta lógica)
* TK-062 (Esquema BBDD unificado `CandidatoIA`)

## Prioridad (Heredada/Ajustada)
Must Have (Heredada de US-018)

## Estimación Técnica Preliminar
[ 4 ] [horas] - [Implementación lógica find-or-create, manejo de array (add unique), operaciones BBDD unificada (SELECT, INSERT, UPDATE)]

## Asignación Inicial
Equipo Backend (Core AI)

## Etiquetas
backend, core-ai, logic, service, candidate, profile, upsert, database, **monolith**, **unified-db**

## Comentarios
Lógica clave para mantener el perfil unificado en la BBDD común.

## Enlaces o Referencias
[Documentación ORM/ODM sobre find/update/insert y manejo de arrays]