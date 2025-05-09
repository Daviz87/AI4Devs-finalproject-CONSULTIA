# Mejores Prácticas de Base de Datos - TalentIA Core AI (Monolito con PostgreSQL)

 Este documento detalla las mejores prácticas para el diseño, implementación y gestión de la base de datos utilizada por la aplicación **monolítica modular** **TalentIA Core AI**, asumiendo el uso recomendado de **PostgreSQL**.

 ## 1. Diseño del Esquema

 - **Modelo Unificado:** Diseñar un **único esquema de base de datos PostgreSQL** que dé soporte a todos los módulos lógicos internos de Core AI (`Generación JD`, `Perfil Candidato`, `Evaluación`, `Feedback`). **Este enfoque se ve reforzado por la arquitectura monolítica.**
 - **Separación Lógica (Opcional):** Si se desea mayor claridad, se pueden usar esquemas de PostgreSQL (`CREATE SCHEMA module_name;`) o prefijos de tabla (`generation_...`, `evaluation_...`) para organizar las tablas por módulo dentro de la misma base de datos. Sin embargo, para el MVP, un único esquema `public` puede ser suficiente.
 - **Uso de UUIDs:** Utilizar UUIDs como identificadores principales para facilitar referencias consistentes entre entidades y con el ATS MVP.
 - **Modelado en PostgreSQL:**
    * Definir tablas, columnas y constraints (PK, FK, NOT NULL, UNIQUE, CHECK) rigurosamente.
    * Utilizar tipos nativos como `UUID`, `JSONB` y `Arrays` donde sea apropiado. `JSONB` es muy útil para campos como `datos_extraidos_cv`, `metadata_ia`, `perfil_enriquecido`, `score_scores_parciales`, `datos_feedback`.
    * Utilizar `TIMESTAMP WITH TIME ZONE` para fechas.
 - **Relaciones:** Definir Claves Foráneas (FKs) explícitas *dentro* de la base de datos de Core AI (ej. entre `EvaluacionCandidatoIA` y `CandidatoIA`). Las referencias a entidades del ATS MVP (`vacante_ats_id`, etc.) se mantienen como UUIDs simples sin FK directa entre bases de datos.

 ## 2. Implementación y Acceso a Datos

 - **ORM Único:** Utilizar un único ORM (Spring Data JPA) para toda la aplicación Core AI.
 - **Encapsulamiento en Repositorios:** La lógica de acceso a datos debe estar encapsulada en Repositorios específicos por entidad (`@Repository`), ubicados dentro de sus respectivos paquetes/módulos lógicos.
 - **Migraciones Centralizadas:** Utilizar una **única herramienta de migración** de esquema (ej. Flyway, Liquibase) para gestionar todos los cambios en la base de datos de Core AI de forma centralizada y versionada.
 - **Transacciones Simplificadas:** Utilizar transacciones de base de datos gestionadas por Spring (`@Transactional`) a nivel de servicio para asegurar la atomicidad de las operaciones que abarcan múltiples escrituras. **Al usar una única BBDD, las transacciones que involucran datos de diferentes módulos lógicos internos son locales y más fáciles de gestionar.**

 ## 3. Rendimiento

 - **Indexación:** Definir índices adecuados (PK, FK, IDX, GIN para JSONB/Arrays). Validar su efectividad.
 - **Optimización de Consultas:** Escribir consultas eficientes (JPA/JPQL/SQL).

 ## 4. Seguridad

 - **Cifrado en Reposo:** Cifrar datos sensibles (RNF-10, RNF-29) usando capacidades de PostgreSQL o del servicio gestionado Azure DB for PostgreSQL.
 - **Control de Acceso:** Configurar credenciales de acceso a la **única** BBDD de Core AI de forma segura y con permisos mínimos. Gestionar mediante secretos de Azure Container Apps (RNF-13).
 - **Auditoría:** Configurar logging de auditoría a nivel de base de datos si es necesario (RNF-14).

 ## 5. Fiabilidad y Consistencia

 - **Backups y Recuperación:** Implementar políticas de backup/recuperación para la BBDD única de Core AI (RNF-22).
 - **Consistencia de Datos:** Aprovechar las transacciones ACID de PostgreSQL para mantener la consistencia **dentro** de Core AI. La consistencia con ATS MVP se gestiona a nivel de aplicación y API externa (RNF-23B).

 ## 6. Mantenibilidad

 - **Documentación del Esquema Único:** Mantener la documentación del esquema de la BBDD de Core AI actualizada ([db_core_ai.md](../db/db_core_ai.md)).
 - **Migraciones Claras:** Escribir scripts de migración claros y gestionarlos a través de herramientas automatizadas.

 Al seguir estas mejores prácticas, se construirá y gestionará la base de datos única de TalentIA Core AI de forma robusta, segura y mantenible, adecuada para el enfoque de monolito modular.