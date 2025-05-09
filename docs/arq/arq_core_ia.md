# Definición de Arquitectura - TalentIA Core AI (Fase 1)

 ## 1. Introducción

 Este documento detalla la definición de la arquitectura del componente **TalentIA Core AI** del proyecto TalentIA en su Fase 1. Se basa en la estructura y el enfoque de la documentación proporcionada para el ATS MVP ([PRD TalentIA FInal.md](../prd/PRD%20TalentIA%20FInal.md), [Features](../features/features-overview.md), [User Stories](../us/us-overview.md), [Technical Tasks](../tasks/tasks-overview.md), [Modelos de Datos](../db/db-overview.md), y [Planes de Prueba/Implementación](../pp/pp-overview.md)). TalentIA Core AI es la plataforma central de inteligencia artificial diseñada como una **aplicación monolítica modular** para proporcionar capacidades analíticas y generativas avanzadas al [ATS MVP](../arq/ats_mvp_arq.md).

 ## 2. Alcance y Límites de TalentIA Core AI (Fase 1)

 TalentIA Core AI se enfoca en proporcionar las funcionalidades de inteligencia artificial esenciales para potenciar el proceso de reclutamiento gestionado por el ATS MVP, tal como se define en el [PRD](../prd/PRD%20TalentIA%20FInal.md). Su alcance funcional en la Fase 1 incluye:

 * **Generación Asistida de Descripciones de Puesto (JD):** Proporcionar borradores de JD basados en parámetros iniciales, utilizando LLMs externos ([RF-06](../rfs/rf-06-generar-contenido-jd-core-ai.md)). Incluye el almacenamiento de parámetros de evaluación IA específicos por vacante ([RF-06B](../rfs/rf-06b-almacenar-parametros-ia-jd-core-ai.md)). Ver [Feature 2](../features/feature-02-asistencia-ia-descripcion-puesto.md).
 * **Creación/Actualización de Perfil Unificado de Candidato (`CandidatoIA`):** Mantener un registro único por candidato (basado en email) agregando referencias a todas sus candidaturas recibidas por el ATS MVP ([RF-09B](../rfs/rf-09b-crear-actualizar-candidato-ia-core-ai.md)). Ver [Feature 4](../features/feature-04-evaluacion-inteligente-candidaturas.md).
 * **Evaluación Inteligente de Candidaturas:**
    * Procesamiento y análisis (parsing) de archivos CV (PDF, DOCX) para extraer datos estructurados (skills, experiencia, educación) ([RF-11](../rfs/rf-11-parsear-cv-core-ai.md)).
    * Cálculo de un score de idoneidad del candidato respecto a la vacante ([RF-12](../rfs/rf-12-calcular-score-idoneidad-core-ai.md)).
    * Comparación del score con el umbral de corte definido para la vacante ([RF-12B](../rfs/rf-12b-comparar-score-corte-core-ai.md)).
    * Determinación de una etapa inicial sugerida basada en la comparación ([RF-12C](../rfs/rf-12c-determinar-etapa-sugerida-core-ai.md)).
    * (Should Have) Generación de un resumen ejecutivo del candidato vs vacante ([RF-24](../rfs/rf-24-generar-resumen-candidato-core-ai.md)).
    * (Could Have) Consideración básica de soft skills ([RF-36](../rfs/rf-36-considerar-soft-skills-core-ai.md)).
    * Devolución de la evaluación completa (score, etapa sugerida, resumen opcional) al ATS MVP ([RF-13](../rfs/rf-13-devolver-evaluacion-core-ai.md)). Ver [Feature 4](../features/feature-04-evaluacion-inteligente-candidaturas.md).
 * **Recepción y Almacenamiento de Feedback:** Recibir y guardar el feedback proporcionado por los usuarios del ATS MVP sobre las evaluaciones IA ([RF-20](../rfs/rf-20-recibir-almacenar-feedback-core-ai.md)). Ver [Feature 6](../features/feature-06-sistema-feedback-ia.md).

 **Límites Clave:**

 * **Interacción Exclusiva vía API Interna:** Core AI se comunica únicamente con el ATS MVP a través de la [API interna v1](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md) ([RF-21](../rfs/rf-21-api-interna-ats-mvp-core-ai.md)). No expone interfaces directas a usuarios finales.
 * **Dependencia de LLM Externo:** Core AI depende de la invocación a un proveedor externo de LLM ([RF-22](../rfs/rf-22-invocacion-proveedor-llm-core-ai.md)) para capacidades generativas y potencialmente de análisis avanzado.
 * **Enfoque en Servicios de IA:** Se centra exclusivamente en proporcionar servicios de IA y gestión de datos asociados (perfiles unificados, evaluaciones, feedback), sin implementar lógica de workflow de reclutamiento (responsabilidad del ATS MVP).
 * **Autonomía de Datos de IA:** Mantiene su propia base de datos ([db_core_ai.md](../db/db_core_ai.md)) para almacenar datos específicos (entidades `DescripcionPuestoGenerada`, `CandidatoIA`, `EvaluacionCandidatoIA`, `RegistroFeedbackIA`), vinculados al ATS MVP mediante IDs.

 ## 3. Patrón Arquitectónico General

 Para TalentIA Core AI, se adopta un patrón arquitectónico de **Monolito Modular**. Este enfoque se justifica por (ver [PRD Sec 12.1](../prd/PRD%20TalentIA%20FInal.md#121-patrón-arquitectónico-general)):

 * **Agilidad en Desarrollo MVP:** Permite un desarrollo y despliegue inicial más rápido al tener una única base de código y artefacto desplegable.
 * **Simplicidad Operativa Inicial:** Reduce la complejidad de la infraestructura inicial al **no requerir un orquestador de contenedores complejo como Kubernetes**. El despliegue se realizará en **Azure Container Apps (ACA)**.
 * **Cohesión Lógica:** Agrupa funcionalidades relacionadas de IA dentro de módulos internos bien definidos, facilitando la comunicación intra-proceso mediante llamadas directas a servicios/métodos.
 * **Compromisos:** Se acepta una posible reducción en la escalabilidad granular y en la flexibilidad para adoptar tecnologías diversas por módulo, en favor de la velocidad y simplicidad para la Fase 1.

 Este enfoque, combinado con el Monolito Modular del [ATS MVP](../arq/ats_mvp_arq.md), resulta en la arquitectura **híbrida** definida para la solución TalentIA Fase 1 (Monolito ATS + Monolito Core AI).

 ## 4. Vista Lógica/Conceptual de TalentIA Core AI

 TalentIA Core AI se estructura lógicamente en varios **módulos internos principales** dentro de la misma aplicación monolítica, cada uno encapsulando un Bounded Context específico dentro del dominio de IA para reclutamiento:

 ### 4.1. `Módulo de Generación JD` (`Generación de Contenido IA` BC)

 * **Responsabilidad:** Gestionar la generación asistida de JDs y la configuración de parámetros IA asociados. Expone la API externa (vía Capa API Externa) para [RF-06](../rfs/rf-06-generar-contenido-jd-core-ai.md) y [RF-06B](../rfs/rf-06b-almacenar-parametros-ia-jd-core-ai.md). Internamente, interactúa con el LLM Externo.
 * **Relación con la Documentación:** Cubre [Feature 2](../features/feature-02-asistencia-ia-descripcion-puesto.md), [[US-15]](../us/us-15-generar-borrador-JD-IA.md), [[US-16]](../us/us-16-almacenar-parametros-evaluacion-IA-vacante.md), [[US-17]](../us/us-17-enriquecer-generacion-JD.md) (Should Have), [TK-055](../tasks/tk-055-CAI-BE-Implementar-API-Solicitud-Generacion-JD.md) a [TK-061](../tasks/tk-061-CAI-BE-Investigar-Implementar-Enriquecimiento-JD-Datos-Internos.md). Interactúa con el LLM Externo ([RF-22](../rfs/rf-22-invocacion-proveedor-llm-core-ai.md), [TK-057](../tasks/tk-057-CAI-BE-Implementar-Integracion-LLM-Externo.md)). Gestiona la entidad `DescripcionPuestoGenerada` ([db_core_ai.md](../db/db_core_ai.md)).

 ### 4.2. `Módulo Perfil Candidato` (`Perfil de Candidato` BC - parte Core AI)

 * **Responsabilidad:** Crear y mantener el perfil unificado `CandidatoIA`, agregando las referencias a las candidaturas del ATS MVP ([RF-09B](../rfs/rf-09b-crear-actualizar-candidato-ia-core-ai.md)). Expone la API externa (vía Capa API Externa) para consulta de historial ([RF-26B](../rfs/rf-26b-mostrar-historial-unificado-ats-mvp.md) - Should Have).
 * **Relación con la Documentación:** Cubre [[US-18]](../us/us-18-gestionar-perfil-unidicado-candidato-IA.md), [TK-062](../tasks/tk-062-CAI-DB-Definir-Schema-CandidatoIA.md) a [TK-064](../tasks/tk-064-CAI-BE-Implementar-Logica-Upsert-CandidatoIA.md) y [[US-35]](../us/us-35-consultar-historial-aplicaciones-anteriores-candidato.md) (parte Core AI), [TK-119](../tasks/tk-119-CAI-BE-API-implementar-endpoint-obtener-detalles-candidato-ia.md), [TK-120](../tasks/tk-120-CAI-BE-Logic-implementar-logica-negocio-obtener-detalles-candidato-ia.md). Gestiona la entidad `CandidatoIA` ([db_core_ai.md](../db/db_core_ai.md)).

 ### 4.3. `Módulo de Evaluación de Candidatos` (`Evaluación IA` BC)

 * **Responsabilidad:** Orquestar el proceso completo de evaluación de una candidatura: recibir la solicitud (API externa - [RF-10](../rfs/rf-10-invocar-evaluacion-ia.md)), obtener el CV, realizar el parsing ([RF-11](../rfs/rf-11-parsear-cv-core-ai.md)), calcular el score ([RF-12](../rfs/rf-12-calcular-score-idoneidad-core-ai.md)), comparar con el corte ([RF-12B](../rfs/rf-12b-comparar-score-corte-core-ai.md)), determinar etapa sugerida ([RF-12C](../rfs/rf-12c-determinar-etapa-sugerida-core-ai.md)), generar resumen opcional ([RF-24](../rfs/rf-24-generar-resumen-candidato-core-ai.md)), y preparar el resultado para ser devuelto (API externa - [RF-13](../rfs/rf-13-devolver-evaluacion-core-ai.md)).
 * **Relación con la Documentación:** Cubre [Feature 4](../features/feature-04-evaluacion-inteligente-candidaturas.md), [[US-19]](../us/us-19-invocar-evaluacion-ia-nueva-candidatura.md) a [[US-26]](../us/us-26-identificar-evaluar-soft-skills-cv-core-ai.md), [TK-065](../tasks/tk-065-CAI-BE-Implementar-API-Recibir-Solicitud-Evaluacion.md) a [TK-080](../tasks/tk-080-CAI-BE-Investigar-Implementar-Identificacion-Soft-Skills.md). Interactúa con el LLM Externo ([RF-22](../rfs/rf-22-invocacion-proveedor-llm-core-ai.md)). Gestiona la entidad `EvaluacionCandidatoIA` ([db_core_ai.md](../db/db_core_ai.md)). Depende internamente (llamadas a servicios) del `Módulo Generación JD` (para obtener parámetros IA) y del `Módulo Perfil Candidato` (para vincular con `CandidatoIA`).

 ### 4.4. `Módulo de Feedback y Aprendizaje` (`Feedback y Aprendizaje IA` BC)

 * **Responsabilidad:** Recibir (vía API externa) y almacenar el feedback de los usuarios sobre las evaluaciones IA ([RF-20](../rfs/rf-20-recibir-almacenar-feedback-core-ai.md)). Proporciona la base de datos para los procesos (potencialmente offline/batch) de re-entrenamiento o ajuste de los modelos de IA.
 * **Relación con la Documentación:** Cubre [Feature 6](../features/feature-06-sistema-feedback-ia.md), [[US-39]](../us/us-39-recibir-almacenar-feedback-usuario-core-ai.md), [TK-134](../tasks/tk-134-CAI-BE-API-asegurar-definir-endpoint-coreai-recibir-feedback.md) a [TK-136](../tasks/tk-136-CAI-BE-Logic-implementar-logica-negocio-validar-almacenar-feedback.md). Gestiona la entidad `RegistroFeedbackIA` ([db_core_ai.md](../db/db_core_ai.md)).

 ### 4.5. `Capa API Externa`

 * **Responsabilidad:** Actúa como la **fachada API del monolito Core AI**. Expone la API interna v1 unificada (documentada en TK-001) para las llamadas desde el ATS MVP. Enruta las peticiones a los **servicios/controladores de los módulos internos** correspondientes. Gestiona la autenticación/autorización interna de la API, limitación de tasa (rate limiting) y centraliza la observabilidad (logging, tracing) para las llamadas entrantes.
 * **Relación con la Documentación:** Implementa el contrato de la [API interna v1](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md) ([RF-21](../rfs/rf-21-api-interna-ats-mvp-core-ai.md)) y facilita futuras integraciones ([RNF-28](../prd/PRD%20TalentIA%20FInal.md#rnf-28-preparación-para-integraciones-futuras)).

 ## 5. Vista Detallada de Módulos (Componentes Internos)

 Esta sección detalla la responsabilidad y componentes clave dentro de cada módulo lógico del monolito Core AI. La comunicación entre estos componentes se realiza mediante llamadas a métodos directos dentro de la aplicación.

 ### 5.1. `Módulo de Generación JD`

 * **Entrada (vía Capa API Externa):** Petición desde ATS con datos básicos de vacante y/o parámetros IA.
 * **Salida (vía Capa API Externa):** JD generada (texto) y/o confirmación de guardado de parámetros.
 * **Componentes Internos Clave:**
    * Controlador API (ej. `GenerationController`).
    * Servicio de Lógica de Negocio (`GeneracionJDService`).
    * Componente de Prompt Engineering.
    * Componente Cliente LLM (interfaz para TK-057).
    * Componente Manejador de Respuesta LLM.
    * Repositorio para `DescripcionPuestoGenerada` (interactúa con BBDD Core AI).
 * **Dependencias Externas:** LLM Externo, BBDD Core AI (Relacional/Unificada).

 ### 5.2. `Módulo Perfil Candidato`

 * **Entrada (vía Capa API Externa):** Petición desde ATS para crear/actualizar perfil (email, id_candidatura), Petición desde ATS para obtener perfil/historial (email).
 * **Salida (vía Capa API Externa):** Confirmación OK, Detalles `CandidatoIA`.
 * **Componentes Internos Clave:**
    * Controlador API (ej. `CandidateProfileController`).
    * Servicio de Lógica de Negocio (`PerfilCandidatoService`).
    * Repositorio para `CandidatoIA`.
 * **Dependencias Externas:** BBDD Core AI (Relacional/Unificada).

 ### 5.3. `Módulo de Evaluación de Candidatos`

 * **Entrada (vía Capa API Externa):** Petición desde ATS con IDs (Candidatura, Vacante, CV).
 * **Salida (vía Capa API Externa):** Evaluación completa (Score, Etapa Sugerida, Resumen opcional).
 * **Componentes Internos Clave:**
    * Controlador API (ej. `EvaluationController`).
    * Servicio Orquestador de Evaluación (`EvaluacionService`).
    * Componente de Acceso a Archivos (interfaz para TK-067).
    * Componente de Extracción de Texto.
    * Componente de Parsing NLP/LLM.
    * Componente de Recuperación de Inputs (Params JD, Datos CV) - *Invoca Servicios/Repositorios del Módulo Generación JD*.
    * Componente de Scoring.
    * Componente de Decisión (Comparación, Sugerencia Etapa).
    * Componente de Generación de Resumen (Opcional).
    * Componente Cliente LLM (interfaz para TK-057).
    * Repositorio para `EvaluacionCandidatoIA`.
    * Componente de Formateo Respuesta.
 * **Dependencias Externas:** LLM Externo, BBDD Core AI (Relacional/Unificada).
 * **Dependencias Internas (Llamadas a Servicios):** `Módulo Generación JD`, `Módulo Perfil Candidato`.

 ### 5.4. `Módulo de Feedback y Aprendizaje`

 * **Entrada (vía Capa API Externa):** Petición desde ATS con datos de feedback.
 * **Salida (vía Capa API Externa):** Confirmación OK.
 * **Componentes Internos Clave:**
    * Controlador API (ej. `FeedbackController`).
    * Servicio de Lógica de Negocio (`FeedbackService`).
    * Repositorio para `RegistroFeedbackIA`.
    * (Futuro) Componentes para re-entrenamiento.
 * **Dependencias Externas:** BBDD Core AI (Relacional/Unificada).

 ## 6. Vista de Procesos (Flujos Clave)

 Los flujos de procesos detallados que ilustran la interacción entre los módulos lógicos *dentro* de Core AI, y entre Core AI y sistemas externos (ATS MVP, LLM) se encuentran documentados mediante diagramas de secuencia en la Sección 7.2 del [PRD TalentIA FInal.md](../prd/PRD%20TalentIA%20FInal.md). Los flujos principales son:

 * **Generación de JD y Configuración IA:** Iniciado por el Reclutador en ATS MVP, invoca la Capa API Externa de Core AI, que enruta la llamada al servicio del `Módulo de Generación JD`. Este módulo internamente puede interactuar con el Cliente LLM y su Repositorio. ([Ver UC1](../prd/PRD%20TalentIA%20FInal.md#uc1-gestionar-vacante-y-generar-descripción-jd-con-ia)).
 * **Recepción y Evaluación de Candidatura:** Iniciado por el ATS MVP, invoca la Capa API Externa de Core AI. Se llama al servicio del `Módulo Perfil Candidato` para gestionar `CandidatoIA`. Luego, se llama al servicio del `Módulo de Evaluación de Candidatos`, que orquesta el proceso, obteniendo parámetros vía llamada interna al servicio del `Módulo Generación JD` y potencialmente llamando al Cliente LLM Externo. ([Ver UC3](../prd/PRD%20TalentIA%20FInal.md#uc3-recepcionar-y-evaluar-candidatura-con-ia)).
 * **Consulta de Historial Unificado:** Iniciado por el ATS MVP, invoca la Capa API Externa de Core AI, que enruta al servicio del `Módulo Perfil Candidato`. ([Ver UC4 - parte historial](../prd/PRD%20TalentIA%20FInal.md#uc4-revisar-candidatos-y-gestionar-pipeline)).
 * **Procesamiento de Feedback:** Iniciado por el usuario en ATS MVP, ATS MVP invoca la Capa API Externa de Core AI, que enruta al servicio del `Módulo de Feedback y Aprendizaje`. ([Ver UC5](../prd/PRD%20TalentIA%20FInal.md#uc5-proporcionar-feedback-a-ia)).

 ## 7. Vista de Datos (Core AI)

 La estructura detallada de las entidades gestionadas por TalentIA Core AI (`DescripcionPuestoGenerada`, `CandidatoIA`, `EvaluacionCandidatoIA`, `RegistroFeedbackIA`) se encuentra en la [documentación de la Base de Datos Core AI](../db/db_overview.md#2-base-de-datos-talentia-core-ai). Con el enfoque de monolito modular, se **recomienda utilizar una única base de datos PostgreSQL** para toda la aplicación Core AI, aprovechando tipos como JSONB para campos como `datos_extraidos_cv`, simplificando la gestión de datos y las transacciones internas.

 ## 8. Vista de Desarrollo (Development View)

 * **Tecnologías:** **Java + Spring Boot** como stack principal propuesto para el monolito. Uso de librerías específicas de Java para NLP (si aplica), interacción con LLMs (APIs REST), ORM (Spring Data JPA), cliente HTTP (WebClient/RestTemplate).
 * **Organización del Código:** Un único repositorio Git (o proyecto multi-módulo Maven/Gradle). Estructura interna por paquetes/módulos Java que representen los módulos lógicos (`generacion_jd`, `perfil`, `evaluacion`, `feedback`) dentro de la aplicación Spring Boot. Cada módulo contendrá sus controladores API (si exponen funcionalidad externa), servicios, repositorios y modelos.
 * **Comunicación Inter-Módulos:** **Llamadas directas a métodos de servicio** entre módulos (utilizando la inyección de dependencias de Spring - Beans). Evitar acoplamiento excesivo definiendo interfaces claras para los servicios.
 * **Testing:**
    * Pruebas Unitarias (JUnit, Mockito) para lógica de negocio y componentes dentro de cada módulo.
    * Pruebas de Integración (Spring Boot Test, Testcontainers) para verificar interacción módulo <-> BBDD y la interacción *entre los servicios de diferentes módulos internos*.
    * Pruebas de API (Contrato TK-001): Validar los endpoints expuestos por la Capa API Externa.
    * Pruebas Específicas de IA (ver [Plan de Pruebas](../pp/pp-overview.md)).
 * **CI/CD:** Pipeline **único** para construir la aplicación monolítica (ej. build JAR ejecutable con Maven/Gradle), crear una imagen Docker, y desplegarla en **Azure Container Apps**.
 * **Mejores Prácticas:** Adherencia a las [Mejores Prácticas para Core AI](../best_practices/bp_core_ai.md) y [Bases de Datos Core AI](../best_practices/bp_core_ai_db.md).

 ## 9. Estructura Propuesta de Carpetas y Archivos (Ejemplo Monolito Java/Spring Boot)

 ```text
 /core-ai-monolith/
 ├── src/
 │   ├── main/
 │   │   ├── java/
 │   │   │   └── com/talentia/coreai/
 │   │   │       ├── CoreAiApplication.java # Punto de entrada Spring Boot
 │   │   │       ├── config/             # Configuración (Beans, Security, DB)
 │   │   │       ├── api/                # Controladores API Externa (hacia ATS MVP)
 │   │   │       │   └── CandidateProfileApiController.java
 │   │   │       │   └── EvaluationApiController.java
 │   │   │       │   └── GenerationApiController.java
 │   │   │       │   └── FeedbackApiController.java
 │   │   │       ├── common/             # Clases comunes, DTOs, Excepciones
 │   │   │       ├── profile/            # Módulo Perfil Candidato
 │   │   │       │   ├── service/        #   PerfilCandidatoService.java (TK-064, TK-120)
 │   │   │       │   ├── model/          #   CandidatoIA.java (Entidad JPA/BBDD)
 │   │   │       │   └── repository/     #   CandidatoIaRepository.java (Spring Data)
 │   │   │       ├── generation/         # Módulo Generación JD
 │   │   │       │   ├── service/        #   GeneracionJDService.java (TK-056, TK-058, TK-060, TK-061)
 │   │   │       │   ├── client/         #   LlmClient.java (Interfaz/Impl para TK-057)
 │   │   │       │   ├── model/          #   DescripcionPuestoGenerada.java
 │   │   │       │   └── repository/     #   DescPuestoGenRepository.java
 │   │   │       ├── evaluation/         # Módulo Evaluación Candidatos
 │   │   │       │   ├── service/        #   EvaluacionService.java (TK-066, TK-067, TK-068, TK-069, TK-071, TK-072, TK-074, TK-075, TK-077, TK-078, TK-080)
 │   │   │       │   ├── model/          #   EvaluacionCandidatoIA.java
 │   │   │       │   └── repository/     #   EvaluacionCandidatoRepository.java (TK-070, TK-073, TK-079)
 │   │   │       └── feedback/           # Módulo Feedback y Aprendizaje
 │   │   │           ├── service/        #   FeedbackService.java (TK-136)
 │   │   │           ├── model/          #   RegistroFeedbackIA.java
 │   │   │           └── repository/     #   FeedbackRepository.java
 │   │   └── resources/
 │   │       ├── application.properties  # Configuración Spring
 │   │       └── db/migration/       # Migraciones SQL (Flyway/Liquibase) - UNIFICADAS para BBDD Core AI
 │   └── test/
 │       └── java/
 │           └── com/talentia/coreai/ # Pruebas Unitarias e Integración
 ├── pom.xml                     # Dependencias Maven (o build.gradle)
 ├── Dockerfile                  # Para construir imagen Docker del monolito
 └── README.md
```

### 10. Vista Física / Despliegue (Physical / Deployment View)

Esta sección describe cómo se espera que los microservicios de TalentIA Core AI sean desplegados en los diferentes entornos (desarrollo, staging, producción).

* **Contenerización:**  La aplicación Core AI (Monolito Modular) se empaquetará como un único contenedor Docker. Ver [bp_core_ai.md](../best_practices/bp_core_ai.md) y [bp_general.md](../best_practices/bp_general.md).
* **Plataforma de despliegue:**  Se utilizará **Azure Container Apps (ACA)** para desplegar y gestionar el contenedor Docker de Core AI. ACA proporciona un entorno gestionado para contenedores con características de autoescalado, HTTPS, integración con redes virtuales, y gestión de secretos.
* **Entornos:** Se definirán entornos separados en ACA para Desarrollo, Pruebas/QA/Staging y Producción. La configuración específica de la aplicación (ej. URLs de BBDD, API Keys de LLM) se gestionará por entorno mediante variables de entorno o secretos en ACA.
* **Infraestructura:** 
    * **Base de Datos:** Se recomienda una instancia de Azure Database for PostgreSQL (o la BBDD relacional elegida) gestionada.
    * **Red:** Configurar la red virtual de ACA para permitir la comunicación segura entre el contenedor de Core AI y la base de datos, así como la comunicación segura con el ATS MVP y el proveedor LLM externo.
* **Escalado:** Configurar las reglas de escalado en Azure Container Apps basadas en métricas como CPU, memoria o peticiones HTTP para escalar horizontalmente las réplicas del contenedor monolítico según la carga **[RNF-05](../prd/PRD%20TalentIA%20FInal.md#rnf-05-escalabilidad-horizontal-de-core-ai)**.

*[Detalles adicionales sobre topología de red, estrategias de despliegue (Blue/Green, Canary), y configuración específica de infraestructura se añadirán cuando estén definidos.]*

## 11. Requisitos No Funcionales Clave (Resumen para Core AI)

Los requisitos no funcionales (NFRs) definen las cualidades del sistema Core AI. Los NFRs completos para la Fase 1 se detallan en la [Sección 9 del PRD TalentIA FInal.md](../prd/PRD%20TalentIA%20FInal.md#9-requisitos-no-funcionales-rnf). A continuación, se resumen los NFRs clave con mayor relevancia directa para la arquitectura e implementación de TalentIA Core AI:

* **Rendimiento y Escalabilidad:**
    * **[RNF-02](../prd/PRD%20TalentIA%20FInal.md#rnf-02-latencia-de-generación-de-jd-asistida-por-ia): Latencia de Generación de JD Asistida por IA:** < 15 segundos (p90).
    * **[RNF-03](../prd/PRD%20TalentIA%20FInal.md#rnf-03-latencia-de-evaluación-de-candidatura-por-ia): Latencia de Evaluación de Candidatura por IA:** < 2 minutos (p90).
    * **[RNF-05](../prd/PRD%20TalentIA%20FInal.md#rnf-05-escalabilidad-horizontal-de-core-ai): Escalabilidad del Monolito Core AI en ACA:** La aplicación monolítica desplegada en ACA debe poder escalar horizontalmente (aumentando número de réplicas) para manejar la carga esperada.
    * **[RNF-06](../prd/PRD%20TalentIA%20FInal.md#rnf-06-capacidad-de-volumen-de-datos-fase-1): Capacidad de Volumen de Datos:** Soportar el volumen esperado en Fase 1 sin degradación.
* **Seguridad:**
    * **[RNF-09](../prd/PRD%20TalentIA%20FInal.md#rnf-09-cifrado-de-datos-en-tránsito): Cifrado de Datos en Tránsito:** TLS 1.2+ para toda comunicación.
    * **[RNF-10](../prd/PRD%20TalentIA%20FInal.md#rnf-10-cifrado-de-datos-sensibles-en-reposo): Cifrado de Datos Sensibles en Reposo:** PII de candidatos y datos sensibles en BBDD Core AI deben estar cifrados.
    * **[RNF-11](../prd/PRD%20TalentIA%20FInal.md#rnf-11-gestión-segura-de-api-keys-externas): Gestión Segura de API Keys Externas:** API Key del LLM gestionada de forma segura (ej. secretos de ACA).
    * **[RNF-13](../prd/PRD%20TalentIA%20FInal.md#rnf-13-gestión-segura-de-secretos-internos): Gestión Segura de Secretos Internos:** Credenciales de BBDD, claves de autenticación interna (usar secretos de ACA).
    * **[RNF-14](../prd/PRD%20TalentIA%20FInal.md#rnf-14-registro-de-auditoría-de-seguridad): Registro de Auditoría de Seguridad:** Logging de eventos de seguridad relevantes.
* **Usabilidad y Accesibilidad:**
    * **[RNF-17](../prd/PRD%20TalentIA%20FInal.md#rnf-17-transparencia-de-la-ia): Transparencia de la IA:** Core AI debe devolver información que permita al ATS MVP explicar las sugerencias/scores.
* **Fiabilidad y Disponibilidad:**
    * **[RNF-20](../prd/PRD%20TalentIA%20FInal.md#rnf-20-disponibilidad-del-servicio-ats-mvp): Disponibilidad del Servicio:** Core AI debe tener una disponibilidad igual o superior al ATS MVP (99.5% en horario laboral) para no ser el cuello de botella.
    * **[RNF-21](../prd/PRD%20TalentIA%20FInal.md#rnf-21-manejo-robusto-de-errores): Manejo Robusto de Errores:** Gestión controlada de errores internos y de comunicación.
    * **[RNF-22](../prd/PRD%20TalentIA%20FInal.md#rnf-22-política-de-copias-de-seguridad-y-recuperación): Política de Copias de Seguridad y Recuperación:** Backups y plan de recuperación para BBDD de Core AI (usar servicios gestionados de Azure).
    * **[RNF-23](../prd/PRD%20TalentIA%20FInal.md#rnf-23-resiliencia-del-procesamiento-ia): Resiliencia del Procesamiento IA:** Manejo de fallos de dependencias (ej. LLM externo) con patrones como Circuit Breaker/Retry.
    * **[RNF-23B](../prd/PRD%20TalentIA%20FInal.md#rnf-23b-consistencia-de-datos-entre-componentes): Consistencia de Datos entre Componentes:** Mantener integridad referencial con ATS MVP.
* **Mantenibilidad y Extensibilidad:**
    * **[RNF-24](../prd/PRD%20TalentIA%20FInal.md#rnf-24-calidad-y-documentación-del-código): Calidad y Documentación del Código:** Código modular, legible, documentado.
    * **[RNF-25](../prd/PRD%20TalentIA%20FInal.md#rnf-25-arquitectura-modular-y-desacoplada): Arquitectura Modular (Lógica):** El diseño del monolito debe ser modular internamente, aunque se pierde el desacoplamiento físico y de despliegue..
    * **[RNF-26](../prd/PRD%20TalentIA%20FInal.md#rnf-26-automatización-de-despliegues-cicd): Automatización de Despliegues (CI/CD):** Pipeline único para construir y desplegar el monolito Core AI en ACA.
    * **[RNF-27](../prd/PRD%20TalentIA%20FInal.md#rnf-27-versionado-y-contrato-de-api-interna): Versionado y Contrato de API Interna:** API interna versionada ([TK-001](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)).
    * **[RNF-28](../prd/PRD%20TalentIA%20FInal.md#rnf-28-preparación-para-integraciones-futuras): Preparación para Integraciones Futuras:** Diseño que permita conectar con ATS externos.
* **Cumplimiento Normativo:**
    * **[RNF-29](../prd/PRD%20TalentIA%20FInal.md#rnf-29-cumplimiento-gdpr/lopdgdd): Cumplimiento GDPR/LOPDGDD:** Tratamiento seguro y legal de datos de candidatos en Core AI.
    * **[RNF-30](../prd/PRD%20TalentIA%20FInal.md#rnf-30-transparencia-en-el-uso-de-ia): Transparencia en el Uso de IA:** Proveer información para explicar las decisiones de IA.

---