# Diccionario y procedencia de los datos

Este documento describe los dos archivos CSV generados con BehaviorSpace y usados por los notebooks. Los archivos originales no se modifican.

## Archivos

| Archivo | Experimento | Filas de datos | Corridas | Métrica registrada |
|---|---|---:|---:|---|
| [`wolfsheep_final.csv`](../wolf_sheep/wolfsheep_final.csv) | `wolfsheep_final` | 296 | 296 | `ticks` |
| [`wolfsheep_evol.csv`](../wolf_sheep/wolfsheep_evol.csv) | `wolfsheep_evol` | 116.469 | 300 | `count sheep` |

Ambos archivos fueron producidos con NetLogo 7.0.4 y el modelo `Wolf Sheep Predation.nlogox`, en la variante `sheep-wolves` (sin pasto), sobre un mundo de 51 × 51 patches (`-25` a `25` en ambos ejes).

- `wolfsheep_final.csv`: generado el 25/05/2026 a las 21:02:23 (UTC-5).
- `wolfsheep_evol.csv`: generado el 25/05/2026 a las 21:11:55 (UTC-5).

## Formato

- Codificación UTF-8 sin BOM.
- Separador: coma.
- Valores entre comillas dobles.
- Las primeras 6 líneas son metadatos de BehaviorSpace.
- La línea 7 contiene el encabezado.
- Los notebooks leen los archivos con `skip = 6`.

Metadatos comunes:

```text
BehaviorSpace results (NetLogo 7.0.4),Table version 2.0
Wolf Sheep Predation.nlogox
wolfsheep_final | wolfsheep_evol
fecha y hora de ejecución
min-pxcor,max-pxcor,min-pycor,max-pycor
-25,25,-25,25
```

## Columnas

### `wolfsheep_final.csv`

| Columna | Tipo | Descripción |
|---|---|---|
| `[run number]` | entero | Número de corrida asignado por BehaviorSpace, de 1 a 300. |
| `initial-number-sheep` | entero | Valor del parámetro: 20, 40, 60, ..., 200. |
| `[step]` | entero | Paso de medición. En este archivo coincide con `ticks` en las 296 filas. |
| `ticks` | entero | Valor del reporter `ticks` al finalizar la corrida. |

`wolfsheep_final` usa `runMetricsEveryStep="false"`, por lo que registra una sola fila por corrida. No registra `count sheep`; por eso el desenlace poblacional no se puede clasificar directamente desde este CSV.

### `wolfsheep_evol.csv`

| Columna | Tipo | Descripción |
|---|---|---|
| `[run number]` | entero | Número de corrida, de 1 a 300. |
| `initial-number-sheep` | entero | Valor del parámetro: 20, 40, 60, ..., 200. |
| `[step]` | entero | Paso de medición; se usa como eje temporal. Va de 0 al último paso de la corrida. |
| `count sheep` | entero | Población de ovejas en ese paso. |

`wolfsheep_evol` usa `runMetricsEveryStep="true"`, por lo que registra la evolución paso a paso. No incluye una columna `ticks`; el tiempo disponible es `[step]`.

En este modelo cada ejecución de `go` avanza un tick, de modo que `[step]` y `ticks` representan la misma escala temporal. En `wolfsheep_final` se verificó la igualdad `[step] == ticks`; en `wolfsheep_evol` se usa `[step]` como tiempo.

## Diseño experimental

- 10 valores de `initial-number-sheep`: 20 a 200 en pasos de 20.
- 30 repeticiones por valor: 300 corridas por experimento.
- `sequentialRunOrder="false"`: el número de corrida avanza primero por los valores del parámetro y luego por las repeticiones. Por ejemplo, la corrida 3 corresponde a 60 ovejas iniciales, no al tercer bloque de 20.
- `wolfsheep_final` y `wolfsheep_evol` son experimentos independientes: no comparten semilla ni estado. Solo 3 de las 296 corridas finales coinciden en el último paso con su homóloga evolutiva.
- No se fijó `random-seed` en la configuración original.

## Condiciones de parada

El procedimiento `go` del modelo se detiene cuando ocurre cualquiera de estas condiciones:

1. `not any? turtles`: no quedan ovejas ni lobos.
2. `not any? wolves and count sheep > max-sheep`: no quedan lobos y las ovejas superan el límite. En la versión de escritorio, `max-sheep` es 30.000.

Además, el experimento original tiene `exitCondition ticks >= 1000`. Es un límite de seguridad que no llegó a activarse: el máximo observado fue 732 en `wolfsheep_final` y 976 en `wolfsheep_evol`.

Los dos desenlaces poblacionales son:

- **Colapso:** la población de ovejas termina en 0.
- **Desborde:** no quedan lobos y la población de ovejas supera 30.000.

En `wolfsheep_evol` se pueden clasificar los desenlaces porque la última fila de cada corrida contiene `count sheep`. En `wolfsheep_final` no es posible hacerlo directamente porque la métrica registrada es solo `ticks`.

## Resumen de `wolfsheep_final`

Faltan 4 corridas en el CSV: 19, 58, 71 y 271. Por grupo, faltan 2 en el grupo de 20 ovejas y 1 en los grupos de 160 y 180. El archivo no documenta la causa.

| `initial-number-sheep` | n | Media de `ticks` | Mediana | Mínimo | Máximo |
|---:|---:|---:|---:|---:|---:|
| 20 | 28 | 307,57 | 218,0 | 190 | 604 |
| 40 | 30 | 336,37 | 337,5 | 187 | 440 |
| 60 | 30 | 289,73 | 271,5 | 212 | 574 |
| 80 | 30 | 325,70 | 260,5 | 214 | 599 |
| 100 | 30 | 373,23 | 375,5 | 206 | 724 |
| 120 | 30 | 443,17 | 421,0 | 193 | 687 |
| 140 | 30 | 415,13 | 371,0 | 202 | 732 |
| 160 | 29 | 419,66 | 359,0 | 202 | 694 |
| 180 | 29 | 461,72 | 448,0 | 308 | 700 |
| 200 | 30 | 421,33 | 355,0 | 172 | 731 |
| **Total** | **296** | **379,43** | **358,0** | **172** | **732** |

## Resumen de `wolfsheep_evol`

- 300 corridas completas y 116.469 filas.
- Cada corrida tiene entre 171 y 977 filas, incluyendo el paso 0.
- El último paso observado es 976.
- El máximo de `count sheep` es 3.390.767, en la corrida 216 (120 ovejas iniciales), paso 502.
- Desenlaces: 156 colapsos y 144 desbordes.
- Mediana del último paso: 365,5; media: 387,2.

| `initial-number-sheep` | n | Colapsos | Desbordes | Mediana del último paso |
|---:|---:|---:|---:|---:|
| 20 | 30 | 12 | 18 | 206,5 |
| 40 | 30 | 28 | 2 | 325,5 |
| 60 | 30 | 25 | 5 | 277,5 |
| 80 | 30 | 20 | 10 | 268,5 |
| 100 | 30 | 11 | 19 | 383,0 |
| 120 | 30 | 10 | 20 | 378,0 |
| 140 | 30 | 14 | 16 | 387,5 |
| 160 | 30 | 13 | 17 | 401,0 |
| 180 | 30 | 8 | 22 | 363,5 |
| 200 | 30 | 15 | 15 | 434,5 |

## Uso en los notebooks

Los notebooks renombran las columnas para simplificar las gráficas:

- [`WolfSheep_PostMortem.ipynb`](../wolf_sheep/WolfSheep_PostMortem.ipynb): `corrida`, `parametro`, `otra_columna`, `resultado_final`. `resultado_final` contiene `ticks`.
- [`WolfSheep_Evolutivo.ipynb`](../wolf_sheep/WolfSheep_Evolutivo.ipynb): `corrida`, `parametro`, `tiempo`, `valor_temporal`. `tiempo` contiene `[step]` y `valor_temporal` contiene `count sheep`.

Los notebooks leen los CSV desde la rama `master` mediante URLs raw de GitHub. Para regenerar los datos, consulta [`reproduccion.md`](reproduccion.md).
