# Documentación Técnica - TalentIA Core AI (Fase 1)

Este documento proporciona una guía técnica de referencia para el desarrollo de la aplicación **TalentIA Core AI** en la Fase 1 del proyecto TalentIA. Detalla la arquitectura monolítica modular, las tecnologías propuestas, los patrones de diseño y las mejores prácticas a seguir.

## 1. Arquitectura Core AI

TalentIA Core AI se implementa siguiendo un patrón arquitectónico de **Monolito Modular**, tal como se describe en la [Definición de Arquitectura de Core AI](../arq/arq_core_ia.md#3-patrón-arquitectónico-general). Este enfoque prioriza la simplicidad y velocidad de desarrollo para el MVP, manteniendo una estructura organizada mediante módulos internos.

* **Módulos Lógicos Propuestos (Fase 1):**
   * `Módulo de Generación JD`: Gestión de JDs asistidas por IA y parámetros de evaluación asociados. ([Ver detalle](../arq/arq_core_ia.md#51-servicio-de-generación-jd))
   * `Módulo Perfil Candidato`: Gestión del perfil unificado `CandidatoIA`. ([Ver detalle](../arq/arq_core_ia.md#52-servicio-perfil-candidato))
   * `Módulo de Evaluación de Candidatos`: Orquestación del parsing de CV, scoring y sugerencia de etapa. ([Ver detalle](../arq/arq_core_ia.md#53-servicio-de-evaluación-de-candidatos))
   * `Módulo de Feedback y Aprendizaje`: Recepción y almacenamiento de feedback de usuario. ([Ver detalle](../arq/arq_core_ia.md#54-servicio-de-feedback-y-aprendizaje))
   * `Capa API Externa`: Expone la API unificada para el ATS MVP y maneja aspectos transversales. ([Ver detalle](../arq/arq_core_ia.md#45-capa-api-externa-sustituye-al-gateway-api))
* **Comunicación:**
   * **ATS MVP <-> Core AI:** APIs RESTful síncronas a través de la Capa API Externa, definidas por la [API Interna v1](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md) ([RF-21](../rfs/rf-21-api-interna-ats-mvp-core-ai.md)).
   * **Inter-Módulos (Core AI):** Llamadas a métodos de servicio directas dentro del mismo proceso/aplicación.
   * **Core AI -> LLM Externo:** APIs RESTful HTTPS ([RF-22](../rfs/rf-22-invocacion-proveedor-llm-core-ai.md)).

## 2. Tecnologías Clave de Core AI

Se propone el siguiente stack tecnológico unificado para el monolito:

* **Lenguaje y Framework:** **Java + Spring Boot** (para aprovechar el ecosistema maduro de Java, las herramientas de Spring y la experiencia potencial del equipo).
* **Base de Datos:** **PostgreSQL** (Recomendado para simplificar la gestión en el monolito, aprovechando JSONB para datos semi-estructurados - ver [db_core_ai.md](../db/db_core_ai.md)).
* **ORM/ODM:** Spring Data JPA (para PostgreSQL).
* **API Externa/Interna:** Spring MVC / WebFlux.
* **Comunicación Externa (LLM):** Clientes HTTP estándar (RestTemplate, WebClient de Spring) o SDK del proveedor LLM si existe.
* **Autenticación Interna API:** Mecanismo basado en tokens (JWT) o API Keys compartidas, gestionado en la Capa API Externa.
* **Contenerización:** Docker.
* **Plataforma de Despliegue:** **Azure Container Apps (ACA)**.
* **Calidad de Código:** Checkstyle, PMD, SonarQube (integrado en CI).
* **Testing:** JUnit 5, Mockito, Spring Boot Test, Testcontainers (para pruebas de integración con BBDD), Pact/Spring Cloud Contract (para pruebas de contrato API con ATS MVP).
* **Build:** Maven o Gradle.
* **Monitorización:** Micrometer con Prometheus, Grafana (integrado con capacidades de ACA).
* **Logging:** SLF4J con Logback/Log4j2, idealmente con logs estructurados (JSON).

## 3. Módulos Lógicos y Responsabilidades

* **`Módulo de Generación JD`:** Implementa [RF-06](../rfs/rf-06-generar-contenido-jd-core-ai.md), [RF-06B](../rfs/rf-06b-almacenar-parametros-ia-jd-core-ai.md), [RF-23](../rfs/rf-23-usar-datos-internos-jd-core-ai.md) (Should Have). Gestiona `DescripcionPuestoGenerada`. Tareas: [TK-055](../tasks/tk-055-CAI-BE-Implementar-API-Solicitud-Generacion-JD.md) - [TK-061](../tasks/tk-061-CAI-BE-Investigar-Implementar-Enriquecimiento-JD-Datos-Internos.md).
* **`Módulo Perfil Candidato`:** Implementa [RF-09B](../rfs/rf-09b-crear-actualizar-candidato-ia-core-ai.md), [RF-26B](../rfs/rf-26b-mostrar-historial-unificado-ats-mvp.md) (Should Have). Gestiona `CandidatoIA`. Tareas: [TK-062](../tasks/tk-062-CAI-DB-Definir-Schema-CandidatoIA.md) - [TK-064](../tasks/tk-064-CAI-BE-Implementar-Logica-Upsert-CandidatoIA.md), [TK-119](../tasks/tk-119-CAI-BE-API-implementar-endpoint-obtener-detalles-candidato-ia.md), [TK-120](../tasks/tk-120-CAI-BE-Logic-implementar-logica-negocio-obtener-detalles-candidato-ia.md).
* **`Módulo de Evaluación de Candidatos`:** Implementa [RF-11](../rfs/rf-11-parsear-cv-core-ai.md) a [RF-13](../rfs/rf-13-devolver-evaluacion-core-ai.md), [RF-24](../rfs/rf-24-generar-resumen-candidato-core-ai.md) (Should Have), [RF-36](../rfs/rf-36-considerar-soft-skills-core-ai.md) (Could Have). Gestiona `EvaluacionCandidatoIA`. Tareas: [TK-065](../tasks/tk-065-CAI-BE-Implementar-API-Recibir-Solicitud-Evaluacion.md) - [TK-080](../tasks/tk-080-CAI-BE-Investigar-Implementar-Identificacion-Soft-Skills.md).
* **`Módulo de Feedback y Aprendizaje`:** Implementa [RF-20](../rfs/rf-20-recibir-almacenar-feedback-core-ai.md). Gestiona `RegistroFeedbackIA`. Tareas: [TK-134](../tasks/tk-134-CAI-BE-API-asegurar-definir-endpoint-coreai-recibir-feedback.md) - [TK-136](../tasks/tk-136-CAI-BE-Logic-implementar-logica-negocio-validar-almacenar-feedback.md).
* **`Capa API Externa`:** Implementa parte de [RF-21](../rfs/rf-21-api-interna-ats-mvp-core-ai.md). Enruta peticiones y gestiona aspectos transversales.

## 4. Patrones y Mejores Prácticas de Desarrollo Core AI

* **Diseño de API Externa:**
   * Seguir la especificación [OpenAPI v3.x definida en TK-001](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md).
   * Utilizar RESTful, JSON, versionado (`/api/v1`).
   * Documentar con Swagger UI (integrado en Spring Boot).
* **Modularidad Interna:**
   * Organizar el código en paquetes Java claros que representen los módulos lógicos (generacion, perfil, evaluacion, feedback).
   * Minimizar dependencias directas entre módulos; usar interfaces y llamadas a servicios (Beans de Spring).
* **Implementación de Lógica de IA:**
   * **Prompt Engineering:** Diseño iterativo de prompts ([TK-056](../tasks/tk-056-CAI-BE-Implementar-Logica-Prompt-Engineering-JD.md), [TK-077](../tasks/tk-077-CAI-BE-Implementar-Logica-Prompt-Engineering-Resumen.md)). Considerar almacenar prompts en configuración para flexibilidad.
   * **Parsing CV:** Elegir librerías robustas (Apache Tika) o utilizar LLMs ([TK-068](../tasks/tk-068-CAI-BE-Extraer-Texto-PDF-DOCX.md), [TK-069](../tasks/tk-069-CAI-BE-Implementar-Parsing-Texto-CV.md)). Enfocarse en la precisión.
   * **Scoring:** Implementar algoritmo basado en reglas V1 ([TK-072](../tasks/tk-072-CAI-BE-Implementar-Algoritmo-Scoring-v1.md)). Hacerlo configurable si es posible.
   * **Evaluación:** Orquestar el flujo completo de forma clara dentro del módulo de evaluación ([TK-066](../tasks/tk-066-CAI-BE-Implementar-Logica-Iniciar-Evaluacion.md)).
* **Acceso a Datos (PostgreSQL):**
   * Utilizar Spring Data JPA.
   * Encapsular la lógica de acceso en Repositorios por módulo.
   * Manejar transacciones a nivel de servicio.
   * Referirse a [bp_core_ai_db.md](../best_practices/bp_core_ai_db.md).
* **Autenticación/Autorización API:** Implementar el mecanismo acordado (JWT/API Key) en la Capa API Externa.
* **Integración con LLM Externo:**
   * Cliente HTTP centralizado y robusto ([TK-057](../tasks/tk-057-CAI-BE-Implementar-Integracion-LLM-Externo.md)).
   * Gestión segura de API Key ([RNF-11](../prd/PRD%20TalentIA%20FInal.md#rnf-11-gestión-segura-de-api-keys-externas)) mediante secretos ACA.
   * Manejo de errores y reintentos ([RNF-21](../prd/PRD%20TalentIA%20FInal.md#rnf-21-manejo-robusto-de-errores)).
   * Parseo y manejo de la respuesta del LLM ([TK-058](../tasks/tk-058-CAI-BE-Implementar-Manejo-Respuesta-LLM.md)).
* **Seguridad General:**
   * Validación de entrada en la Capa API.
   * Protección de datos (GDPR - [RNF-29](../prd/PRD%20TalentIA%20FInal.md#rnf-29-cumplimiento-gdpr/lopdgdd)).
   * Gestión segura de secretos ([RNF-13](../prd/PRD%20TalentIA%20FInal.md#rnf-13-gestión-segura-de-secretos-internos)).
   * OWASP Top 10.
* **Manejo de Errores y Logging:**
   * Estrategia consistente para errores.
   * Logging estructurado (JSON) y centralizado (integrado con ACA Log Analytics). Incluir IDs de correlación para tracing.
* **Resiliencia y Escalabilidad:**
   * Diseño stateless donde sea posible.
   * Uso de patrones (Circuit Breaker, Retry) para llamadas externas (LLM).
   * Configuración de escalado horizontal en Azure Container Apps.

## 5. Base de Datos (PostgreSQL)

Referirse a la documentación específica:
* [Esquema Core AI](../db/db_core_ai.md) (Asumiendo base única PostgreSQL)
* [Mejores Prácticas Base de Datos Core AI](../best_practices/bp_core_ai_db.md)

## 6. Estrategia de Testing Core AI

* **Pruebas Unitarias (JUnit/Mockito):** Probar lógica de negocio, algoritmos IA, construcción de prompts, etc., dentro de cada módulo.
* **Pruebas de Integración (Spring Boot Test/Testcontainers):**
   * Verificar interacción Módulo <-> Repositorio <-> Base de Datos (PostgreSQL en contenedor).
   * Verificar llamadas *entre módulos* internos (interacción entre Beans de Spring).
   * Verificar llamadas a LLM Externo (usando mocks/stubs).
* **Pruebas de Contrato (Pact/Spring Cloud Contract):** Validar que la API externa expuesta a ATS MVP cumpla el contrato definido ([TK-001](../tasks/tk-001-arq-definir-documentar-contrato-api-v1.md)).
* **Pruebas de API:** Probar directamente los endpoints expuestos por la Capa API Externa.
* **Pruebas Específicas de IA:** Validar precisión de parsing, calidad de generación, correlación de scoring (ver [Plan de Pruebas - Feature 4](../pp/pp-feature-4.md)).

## 7. Calidad del Código

* **Guías de Estilo Java/Spring:** Adherirse a convenciones estándar.
* **Código Limpio:** Principios SOLID, DRY, KISS.
* **Revisión de Código:** Pull Requests con revisión por pares obligatoria.
* **Análisis Estático:** Integrar herramientas (SonarQube, Checkstyle) en CI.

## 8. Proceso de Construcción e Implementación (CI/CD)

* **Pipeline Único:** Configurar un único pipeline de CI/CD para Core AI.
* **Pasos:** Checkout -> Build (Maven/Gradle) -> Pruebas (Unit, Integration, Contract) -> Análisis Calidad -> Build Imagen Docker -> Push a Registro -> Despliegue en **Azure Container Apps**.
* **Gestión de Migraciones DB:** Integrar ejecución de migraciones (Flyway/Liquibase) en el pipeline de despliegue.

---
Referencias:
* [Arquitectura Core AI](../arq/arq_core_ia.md)
* [Base de Datos Core AI](../db/db_core_ai.md)
* [Mejores Prácticas Core AI](../best_practices/bp_core_ai.md)
* [Mejores Prácticas Base de Datos Core AI](../best_practices/bp_core_ai_db.md)
* [TalentIA PRD](../prd/PRD%20TalentIA%20FInal.md)
* [Documentación de Spring Boot](https://spring.io/projects/spring-boot)
* [Documentación de Java](https://dev.java/)
* [Documentación de PostgreSQL](https://www.postgresql.org/docs/)
* [Documentación de Azure Container Apps](https://docs.microsoft.com/azure/container-apps/)
* [Documentación de Docker](https://docs.docker.com/)