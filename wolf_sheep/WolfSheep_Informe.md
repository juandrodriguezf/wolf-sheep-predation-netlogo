# Wolf Sheep Predation: Efecto del número inicial de ovejas en la dinámica depredador-presa

**Curso:** Teoría de la Computación  
**Grupo:** —  
**Fecha:** 25/05/2026  
**Integrantes:** Juan David Rodríguez Fernández

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

| Elemento | Valor |
|---|---|
| **Parámetro independiente** | `initial-number-sheep` |
| **Valores probados** | 20, 40, 60, 80, 100, 120, 140, 160, 180, 200 |
| **Número de repeticiones** | 30 por valor (total: 300 corridas por experimento) |
| **Métrica final** | `ticks` (tiempo hasta que la simulación termina) |
| **Métrica temporal** | `count sheep` (ovejas vivas en cada tick) |
| **Condición de parada** | Natural del modelo (no quedan turtles) |
| **Experimento final** | `wolfsheep_final` — mide `ticks` al final de cada corrida |
| **Experimento evolutivo** | `wolfsheep_evol` — mide `count sheep` en cada tick |
| **CSV final** | `wolf_sheep/wolfsheep_final.csv` |
| **CSV evolutivo** | `wolf_sheep/wolfsheep_evol.csv` |

---

## 4. Resultados del análisis final

### Gráfica: Dispersión, jitter y boxplot de `initial-number-sheep` vs `ticks`

Las gráficas en `WolfSheep_PostMortem.ipynb` muestran la relación entre el número inicial de ovejas y el tiempo de simulación.

### Interpretación

La gráfica de dispersión muestra una alta variabilidad en los resultados. Para un mismo valor de `initial-number-sheep`, hay corridas que terminan en ~170 ticks y otras que duran más de 700 ticks. Los boxplots revelan que los rangos intercuartílicos se superponen entre valores adyacentes del parámetro.

**No se observa una tendencia monótona clara** entre el número inicial de ovejas y la duración de la simulación. Sin embargo, los valores extremos (tanto mínimos como máximos) aparecen con mayor frecuencia en poblaciones altas (160–200 ovejas). Esto tiene sentido desde la dinámica del modelo: más ovejas iniciales significan más presas, lo que puede sostener a los lobos por más tiempo, pero también aumenta la probabilidad de que ocurran eventos estocásticos que aceleren la extinción.

---

## 5. Resultados del análisis evolutivo

### Gráfica: Curvas de evolución temporal de `count sheep` por valor de `initial-number-sheep`

Las gráficas en `WolfSheep_Evolutivo.ipynb` muestran la población promedio de ovejas a lo largo del tiempo, separada por valor inicial.

### Interpretación

En todos los casos, la población de ovejas comienza en el valor inicial y desciende progresivamente. La velocidad del declive depende del número inicial:

- **Poblaciones pequeñas (20–40):** la curva cae abruptamente y la simulación termina rápido. Los lobos cazan a las pocas ovejas disponibles y luego se extinguen por falta de alimento.
- **Poblaciones medianas (60–120):** el declive es más gradual. Se observan fluctuaciones que reflejan los ciclos depredador-presa: cuando crecen las ovejas, los lobos tienen más comida y se reproducen, lo que aumenta la presión de caza y reduce las ovejas nuevamente.
- **Poblaciones grandes (140–200):** la curva es aún más suave y se extiende por cientos de ticks. El sistema tarda más en alcanzar un estado de equilibrio o extinción porque hay suficientes presas para mantener a los lobos durante largos períodos.

Las **facetas** permiten ver que la forma de la curva cambia cualitativamente con el parámetro: de caídas abruptas (bajo) a declives prolongados (alto).

---

## 6. Discusión

Los resultados muestran que el modelo Wolf Sheep Predation es altamente sensible a las condiciones iniciales y a la estocasticidad de los eventos locales. Las reglas simples de cada agente (moverse, comer, reproducirse, morir) producen comportamientos globales complejos que no son predecibles solo a partir del valor del parámetro.

El número inicial de ovejas afecta la dinámica temporal de forma clara: más ovejas iniciales sostienen el sistema por más tiempo. Sin embargo, la métrica final (ticks) es menos informativa porque la alta variabilidad entre repeticiones enmascara la relación.

La metodología de Helechos Básico se replica exitosamente. El parámetro `num-agentes` del modelo original se traduce a `initial-number-sheep`, la métrica final `ticks` se mantiene (porque NetLogo la reporta en ambos modelos), y la métrica temporal `count turtles` se traduce a `count sheep`.

---

## 7. Conclusiones

1. **El número inicial de ovejas influye en la dinámica temporal del modelo**: a mayor población inicial, más tiempo tarda el sistema en alcanzar la extinción. Las curvas de población decrecen más lentamente y presentan fluctuaciones más prolongadas.
2. **La métrica final (ticks) tiene alta variabilidad** y no muestra una relación lineal o monótona clara con el parámetro. Esto sugiere que para este modelo, el análisis evolutivo es más informativo que el análisis post-mortem.
3. **La metodología de Helechos Básico se puede aplicar a otros modelos de NetLogo** con adaptaciones mínimas en los nombres de columnas y variables. El flujo de trabajo (BehaviorSpace → CSV → Colab → ggplot) es robusto y reproducible.

---

## 8. Limitaciones y mejoras

### Limitaciones

- **Número de repeticiones:** 30 repeticiones por valor del parámetro puede ser insuficiente para capturar toda la variabilidad del modelo. Con más repeticiones (100+), las estimaciones del promedio serían más estables.
- **Condición de parada:** El modelo no tiene una condición de parada artificial, por lo que algunas simulaciones pueden extenderse mucho si las poblaciones entran en ciclos prolongados.
- **Una sola métrica temporal:** Solo se midió `count sheep`. Medir también `count wolves` permitiría analizar la dinámica conjunta depredador-presa.
- **Parámetro fijo de lobos:** `initial-number-wolves` se mantuvo en su valor predeterminado. Variar ambos parámetros simultáneamente podría revelar interacciones más complejas.

### Mejoras posibles

- Aumentar las repeticiones a 100 por valor del parámetro.
- Incluir `count wolves` como métrica temporal adicional para graficar ambas poblaciones.
- Realizar un experimento factorial variando tanto `initial-number-sheep` como `initial-number-wolves`.
- Agregar una condición de parada por tiempo máximo (ej. `ticks >= 1000`) para evitar corridas excesivamente largas.
- Incorporar análisis estadístico formal (ANOVA o regresión) para cuantificar la significancia del efecto del parámetro.

---

*Documento generado como parte del proyecto de Teoría de la Computación. Repositorio: https://github.com/juandrodriguezf/proyecto_netlogo*
