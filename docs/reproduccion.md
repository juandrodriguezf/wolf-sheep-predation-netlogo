# Guía de reproducción

Esta guía permite repetir la metodología experimental y el análisis. Los resultados numéricos no serán idénticos porque los experimentos originales no fijaron una semilla aleatoria.

## Requisitos

- NetLogo 7.0.4.
- R 4.6.0 o superior, o el entorno R de Google Colab.
- Paquete `tidyverse`, instalable con [`requirements.R`](../requirements.R).
- Los archivos del repositorio, incluyendo [`wolfsheep_experiments.xml`](experimentos/wolfsheep_experiments.xml).

El modelo del repositorio está guardado como NetLogo 7.0.0-beta0, pero su código es equivalente al modelo 7.0.4 usado para generar los datos. Se recomienda abrirlo con NetLogo 7.0.4.

## 1. Abrir el modelo y verificar la variante

Abre [`wolf_sheep/Wolf Sheep Predation.nlogox`](../wolf_sheep/Wolf%20Sheep%20Predation.nlogox) en NetLogo 7.0.4.

En la pestaña Interface, verifica que el chooser `model-version` esté en:

```text
sheep-wolves
```

Ese es el valor por defecto del modelo y la variante usada en los datos. El XML de experimentos no fija `model-version`; si el chooser se cambia a `sheep-wolves-grass`, los resultados ya no serán comparables con los CSV del repositorio.

## 2. Importar los experimentos

1. Abre **Tools → BehaviorSpace**.
2. Pulsa **Import**.
3. Selecciona `docs/experimentos/wolfsheep_experiments.xml`.
4. Deben aparecer dos experimentos: `wolfsheep_final` y `wolfsheep_evol`.

Configuración exacta:

| Elemento | `wolfsheep_final` | `wolfsheep_evol` |
|---|---|---|
| Repeticiones | 30 | 30 |
| Orden de corridas | No secuencial | No secuencial |
| `preExperiment` | `reset-timer` | `reset-timer` |
| `setup` | `setup` | `setup` |
| `go` | `go` | `go` |
| Condición de parada | `ticks >= 1000` | `ticks >= 1000` |
| Métrica | `ticks` | `count sheep` |
| Medir en cada paso | No | Sí |
| Variable variada | `initial-number-sheep` = 20, paso 20, hasta 200 | Igual |

La condición `ticks >= 1000` es un límite de seguridad del experimento. La parada principal la produce el propio modelo: se detiene si no quedan tortugas, o si no quedan lobos y las ovejas superan `max-sheep` (30.000 en escritorio).

`wolfsheep_final` registra solo el valor final de `ticks`; no registra `count sheep`. `wolfsheep_evol` registra `count sheep` en cada paso y permite reconstruir la dinámica temporal.

## 3. Ejecutar desde la interfaz

Para cada experimento:

1. Selecciónalo y pulsa **Run**.
2. Elige **Table output**.
3. Usa nombres nuevos si no quieres sobrescribir los CSV originales:
   - `wolfsheep_final_repro.csv`
   - `wolfsheep_evol_repro.csv`
4. Ejecuta las 30 repeticiones.

El orden de las filas puede no ser secuencial, especialmente si se usan corridas en paralelo. Ordena siempre por `[run number]` antes de analizar.

## 4. Ejecución sin interfaz

Desde la carpeta de instalación de NetLogo 7.0.4, usando `NetLogo_Console` (en Windows suele ser `NetLogo_Console.exe`):

```powershell
.\NetLogo_Console.exe --headless `
  --model "C:\ruta\al\repo\wolf_sheep\Wolf Sheep Predation.nlogox" `
  --setup-file "C:\ruta\al\repo\docs\experimentos\wolfsheep_experiments.xml" `
  --experiment "wolfsheep_final" `
  --table "C:\ruta\al\repo\wolf_sheep\wolfsheep_final_repro.csv"
```

Para el experimento evolutivo, cambia el nombre del experimento y el archivo de salida:

```powershell
.\NetLogo_Console.exe --headless `
  --model "C:\ruta\al\repo\wolf_sheep\Wolf Sheep Predation.nlogox" `
  --setup-file "C:\ruta\al\repo\docs\experimentos\wolfsheep_experiments.xml" `
  --experiment "wolfsheep_evol" `
  --table "C:\ruta\al\repo\wolf_sheep\wolfsheep_evol_repro.csv"
```

En macOS o Linux, usa `./NetLogo_Console`. Si el XML contiene más de un experimento, `--experiment` es obligatorio.

## 5. Ejecutar el análisis en R

Desde la raíz del repositorio:

```r
source("requirements.R")
```

O desde la terminal:

```bash
Rscript requirements.R
```

Luego abre los notebooks con kernel de R, no de Python:

- [`WolfSheep_PostMortem.ipynb`](../wolf_sheep/WolfSheep_PostMortem.ipynb)
- [`WolfSheep_Evolutivo.ipynb`](../wolf_sheep/WolfSheep_Evolutivo.ipynb)

Los notebooks leen los CSV desde la rama `master` mediante URLs raw y usan `skip = 6` para saltar los metadatos de BehaviorSpace. Si regeneras los CSV localmente, actualiza las URLs o sube los archivos antes de ejecutar las celdas.

## 6. Limitaciones de reproducción

- **Sin semilla fija:** los experimentos originales no incluyeron `random-seed`, por lo que una nueva ejecución produce resultados distintos. Es reproducible la metodología, no cada valor numérico.
- **Experimentos independientes:** `wolfsheep_final` y `wolfsheep_evol` no comparten estado ni semilla. Sus corridas con el mismo número no son parejas.
- **Límite de 1000 ticks:** no se alcanzó en los datos originales, pero puede activarse en corridas con poblaciones que no colapsan ni desbordan antes.
- **`wolfsheep_final` no registra poblaciones:** para clasificar colapso o desborde desde ese experimento habría que añadir `count sheep` como métrica, lo que cambia el formato del CSV.
- **Corridas faltantes:** el CSV final original contiene 296 de 300 corridas. El archivo no documenta la causa de las ausencias.

### Semilla opcional para corridas pareadas

Si se quiere que `wolfsheep_final` y `wolfsheep_evol` usen la misma semilla por número de corrida, se puede modificar una copia del XML y usar como `setup`:

```text
set random-seed (1000 + behaviorspace-run-number)
setup
```

Esto no forma parte de los experimentos originales y cambia los resultados. Úsalo solo para nuevos experimentos pareados, no para reproducir los CSV existentes.

## 7. Verificación rápida

- El XML está validado contra el DTD oficial de BehaviorSpace de NetLogo 7.0.4.
- `wolfsheep_final` debe producir una fila por corrida; en los datos originales, 296 filas.
- `wolfsheep_evol` debe producir muchas filas por corrida; en los datos originales, 116.469 filas.
- Los conteos, columnas y desenlaces están documentados en [`datos.md`](datos.md).

## Referencias internas

- [Diccionario de datos](datos.md)
- [Experimentos importables](experimentos/wolfsheep_experiments.xml)
- [README del repositorio](../README.md)
- [Licencias y atribución](../NOTICE.md)
