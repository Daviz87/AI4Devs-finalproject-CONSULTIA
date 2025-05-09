# Mejores Prácticas de Desarrollo - TalentIA Core AI (Monolito Modular)

 Este documento detalla las mejores prácticas específicas para el desarrollo de la aplicación **monolítica modular** **TalentIA Core AI**, utilizando Java con Spring Boot (propuesta) y enfocándose en las capacidades de IA, integración y escalabilidad, basándose en la arquitectura y decisiones técnicas definidas.

 ## 1. Arquitectura y Organización del Código

 - **Monolito Modular:** Diseñar la aplicación como un **único artefacto desplegable** pero estructurado internamente en **módulos lógicos claros y cohesivos** (ej. paquetes Java `generacion_jd`, `evaluacion`, `perfil`, `feedback`). Cada módulo debe tener responsabilidades bien definidas y mantener bajo acoplamiento con otros módulos.
 - **Comunicación Inter-Módulos:** La comunicación principal entre módulos se realizará mediante **llamadas directas a métodos de servicio** (ej. inyección de Beans de Spring). **Evitar dependencias circulares** y definir interfaces claras para los servicios expuestos por cada módulo.
 - **Capa API Externa:** Implementar una capa API clara (ej. usando Spring MVC/WebFlux) que exponga los endpoints definidos en el contrato **externo** (TK-001) hacia el ATS MVP. Esta capa actúa como fachada y punto de entrada al monolito.
 - **Patrones de Diseño:** Aplicar principios SOLID y patrones de diseño apropiados (Repository, Service, etc.) dentro de cada módulo para mantener la calidad del código y la separación de conceptos.

 ## 2. Desarrollo con Java y Spring Boot

 - **Spring Boot:** Utilizar Spring Boot para facilitar la configuración, inyección de dependencias y el arranque de la aplicación monolítica.
 - **REST Controllers (Capa API Externa):** Implementar los endpoints API *externos* utilizando Spring MVC o Spring WebFlux. Validar la entrada de las peticiones.
 - **Servicios de Negocio:** Implementar la lógica principal de cada módulo en la capa de servicios (`@Service`). Estos servicios orquestan las llamadas a repositorios y, **si es necesario, a servicios de otros módulos internos** o al cliente LLM.
 - **Programación Reactiva (Opcional):** Considerar Spring WebFlux si se anticipa alta concurrencia o necesidad de manejar eficientemente llamadas externas bloqueantes (ej. LLM).

 ## 3. Capacidades de IA y Procesamiento

 - **Integración con Proveedores LLM:** Implementar la integración segura con APIs de proveedores LLM externos (TK-057). Gestionar API Keys de forma segura (RNF-11) usando secretos de ACA (Azure Container Apps).
 - **Prompt Engineering:** Diseñar y refinar los prompts enviados a los LLMs (TK-056, TK-077). Versionar o configurar los prompts.
 - **Procesamiento de Texto (NLP):** Utilizar librerías de NLP o invocar LLMs para extracción de texto (TK-068), parsing de CVs (TK-069), etc.
 - **Algoritmos de Matching y Scoring:** Implementar los algoritmos (TK-072). Documentar la lógica. Hacerlos configurables si es posible.
 - **Transparencia de la IA:** Asegurar que los resultados devueltos permitan explicaciones (RNF-17).

 ## 4. Base de Datos (PostgreSQL Unificada)

 - **Persistencia Unificada:** Utilizar la **única base de datos PostgreSQL** recomendada para todos los módulos de Core AI para simplificar la gestión de datos y transacciones (RNF-25, RNF-23B). Utilizar JSONB para datos semi-estructurados si es necesario.
 - **ORM/ODM:** Utilizar Spring Data JPA para interactuar con PostgreSQL.
 - **Modelo de Datos Core AI:** Mantener el modelo de datos de Core AI ([db_core_ai.md](../db/db_core_ai.md)) desacoplado del modelo del ATS MVP, referenciándose por IDs. Aplicar migraciones (Flyway/Liquibase) de forma **unificada** para la BBDD Core AI.
 - Referirse a [bp_core_ai_db.md](../best_practices/bp_core_ai_db.md) (reforzando BBDD única).

 ## 5. Seguridad

 - **Autenticación y Autorización API Externa:** Proteger la API expuesta al ATS MVP utilizando un mecanismo acordado (JWT/API Key) implementado en la Capa API Externa (RNF-07, RNF-08).
 - **Validación de Entrada:** Validar siempre la entrada en los endpoints de la API externa (RNF-12).
 - **Gestión Segura de Secretos:** Gestionar credenciales de BBDD, API Keys externas, etc., de forma segura (ej. secretos de Azure Container Apps) (RNF-11, RNF-13).
 - **Protección de Datos Sensibles:** Implementar cifrado en tránsito y en reposo (RNF-09, RNF-10, RNF-29). Minimizar PII.

 ## 6. Fiabilidad y Resiliencia

 - **Manejo Robusto de Errores:** Implementar manejo de errores consistente dentro de la aplicación. Usar patrones de resiliencia (Circuit Breaker, Retry) para llamadas externas (LLM) (RNF-21, RNF-23).
 - **Monitorización y Alertas:** Implementar monitorización de salud y rendimiento de la aplicación monolítica en ACA. Configurar alertas.
 - **Logging:** Implementar logging centralizado y estructurado (RNF-14), idealmente JSON, para integración con ACA.

 ## 7. Escalabilidad

 - **Diseño sin Estado (Stateless):** Diseñar los servicios (donde sea posible) para que sean stateless y facilitar el escalado horizontal del monolito en **Azure Container Apps** (RNF-05).
 - **Plataforma de Despliegue:** Utilizar **Azure Container Apps (ACA)** para gestionar el despliegue, escalado (configurar reglas de autoescalado) y alta disponibilidad del contenedor monolítico.

 ## 8. Mantenibilidad y Extensibilidad

 - **Código Modular y Cohesivo:** Mantener una estructura clara de paquetes/módulos internos. Usar interfaces.
 - **API Externa Clara:** La API expuesta al ATS MVP debe estar bien definida y documentada (TK-001, RNF-27).
 - **Automatización de Despliegues:** Implementar un pipeline **CI/CD único** para construir, probar y desplegar el monolito en ACA (RNF-26).

 Al seguir estas mejores prácticas, el equipo de Core AI podrá construir un motor de inteligencia robusto, escalable y mantenible para TalentIA.