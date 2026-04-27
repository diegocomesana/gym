# App de entrenamiento — Documento maestro

> Documento vivo. Concentra visión, features, modelo de datos y roadmap.  
> Toda sección "Para la app" de otros docs apunta acá.

---

## El problema que resuelve

El entrenamiento de Diego vive hoy en markdowns. Funciona para planificar, pero en el momento de entrenar hay fricción:

- Hay que ir mirando el celular para ver qué sigue
- No hay quien cuente el tiempo ni avise cuándo cambiar
- No se registra qué pasó realmente en la sesión
- Si estás en el gym o en el parque, el plan no se adapta solo
- No hay nadie que ajuste el plan según cómo te fue la semana anterior

La app resuelve eso: **entrenador personal en el bolsillo, siempre disponible, que aprende y se adapta.**

---

## Módulos principales

### 1. Perfil y contexto del usuario

El punto de partida de todo. La app conoce al usuario antes de darle cualquier cosa.

- **Objetivos:** rendimiento en longboard, fuerza general, movilidad, pérdida de grasa, etc.
- **Historial físico:** lesiones activas o pasadas (lumbalgia, rodilla, tobillo, etc.)
- **Preferencias:** qué le gusta y qué detesta, tono del asistente
- **Disponibilidad:** días y franja horaria disponibles por semana
- **Ubicación habitual:** casa, gym, exterior — puede cambiar por sesión
- **Materiales disponibles:** qué tiene en casa (mancuernas, colchoneta, silla, soga, etc.)
- **Nivel actual:** fase en el macrociclo de Anselmi (funcional / reclutamiento / específico)

> El agente AI tiene acceso a todo este perfil en todo momento.

---

### 2. Plan de entrenamiento personalizado

Genera y mantiene el plan semanal del usuario basado en su perfil.

- Basado en el marco de **Horacio Anselmi**: orden de intensidad, organización semanal, microciclos, macrociclo integrado, variación de carga
- Respeta el **orden de sesión** (ver sección *Modelo de datos — Sesión*):
  `zona media → dinámico → fuerza → velocidad/transferencia → flexibilidad`
- Selecciona ejercicios según `ubicacion` y `requiere` del día
- Progresión automática: sube de nivel Anselmi cuando el usuario consolida la fase actual
- **Switch dinámico por contexto:** el usuario avisa dónde está y la app intercambia los ejercicios por equivalentes sin romper la estructura del entrenamiento

```
usuario: "hoy voy al gym"
app: → reemplaza sentadilla libre por sentadilla con barra
     → reemplaza remo a mesa por remo en polea
     → mantiene estructura de sesión y objetivos del día
```

- Ajuste post-sesión: si el usuario reportó que algo fue muy fácil o difícil, la app ajusta la siguiente sesión
- Aplica variación de series por Anselmi: en la sesión pico (miércoles) más volumen; en la sesión complementaria con fuerza (viernes) una serie menos por ejercicio

---

### 3. Pre-sesión — revisión y personalización manual

Antes de iniciar, el usuario ve el plan del día y puede ajustarlo. Es el puente entre el plan generado automáticamente y la sesión real.

#### Flujo

```
Plan del día generado
        ↓
┌─────────────────────────────────┐
│  LUNES — Circuito B             │
│  📍 Casa  •  ~45 min            │
│  Materiales: colchoneta, silla  │
│                                 │
│  ── Calentamiento ──            │
│  1. Gato–vaca            [swap] │
│  2. Piriforme en prono   [swap] │
│  3. Sentadilla           [swap] │
│  ── Circuito B ──               │
│  4. Bird dog             [swap] │
│  5. Sentadilla búlgara   [swap] │
│  6. Dead bug             [swap] │
│  7. Plancha frontal      [swap] │
│  ...                            │
│                                 │
│        [Iniciar sesión]         │
└─────────────────────────────────┘
```

#### Al tocar "swap" en un ejercicio

La app muestra variantes del mismo objetivo, filtradas por:
- **`patron_motor` igual** (ej: si el original es `tirón`, las variantes también)
- **`nivel_anselmi` igual o adyacente** (±1 como máximo)
- **`ubicacion` compatible** con donde está el usuario hoy
- **`requiere` ⊆ materiales disponibles** del usuario en ese contexto
- **`contraindicaciones` que no choquen** con el perfil del usuario

```
swap: sentadilla búlgara (patron: sentadilla, nivel: 1, requiere: silla)

Variantes disponibles:
  → Sentadilla libre          (nivel 1, requiere: nada)      ← más fácil
  → Zancada atrás             (nivel 1, requiere: nada)
  → Sentadilla + mancuernas   (nivel 1, requiere: mancuernas) ← si tiene
  [no aparece: hip thrust (bisagra), box jump (nivel 4)]
```

#### Reglas adicionales

- Si el usuario está **en casa sin silla**, el swap es automáticamente sugerido (alerta antes de entrar a pre-sesión)
- El usuario puede también **eliminar** un ejercicio de la sesión (queda registrado como "salteado por decisión previa")
- Puede **reordenar** dentro de un bloque, pero no entre bloques (el orden de bloques de Anselmi es fijo)
- Los cambios manuales se registran y el agente los considera al ajustar el plan futuro: si siempre swapea X por Y, puede proponerlo directamente

#### Resumen de materiales necesarios

Al final de la pantalla de pre-sesión, la app muestra un checklist de lo que necesita juntar antes de arrancar:

```
Para esta sesión necesitás:
  ☐ Colchoneta
  ☐ Silla
  ☐ Mancuernas (2 x ~6 kg)
```

---

### 4. Modo sesión — el núcleo de la app

**El más importante.** Guía al usuario en tiempo real durante el entrenamiento. Manos libres en lo posible.

#### Vista principal de sesión

```
┌─────────────────────────────────┐
│  LUNES — Circuito B             │
│  Progreso: 4/9 ejercicios ✓     │
│  Tiempo total: 18:22 / ~45 min  │
│  Tiempo restante: ~27 min        │
├─────────────────────────────────┤
│  ► AHORA: Sentadilla búlgara    │
│    ⏱ 00:28 / 00:40              │
│    Pierna izquierda — Vuelta 1/2 │
│  [ilustración / video]           │
├─────────────────────────────────┤
│  SIGUIENTE: Plancha frontal      │
│  En 12 seg                       │
└─────────────────────────────────┘
```

#### Flujo de sesión

1. **Intro:** resumen de lo que viene, materiales necesarios, tiempo estimado total
2. **Zona media:** circuito de 6 min (Anselmi: va siempre primero)
3. **Circuito principal:** 30–40 min, cuenta regresiva por ejercicio, aviso de cambio, descansos cronometrados
4. **Bloque de fuerza (si aplica):** series × reps, descanso más largo entre series
5. **Cierre / flexibilidad:** cuenta regresiva, tono más suave
6. **Resumen post-sesión**

#### Interacción durante la sesión

- El usuario **confirma** cuando termina cada ejercicio (botón grande o voz)
- Puede marcar: "completé" / "no pude terminar" / "fue fácil" / "fue difícil"
- Puede pausar, saltar un ejercicio o pedir una alternativa en el momento
- Cuenta regresiva visible y sonora — no es necesario mirar el celular

#### Stats en tiempo real

- Tiempo transcurrido y restante de sesión
- Ejercicios completados vs. pendientes
- Barra de progreso del circuito actual
- Calorías estimadas (si hay datos biométricos)

#### Motivación

- Mensajes contextuales en momentos clave (mitad de circuito, último ejercicio, nuevo récord)
- Tono configurable: directo / motivador / silencioso
- Feedback positivo cuando se mejora respecto a la sesión anterior

---

### 4. Registro y seguimiento de progreso

Todo lo que pasó en cada sesión queda guardado.

- **Por sesión:** duración real, ejercicios completados, salteados, notas
- **Por ejercicio:** tiempo promedio, tendencia de completitud, veces que pidió alternativa
- **Por semana:** volumen total, adherencia al plan (% sesiones completas), carga acumulada
- **Por fase del macrociclo:** progresión dentro de cada capa de Anselmi

#### Métricas clave

| Métrica | Qué muestra |
|---------|-------------|
| Adherencia semanal | % de sesiones planificadas vs. realizadas |
| Tiempo promedio por circuito | Tendencia de mejora en resistencia |
| Ejercicios completados / sesión | Volumen real vs. planificado |
| Progresión de carga | Peso / dificultad a lo largo del tiempo |
| Zona de esfuerzo (si hay smartwatch) | Distribución aeróbico / anaeróbico |

#### Visualizaciones

- Gráfico de carga semanal (refleja la curva de Anselmi)
- Heatmap de días entrenados (estilo GitHub contributions)
- Progresión por ejercicio específico (tiempo de plancha, reps de sentadilla, etc.)

---

### 5. Agente AI — siempre disponible

**AI-first:** el agente no es un chatbot aparte, es el hilo conductor de toda la app.

#### Tiene acceso a todo:
- Perfil del usuario
- Historial completo de sesiones
- Plan actual y metodología (Anselmi)
- Base de ejercicios con clasificación completa
- Datos biométricos si están disponibles

#### Qué puede hacer

**Antes de entrenar:**
- Sugerir ajustes al plan de hoy según descanso, sesión anterior, contexto
- Responder preguntas técnicas: "¿por qué hacemos esto primero?" / "¿cómo sé que estoy en la fase correcta?"
- Armar una sesión exprés si el usuario tiene solo 20 minutos
- Informar qué materiales necesita para la sesión del día

**Durante el entrenamiento:**
- Dar cues técnicos por voz en el momento justo ("mantené la espalda recta en este ejercicio")
- Adaptar en tiempo real si el usuario reporta dolor o fatiga excesiva
- Ajustar la siguiente serie si la anterior fue muy fácil o muy difícil

**Después de entrenar:**
- Analizar la sesión: qué salió bien, qué ajustar
- Proyectar la próxima semana
- Responder preguntas sobre el entrenamiento realizado

#### Modo conversacional

El usuario puede hablar con el agente en cualquier momento como con un entrenador:
- "No pude hacer las búlgaras, me dolió la rodilla"
- "¿Cuándo subo al siguiente nivel?"
- "Hoy tengo 25 minutos, armame algo"
- "¿Qué materiales necesito para mañana?"

---

## Features deseables (roadmap)

### Control por voz

- El usuario puede decir "listo", "siguiente", "pausa", "más tiempo", "no pude"
- El agente responde por voz cuando corresponde
- Wake word configurable
- **Motivación:** en sesión es un problema tocar el celular — manos sucias, agitado, ritmo cortado

### Música adaptativa

- BPM ajustado al tipo de ejercicio en curso:
  - Zona media / calentamiento → ritmo suave / instrumental
  - Circuito principal → BPM alto, energizante
  - Descanso → baja el ritmo automáticamente
  - Cierre / flexibilidad → música suave
- Indicaciones de voz mezcladas sobre la música (como guía de audio)
- Integración con Spotify / Apple Music

### Integración biométrica (smartwatch)

- **Frecuencia cardíaca en tiempo real:** la app ve si estás en zona aeróbica o anaeróbica
- Ajuste de descansos según FC: si no bajaste a zona de recuperación, el descanso se extiende automáticamente
- Registro de FC por ejercicio → progresión cardiovascular a lo largo del tiempo
- **HRV (variabilidad de FC):** indicador de recuperación → la app puede sugerir bajar la carga del día
- Compatibilidad objetivo: Apple Watch (HealthKit), Garmin, Fitbit, Polar

---

## Modelo de datos

### Ejercicio

> Fuente de verdad: [`../ejercicios/schema.md`](../ejercicios/schema.md)

Campos principales de cada ejercicio:

```
id                    -- snake_case único. Ej: bird_dog, sentadilla_bulgara
nombre                -- nombre en español
nombre_alternativo[]  -- otros nombres conocidos
nivel_anselmi         -- 1 | 2 | 3 | 4 | 5
patron_motor          -- empuje | tirón | bisagra | sentadilla | core | rotación | locomoción | estabilidad
plano                 -- sagital | frontal | transversal | multiplanar
es_bilateral          -- bool
musculos_primarios[]
musculos_secundarios[]
equipo[]              -- técnico: nada | mancuernas | barra | banco | silla | ...
ubicacion             -- universal | casa | gym | exterior | casa/gym | exterior/casa | gym/exterior
requiere[]            -- para el usuario: nada | colchoneta | silla | mancuernas | banco | pelota | soga | barra | mesa | pared | conos | caja | escalera | palo
formato               -- reps | tiempo | distancia
fase_sesion[]         -- calentamiento | principal | cierre | cualquiera
dificultad            -- 1–5 dentro del nivel Anselmi
contraindicaciones[]  -- lumbalgia | rodilla | hombro | tobillo | cervical | ninguna
variantes_mas_faciles[] -- ids de regresiones
variantes_mas_dificiles[] -- ids de progresiones
en_plan_actual        -- bool
video_url
notas
```

### Sesión (armado por Anselmi)

```
sesion: {
  bloques: [
    { orden: 1, tipo: "zona_media",         duracion_min: 6,     ejercicios: [...] },
    { orden: 2, tipo: "dinamico",           duracion_min: 5,     ejercicios: [...] },
    { orden: 3, tipo: "fuerza",             duracion_min: 30–40, ejercicios: [...] },
    { orden: 4, tipo: "velocidad_transfer", duracion_min: null,  ejercicios: [...] },
    { orden: 5, tipo: "flexibilidad",       duracion_min: 5–10,  ejercicios: [...] }
  ]
}
```

### Día (organización semanal)

```
dia: {
  nombre:              "lunes" ... "domingo"
  carga_relativa:      1–12
  rol:                 "inicio" | "alto" | "pico" | "valle" | "segunda_ola" | "pico_opcional" | "recuperacion"
  bloque_principal:    "fuerza" | "coordinacion" | "dinamico" | "pliometrico" | "recuperacion"
  longboard_intensidad: "suave" | "habitual" | "intenso" | "pico"
}
```

### Microciclo (semana)

```
microciclo: {
  variacion:    1 | 2 | 3 | 4,  -- patron de carga entre las 3 sesiones principales
  sesiones: [
    { dia: "lunes",     carga: 1–5, circuito: "B" },
    { dia: "miercoles", carga: 1–5, circuito: "A+fuerza" },
    { dia: "viernes",   carga: 1–5, circuito: "A|B" }
  ],
  semana_numero: int,
  notas: string
}
```

### Mesociclo (bloque de semanas)

```
mesociclo: {
  duracion_semanas: 6,
  semanas: [
    { numero: 1, variacion_microciclo: 1, carga_relativa: 50, tipo: "inicio"           },
    { numero: 2, variacion_microciclo: 2, carga_relativa: 65, tipo: "desarrollo"       },
    { numero: 3, variacion_microciclo: 3, carga_relativa: 73, tipo: "desarrollo"       },
    { numero: 4, variacion_microciclo: 2, carga_relativa: 85, tipo: "pico"             },
    { numero: 5, variacion_microciclo: 1, carga_relativa: 60, tipo: "descarga"         },
    { numero: 6, variacion_microciclo: 2, carga_relativa: 95, tipo: "supercompensacion"}
  ]
}
```

### Macrociclo (largo plazo)

```
macrociclo: {
  fase_actual:      "funcional" | "reclutamiento" | "especifica",
  semana_inicio:    date,
  duracion_semanas: int,
  objetivo:         string,
  mesociclos:       [ ...refs ]
}
```

---

## Lógica de switch por contexto (ejercicio equivalente)

El switch ocurre en dos momentos:

| Momento | Quién lo activa | Dónde |
|---------|----------------|-------|
| **Automático** | La app, al generar el plan del día | En segundo plano |
| **Manual** | El usuario, tocando "swap" | Pantalla de pre-sesión |

Ambos usan la misma lógica de filtrado:

```
contexto: {
  ubicacion_hoy:       "casa" | "gym" | "exterior"
  equipo_disponible:   ["colchoneta", "silla", ...]  ← declarado por el usuario
  contraindicaciones:  ["lumbalgia", ...]             ← perfil del usuario
}

para cada ejercicio a reemplazar:
  candidatos = ejercicios donde:
    patron_motor      == original.patron_motor
    nivel_anselmi     in [original.nivel - 1, original.nivel, original.nivel + 1]
    ubicacion         incluye contexto.ubicacion_hoy
    requiere          ⊆ contexto.equipo_disponible
    contraindicaciones ∩ contexto.contraindicaciones == ∅

ordenar candidatos por: nivel_anselmi más cercano → dificultad más cercana
```

| Contexto del usuario | Filtro ubicacion | Filtro requiere |
|---------------------|-----------------|-----------------|
| Casa sin equipo | `universal` o `casa` | `nada` o `colchoneta` |
| Casa con mancuernas | `universal` o `casa` | + `mancuernas` |
| Gym | `universal`, `casa`, `gym` | cualquier equipo gym |
| Parque / calle | `universal` o `exterior` | `nada`, `soga`, `conos` |

---

## Esquema de tablas para DB relacional

```sql
-- Tabla principal
ejercicios (id, nombre, nivel_anselmi, patron_motor, plano, es_bilateral,
            formato, dificultad, en_plan_actual, video_url, notas)

-- Relaciones (many-to-many)
ejercicio_musculos_primarios    (ejercicio_id, musculo)
ejercicio_musculos_secundarios  (ejercicio_id, musculo)
ejercicio_equipo                (ejercicio_id, equipo)
ejercicio_ubicacion             (ejercicio_id, ubicacion)   -- 'universal'|'casa'|'gym'|'exterior'
ejercicio_requiere              (ejercicio_id, item)         -- items de requiere[]
ejercicio_fase_sesion           (ejercicio_id, fase)
ejercicio_contraindicaciones    (ejercicio_id, contraindicacion)
ejercicio_variantes             (ejercicio_id, variante_id, tipo) -- 'regresion'|'progresion'
ejercicio_nombres_alternativos  (ejercicio_id, nombre_alternativo)

-- Plan y progresión
usuarios        (id, nombre, perfil_json, fase_macrociclo, semana_macrociclo)
sesiones        (id, usuario_id, fecha, duracion_real, completitud_pct, notas)
sesion_ejercicios (sesion_id, ejercicio_id, orden, completado, duracion_seg, dificultad_reportada)
microciclos     (id, usuario_id, variacion, semana_numero)
mesociclos      (id, usuario_id, semana_inicio, fase_macrociclo)
```

---

## Principios de diseño

1. **El entrenamiento primero:** en modo sesión, la UI debe estar al mínimo — nada que distraiga
2. **Fricción cero:** un tap o una palabra para lo más frecuente
3. **Siempre adaptable:** ninguna sesión es igual a la anterior, la app aprende
4. **Metodología sólida:** toda la lógica sigue el marco de Anselmi — no es un generador random de ejercicios
5. **Voz primero en movimiento:** el usuario no debería tener que mirar el celular mientras entrena

---

## Base de datos ya construida (en este repo)

| Recurso | Ubicación | Estado |
|---------|-----------|--------|
| 69 ejercicios clasificados | [`../ejercicios/`](../ejercicios/README.md) | ✓ |
| Schema de ejercicios | [`../ejercicios/schema.md`](../ejercicios/schema.md) | ✓ |
| Orden de intensidad Anselmi | [`../metodologia/orden-intensidad-anselmi.md`](../metodologia/orden-intensidad-anselmi.md) | ✓ |
| Organización semanal | [`../metodologia/organizacion-semanal-anselmi.md`](../metodologia/organizacion-semanal-anselmi.md) | ✓ |
| Microciclos | [`../metodologia/microciclos-anselmi.md`](../metodologia/microciclos-anselmi.md) | ✓ |
| Macrociclo integrado | [`../metodologia/macrociclo-integrado-anselmi.md`](../metodologia/macrociclo-integrado-anselmi.md) | ✓ |
| Variación de carga (mesociclo) | [`../metodologia/variacion-carga-semanal-anselmi.md`](../metodologia/variacion-carga-semanal-anselmi.md) | ✓ |
| Armado de sesión | [`../metodologia/armado-de-sesion-anselmi.md`](../metodologia/armado-de-sesion-anselmi.md) | ✓ |
| Plan actual de Diego | [`../planes/longboard-complemento/`](../planes/longboard-complemento/) | ✓ |
| Assets visuales (slides Anselmi) | [`../../assets/anselmi/`](../../assets/anselmi/README.md) | ✓ |
| Video fuente — Parte 1 | [YouTube](https://www.youtube.com/watch?v=XWN5EGva2jo) | ✓ |
| Video fuente — Parte 2 | [YouTube](https://www.youtube.com/watch?v=3r6jcVtaeWo) | ✓ |

---

## Roadmap

### Base de datos de ejercicios
- [ ] Completar videos faltantes en los 5 archivos de nivel
- [ ] Agregar campo `variantes_mas_faciles` / `variantes_mas_dificiles` a todas las filas
- [ ] Agregar ejercicios de las slides de Anselmi (zona media, tracción, empuje, tren inferior)
- [ ] Script de exportación MD → JSON / CSV para importar a DB real

### App
- [ ] Definir stack (iOS nativa vs. web app)
- [ ] Prototipo de modo sesión (flujo mínimo: cuenta regresiva + confirmación)
- [ ] Motor de switch dinámico por contexto (ubicacion + requiere)
- [ ] Integración agente AI con contexto de usuario
- [ ] Control por voz
- [ ] Música adaptativa
- [ ] Integración HealthKit / smartwatch

---

## Preguntas abiertas

- [ ] ¿App nativa (iOS first) o web app progresiva (PWA)?
- [ ] ¿El agente AI corre en el dispositivo o en la nube?
- [ ] ¿Multi-usuario desde el arranque o arrancamos con el caso de Diego?
- [ ] ¿Qué pasa con el plan cuando el usuario saltea varios días seguidos?
- [ ] ¿Cómo modela la app la "equivalencia" entre ejercicios para el switch?
- [ ] ¿Cómo maneja contraindicaciones activas del usuario en tiempo real?
- [ ] Monetización / modelo de negocio (si aplica)
