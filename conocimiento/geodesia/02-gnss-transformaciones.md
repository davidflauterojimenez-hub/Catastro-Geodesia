# Geodesia — GNSS, nivelación y transformaciones

Fuente: documentación técnica de IGS/EUREF, manuales de receptores GNSS y
especificaciones de transformación de EPSG (métodos de Helmert y rejilla).

## 1. GNSS: modos y precisiones típicas

| Modo | Cómo funciona | Precisión típica |
| --- | --- | --- |
| Autónomo | solo receptor | 3–10 m |
| DGPS | corrección de una estación | 0,5–2 m |
| RTK | corrección en tiempo real por radio/NTRIP | 1–3 cm |
| PPK | postproceso con efemérides | 1–2 cm |
| Estático | sesiones largas, postproceso | 5 mm + 1 ppm |

Requisitos de RTK:
1. Receptor con doble frecuencia.
2. Corrección de una red (NTRIP) o base propia.
3. Cielo despejado y PDOP bajo.
4. Distancia a la base razonable (menos de 20–30 km para redes VRS).

## 2. Transformación entre datums

Dos métodos habituales:

1. **Helmert (7 parámetros)**: 3 traslaciones, 3 rotaciones y 1 factor de
   escala. Precisión de metros a decímetros.
2. **Rejilla (grid shift)**: tabla de desplazamientos por zona. Es el método
   recomendado para ED50 → ETRS89; precisión decimétrica.

En QGIS/Proj: `+proj=pipeline` encadena pasos. Un pipeline típico:

```
+proj=pipeline
+step +proj=axisswap +order=2,1
+step +proj=unitconvert +xy_in=deg +xy_out=rad
+step +proj=push +v_3
+step +proj=cart +ellps=intl
+step +proj=helmert +x=... +y=... +z=... +rx=... +ry=... +rz=... +s=...
+step +proj=cart +ellps=GRS80 +inv
+step +proj=pop +v_3
+step +proj=unitconvert +xy_in=rad +xy_out=deg
+step +proj=axisswap +order=2,1
```

Nunca inventes los parámetros: se toman de la autoridad (IGN, EPSG, EUREF) y se
documentan en el proyecto.

## 3. Nivelación

- **Nivelación geométrica**: miras y nivel; la más precisa (mm/km).
- **Nivelación trigonométrica**: con estación total y ángulo vertical.
- **Nivelación GNSS**: cotas elipsoidales convertidas con modelo de geoide
  (EGM2008, EGM96, modelo local).

Error típico del principiante: entregar cotas GNSS sin convertir, con 50 m de
error en España. Siempre indicar: `cota ortométrica, modelo EGM2008`.

## 4. Tolerancias y buenas prácticas

| Trabajo | Tolerancia razonable |
| --- | --- |
| Levantamiento para catastro | 5–10 cm en planta |
| Replanteo de obra | 1–2 cm |
| Cartografía 1:1.000 | 0,2 m |
| Cartografía 1:5.000 | 1 m |

Buenas prácticas:
1. Fijar el CRS del proyecto **antes** de dibujar.
2. Registrar el sistema, la época y la fuente de la corrección.
3. Comprobar con un punto de control conocido.
4. No mezclar capas con CRS distintos sin reproyectar.

## 5. Caso práctico resuelto

**Enunciado**: dos puntos medidos con RTK en ETRS89/UTM30N están a 100,00 m.
¿La distancia real es esa?

**Razonamiento**:
1. UTM no conserva distancias: aplica un factor de escala cercano a 0,9996.
2. También influye la altura sobre el elipsoide (factor de elevación).
3. La distancia geodésica se obtiene dividiendo por esos factores.

**Conclusión**: 100,00 m en el plano UTM equivalen a ~100,04 m en el terreno en
esa zona. Para catastro se trabaja con superficies y distancias en el plano,
pero se documenta el factor aplicado.
