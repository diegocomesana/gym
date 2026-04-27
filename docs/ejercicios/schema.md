# Schema — Base de ejercicios

> Definición de todos los campos y valores posibles. Este es el contrato de datos: cada ejercicio en los archivos de nivel debe respetar estos campos. Cuando migremos a DB real, cada campo se convierte en columna.

---

## Campos por ejercicio

| Campo | Tipo | Valores posibles | Descripción |
|-------|------|-----------------|-------------|
| `id` | string | snake_case único | Identificador estable. Ej: `sentadilla`, `bird_dog`, `box_jump` |
| `nombre` | string | — | Nombre principal en español |
| `nombre_alternativo` | string[] | — | Otros nombres conocidos (inglés, jerga) |
| `nivel_anselmi` | int | 1 · 2 · 3 · 4 · 5 | Ver [orden-intensidad-anselmi.md](../metodologia/orden-intensidad-anselmi.md) |
| `patron_motor` | string | `empuje` `tirón` `bisagra` `sentadilla` `core` `rotación` `locomoción` `estabilidad` | Patrón de movimiento principal |
| `zona` | string[] | `tren_superior` `zona_media` `tren_inferior` | Zona corporal principalmente trabajada. Permite múltiples valores para ejercicios compuestos |
| `plano` | string | `sagital` `frontal` `transversal` `multiplanar` | Plano de movimiento |
| `es_bilateral` | bool | `true` `false` | Ambas extremidades trabajan igual |
| `musculos_primarios` | string[] | — | Músculos que llevan la carga principal |
| `musculos_secundarios` | string[] | — | Músculos estabilizadores / sinérgicos |
| `equipo` | string[] | `nada` `mancuernas` `barra` `caja` `cuerda` `conos` `silla` `pared` `banda` `banco` `escalon` | Equipo técnico necesario |
| `ubicacion` | string | `universal` `casa` `gym` `exterior` `casa/gym` `exterior/casa` `gym/exterior` | Dónde se puede hacer. `universal` = sin requerimiento de espacio o infraestructura especial |
| `requiere` | string[] | `nada` `colchoneta` `silla` `mancuernas` `banco` `pelota` `soga` `barra` `mesa` `pared` `conos` `caja` `escalera` `palo` | Materiales que el usuario necesita tener disponibles (vocabulario del usuario, base del motor de switch de la app) |
| `formato` | string | `reps` `tiempo` `distancia` | Cómo se mide |
| `fase_sesion` | string[] | `calentamiento` `principal` `cierre` `cualquiera` | Dónde cae en la sesión |
| `dificultad` | int | 1–5 | Dentro del nivel Anselmi (1=más fácil) |
| `contraindicaciones` | string[] | `lumbalgia` `rodilla` `hombro` `tobillo` `cervical` `ninguna` | Lesiones o condiciones que lo limitan |
| `variantes_mas_faciles` | string[] | ids de ejercicios | Regresiones |
| `variantes_mas_dificiles` | string[] | ids de ejercicios | Progresiones |
| `en_plan_actual` | bool | `true` `false` | Está en el plan activo de Diego |
| `video_url` | string | URL | Video de referencia |
| `notas` | string | — | Observaciones técnicas clave |

---

## Valores de `patron_motor`

| Valor | Qué es | Ejemplos |
|-------|--------|---------|
| `empuje` | Alejar carga del cuerpo | Flexión, press hombro, fondos |
| `tirón` | Acercar carga al cuerpo | Remo, dominadas, jalón |
| `bisagra` | Flexión de cadera con espalda neutra | Peso muerto, good morning, RDL |
| `sentadilla` | Flexión de rodilla + cadera simultánea | Sentadilla, búlgara, goblet |
| `core` | Resistencia a movimiento de tronco | Plancha, dead bug, bird dog |
| `rotación` | Movimiento rotacional de tronco | Rotación de tronco, pallof press |
| `locomoción` | Desplazamiento o patrón de marcha | Zancada, marcha, skipping |
| `estabilidad` | Control postural sin movimiento primario | Equilibrio 1 pierna, plancha |

---

## Lógica de switch por ubicación / equipo

La app usa `ubicacion` y `requiere` para ofrecer ejercicios equivalentes según dónde esté el usuario:

```
usuario.ubicacion = "gym"
usuario.equipo_disponible = ["mancuernas", "barra", "banco", "caja"]

→ filtrar ejercicios donde ubicacion incluye "gym"
→ mostrar los que requiere ⊆ equipo_disponible
→ si hay ejercicio en_plan_actual=true que no cumple,
   buscar variante_equivalente con mismo patron_motor y nivel_anselmi
   pero con requiere compatible
```

| Contexto del usuario | Filtro de ubicacion | Filtro de requiere |
|---------------------|--------------------|--------------------|
| En casa (sin equipo) | `universal` o `casa` | `nada` o `colchoneta` |
| En casa (con mancuernas) | `universal` o `casa` | + `mancuernas` |
| En el gym | `universal`, `casa`, `gym` | cualquier equipo gym |
| Al aire libre (parque) | `universal` o `exterior` | `nada`, `soga`, `conos` |

---

## Notas para migración a DB

Cuando pasemos a una base de datos real (SQL o NoSQL), la estructura sería:

```sql
-- Tabla principal
ejercicios (id, nombre, nivel_anselmi, patron_motor, plano, es_bilateral, formato, dificultad, en_plan_actual, video_url, notas)

-- Tablas relacionales (many-to-many)
ejercicio_musculos_primarios (ejercicio_id, musculo)
ejercicio_musculos_secundarios (ejercicio_id, musculo)
ejercicio_zona (ejercicio_id, zona)             -- 'tren_superior'|'zona_media'|'tren_inferior'
ejercicio_equipo (ejercicio_id, equipo)
ejercicio_ubicacion (ejercicio_id, ubicacion)  -- 'universal'|'casa'|'gym'|'exterior'
ejercicio_requiere (ejercicio_id, item)         -- items de requiere[]
ejercicio_fase_sesion (ejercicio_id, fase)
ejercicio_contraindicaciones (ejercicio_id, contraindicacion)
ejercicio_variantes (ejercicio_id, variante_id, tipo) -- 'regresion' | 'progresion'
ejercicio_nombres_alternativos (ejercicio_id, nombre_alternativo)
```
