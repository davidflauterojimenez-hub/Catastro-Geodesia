# Bitácora del proyecto — Cuerpo de conocimiento de ingeniería catastral y geodesia

- **Estudiantes:** Yherman Flautero y Maicol Torres (OpenCode School)
- **Proyecto:** sistema multiagente local orquestado por OpenCode
- **Fecha de construcción:** 10 de noviembre de 2026
- **Repositorio:** `catastro-geodesia-agentes`

---

## 1. Objetivo

Construir un sistema de consulta en español que responda preguntas de
**catastro** y **geodesia** usando agentes especializados que corren en local
(Ollama) y que solo responden con lo escrito en una base de conocimiento
propia. Restricción dura: **8 GB de RAM** en el equipo de trabajo.

---

## 2. Cronología de trabajo

Todo el trabajo se realizó el 10 de noviembre de 2026, en cuatro fases que
coinciden con los commits del repositorio.

### Fase 1 · Diseño y entrega inicial (commit `b99ba34`, 19:11)

- Se diseñó la arquitectura de tres agentes en cadena:

  | Agente | Rol | Modelo | Dónde |
  | --- | --- | --- | --- |
  | **C** · enrutador | recibe la consulta, clasifica y delega | `opencode/deepseek-v4.1-flash-free` | nube gratuita |
  | **A** · catastro | especialista de dominio | `catastro-qwen:latest` (Qwen 2.5 7B, Q4_K_M) | local · 4,7 GB |
  | **B** · geodesia | especialista de dominio | `geodesia-qwen:latest` (Qwen 2.5 7B, Q4_K_M) | local · 4,7 GB |

- Se escribió la base de conocimiento `conocimiento/` con 5 archivos:
  - `catastro/01-conceptos.md` (parcela, polígono, referencia catastral, linderos)
  - `catastro/02-procesos.md` (superficie, valor catastral, deslinde, concentración parcelaria)
  - `geodesia/01-sistemas-referencia.md` (datum, ETRS89, WGS84, EPSG, UTM, geoide)
  - `geodesia/02-gnss-transformaciones.md` (GNSS, RTK, nivelación, tolerancias)
  - `indice.md` con el mapa de temas y las reglas de la base
- Cada archivo de conocimiento empieza con una línea `Fuente:` con la norma o
  referencia de origen, para que las respuestas sean verificables.
- Se crearon los archivos de agente (`.opencode/agent/`), el comando de atajo
  `/consulta` (`.opencode/command/consulta.md`) y la configuración del proveedor
  Ollama en `opencode.json`.
- Se escribió `AGENTS.md` con el contexto global del proyecto y `README.md` con
  el diagrama de flujo y las instrucciones de uso.

### Fase 2 · Limpieza del repositorio (commit `43fcf59`, 19:28)

- Se eliminó la carpeta `.opencode/` del repositorio porque contenía
  `node_modules` y artefactos locales que no aportan nada en GitHub
  (cada usuario los regenera con `npm install`).

### Fase 3 · Carpeta de configuración portable (commit `6a76e80`, 19:36)

- Se creó `config_agentes/` con una copia limpia de los tres agentes, el
  comando `/consulta`, el `.gitignore` y el `package.json` de dependencias,
  para que cualquier persona (por ejemplo, el profesor) pueda llevarse el
  contenido a su propio `.opencode` local y ejecutar el sistema sin tocar
  nada más.

### Fase 4 · Instrucciones de ejecución (commit `310bbe5`, 19:42)

- Se amplió el `README.md` con la sección "Instrucciones de ejecución para el
  profesor": clonar, copiar `config_agentes` a `.opencode`, `npm install` y
  ejecutar `opencode`.

---

## 3. Decisiones técnicas

| Decisión | Motivo |
| --- | --- |
| Enrutador (C) en nube gratuita y especialistas (A, B) en local | el enrutado solo necesita clasificar y delegar (tarea ligera); el conocimiento de dominio pesa y debe correr sin coste en local |
| Qwen 2.5 7B con cuantización Q4_K_M (4,7 GB) | es la variante más capaz que cabe en 8 GB de RAM dejando margen para el contexto |
| Dos variantes derivadas (`catastro-qwen`, `geodesia-qwen`) con `num_ctx` 16384 | el contexto por defecto de Ollama (4k) es demasiado pequeño para el prompt de sistema de OpenCode y los agentes fallaban |
| `OLLAMA_MAX_LOADED_MODELS=1` | así Ollama nunca carga A y B a la vez y los 4,7 GB del modelo no se duplican en RAM |
| Cada especialista solo puede leer (`read`, `glob`, `grep`) y tiene `bash`, `edit`, `task`, `webfetch` denegados | los modelos 7B son poco fiables: se les encierra en su dominio y en la base local |
| El enrutador verifica cifras contra el archivo `Fuente:` citado | los modelos 7B fallan en aritmética; C (nube) es quien garantiza que lo publicado coincide con la base |
| Regla dura en los especialistas: "toda cifra debe aparecer literalmente en el archivo leído" | reduce alucinaciones numéricas en un dominio (catastro/geodesia) donde los números importan |

---

## 4. Problemas encontrados y soluciones

| # | Problema | Diagnóstico | Solución |
| --- | --- | --- | --- |
| 1 | Se probó `venkataparswanadh/ornith:9b-q2` (3,8 GB) como candidato local y no respondía | la cuantización Q2_K está rota: devuelve `content: ""` con `completion_tokens: 1` y en modo generate solo repite el prompt; no existe variante Q4 ni Q8 publicada | se descartó Ornith y se pasó a Qwen 2.5 7B Q4_K_M |
| 2 | Los agentes locales no cargaban el prompt de sistema de OpenCode | el contexto por defecto de Ollama (4k tokens) es menor que el prompt | se crearon variantes derivadas con `Modelfile` (`FROM qwen2.5:latest` + `num_ctx 16384`) mediante `ollama create` |
| 3 | Riesgo de quedarse sin RAM con dos modelos de 4,7 GB | Ollama mantiene cargado el último modelo | `[Environment]::SetEnvironmentVariable("OLLAMA_MAX_LOADED_MODELS", "1", "User")` |
| 4 | El repositorio subía `node_modules` y archivos locales de `.opencode` | dependencias regenerables, no fuente | se sacó `.opencode` del repo y se creó `config_agentes/` como copia portable |
| 5 | Quien clonara el repo no podría ejecutar el sistema | faltaban los agentes y dependencias fuera de `.opencode` | `config_agentes/` + instrucciones paso a paso en el README |

---

## 5. Verificación

- Se comprobó con `ollama list` que existen las dos variantes derivadas:
  `catastro-qwen:latest` y `geodesia-qwen:latest` (4,7 GB cada una), además de
  la base `qwen2.5:latest` y los modelos Ornith descartados.
- Se ejecutaron consultas de prueba de cada tipo por el flujo completo:
  - consulta de **catastro** → enrutada a `agente-a-catastro`;
  - consulta de **geodesia** → enrutada a `agente-b-geodesia`;
  - consulta **mixta** → los dos especialistas y respuesta integrada en dos
    secciones (`## Catastro` y `## Geodesia`).
- El enrutador imprime siempre el bloque `┌─ ENRUTADOR ─...` con consulta,
  señales, destino y motivo antes de delegar, y cierra cada respuesta con
  `Respondido por: <agente>`.
- El flujo git quedó limpio en cuatro commits con mensajes descriptivos.

---

## 6. Resultado final

```
catastro-geodesia-agentes/
├─ opencode.json                  provider Ollama + modelos locales
├─ AGENTS.md                      contexto global del proyecto
├─ README.md                      documentación y flujo
├─ BITACORA.md                    este documento
├─ config_agentes/                configuración portable (para ejecutar)
│  ├─ agente-a-catastro.md
│  ├─ agente-b-geodesia.md
│  ├─ agente-c-enrutador.md
│  ├─ consulta.md                 comando /consulta
│  ├─ package.json / package-lock.json / .gitignore
└─ conocimiento/                  base de conocimiento citable
   ├─ indice.md
   ├─ catastro/01-conceptos.md
   ├─ catastro/02-procesos.md
   ├─ geodesia/01-sistemas-referencia.md
   └─ geodesia/02-gnss-transformaciones.md
```

## 7. Cómo reproducirlo

```powershell
git clone <este repositorio>
cd catastro-geodesia-agentes

# 1. Modelos locales (una vez)
ollama pull qwen2.5:7b
# crear las variantes con num_ctx 16384 (ver README)

# 2. Configurar OpenCode
#    copiar el contenido de config_agentes/ a .opencode/
npm install   # dentro de .opencode

# 3. Ejecutar
opencode
# y consultar con:
# /consulta ¿Qué diferencia hay entre referencia catastral y número de parcela?
```

## 8. Lecciones aprendidas

1. **Probar siempre la cuantización antes de construir encima**: que un modelo
   exista en Ollama no garantiza que funcione (caso Ornith Q2_K).
2. **El contexto por defecto de Ollama (4k) no basta para OpenCode**: derivar
   el modelo con `num_ctx` explícito evita fallos silenciosos.
3. **Un modelo 7B local es fiable solo si se le encierra**: permisos mínimos,
   dominio acotado y reglas duras sobre cifras reducen las alucinaciones.
4. **Separar el conocimiento de los agentes**: añadir un tema nuevo es
   añadir un `.md` y enlazarlo en `indice.md`, sin tocar ningún agente.
5. **El repo debe ser reproducible por terceros**: `config_agentes/` +
   instrucciones hacen que cualquiera ejecute el sistema en su máquina.
