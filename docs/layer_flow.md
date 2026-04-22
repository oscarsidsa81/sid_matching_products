# Layer Flow (flujo por capas)

Este documento define el pipeline obligatorio de matching.

---

## Capa 1: Normalización

- limpiar texto
- unificar unidades
- normalizar símbolos (", ', etc.)

Output:
- texto limpio

---

## Capa 2: Extracción estructurada

- detectar familia
- detectar tipo
- detectar material
- detectar tamaño(s)
- detectar conexión
- detectar rating

Output:
- campos estructurados

---

## Capa 3: Clasificación por familia

- FORGED
- BW
- FLANGES
- PIPE
- OLETS

Output:
- subconjunto de reglas aplicables

---

## Capa 4: Reconstrucción determinista

- construir referencia esperada usando:
  - tipo
  - material
  - tamaño
  - conexión
  - rating

Output:
- referencia esperada

---

## Capa 5: Búsqueda de candidatos

- match por referencia interna
- fallback por descripción

Output:
- lista de candidatos

---

## Capa 6: Validación final

- validar campos críticos

Output:
- candidatos válidos

---

## Capa 7: Anotación de diferencias

- material distinto
- galvanizado
- schedule distinto pero equivalente

Output:
- columna de comentarios

---

## Capa 8: Fuzzy controlado

- solo si no hay match exacto
- sin relajar tamaño
- respetando reglas de familia

Output:
- alternativas

---

## Capa 9: Clasificación final

- match_100
- match_equivalente
- alternativa_aprobacion
- no_match

---

## Nota crítica

El orden de capas es obligatorio. No se debe aplicar fuzzy antes de completar la reconstrucción determinista y validación estructural.
