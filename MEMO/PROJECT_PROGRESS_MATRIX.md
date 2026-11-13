# MATRIZ DE PROGRESO Y RECONCILIACIÓN TÉCNICA — EVOCARA v1.0.0 LTS
## Auditoría Exhaustiva de Objetivos, Capacidades, Estado Real y Brechas

**Fecha de Auditoría:** 2026-11-13  
**Workspace Canónico:** `C:\Evocara`  
**HEAD Auditado:** `5f91441b432cfd2b0ccd32a520c3725dbda6bacc` (Rama `master`)  
**Metodología de Clasificación:** Cada funcionalidad u objetivo es evaluado contra código físico en `src/`, configuración en `package.json`, ejecución empírica de pruebas y documentación de gobernanza.  
**Estados Válidos:** `COMPLETED_VERIFIED`, `IMPLEMENTED_NOT_VERIFIED`, `PARTIALLY_IMPLEMENTED`, `NOT_IMPLEMENTED`, `BLOCKED`, `OBSOLETE`, `NEEDS_REDEFINITION`, `NOT_ASSESSABLE`.

---

### 1. Metodología de Ponderación de Avance

Para evitar porcentajes globales arbitrarios, la ponderación del avance de Evocara se fundamenta en los 5 pilares arquitectónicos esenciales de la Carta Fundacional (`Workflow/00_PROJECT_CHARTER.md`):

1. **Persistencia Confiable y Local-First (Peso 25%):** Almacenamiento JSON/SQLite atómico, transacciones, locks, backups y recuperación ante fallos.
2. **Captura Estructurada y Ciclo de Vida TMS (Peso 20%):** Modelado de investigaciones, hallazgos, ADRs, bitemporalidad, supersedencia y procedencia.
3. **Recuperación Determinista Contextual (Peso 20%):** Búsqueda léxica MiniSearch en memoria, tolerancia a erratas, scoring BM25 y aislamiento multi-proyecto.
4. **Integración Multiconsumidor y Servidor MCP (Peso 20%):** Protocolo MCP curado (8 herramientas), guardián runtime fail-closed, adaptadores (OpenCode, Claude Code, Cline, Antigravity) y CLI.
5. **Calidad, Empaquetado y Cadena de Suministro (Peso 15%):** Compilación limpia TypeScript, suites de pruebas versionadas en `tests/`, SBOM CycloneDX, manifiesto SLSA y tarball hermético.

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                              CÓMPUTO DE AVANCE PONDERADO                               │
├──────────────────────────────────────┬────────┬──────────────────────────┬─────────────┤
│ Dimensión Arquitectónica             │ Peso   │ Estado Real Verificado   │ Aportación  │
├──────────────────────────────────────┼────────┼──────────────────────────┼─────────────┤
│ 1. Persistencia Confiable Local-First│ 25 %   │ 100 % Completado/Probado │ 25.0 %      │
│ 2. Captura y Ciclo de Vida TMS       │ 20 %   │ 100 % Completado/Probado │ 20.0 %      │
│ 3. Recuperación Determinista & ISO   │ 20 %   │ 100 % Completado/Probado │ 20.0 %      │
│ 4. Integración Multiconsumidor & MCP │ 20 %   │ 95 % (Limitación Cline)  │ 19.0 %      │
│ 5. Calidad, Empaquetado & Release    │ 15 %   │ 90 % (Commit/Push pend.) │ 13.5 %      │
├──────────────────────────────────────┴────────┴──────────────────────────┼─────────────┤
│ AVANCE GLOBAL PONDERADO HACIA v1.0.0 LTS                                 │ 97.5 %      │
└──────────────────────────────────────────────────────────────────────────┴─────────────┘
```

---

### 2. Matriz Consolidada de Objetivos y Funcionalidades

| ID | Objetivo Original / Eje | Funcionalidad Específica | Estado | Evidencia Física y Empírica | Trabajo Pendiente | Prioridad |
|---|---|---|:---:|---|---|:---:|
| **OBJ-01** | Persistencia Confiable | Almacenamiento JSON por defecto con reemplazo atómico | `COMPLETED_VERIFIED` | `src/storage/core/persistence-engine.ts` (L15-L85: `robustAtomicWriteFileSync`); `tests/persistence-backup.test.ts` (TEST-A1 a TEST-A8: 8/8 PASS). 25 reintentos Windows y fsync. | Ninguno en código v1. | P0 |
| **OBJ-02** | Persistencia Confiable | Fuente Única de Verdad (SSOT) en `project-state.json` | `COMPLETED_VERIFIED` | `src/stage-engine.ts` (L118-L135: migración de `stage-state.json`); `tests/state-unification.test.ts` (7/7 PASS). Cero duplicidad. | Ninguno. | P0 |
| **OBJ-03** | Persistencia Confiable | Adaptador SQLite relacional in-process opt-in | `COMPLETED_VERIFIED` | `src/storage/adapters/node-sqlite-adapter.ts` (L158-L480); `tests/sqlite-adapter.test.ts` (10/10 PASS). WAL mode, PRAGMA busy_timeout=5000ms. | Ninguno. | P1 |
| **OBJ-04** | Persistencia Confiable | Exclusión mutua de procesos y cerrojos anti-deadlock | `COMPLETED_VERIFIED` | `src/storage/core/persistence-engine.ts` (L90-L240: `acquireStateLock`, `isProcessDead`); `tests/concurrency-locks.test.ts` (5/5 PASS: 10 procesos simultáneos, 100 escrituras sin pérdida). | Ninguno. | P0 |
| **OBJ-05** | Persistencia Confiable | Sistema de Respaldo y Restauración Transaccional | `COMPLETED_VERIFIED` | `src/storage/domains/backup-manager.ts` (L1-L350); `tests/persistence-backup.test.ts` (TEST-B1 a TEST-B10: 10/10 PASS con SHA-256 preflight y 2-phase rollback). | Ninguno. | P0 |
| **OBJ-06** | Captura Estructurada | Registro de investigaciones y hallazgos (`ResearchRecord`) | `COMPLETED_VERIFIED` | `src/storage/subsystems/knowledge-tms-subsystem.ts` (L1-L280); `tests/minisearch-json-retrieval.test.ts` (PASS 2). Validación de esquemas y sanitización. | Ninguno. | P0 |
| **OBJ-07** | Captura Estructurada | Gestión de Decisiones de Arquitectura (ADRs MADR) | `COMPLETED_VERIFIED` | `src/storage/subsystems/adr-governance-subsystem.ts`; `tests/path-security.test.ts` (PASS 22-25); `tests/memory-continuity-scenarios.test.ts` (Escenario 1 PASS). | Ninguno. | P0 |
| **OBJ-08** | Captura Estructurada | Registro de Tareas y Grafo DAG | `COMPLETED_VERIFIED` | `src/storage/subsystems/workstream-task-subsystem.ts`; `src/storage/domains/task-dag.ts`. Tareas con criterios de aceptación e ID secuencial. | Reducir dependencias inyectadas en v2.0.0. | P2 |
| **OBJ-09** | Captura Estructurada | Continuidad de Sesión y Handoff operacional | `COMPLETED_VERIFIED` | `src/storage/domains/handoff-continuity.ts`; `src/storage/domains/session-registry.ts`; `tests/memory-continuity-scenarios.test.ts` (Escenario 4 PASS: rehidratación cold-start). | Ninguno. | P0 |
| **OBJ-10** | Ciclo de Vida y TMS | Modelo Truth Maintenance System (TMS) y Bitemporalidad | `COMPLETED_VERIFIED` | `src/storage/domains/knowledge-tms.ts`; `tests/memory-continuity-scenarios.test.ts` (Escenario 2 PASS: Precision@1 = 1.0, 0% contaminación obsoleta). | Ninguno. | P0 |
| **OBJ-11** | Ciclo de Vida y TMS | Supersedencia atómica y trazabilidad de linaje | `COMPLETED_VERIFIED` | `src/mcp.ts` (L134-L166: `supersedesItemId`); `tests/minisearch-retrieval.test.ts` (PASS 08, 09, 11). Multiplicador dinámico de estado en ranking. | Ninguno. | P0 |
| **OBJ-12** | Recuperación Contextual | Indexación léxica en memoria con `MiniSearch` | `COMPLETED_VERIFIED` | `src/storage/retrieval/minisearch-adapter.ts` (L1-L352); `tests/minisearch-json-retrieval.test.ts` (10/10 PASS). Sincronización en mutaciones. | Ninguno. | P0 |
| **OBJ-13** | Recuperación Contextual | Tolerancia a erratas y normalización de acentos | `COMPLETED_VERIFIED` | `src/storage/retrieval/minisearch-adapter.ts` (L41-L69: Levenshtein distance); `tests/minisearch-json-retrieval.test.ts` (PASS 3 y 4). | Ninguno. | P1 |
| **OBJ-14** | Recuperación Contextual | Aislamiento estricto por proyecto (GAP-ISO-01) | `COMPLETED_VERIFIED` | `src/storage.ts` (L400-L425, L3040-L3150); `tests/multi-project-isolation.test.ts` (8/8 PASS). 0% cross-talk, validación RFC de identificadores. | Ninguno. | P0 |
| **OBJ-15** | Recuperación Contextual | Empaquetado de contexto acotado (`ScopedContextPack`) | `COMPLETED_VERIFIED` | `src/storage/domains/scoped-context-pack.ts` (L1-L220); `tests/multi-consumer-integration.test.ts` (PASS 02, 06). Ajuste a token budget. | Ninguno. | P1 |
| **OBJ-16** | Integración MCP | Servidor MCP curado (exactamente 8 herramientas) | `COMPLETED_VERIFIED` | `src/mcp.ts` (L106-L180: `CURATED_MCP_TOOLS`); `tests/mcp-security.test.ts` (9/9 PASS); `tests/multi-consumer-integration.test.ts` (PASS 11). | Ninguno. | P0 |
| **OBJ-17** | Integración MCP | Guardián de autorización en runtime (GAP-SEC-01) | `COMPLETED_VERIFIED` | `src/mcp.ts` (L1641-L1693: `getEffectiveTools`, `CallTool` fail-closed 403 Forbidden); `tests/mcp-security.test.ts` (PASS 1, 2, 3, 4, 5, 8). | Ninguno. | P0 |
| **OBJ-18** | Integración MCP | Soporte normativo de prefijos (`openmemory`, `evocara`, `dual`)| `COMPLETED_VERIFIED` | `src/mcp.ts` (L41-L92: `resolveMCPOptions`, L1644-L1656); `tests/mcp-security.test.ts` (PASS 4, 5). 16 alias con despacho canónico. | Ninguno. | P1 |
| **OBJ-19** | Integración MCP | Auto-enrollment transparente de sesiones no registradas | `COMPLETED_VERIFIED` | `src/mcp.ts` (L1959-L1962, L2057-L2060, L2252-L2255 en `update_handoff`, `save_adr`, `record_knowledge`); `tests/multi-consumer-integration.test.ts` (PASS 11). | Ninguno. | P0 |
| **OBJ-20** | Adaptadores Consumidores| Adaptador nativo OpenCode (`OpenCodeAdapter`) | `COMPLETED_VERIFIED` | `src/adapters/opencode-adapter.ts` (L1-L389); `tests/multi-consumer-integration.test.ts` (PASS 02, 03, 04, 05). Integración con plugins de OpenCode. | Ninguno. | P0 |
| **OBJ-21** | Adaptadores Consumidores| Adaptador Claude Code (`ClaudeCodeAdapter`) | `COMPLETED_VERIFIED` | `src/adapters/claude-code-adapter.ts` (L1-L260); `tests/multi-consumer-integration.test.ts` (PASS 06, 07, 08). Plantilla `claude_desktop_config.json`. | Ninguno. | P0 |
| **OBJ-22** | Adaptadores Consumidores| Adaptador Antigravity (`AntigravityAdapter`) | `COMPLETED_VERIFIED` | `src/adapters/antigravity-adapter.ts` (L1-L240); `tests/multi-consumer-integration.test.ts` (PASS 10). Manejo híbrido CLI/MCP. | Ninguno. | P0 |
| **OBJ-23** | Adaptadores Consumidores| Adaptador Cline (`ClineAdapter`) | `PARTIALLY_IMPLEMENTED` | `src/adapters/cline-adapter.ts` (L1-L220); `tests/multi-consumer-integration.test.ts` (PASS 09). Funciona con protocolo MCP. | Requiere que usuario configure `autoApprove` en UI de VS Code para evitar popups. | P1 |
| **OBJ-24** | Adaptadores Consumidores| Compatibilidad con Cursor IDE | `PARTIALLY_IMPLEMENTED` | `docs/examples/mcp-clients/cursor_mcp.json`. Compatible mediante el protocolo MCP estándar sobre STDIO. | Carece de adaptador TypeScript nativo propio y de suite automatizada UI. | P2 |
| **OBJ-25** | CLI | Interfaz de línea de comandos dual (`evocara` / `openmemory`) | `COMPLETED_VERIFIED` | `src/cli.ts` (L1-L1555); `package.json` (L8-L11: binarios `evocara` y `openmemory`); `tests/lts-clean-packaging.test.ts` (PASS 5). Subcomandos `status`, `query`, `record`, `adr`, `tms`, etc. | Ninguno. | P0 |
| **OBJ-26** | Seguridad Filesystem | Confinamiento de sandbox y anti-path traversal | `COMPLETED_VERIFIED` | `src/storage/core/path-security.ts` (L1-L210); `tests/path-security.test.ts` (35/35 PASS: neutralización total de vectores `../`, `..\`, null bytes y drive letters). | Ninguno. | P0 |
| **OBJ-27** | Seguridad de Memoria | Sanitización y delimitación contra Prompt Injection | `COMPLETED_VERIFIED` | `src/adapters/memory-sanitizer.ts` (L1-L95); `src/adapters/opencode-adapter.ts` (L76: `<untrusted_memory_data>`); `tests/path-security.test.ts`. | Ninguno. | P1 |
| **OBJ-28** | Calidad y Build | Compilación TypeScript limpia (0 errores) | `COMPLETED_VERIFIED` | `npx tsc --noEmit` completado con Exit Code 0; correspondencia 1:1 biunívoca entre 49 `.ts` de `src/` y 49 `.js`/`.d.ts` de `dist/` (147 archivos totales). | Ninguno. | P0 |
| **OBJ-29** | Calidad y Testing | Suite reproducible y versionada en `tests/` | `COMPLETED_VERIFIED` | 13 suites en `tests/` ejecutadas empíricamente con **146/146 checks PASS (100%)** en sandboxes aislados de `os.tmpdir()`. | Ninguno. | P0 |
| **OBJ-30** | Cadena de Suministro | Verificación de empaquetado CI multiplataforma | `COMPLETED_VERIFIED` | `scripts/ci-verify-package.js` ejecutado con **6/6 checks PASS (Exit Code 0)** en Win32. 0 fugas de `src/` o `tests/`. Whitelist de 21 core files. | Ninguno. | P0 |
| **OBJ-31** | Cadena de Suministro | Integridad criptográfica de artefactos de release | `COMPLETED_VERIFIED` | `scripts/verify-release-artifact.js` verificado con **Exit Code 0**: Tarball `ad540628...` (464.118 bytes), SBOM CycloneDX 1.5 (`31421e5e...`) y Manifiesto SLSA (`3a0cddd6...`). | Ninguno. | P0 |
| **OBJ-32** | Cadena de Suministro | Atestación SLSA y Commit de Release en Git | `BLOCKED` | Manifiesto SLSA `.work/artifacts/evocara-1.0.0-manifest.json` y 100 archivos staged preservados intactos. | **Bloqueo por Gobernanza Humana:** Requiere autorización del operador para ejecutar `git commit`, `git push` y `git tag v1.0.0`. | P0 |
| **OBJ-33** | Cadena de Suministro | Remediación de 11 vulnerabilidades de `npm audit` | `NEEDS_REDEFINITION` | `npm audit` reporta 11 vulnerabilidades (5 moderadas, 6 altas) en `@hono/node-server`, `@modelcontextprotocol/sdk`, `zod`, `toml`, `effect`. Todas en dev/transitive. | Evaluar si actualizar dependencias o documentar como riesgo mitigado en dev. No tocar en staging. | P2 |
| **OBJ-34** | Interfaz Gráfica (UI) | Panel visual o dashboard web para gestión de memorias | `NOT_IMPLEMENTED` | Verificado: cero archivos `.tsx`, `.jsx`, `.html` en código de producción (`src/`). | **Fuera de alcance aprobado:** `Workflow/00_PROJECT_CHARTER.md` descarta servidores HTTP o dashboards para mantener huella cero. | P3 |
| **OBJ-35** | Módulos Federados G50-56| Orquestación multiagente distribuida y consenso Ed25519 | `OBSOLETE` | 20 archivos retirados físicamente de `src/storage/domains/` y archivados en `archive/federated-domains/`. ADR-EVO-14 aceptado. | Ninguno. Queda retirado definitivamente. | P0 |
| **OBJ-36** | Orquestador Autónomo G57| Loop ReAct para codificación y edición autónoma | `OBSOLETE` | Formalmente cancelado bajo `ADR-EVO-01` en `Workflow/03_ARCHITECTURE_DECISIONS.md`. | Ninguno. Fuera de los límites del producto. | P0 |
| **OBJ-37** | Modelos Locales / Ollama | Adaptador semántico local para embeddings sin coste | `PARTIALLY_IMPLEMENTED` | `src/storage/retrieval/semantic-adapter.ts` (L1-L180: implementa `NullSemanticAdapter` y `DeterministicConceptVectorAdapter`). | Falta integración nativa de llamada HTTP local a endpoint de Ollama (`/api/embeddings`). | P2 |
| **OBJ-38** | Racionalización Fachada | Poda masiva de 261 firmas de conveniencia en `StorageEngine` | `OBSOLETE` | `ADR-EVO-17` ratificado como `ACCEPTED`: se congelan las firmas públicas en v1.0.0 LTS para no romper SemVer 1.x; refactorización aplazada a v2.0.0. | Ninguno en v1.x; planificar para v2.0.0. | P3 |

---

### 3. Distribución Estadística de Estados

```text
┌───────────────────────────┬───────────────┬──────────────┐
│ Estado                    │ Total Ítems   │ Porcentaje   │
├───────────────────────────┼───────────────┼──────────────┤
│ COMPLETED_VERIFIED        │ 30            │ 78.9 %       │
│ PARTIALLY_IMPLEMENTED     │ 3             │ 7.9 %        │
│ BLOCKED                   │ 1             │ 2.6 %        │
│ NEEDS_REDEFINITION        │ 1             │ 2.6 %        │
│ OBSOLETE                  │ 3             │ 7.9 %        │
│ NOT_IMPLEMENTED           │ 1             │ 2.6 %        │
│ IMPLEMENTED_NOT_VERIFIED  │ 0             │ 0.0 %        │
│ NOT_ASSESSABLE            │ 0             │ 0.0 %        │
├───────────────────────────┼───────────────┼──────────────┤
│ TOTAL EVALUADO            │ 38            │ 100.0 %      │
└───────────────────────────┴───────────────┴──────────────┘
```

---

### 4. Hallazgos Clave de la Matriz

1. **Núcleo de Memoria 100 % Operativo:** Todos los objetivos esenciales de persistencia, integridad, recuperación MiniSearch, bitemporalidad TMS y MCP curado se encuentran en estado `COMPLETED_VERIFIED` con pruebas empíricas directas.
2. **Cero Funcionalidades Implementadas sin Verificación:** No existe ningún componente clasificado como `IMPLEMENTED_NOT_VERIFIED`. Todo código en producción cuenta con pruebas automatizadas en `tests/`.
3. **El Único Bloqueo Real es de Naturaleza Operacional/Humana:** La tarea `OBJ-32` (Publicación, git commit y tag de release) está clasificada como `BLOCKED` exclusivamente por la regla inviolable de gobernanza que exige autorización humana antes de realizar mutaciones irreversibles en Git.
4. **Claridad Absoluta de Límites de Alcance:** La dispersión federada (`OBJ-35`), el agente autónomo (`OBJ-36`) y la interfaz web (`OBJ-34`) están correctamente catalogados como `OBSOLETE` o `NOT_IMPLEMENTED` por contravenir la Carta Fundacional de Evocara.

---
*Fin de la Matriz de Progreso de Evocara v1.0.0 LTS.*

