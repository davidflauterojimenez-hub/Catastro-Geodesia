# Geodesia — GNSS, nivelación y transformaciones (Colombia)

Fuente: Resolución IGAC 388 de 2020, modificada por la Resolución IGAC 679 de
2022 (exactitud posicional del levantamiento catastral); Resolución IGAC 715 de
2018 (MAGNA-SIRGAS 2018 y cambio de época); herramienta MAGNA-SIRGAS PRO 7 del
IGAC; documentación de EPSG sobre métodos de Helmert y de rejilla; manuales de
receptores GNSS.

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

En Colombia, el IGAC opera la red de estaciones **MAGNA-ECO**, que ofrece
servicios **NTRIP** y **VRS** de forma gratuita. Usar esa red evita montar una
base propia y garantiza que el levantamiento quede en MAGNA-SIRGAS.

## 2. Transformación entre sistemas

Dos métodos habituales:

1. **Helmert (7 parámetros)**: 3 traslaciones, 3 rotaciones y 1 factor de
   escala. Precisión de metros a decímetros.
2. **Rejilla (grid shift)**: tabla de desplazamientos por zona. Es el método
   recomendado para pasar de Bogotá 1975 a MAGNA-SIRGAS; precisión decimétrica.

En Colombia hay un tercer paso que no se puede olvidar: el **cambio de época**.
MAGNA-SIRGAS (original) está en la época 1995.4 y MAGNA-SIRGAS 2018 en la época
2018.0. Pasar de una a otra exige aplicar el modelo de velocidades
**VEMOS SIRGAS 2003–2022**. La herramienta **MAGNA-SIRGAS PRO 7** del IGAC hace
ese cálculo, además de conversión de coordenadas y nivelación GNSS.

En QGIS/Proj: `+proj=pipeline` encadena pasos. Un pipeline típico de paso a
CTM12:

```
+proj=pipeline
+step +proj=axisswap +order=2,1
+step +proj=unitconvert +xy_in=deg +xy_out=rad
+step +proj=push +v_3
+step +proj=cart +ellps=GRS80
+step +proj=helmert +x=... +y=... +z=... +rx=... +ry=... +rz=... +s=...
+step +proj=cart +ellps=GRS80 +inv
+step +proj=pop +v_3
+step +proj=tmerc +lat_0=4 +lon_0=-73 +k=0.9992 +x_0=5000000 +y_0=2000000 +ellps=GRS80
+step +proj=unitconvert +xy_in=rad +xy_out=deg
+step +proj=axisswap +order=2,1
```

Nunca inventes los parámetros: se toman de la autoridad (IGAC, EPSG, SIRGAS) y
se documentan en el proyecto.

## 3. Nivelación

- **Nivelación geométrica**: miras y nivel; la más precisa (mm/km). Es la base
  de la Red de Nivelación de Alta Precisión (RNVAP) del IGAC.
- **Nivelación trigonométrica**: con estación total y ángulo vertical.
- **Nivelación GNSS**: cotas elipsoidales convertidas con modelo de geoide
  (QGeoidCOL2023 en Colombia).

Error típico del principiante: entregar cotas GNSS sin convertir. Siempre
indicar: `cota ortométrica, modelo QGeoidCOL2023`.

## 4. Tolerancias y buenas prácticas

Exactitud posicional exigida para el levantamiento catastral (error circular al
95 %, Resolución IGAC 388 de 2020, modificada por la 679 de 2022):

| Escala | Suelo | Error máximo al 95 % |
| --- | --- | --- |
| 1:1.000 | urbano | 0,52 m |
| 1:2.000 | urbano | 1,04 m |
| 1:5.000 | rural con comportamiento urbano | 2,60 m |
| 1:10.000 | rural | 5,2 m |
| 1:25.000 | bosque | 13,01 m |

Tolerancias de orden general en obra:

| Trabajo | Tolerancia razonable |
| --- | --- |
| Replanteo de obra | 1–2 cm |
| Cartografía 1:1.000 | 0,2 m |
| Cartografía 1:5.000 | 1 m |

Buenas prácticas:

1. Fijar el CRS del proyecto **antes** de dibujar.
2. Registrar el sistema, la **época** y la fuente de la corrección.
3. Comprobar con un punto de control conocido.
4. No mezclar capas con CRS distintos sin reproyectar.

## 5. Caso práctico resuelto

**Enunciado**: dos puntos medidos con RTK en MAGNA-SIRGAS / CTM12 están a
100,00 m en el plano. ¿La distancia real es esa?

**Razonamiento**:

1. CTM12 no conserva distancias: aplica un factor de escala cercano a `0,9992`.
2. También influye la altura sobre el elipsoide (factor de elevación).
3. La distancia en el terreno se obtiene dividiendo por esos factores.

**Conclusión**: 100,00 m en el plano CTM12 equivalen a unos 100,08 m en el
terreno en esa zona. Para catastro se trabaja con superficies y distancias en el
plano, pero se documenta el factor aplicado.
