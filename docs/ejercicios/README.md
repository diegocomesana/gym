# Base de ejercicios

> Catálogo clasificado según el **orden de intensidad de Anselmi**. Cada archivo es una colección (nivel). Cada fila es un registro. Diseñado para migrar a base de datos real cuando se construya la app.

---

## Archivos

| Archivo | Nivel | Ejercicios | Descripción |
|---------|-------|------------|-------------|
| [nivel-1-fuerza.md](./nivel-1-fuerza.md) | 1 | 24 | Tensión muscular con carga, series × reps |
| [nivel-2-dinamico.md](./nivel-2-dinamico.md) | 2 | 15 | Movilidad activa, calentamiento fluido |
| [nivel-3-coordinacion.md](./nivel-3-coordinacion.md) | 3 | 11 | Control motor, arranque y freno |
| [nivel-4-pliometrico.md](./nivel-4-pliometrico.md) | 4 | 10 | Ciclo estiramiento-acortamiento, saltos |
| [nivel-5-pliometrico-coordinativo.md](./nivel-5-pliometrico-coordinativo.md) | 5 | 9 | Explosividad + coordinación compleja |
| [schema.md](./schema.md) | — | — | Definición de campos y valores posibles |

**Total: 69 ejercicios**

---

## Columna `en plan` 

Los ejercicios marcados con ✓ en `en plan` están activos en el plan actual de Diego:  
[`../planes/longboard-complemento/`](../planes/longboard-complemento/)

---

## Cómo agregar un ejercicio

1. Identificar el nivel Anselmi (1–5)
2. Abrir el archivo del nivel correspondiente
3. Agregar una fila respetando todos los campos del [schema](./schema.md)
4. Si hay video, incluir la URL

---

## Roadmap

> Roadmap completo de la app en [`../app/vision-y-features.md`](../app/vision-y-features.md)

Pendientes específicos de esta base de ejercicios:
- [ ] Agregar campo `variantes_mas_faciles` / `variantes_mas_dificiles` a todas las filas
- [ ] Completar videos faltantes
- [ ] Agregar ejercicios de las slides de Anselmi (zona media, tracción, empuje, tren inferior)
- [ ] Script de exportación MD → JSON / CSV para importar a DB real
- [ ] Separar en archivos individuales por ejercicio cuando el volumen lo justifique

---

> Marco metodológico: [`../metodologia/orden-intensidad-anselmi.md`](../metodologia/orden-intensidad-anselmi.md)
