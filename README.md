# Calculadora de categoría PED (Anexo II) — Directiva 2014/68/UE

**Artefacto publicado:** https://claude.ai/artifact/Cujie1cXXjWqE86muJwsG1
**Archivo descargable:** `index.html` (autocontenido, sin dependencias externas).

## Qué hace
Página con pestañas (Cuadros 1-9 del Anexo II), redibujados como **gráficos vectoriales interactivos** (motor Chart.js, ejes log-log reales) a partir de las fórmulas de las líneas de demarcación — ya no son imágenes escaneadas del PDF. En la pantalla inicial se introduce PS (bar), V (litros) y el fluido (desplegable alfabético). La herramienta:
1. Clasifica el fluido en grupo 1 o 2 (Art. 13, según CLP 1272/2008) y en naturaleza gas/líquido.
2. Selecciona el Cuadro correspondiente según Art. 4(1):
   - Cuadro 1: recipientes, gas, grupo 1 — Art. 4(1)(a)(i) 1er guion
   - Cuadro 2: recipientes, gas, grupo 2 — Art. 4(1)(a)(i) 2º guion
   - Cuadro 3: recipientes, líquido, grupo 1 — Art. 4(1)(a)(ii) 1er guion
   - Cuadro 4: recipientes, líquido, grupo 2 — Art. 4(1)(a)(ii) 2º guion
   - Cuadros 5-9: se renderizan igual (con relleno de categoría y tooltip), pero sin marcador automático porque requieren temperatura (Cuadro 5) o DN (Cuadros 6-9), no PS+V.
3. Calcula la categoría (SEP, I, II, III, IV) y, **automáticamente al pulsar "Calcular categoría"** (sin necesidad de ningún clic adicional), muestra debajo del resultado el gráfico del cuadro correspondiente con el punto de trabajo (PS, V) marcado. Un enlace secundario ("Ver este cuadro con su descripción completa") lleva a la pestaña de ese cuadro con su texto legal completo.

## Motor de gráficos: Chart.js
- Reescrito sobre **Chart.js 4.5.1** + `chartjs-plugin-zoom` (+ hammer.js para gestos táctiles), con aspecto más "moderno" (ejes logarítmicos nativos, bandas de categoría suavizadas, leyenda, interacción estándar de librería de gráficos) en vez del motor Canvas artesanal de la iteración anterior.
- Las 9 funciones `cuadroN(PS,X)` (ya verificadas) no cambiaron: para cada cuadro se muestrea su fórmula en una rejilla fina log-log (130×360 puntos) para obtener los bordes superior/inferior de cada banda de categoría, y esas bandas se dibujan como datasets de Chart.js con relleno apilado (evita rehacer a mano la geometría de cada frontera).
- Las 3 librerías (~244 KB) están **inlineadas** dentro del propio HTML (los artefactos publicados no pueden cargar scripts externos de CDN), así que el archivo sigue siendo autocontenido.
- Etiquetas de categoría (círculos S/I/II/III/IV) y el marcador de la calculadora se dibujan con plugins propios registrados en Chart.js (`catLabels`, `marker`).
- Tooltip propio (no el de Chart.js) mediante `mousemove` sobre el canvas, leyendo `chart.scales.x/y.getValueForPixel()`.
- Zoom (rueda del ratón / pellizco táctil) y panorámica (arrastrar), con botón "Restablecer", vía `chartjs-plugin-zoom`.
- El gráfico de la calculadora (`cv-calc`) se reconstruye por completo cada vez que el cuadro aplicable cambia (p. ej. al cambiar de fluido de un gas a un líquido), no solo se reposiciona el marcador — se detecta el cambio de cuadro y se destruye/recrea la instancia de Chart.js correspondiente.

## Fórmulas — Cuadros 1-4 (verificadas contra el diagrama original)
Ya probadas en la iteración anterior (incluye la corrección del límite PS=500 que faltaba en Cuadro 4).

## Fórmulas — Cuadros 5-9 (reconstruidas a partir de los valores rotulados en cada diagrama)
- **Cuadro 5** (equipos con llama/aporte de calor, Art. 4(1)(b)): V>2 L, tope PS=32 bar combinado con PS·V=3000 (diagonal) hasta V=1000 L (a partir de ahí, categoría IV siempre); bandas internas PS·V=50 y PS·V=200.
- **Cuadro 6** (tuberías, gas, grupo1): DN 25/100/350, PS·DN=1000 y 3500.
- **Cuadro 7** (tuberías, gas, grupo2): DN 32/100/250, PS·DN=1000/3500/5000.
- **Cuadro 8** (tuberías, líquido, grupo1): DN 25/200, PS·DN=2000, techos PS=10 y PS=500.
- **Cuadro 9** (tuberías, líquido, grupo2): DN 200/500, PS·DN=5000, techo PS=10 (solo categorías I y II).

Estas cinco fórmulas se derivaron leyendo los valores explícitos rotulados en cada diagrama del Anexo II (páginas 54-56) y razonando la topología de las regiones (qué zona queda a cada lado de cada línea); a diferencia de los Cuadros 1-4, **no** se verificaron con un tercer método independiente (ej. calibración píxel a píxel), así que antes de un uso normativo conviene contrastar la forma exacta de cada región contra el texto oficial, especialmente los límites inferiores "excluidos" (zonas en blanco) de los Cuadros 6-7.

## Fluidos incluidos (54) y clasificación asumida
Lista completa, orden alfabético (español), con grupo (1/2, Art. 13) y naturaleza (gas/líquido, Art. 4(1)(a)):

Aceite ligero, Aceite pesado/fuel-oil, Agua, Aire comprimido, Amoníaco verde, ATJ-SPK (derivado alcohol), AVGAS 100LL (F-18), Butano, CO₂, Diésel, Diésel marino MDO, F-54 (gasóleo OTAN), F-65 (fuelóleo OTAN), F-67 (fuelóleo OTAN), F-75 (destilado naval, turbinas), F-76 (destilado naval), FT-SPK (Sintético), Fueloil pesado, Gas natural/genérico, Gasóleo, Gasóleo B, Gasóleo C, Gasóleo marino MGO, Gasolina, GLP, GNL, HEFA-SPK (Biocombustible), HFO, Jet A, Jet A-1 (F-35), Jet Fuel (queroseno de aviación), JP-1, JP-10, JP-2, JP-3, JP-4 (F-40), JP-5 (F-44), JP-6 (misiles), JP-8 (F-34), JP-8+100 (F-24), JPTS, Metanol, Propano, Queroseno, RP-1 (China), RP-2 (China), RP-3 (China), RP-5 (China), SAF (Sustainable Aviation Fuel), T-1, T-2, T-6, T-8V, TS-1 (TC-1) (Rusia).

Nota sobre el orden: es alfabético estricto (`localeCompare` español), no numérico — por eso "JP-10" aparece antes que "JP-2" (compara carácter a carácter: "1" < "2"), tal como se pidió ("por orden alfabético").

Notas de clasificación relevantes:
- **Combustibles "wide-cut" (muy inflamables, grupo 1):** JP-2, JP-3, JP-4 (F-40), T-2, RP-5 (China) — mezclas nafta/queroseno de punto de inflamación bajo, a diferencia del resto de la familia JP/T/RP que son queroseno puro de punto de inflamación alto (grupo 2).
- **JP-4 (F-40):** José pidió "F-44 (JP-5)" y "F-44 (JP-4)" — el segundo es un error de código OTAN habitual (F-44 es JP-5); el código correcto de JP-4 es **F-40**, usado aquí en vez de duplicar F-44.
- **JP-8+100 (F-24):** mismo producto que JP-8 con aditivo de estabilidad térmica; código OTAN F-24 fusionado en esta única entrada.
- **AVGAS** renombrado a "AVGAS 100LL (F-18)" para incorporar el código OTAN sin duplicar la entrada.
- **SAF (HEFA-SPK, FT-SPK, ATJ-SPK):** combustibles sintéticos/biogénicos "drop-in", especificación asimilada a Jet A-1 (grupo 2).
- **RP-1/RP-2/RP-5 (China) y los combustibles rusos T-1/T-2/T-6/T-8V/TS-1:** clasificación por analogía con sus equivalentes occidentales (queroseno de alto punto de inflamación → grupo 2; wide-cut → grupo 1); **no verificados contra la norma nacional exacta** — revisar ficha técnica antes de uso normativo.
- **Amoníaco verde** y **Metanol**: grupo 1 por ser tóxicos/inflamables además de por presión.
- **GLP** y **GNL**: grupo 1, naturaleza gas (GNL con nota sobre almacenamiento criogénico, ya que su presión de vapor real depende de la TS de diseño).

Para los líquidos inflamables de categoría 3 en general (diésel, gasóleos, queroseno, familias Jet/JP/T/RP no "wide-cut", SAF, aceites, fuelóleos) se asumió grupo 2 salvo que la TS máxima admisible supere el punto de inflamación, lo que debe verificarse caso a caso con la FDS y la TS real de servicio.

## Limitaciones
- Cuadros 5-9 no reciben el marcador automático del punto calculado (requieren T o DN, no V).
- La clasificación de grupo de fluido para sustancias límite (hidrocarburos líquidos, códigos OTAN, y sobre todo los combustibles rusos y chinos) es una asunción de ingeniería a confirmar con la ficha de seguridad o especificación nacional real.
- Cuadros 5-9: topología de regiones razonada a partir de los rótulos del diagrama, no verificada por un segundo método independiente — revisar antes de uso normativo.

## Historial de versiones del artefacto
- v1: imágenes PNG recortadas del PDF original, con pestañas.
- v2: motor Canvas artesanal (sin librerías), gráficos vectoriales interactivos para los 9 cuadros.
- v3: reescrito sobre Chart.js; el gráfico aparece automáticamente al calcular, sin clic adicional; corregido un bug de desarrollo por el que el gráfico de la calculadora no se reconstruía al cambiar de cuadro (solo se movía el marcador); quitado el subtítulo introductorio.
- v4: ampliado el listado de fluidos de 14 a 35, con combustibles marinos, de aviación civil y códigos OTAN básicos.
- v5 (actual): ampliado de 35 a 54 fluidos, con la familia completa de combustibles de aviación militar/histórica (JP-1 a JP-10, JPTS, JP-8+100), SAF (HEFA/FT/ATJ-SPK), combustibles rusos (T-1, T-2, T-6, T-8V, TS-1) y chinos (RP-1, RP-2, RP-3, RP-5); archivo descargable renombrado de `categorizacion-ped.html` a `index.html`.
