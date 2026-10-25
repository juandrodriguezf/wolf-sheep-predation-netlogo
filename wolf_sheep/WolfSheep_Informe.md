# Wolf Sheep Predation: Efecto del número inicial de ovejas en la dinámica depredador-presa

**Curso:** Teoría de la Computación
**Grupo:** —
**Fecha:** 25/05/2026
**Integrantes:** Juan David Rodríguez Franco

---

## 1. Descripción del modelo

El modelo **Wolf Sheep Predation** es un modelo clásico de depredador-presa incluido en la biblioteca de NetLogo. Representa un ecosistema donde interactúan tres elementos:

- **Ovejas (presas):** se mueven aleatoriamente, consumen pasto y se reproducen.
- **Lobos (depredadores):** se mueven aleatoriamente, cazan ovejas para alimentarse y se reproducen.
- **Pasto:** crece en los patches y es consumido por las ovejas.

La simulación utiliza la variante `sheep-wolves-grass`, donde el pasto tiene un tiempo de regeneración. Esto hace que el modelo sea más realista y estable, ya que las ovejas no pueden sobrevivir sin pasto y los lobos no pueden sobrevivir sin ovejas.

**Reglas principales:**

- Las ovejas caminan, consumen pasto del patch donde están y ganan energía. Si tienen suficiente energía, se reproducen.
- Los lobos caminan, cazan ovejas en su mismo patch para ganar energía. Si tienen suficiente energía, se reproducen.
- Cada agente pierde energía por moverse. Si un agente se queda sin energía, muere.
- La simulación termina cuando no quedan agentes vivos en el mundo.

---

## 2. Pregunta experimental

**¿Cómo afecta el número inicial de ovejas (`initial-number-sheep`) al tiempo de duración de la simulación y a la dinámica temporal de la población de ovejas?**

---

## 3. Diseño experimental

| Elemento                           | Valor                                                          |
| ---------------------------------- | -------------------------------------------------------------- |
| **Parámetro independiente** | `initial-number-sheep`                                       |
| **Valores probados**         | 20, 40, 60, 80, 100, 120, 140, 160, 180, 200                   |
| **Número de repeticiones**  | 30 por valor (296 corridas válidas en el experimento final)    |
| **Métrica final**           | `ticks` (tiempo hasta que la simulación termina)            |
| **Métrica temporal**        | `count sheep` (ovejas vivas en cada tick)                    |
| **Condición de parada**     | Natural del modelo (no quedan turtles)                         |
| **Experimento final**        | `wolfsheep_final` — mide `ticks` al final de cada corrida |
| **Experimento evolutivo**    | `wolfsheep_evol` — mide `count sheep` en cada tick        |
| **CSV final**                | `wolf_sheep/wolfsheep_final.csv`                             |
| **CSV evolutivo**            | `wolf_sheep/wolfsheep_evol.csv`                              |

> En el CSV final quedaron 296 corridas: 28 para 20 ovejas y 29 para 160 y 180; el resto de los valores tiene 30.

---

## 4. Resultados del análisis final

### Gráfica: Dispersión, jitter y boxplot de `initial-number-sheep` vs `ticks`

Las gráficas en `WolfSheep_PostMortem.ipynb` muestran la relación entre el número inicial de ovejas y el tiempo de simulación. La tabla siguiente resume las 296 corridas del experimento final:

| Ovejas iniciales | Corridas | Mín | Mediana | Media | Máx |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 20 | 28 | 190 | 218 | 307.6 | 604 |
| 40 | 30 | 187 | 338 | 336.4 | 440 |
| 60 | 30 | 212 | 272 | 289.7 | 574 |
| 80 | 30 | 214 | 260 | 325.7 | 599 |
| 100 | 30 | 206 | 376 | 373.2 | 724 |
| 120 | 30 | 193 | 421 | 443.2 | 687 |
| 140 | 30 | 202 | 371 | 415.1 | 732 |
| 160 | 29 | 202 | 359 | 419.7 | 694 |
| 180 | 29 | 308 | 448 | 461.7 | 700 |
| 200 | 30 | 172 | 355 | 421.3 | 731 |

### Interpretación

La duración de las corridas abarca desde 172 hasta 732 ticks y los rangos de todos los grupos se superponen. Para un mismo valor de `initial-number-sheep` hay corridas que terminan alrededor de 190 ticks y otras que superan los 700, de modo que la variabilidad entre repeticiones es grande.

**No se observa una tendencia monótona** entre el número inicial de ovejas y la duración de la simulación:

- Las medias más bajas del experimento final corresponden a 60 (289.7 ticks) y 20 (307.6); las más altas a 180 (461.7) y 120 (443.2).
- A partir de 100 ovejas las medias se mantienen entre 373 y 462 ticks, pero no aumentan de forma sostenida: 140 y 200 son menores que 120 y 180.
- Los valores extremos aparecen en todo el rango: los máximos en 140 (732), 200 (731) y 100 (724); los mínimos en 200 (172), 40 (187) y 20 (190). El grupo 180 tiene el mínimo más alto (308), mientras que 200 combina el mínimo más bajo con un máximo cercano a 731.

Este patrón es consistente con la estocasticidad del modelo: más ovejas pueden sostener a los lobos y alargar la simulación, pero también intensifican la depredación y los ciclos de reproducción, de modo que el resultado de cada corrida depende de encuentros locales y de la regeneración del pasto. Con 30 repeticiones por valor, las diferencias entre grupos cercanos no son concluyentes.

---

## 5. Resultados del análisis evolutivo

### Gráfica: Curvas de evolución temporal de `count sheep` por valor de `initial-number-sheep`

Las gráficas en `WolfSheep_Evolutivo.ipynb` muestran el promedio de ovejas vivas en cada tick para cada valor inicial. El promedio se calcula solo con las corridas que siguen activas en ese tick.

### Interpretación

La dinámica no es un declive simple: en la mayoría de los grupos la población de ovejas **crece rápidamente al inicio**, alcanza un pico y luego colapsa, seguido en algunos casos por un nuevo crecimiento. Por ejemplo, con 20 ovejas iniciales la mediana pasa de 20 en el tick 0 a 92 en el 50, 628 en el 100 y 3.871 en el 150; con 100 ovejas la mediana llega a 486 en el tick 75 y cae a 4 en el 150. Después del primer colapso quedan pocas corridas activas, por lo que los promedios tardíos se calculan sobre una muestra pequeña y deben interpretarse con cautela.

El número inicial cambia la escala y el momento de los picos, pero no elimina el patrón de auge y caída:

- **Poblaciones pequeñas (20–40):** el primer pico es menor en valor absoluto, pero el crecimiento es muy rápido; la duración mediana es de ~207 ticks para 20 y ~326 para 40.
- **Poblaciones medianas (60–120):** alcanzan picos altos (mediana de 822 ovejas con 60 en el tick 100; 486 con 100 en el 75) y colapsan antes del tick 175; la duración mediana aumenta con el valor inicial hasta ~383 ticks en 100 y ~378 en 120.
- **Poblaciones grandes (140–200):** tienden a sostener la simulación por más tiempo (medianas de ~364 a ~435 ticks) y presentan picos tardíos muy grandes, como el de 200 ovejas alrededor del tick 300 (mediana de 4.924).

En conjunto, el análisis evolutivo es más informativo que `ticks` para describir el mecanismo: revela ciclos de crecimiento y colapso que la métrica final resume en un solo número.

---

## 6. Discusión

Los resultados muestran que el modelo Wolf Sheep Predation es altamente sensible a las condiciones iniciales y a la estocasticidad de los eventos locales. Las reglas simples de cada agente (moverse, comer, reproducirse, morir) producen comportamientos globales complejos que no son predecibles solo a partir del valor del parámetro.

El número inicial de ovejas afecta la dinámica temporal, pero su efecto sobre `ticks` es débil y no monótono. Las medias del experimento final van de 289.7 ticks (60 ovejas) a 461.7 (180), sin un orden estable: 20 supera a 60, 140 y 200 quedan por debajo de 120 y 180, y el experimento evolutivo produce ordenamientos algo distintos (por ejemplo, la media de 200 es 479.0 frente a 421.3 en el final). Esa falta de estabilidad indica que, con 30 repeticiones, las diferencias entre grupos cercanos están dominadas por el ruido estocástico. La métrica final es, por tanto, menos informativa que las curvas temporales, que muestran los ciclos de auge y colapso.

La metodología de Helechos Básico se replica exitosamente. El parámetro `num-agentes` del modelo original se traduce a `initial-number-sheep`, la métrica final `ticks` se mantiene (porque NetLogo la reporta en ambos modelos), y la métrica temporal `count turtles` se traduce a `count sheep`.

---

## 7. Conclusiones

1. **El número inicial de ovejas influye en la dinámica temporal, pero el efecto sobre `ticks` es débil y no monótono**: las medias del experimento final van de 289.7 ticks (60 ovejas) a 461.7 (180), con alta variabilidad y rangos superpuestos. A partir de 100 ovejas la duración tiende a estabilizarse entre 373 y 462 ticks, sin que 200 supere a 180. El análisis evolutivo muestra ciclos de crecimiento y colapso en todos los grupos, con picos más tardíos y mayores en poblaciones grandes.
2. **La métrica final (ticks) tiene alta variabilidad** y no muestra una relación lineal o monótona clara con el parámetro. Esto sugiere que para este modelo, el análisis evolutivo es más informativo que el análisis post-mortem.
3. **La metodología de Helechos Básico se puede aplicar a otros modelos de NetLogo** con adaptaciones mínimas en los nombres de columnas y variables. El flujo de trabajo (BehaviorSpace → CSV → Colab → ggplot) es robusto y reproducible.

---

## 8. Limitaciones y mejoras

### Limitaciones

- **Número de repeticiones:** 30 repeticiones por valor del parámetro puede ser insuficiente para capturar toda la variabilidad del modelo. Con más repeticiones (100+), las estimaciones del promedio serían más estables.
- **Sesgo de supervivencia en los promedios temporales:** al promediar `count sheep` por tick solo contribuyen las corridas que siguen activas; después de los primeros colapsos el promedio se calcula sobre pocas corridas y puede exagerar los valores.
- **Experimentos separados:** el experimento final y el evolutivo se ejecutaron de forma independiente, por lo que sus tiempos de finalización no son directamente comparables (por ejemplo, la media de 20 ovejas es 307.6 en el final y 298.4 en el evolutivo).
- **Condición de parada:** El modelo no tiene una condición de parada artificial, por lo que algunas simulaciones pueden extenderse mucho si las poblaciones entran en ciclos prolongados.
- **Una sola métrica temporal:** Solo se midió `count sheep`. Medir también `count wolves` permitiría analizar la dinámica conjunta depredador-presa.
- **Parámetro fijo de lobos:** `initial-number-wolves` se mantuvo en su valor predeterminado. Variar ambos parámetros simultáneamente podría revelar interacciones más complejas.

### Mejoras posibles

- Aumentar las repeticiones a 100 por valor del parámetro.
- Graficar trayectorias individuales o medianas en lugar del promedio de las corridas supervivientes, para evitar el sesgo de supervivencia.
- Incluir `count wolves` como métrica temporal adicional para graficar ambas poblaciones.
- Realizar un experimento factorial variando tanto `initial-number-sheep` como `initial-number-wolves`.
- Agregar una condición de parada por tiempo máximo (ej. `ticks >= 1000`) para evitar corridas excesivamente largas.
- Incorporar análisis estadístico formal (ANOVA o regresión) para cuantificar la significancia del efecto del parámetro.

---

*Documento generado como parte del proyecto de Teoría de la Computación. Repositorio: https://github.com/juandrodriguezf/proyecto_netlogo*
