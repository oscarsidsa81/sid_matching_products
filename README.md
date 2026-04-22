# sid_matching_products

Repositorio base del proyecto de matching de productos de piping y stock.

## Objetivo

Convertir una descripción de alcance de cliente o una fila de plantilla en una propuesta de matching trazable contra el stock, priorizando un enfoque determinista basado en la codificación interna del producto y dejando el fuzzy solo para capas finales controladas.

El objetivo no es solo encontrar coincidencias de texto, sino reconstruir una referencia esperada a partir de campos estructurados y validar después contra referencias y descripciones reales del stock.

## Entradas admitidas

El sistema debe poder trabajar con estas entradas:

- Descripción libre de cliente.
- Filas de Excel con campos separados en varias celdas.
- Plantillas de codificación de productos.
- Maestro de stock con referencia interna, descripción, código y cantidad.
- Reglas de negocio aprendidas del proyecto.

## Salidas esperadas

Cada propuesta de matching debe devolver, como mínimo:

- clasificación del resultado
- referencia interna candidata
- descripción candidata
- familia detectada
- campos estructurados utilizados para codificar
- validaciones superadas o fallidas
- comentario de diferencias
- observaciones sobre necesidad de aprobación comercial o técnica

## Clasificaciones de match

### `match_100`

Usar cuando el producto solicitado y el producto de stock coinciden en los atributos obligatorios de validación final:

- familia
- tipo
- material
- conexión
- tamaño
- rating cuando aplique
- facing cuando aplique
- schedule o equivalencia por espesor cuando aplique

También puede entrar aquí un caso donde cambie una etiqueta comercial de schedule, pero el espesor real coincida exactamente según ASME B36.10 o B36.19 y la regla del proyecto permita tratarlo como equivalente técnico exacto; en ese caso conviene dejarlo documentado como exacto por espesor o bien clasificarlo aparte según el flujo operativo.

### `match_equivalente`

Usar cuando no hay coincidencia literal completa de todos los textos, pero sí existe equivalencia técnica aceptada por las reglas del proyecto. Ejemplos:

- mismo producto con distinta etiqueta de schedule pero mismo espesor real
- diferencias menores de nomenclatura interna
- galvanizado no confirmado pero pieza base compatible

Nunca relajar tamaño. Los tamaños deben coincidir exactamente, salvo la excepción definida para OLETS.

### `alternativa_aprobacion`

Usar cuando la pieza encontrada puede servir como sustitución comercial o técnica superior o cercana, pero requiere aprobación explícita. Ejemplo confirmado en el proyecto:

- pedido en A304L y stock en A316L para ciertas bridas inoxidables: alternativa válida sujeta a aprobación del cliente

No usar esta clasificación para sustituciones a la baja no permitidas.

### `no_match`

Usar cuando falla cualquier validación crítica o no existe candidato fiable. También cuando solo aparece una similitud textual sin validación estructural suficiente.

## Flujo por capas

El flujo operativo completo está descrito en `docs/layer_flow.md`, pero resumidamente debe seguir este orden:

1. normalización del texto de entrada
2. extracción de atributos estructurados
3. separación estricta por familia
4. reconstrucción determinista de referencia esperada
5. búsqueda de candidatos por referencia y descripción
6. validación final obligatoria
7. anotación de diferencias en comentarios
8. fuzzy controlado solo para alternativas permitidas

## Columnas de codificación obligatorias

Siempre que sea posible, exponer y trabajar con estas columnas:

- FAMILIA
- NORMA
- TIPO
- ESTÁNDAR
- GRADO
- ADD
- NPS1
- END1
- SCH1
- RATING
- NPS2
- END2
- SCH2
- NPS3

## Reglas clave ya consolidadas

- Separación estricta por familias: FORGED, BW, FLANGES y PIPE.
- Validación final obligatoria de tipo, material, conexión, tamaño y rating.
- En fuzzy no se relaja tamaño.
- Excepción OLETS: el diámetro pequeño debe coincidir exactamente; el diámetro grande o rango puede tratarse con cobertura controlada.
- COUPLING implica FULL COUPLING por defecto; HALF COUPLING solo si se especifica.
- Rosca por defecto NPT salvo indicación explícita BSP.
- En TEE, un diámetro implica equal; dos diámetros implica reducing.
- En NIPPLE, con rating suele tratarse como forged HEX por defecto; sin rating y con material de tubería puede ser pipe nipple; con dos diámetros es reducing.
- Galvanizado no bloquea el matching, pero debe anotarse.
- En bridas, el facing debe validarse estrictamente con normalización RF↔R, FF↔F y RTJ↔RJ sin mezclar familias de facing.

## Documentación del repositorio

- `docs/matching_rules.md`: reglas de negocio casi programables.
- `docs/layer_flow.md`: flujo por capas y orden de decisión.
- `docs/reference_fields.md`: definición operativa de los campos de codificación.

## Estado actual

Este repositorio recoge el conocimiento consolidado hasta la fecha para poder convertirlo después en lógica reproducible, scripts y validaciones ejecutables.
