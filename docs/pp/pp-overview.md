# Estrategia General de Pruebas - TalentIA Fase 1 (ATS MVP + Core AI)

## 1. Introducción y Objetivos

Este documento describe la estrategia general de pruebas para la Fase 1 del proyecto TalentIA. El objetivo principal de las pruebas en esta fase es **validar la funcionalidad MVP** del sistema integrado (ATS MVP + TalentIA Core AI), **asegurar un nivel adecuado de calidad y fiabilidad**, mitigar riesgos clave y **validar la efectividad y usabilidad** de la solución conjunta, en línea con los objetivos definidos en el PRD.

## 2. Alcance de las Pruebas

### 2.1. En Alcance (Fase 1)

* **ATS MVP:**
   * Funcionalidades CRUD de Vacantes (Crear, Editar, Cambiar Estado).
   * Configuración del sistema (Gestión de Usuarios, Etapas de Pipeline).
   * Autenticación y Autorización básica por roles.
   * Portal Público de Empleo (Listado de Vacantes).
   * Proceso de Aplicación de Candidatos (Formulario, Subida CV).
   * Recepción y almacenamiento de Candidaturas.
   * Visualización de Candidatos (Lista, Detalle, Kanban).
   * Visualización de resultados de IA (Score, Etapa Sugerida, Resumen).
   * Gestión del Pipeline (Movimiento manual de candidatos).
   * Captura de Feedback IA (Básico y Detallado).
   * Funcionalidades "Could Have" (Notificaciones, Búsqueda, Dashboard, Exportación) si se implementan.
* **TalentIA Core AI (Monolito Modular - Funcionalidad y Calidad):**
   * Generación de JD (Calidad y relevancia del contenido).
   * Almacenamiento de parámetros IA por vacante.
   * Gestión del Perfil Unificado `CandidatoIA`.
   * Parsing de CV (Precisión en extracción de skills, experiencia, educación).
   * Cálculo de Score (Consistencia, correlación inicial con evaluación manual).
   * Determinación de Etapa Sugerida (Corrección basada en score y configuración).
   * Generación de Resumen (Calidad y relevancia - Should Have).
   * Recepción y Almacenamiento de Feedback.
* **Integración:**
   * Comunicación API ATS MVP <-> Core AI (Monolito) (Según contrato TK-001).
   * Comunicación Core AI (Monolito) <-> Proveedor LLM Externo.
* **Requisitos No Funcionales (NFRs):**
   * Pruebas básicas de Rendimiento (según RNF-01 a RNF-06, **enfocadas en los monolitos y ACA**).
   * Pruebas básicas de Seguridad (según RNF-07 a RNF-14).
   * Evaluación de Usabilidad básica (según RNF-15 a RNF-19).
   * Fiabilidad (manejo de errores, backups básicos - RNF-20 a RNF-23B).

### 2.2. Fuera de Alcance (Fase 1)

* Integración con ATS externos (TeamTailor, etc.).
* Funcionalidades avanzadas de ATS no incluidas en el MVP.
* Analíticas avanzadas y reporting complejo.
* Soporte multi-idioma avanzado.
* Pruebas de carga extensivas más allá de validar NFR básicos.
* Pruebas exhaustivas de penetración (verificaciones básicas).
* Funcionalidades de extensión futura (Anexo II).

## 3. Niveles de Prueba

Se aplicarán los siguientes niveles de prueba:

* **Pruebas Unitarias:** Realizadas por los desarrolladores para verificar componentes individuales (funciones, métodos, clases) en aislamiento, tanto en el Backend (ATS y Core AI) como en el Frontend. Cobertura mínima objetivo: 70-80%.
* **Pruebas de Integración:**
   * Verificar la correcta comunicación y flujo de datos entre ATS MVP y el monolito Core AI a través de la API interna (TK-001).
   * **Verificar la interacción entre los módulos internos del monolito Core AI.**
   * Verificar la interacción de Core AI con el Proveedor LLM externo.
   * Verificar la interacción de los servicios backend (ATS y Core AI) con sus respectivas bases de datos.
* **Pruebas de Sistema / E2E (End-to-End):** Simular flujos de usuario completos basados en las User Stories para verificar la funcionalidad del sistema integrado como un todo.
* **Pruebas de API:** Probar directamente los endpoints API expuestos por ATS MVP y Core AI usando herramientas como Postman/Insomnia o frameworks de automatización.

## 4. Tipos de Prueba

* **Pruebas Funcionales:**
    * **Basadas en Requisitos:** Verificar cada RF y los criterios de aceptación de cada US.
    * **Basadas en Tareas Técnicas:** Usar los criterios de aceptación técnicos de los TKs para pruebas más granulares.
    * **Priorización:** Enfocarse primero en las features "Must Have", luego "Should Have", y finalmente "Could Have".
* **Pruebas No Funcionales:**
   * **Rendimiento:** Pruebas básicas de tiempos de respuesta (RNF-01 a RNF-03) bajo carga simulada moderada (RNF-04), validando el rendimiento del **monolito Core AI en Azure Container Apps**.
* **Seguridad:** Verificaciones de autenticación, autorización por roles, protección básica contra OWASP Top 10 (ej. SQL Injection, XSS), seguridad de API Keys (RNF-07 a RNF-14).
    * **Usabilidad:** Evaluación heurística de la interfaz del ATS y Portal, y feedback durante UAT (RNF-15 a RNF-19).
    * **Fiabilidad:** Pruebas de manejo de errores y validación del plan básico de backup/restore (RNF-20 a RNF-23B).
* **Pruebas de Regresión:** Ejecutar un conjunto definido de casos de prueba (manuales y/o automatizados) antes de cada release o cambio significativo para asegurar que no se han introducido nuevos defectos.
* **Pruebas de Aceptación de Usuario (UAT) / Piloto:** Realizadas por los usuarios finales definidos (Reclutadores, Hiring Managers internos) en un entorno controlado (Staging o Piloto) para validar la funcionalidad y usabilidad desde su perspectiva. El feedback recogido aquí será crucial (ver PRD Sec 13).

## 5. Estrategia de Automatización

* **Pruebas Unitarias:** Automatizadas.
* **Pruebas de API:** Alta prioridad para automatización (ATS y Core AI).
* **Pruebas de Integración (API Layer):** Automatizar pruebas que verifiquen la comunicación ATS <-> Core AI (API externa de Core AI). **Las pruebas de integración internas de Core AI ahora prueban llamadas a métodos/servicios, no APIs.**
* **Pruebas E2E:** Automatizar flujos críticos principales.
* **Pruebas de Regresión:** Suite automatizada.
* **Pruebas Manuales:** Necesarias para pruebas exploratorias, de usabilidad, validación inicial de nuevas features, y escenarios complejos difíciles de automatizar.

## 6. Enfoque Específico para Pruebas de IA (Core AI)

Dado que la validación de la IA es un objetivo clave, se requiere un enfoque específico:

* **Calidad de Datos de Entrada:** Usar un conjunto diverso y representativo de CVs (diferentes formatos, estructuras, calidades, idiomas si aplica aunque limitado en Fase 1) para probar el parsing (US-020).
* **Precisión de Parsing (RF-11):** Medir la precisión (Precision/Recall/F1) en la extracción de campos clave (skills, experiencia, educación) contra un conjunto de datos etiquetado manualmente (ground truth).
* **Calidad de Generación JD (RF-06):** Evaluación cualitativa por parte de Reclutadores/Expertos sobre la relevancia, coherencia y completitud de las JDs generadas para diferentes inputs.
* **Validación de Scoring (RF-12):**
    * **Consistencia:** Verificar que la misma entrada produce el mismo score.
    * **Correlación:** Comparar el ranking/score generado por la IA con una evaluación realizada por reclutadores expertos sobre un conjunto de candidatos de prueba. Medir correlación (ej. Spearman, Kendall Tau) o precisión en clasificación (ej. % acuerdo en Top-K).
    * **Análisis de Errores:** Investigar casos donde el score IA difiere significativamente de la evaluación humana para entender y mejorar el algoritmo.
* **Validación de Sugerencias:** Verificar que la `etapa_sugerida` (RF-12C) sea consistente con el `score` y el `evaluacion_corte` configurado.
* **Validación del Feedback:** Asegurar que el feedback enviado por el ATS (US-038) se almacena correctamente (US-039) y que existe un plan (aunque sea futuro) para utilizarlo en la mejora de modelos.

## 7. Entornos, Datos y Herramientas

* **Entornos:** Se necesitarán entornos separados para Desarrollo, QA/Staging y Producción. Core AI se desplegará en **Azure Container Apps** en estos entornos.
* **Datos de Prueba:** Se creará un conjunto de datos de prueba que incluya:
    * Usuarios con diferentes roles.
    * Vacantes con diferentes estados y configuraciones IA.
    * Un conjunto diverso de CVs (PDF, DOCX) con perfiles variados (diferentes niveles de experiencia, skills, formatos) para probar la IA. Algunos CVs tendrán "ground truth" para validación de parsing/scoring.
    * Candidaturas en diferentes etapas del pipeline.
* **Herramientas (Sugeridas):**
    * **Gestión de Casos de Prueba y Defectos:** Jira, TestRail, Xray, etc.
    * **Pruebas de API:** Postman, Insomnia, o frameworks de automatización (RestAssured, Karate).
    * **Automatización UI:** Cypress, Selenium, Playwright.
    * **Pruebas de Carga:** k6, JMeter (para pruebas básicas de NFRs).
   * **CI/CD:** Jenkins, GitLab CI, GitHub Actions (configurados para construir y desplegar en **ACA** para Core AI).

## 8. Roles y Responsabilidades

* **Desarrolladores:** Pruebas unitarias, corrección de defectos, soporte en pruebas de integración.
* **Equipo QA / QA Lead:** Definición del plan detallado y casos de prueba, ejecución de pruebas manuales (funcionales, exploratorias, usabilidad), desarrollo y mantenimiento de pruebas automatizadas (API, E2E, Regresión), gestión de defectos, reporte de estado de calidad.
* **Product Owner / Stakeholders:** Participación en UAT, definición de criterios de aceptación, priorización de defectos.
* **Usuarios Piloto (Reclutadores, Managers):** Ejecución de UAT, proporcionar feedback cualitativo.

## 9. Criterios de Entrada y Salida

* **Criterios de Entrada:** Build desplegado en entorno de pruebas (ATS y Core AI en ACA).
* **Criterios de Salida (para release/piloto):** Todos los casos de prueba de alta prioridad ejecutados y superados, sin defectos críticos o bloqueantes abiertos, cobertura de pruebas definida alcanzada, UAT completada con éxito (o issues aceptados).

## 10. Gestión de Defectos

* Se utilizará una herramienta de seguimiento (ej. Jira).
* Los defectos se reportarán con información clara (pasos para reproducir, resultado esperado vs. actual, severidad, prioridad).
* Se realizarán reuniones periódicas de triaje de defectos para su priorización y asignación.
* Se realizará seguimiento hasta la resolución y verificación del defecto.

## 11. Planes de Prueba Detallados por Feature

A continuación se enlaza el desglose de la estrategia y casos de prueba específicos para los principales grupos de funcionalidades (features) identificados:

*   [Feature 7: Administración y Configuración del Sistema](./pp-feature-7.md)
*   [Feature 1: Gestión del Ciclo de Vida de la Vacante](./pp-feature-1.md)
*   [Feature 3: Portal de Empleo y Proceso de Aplicación](./pp-feature-3.md)
*   [Feature 2: Asistencia IA para Descripción de Puesto (JD)](./pp-feature-2.md)
*   [Feature 4: Evaluación Inteligente de Candidaturas](./pp-feature-4.md)
*   [Feature 5: Visualización y Gestión del Pipeline de Selección](./pp-feature-5.md)
*   [Feature 6: Sistema de Feedback para IA](./pp-feature-6.md)
*   [Feature 8: Mejoras Adicionales de Usabilidad y Funcionalidad](./pp-feature-8.md)
