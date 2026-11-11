# Cuerpo de conocimiento de ingeniería catastral y geodesia

Sistema multiagente local orquestado por **OpenCode**. Un enrutador (agente C)
recibe la consulta, decide si es de catastro o de geodesia y la delega al
especialista local correspondiente.

## Flujo de trabajo

```
                          ┌──────────────────────────────────────┐
   pregunta del usuario   │        AGENTE C · ENRUTADOR          │
   ──────────────────────►│  opencode/deepseek-v4.1-flash-free   │
                          │  (nube · gratuita)                   │
                          │                                      │
                          │  1. reformula la consulta            │
                          │  2. clasifica el dominio             │
                          │  3. imprime el bloque de enrutado    │
                          │  4. llama a task(subagent_type=...)  │
                          └──────────────┬───────────────────────┘
                                         │
              ┌──────────────────────────┼──────────────────────────┐
              │ CATASTRO                 │ GEODESIA                 │ MIXTA
              ▼                          ▼                          ▼
   ┌────────────────────┐     ┌────────────────────┐    llama a los dos
   │ AGENTE A           │     │ AGENTE B           │    y une secciones
   │ catastro           │     │ geodesia           │
   │ catastro-qwen      │     │ geodesia-qwen      │
   │ 4.7 GB · local     │     │ 4.7 GB · local     │
   └─────────┬──────────┘     └─────────┬──────────┘
             │ lee                      │ lee
             ▼                          ▼
   conocimiento/catastro/*     conocimiento/geodesia/*
             │                          │
             └────────────┬─────────────┘
                          ▼
              C integra y responde
              "Respondido por: agente-..."
```

## Modelos elegidos (objetivo: 8 GB de RAM)

| Agente | Modelo | Variante | Tamaño | Por qué |
| --- | --- | --- | --- | --- |
| A | **Qwen** 2.5 7B | `catastro-qwen:latest` (Q4_K_M) | 4,7 GB | responde bien en español técnico y cabe en 8 GB con 16k de contexto |
| B | **Qwen** 2.5 7B | `geodesia-qwen:latest` (Q4_K_M) | 4,7 GB | la más equilibrada de Qwen que cabe en 8 GB con contexto |
| C | DeepSeek V4.1 Flash | `opencode/deepseek-v4.1-flash-free` | nube | gratuita, rápida y fiable para enrutar |

Los dos modelos locales son derivados con contexto fijado a 16k, porque el
contexto por defecto de Ollama (4k) es demasiado pequeño para el prompt de
OpenCode:

```powershell
ollama pull qwen2.5:7b

# contextos de 16k para el prompt del sistema de OpenCode
ollama create catastro-qwen:latest  -f Modelfile.catastro   # FROM qwen2.5:latest + num_ctx 16384
ollama create geodesia-qwen:latest  -f Modelfile.geodesia   # FROM qwen2.5:latest + num_ctx 16384
```

Nota sobre Ornith: se probó `venkataparswanadh/ornith:9b-q2` (3,8 GB) y se
descartó. Esa cuantización Q2_K está rota: devuelve `content: ""` con
`completion_tokens: 1` y, en modo generate, solo repite el prompt. No existe
publicada una variante Q4 ni Q8 de ese modelo.

Alternativas válidas si sobra RAM:
- Qwen `qwen3.5:9b` (6,6 GB): más capaz, pero deja poco margen.

Nunca se cargan A y B a la vez si limitas Ollama a un modelo:

```powershell
[Environment]::SetEnvironmentVariable("OLLAMA_MAX_LOADED_MODELS", "1", "User")
```

## Uso

```powershell
cd C:\Users\yherm\catastro-geodesia-agentes
opencode
```

El agente por defecto es `agente-c-enrutador`. También existe el atajo:

```
/consulta ¿Qué diferencia hay entre referencia catastral y número de finca?
```

## Ejemplo de sesión

```
┌─ ENRUTADOR ─────────────────────────────────────────────
│ Consulta: diferencia entre referencia catastral y finca registral
│ Señales:  referencia catastral, finca registral, catastro
│ Destino:  agente-a-catastro
│ Motivo:   identificadores de inmuebles, dominio catastral
└─────────────────────────────────────────────────────────

## Catastro

**Definición** ...

Respondido por: agente-a-catastro
```

## Estructura

```
catastro-geodesia-agentes/
├─ opencode.json                  configuración del proyecto (provider Ollama)
├─ AGENTS.md                      contexto que OpenCode carga siempre
├─ README.md                      este archivo
├─ .opencode/
│  ├─ agent/
│  │  ├─ agente-c-enrutador.md
│  │  ├─ agente-a-catastro.md
│  │  └─ agente-b-geodesia.md
│  └─ command/consulta.md
└─ conocimiento/
   ├─ indice.md
   ├─ catastro/01-conceptos.md
   ├─ catastro/02-procesos.md
   ├─ geodesia/01-sistemas-referencia.md
   └─ geodesia/02-gnss-transformaciones.md
```

## Cómo ampliar

- Nuevo tema de catastro: añade un `.md` en `conocimiento/catastro/` y enlázalo
  en `conocimiento/indice.md`.
- Nuevo especialista: copia un agente de `.opencode/agent/`, cambia `model`,
  `description` y su dominio, y añádelo al prompt del enrutador.
- Cambiar C a local: en `.opencode/agent/agente-c-enrutador.md` pon
  `model: ollama/geodesia-qwen:latest`. Requiere reiniciar OpenCode.
