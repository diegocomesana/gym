# Organización semanal — Anselmi

> Modelo de **ondulación de carga** a lo largo de la semana. La carga no es plana ni monotonamente decreciente: sube, baja, vuelve a subir. Esto evita la adaptación y el estancamiento.

---

## El modelo (unidades de carga relativa)

| Día | Carga | Rol |
|-----|-------|-----|
| Lunes | 9 | Alto — arrancás fuerte la semana |
| Martes | 8 | Alto-medio — no bajar demasiado |
| **Miércoles** | **10** | **Pico de la semana** |
| Jueves | 5 | Valle de recuperación profunda |
| Viernes | 8 | Alto-medio — segunda ola |
| Sábado | 7 → ↑12 | Variable — puede ser el pico máximo (día de partido / sesión intensa) |
| Domingo | 3 | Recuperación activa muy suave |

La flecha en sábado indica que ese día puede escalar hasta el máximo de la semana si es el día de competencia o la sesión más larga/intensa.

---

## Por qué funciona

**El sistema nervioso necesita variación para adaptarse.** Si todos los días tienen carga similar, el cuerpo se adapta a ese nivel y deja de progresar. La ondulación fuerza adaptaciones continuas:

- Los días altos generan el estímulo.
- El jueves (valle) permite que el sistema nervioso se recupere antes de la segunda ola.
- El sábado como pico máximo opcional permite un esfuerzo total sin acumular fatiga de la semana.

**Lo que no hay que hacer:**
- Semanas planas (mismo esfuerzo todos los días) → adaptación → estancamiento.
- Carga descendente toda la semana (lunes máximo, luego baja siempre) → no hay segunda ola, se pierde estímulo.
- Subir todo a la vez cada semana → sobreentrenamiento.

---

## Adaptación al plan Diego (longboard + complemento en casa)

Las unidades de carga combinan longboard + complemento en casa:

| Día | Carga | Longboard | Complemento en casa |
|-----|-------|-----------|---------------------|
| Lunes | ~8 | Vuelta habitual | Circuito B |
| Martes | ~7 | Vuelta habitual | — |
| **Miércoles** | **~10** | **Vuelta habitual** | **Circuito A + Bloque de fuerza** |
| Jueves | ~5 | Suave / corto | — (recuperación) |
| Viernes | ~8 | Vuelta habitual | Circuito A o B |
| **Sábado** | **~7–12** | **Sesión larga / intensa** | Movilidad opcional |
| Domingo | ~3 | Suave / corto | Cierre piriforme + gato-vaca |

**Cambio clave respecto a la versión anterior:** el Circuito A + Bloque de fuerza (el estímulo más intenso) se mueve al **miércoles** para que sea el pico de la semana, no el lunes. El lunes arranca alto pero no al máximo.

**Sábado como pico opcional:** si ese día hacés la sesión de longboard más larga e intensa de la semana (+ eventual movilidad), puede ser el día de carga máxima — equivalente al "día de partido" de Anselmi.

---

## Para la app

Atributos que necesita cada día en el modelo:

```
- dia: "lunes" ... "domingo"
- carga_relativa: 1–12
- rol: "inicio" | "alto" | "pico" | "valle" | "segunda_ola" | "pico_opcional" | "recuperacion"
- bloque_principal: "fuerza" | "coordinacion" | "dinamico" | "pliometrico" | "recuperacion"
- longboard_intensidad: "suave" | "habitual" | "intenso" | "pico"
```

> Fuente: presentación de Horacio Anselmi (diapositiva "Organización semanal").  
> Marco general: [`horacio-anselmi-marco-y-vinculos.md`](../referentes/horacio-anselmi-marco-y-vinculos.md)  
> Orden de intensidad: [`orden-intensidad-anselmi.md`](./orden-intensidad-anselmi.md)
