# Matching Rules (versión operativa)

Este documento recoge reglas de negocio en formato cercano a implementación.

---

## Reglas generales

### Validación final obligatoria
Condition:
- existe candidato

Action:
- validar coincidencia de:
  - familia
  - tipo
  - material
  - conexión
  - tamaño
  - rating (si aplica)

Classification:
- match_100 si todo coincide
- no_match si falla cualquier campo crítico

Comment:
- indicar qué campo falla en caso negativo

---

## FORGED

### CAP SW ejemplo
Condition:
- familia = FORGED
- tipo = CAP
- conexión = SW
- tamaño coincide
- rating coincide

Action:
- construir referencia esperada
- buscar coincidencia en referencia interna

Classification:
- match_100

Comment:
- ejemplo validado: 2" CAP 6000# ASTM A105 SW

---

## BW

### Equivalencia de schedule por espesor
Condition:
- familia = BW
- mismo NPS
- espesor real coincide según ASME B36.10 o B36.19

Action:
- aceptar equivalencia

Classification:
- match_equivalente o match_100 según política

Comment:
- indicar equivalencia por espesor

---

## FLANGES

### Validación de facing
Condition:
- familia = FLANGES

Action:
- normalizar:
  - RF ↔ R
  - FF ↔ F
  - RTJ ↔ RJ

Classification:
- match solo si facing coincide tras normalización

Comment:
- indicar conversión aplicada

---

## PIPE

### Validación de extremos
Condition:
- familia = PIPE

Action:
- validar tipo de extremo (BE, PE, etc.)

Classification:
- no_match si no coincide

Comment:
- indicar diferencia de extremos

---

## OLETS

### Regla de diámetros
Condition:
- familia = OLETS

Action:
- exigir coincidencia exacta del diámetro pequeño
- permitir fuzzy en diámetro grande o rango

Classification:
- match_equivalente si cobertura válida

Comment:
- indicar rango cubierto

---

## Materiales inox

### Sustitución 304L vs 316L
Condition:
- pedido = A304L
- stock = A316L

Action:
- permitir como alternativa

Classification:
- alternativa_aprobacion

Comment:
- material superior, requiere aprobación

---

## Galvanizado

Condition:
- diferencia en galvanizado

Action:
- no bloquear matching

Classification:
- match_equivalente

Comment:
- galvanizado no confirmado
