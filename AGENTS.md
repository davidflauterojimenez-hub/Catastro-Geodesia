# Cuerpo de conocimiento: ingeniería catastral y geodesia

Este proyecto es un **cuerpo de conocimiento** que responde consultas sobre
catastro y geodesia. Tres agentes de OpenCode trabajan en cadena:

```
                     ┌─────────────────────────────┐
   tu pregunta ─────►│  AGENTE C · enrutador       │
                     │  (nube, gratis)             │
                     │  1. reformula               │
                     │  2. clasifica               │
                     │  3. muestra el bloque       │
                     │  4. delega con task         │
                     └───────┬─────────────┬───────┘
                             │             │
              consulta de    │             │   consulta de
              catastro       ▼             ▼   geodesia
                    ┌────────────────┐  ┌────────────────┐
                    │ AGENTE A       │  │ AGENTE B       │
                    │ catastro       │  │ geodesia       │
                     │ Qwen 2.5 7B    │  │ Qwen 2.5 7B    │
                     │ local 4.7 GB   │  │ local 4.7 GB   │
                    └────────┬───────┘  └────────┬───────┘
                             │                   │
                             └────────┬──────────┘
                                      ▼
                          conocimiento/ (base local)
                          catastro/ + geodesia/
                                      ▼
                          respuesta integrada por C
```

## Agentes

| Agente | Archivo | Modelo | Dónde | Tamaño |
| --- | --- | --- | --- | --- |
| C · enrutador | `.opencode/agent/agente-c-enrutador.md` | `opencode/deepseek-v4.1-flash-free` | nube gratuita | — |
| A · catastro | `.opencode/agent/agente-a-catastro.md` | `catastro-qwen:latest` (Ollama) | local | 4.7 GB |
| B · geodesia | `.opencode/agent/agente-b-geodesia.md` | `geodesia-qwen:latest` (Ollama) | local | 4.7 GB |

Por qué estas variantes caben en 8 GB de RAM: los dos agentes usan Qwen 2.5 7B en
cuantización Q4_K_M (4.7 GB cada uno), con `num_ctx` limitado a 16k. Nunca se
cargan los dos a la vez si Ollama está limitado a un modelo:

```powershell
# una sola vez, para que Ollama descargue un modelo antes de cargar el otro
[Environment]::SetEnvironmentVariable("OLLAMA_MAX_LOADED_MODELS", "1", "User")
```

## Uso

```powershell
cd C:\Users\yherm\catastro-geodesia-agentes
opencode
```

Dentro de OpenCode, el agente C es el agente por defecto. También puedes usar el
atajo:

```
/consulta ¿Qué diferencia hay entre referencia catastral y número de parcela?
```

## Reglas del sistema

- El agente C **nunca** responde de dominio: siempre delega.
- El agente A solo responde catastro; si le preguntas geodesia, deriva a B.
- El agente B solo responde geodesia; si le preguntas catastro, deriva a A.
- Las consultas mixtas se envían a los dos y C integra en dos secciones.
- Los agentes citan el archivo de `conocimiento/` que usaron.
