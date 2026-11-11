# Base de conocimiento — Ingeniería catastral y geodesia

Este directorio es el **cuerpo de conocimiento** que consultan los agentes.
Cada especialista lee solo su carpeta.

```
conocimiento/
├─ indice.md                  ← este archivo (mapa general)
├─ catastro/
│  ├─ 01-conceptos.md         ← Agente A
│  └─ 02-procesos.md          ← Agente A
└─ geodesia/
   ├─ 01-sistemas-referencia.md   ← Agente B
   └─ 02-gnss-transformaciones.md ← Agente B
```

## Reglas de la base

1. Solo se afirma lo que está escrito aquí. Si un dato no está, el agente lo
   dice y nombra el documento que faltaría.
2. Cada archivo empieza con una línea `Fuente:` con la norma o referencia de la
   que sale el contenido.
3. Al añadir un tema nuevo: crear el archivo en la carpeta del dominio y
   enlazarlo en este índice. No hace falta tocar los agentes.

## Mapa de temas

| Tema | Dominio | Archivo |
| --- | --- | --- |
| Parcela, polígono, referencia catastral, linderos | Catastro | `catastro/01-conceptos.md` |
| Superficie, valor catastral, deslinde, concentración parcelaria | Catastro | `catastro/02-procesos.md` |
| Datum, ETRS89, WGS84, EPSG, UTM, geoide | Geodesia | `geodesia/01-sistemas-referencia.md` |
| GNSS, RTK, nivelación, transformación de coordenadas, tolerancias | Geodesia | `geodesia/02-gnss-transformaciones.md` |
