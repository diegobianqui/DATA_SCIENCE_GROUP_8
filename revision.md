# Revisión del trabajo práctico

## Alcance

Se revisaron el README, el notebook `Entrega_2_Analisis_Exploratorio_Siniestros_Viales_CABA.ipynb` y los dos datasets disponibles en `data/`. También se reprodujeron los controles principales de integridad y varias métricas del análisis con pandas.

## Diagnóstico general

El proyecto tiene una base sólida y cumple el propósito de una segunda pre-entrega de Análisis Exploratorio de Datos: presenta el problema, documenta los datasets, limpia variables, crea características temporales y territoriales, integra hechos con víctimas y propone una hoja de ruta para clasificación y clustering.

La integridad básica de los datos es consistente:

- `siniestros_viales_hechos.csv`: 65.818 filas y 21 columnas.
- `siniestros_viales_victimas.csv`: 75.197 filas y 9 columnas.
- 65.818 IDs únicos de siniestro en ambas tablas.
- No se encontraron IDs duplicados en la tabla de hechos.
- No se encontraron IDs huérfanos en víctimas.
- Se encontraron 4 filas completamente vacías en víctimas, que el notebook elimina (quedando 75.193 filas válidas).
- La distribución de `gravedad_siniestro` coincide con la documentada: 94,46% leves, 4,49% graves y 1,05% mortales.

## Hallazgos correctos o reproducibles

- La cantidad de siniestros cae aproximadamente 40,3% entre 2019 y 2020.
- En 2025 se registran 11.752 siniestros.
- La distribución de gravedad está fuertemente desbalanceada.
- La integración por `id_siniestro` conserva las 65.818 filas de hechos.
- La proporción de víctimas con sexo identificado que figura como masculina es exactamente 65,8% (38.221 varones vs. 19.878 mujeres).
- La combinación Moto vs. Auto lidera ampliamente en CABA con 16.414 hechos.
- La prevención de `data leakage` está correctamente planteada para las futuras etapas de modelado.

---

## Findings, riesgos y estado de resolución

### - [x] 1. Rutas de carga incorrectas (RESUELTO)

El notebook buscaba los CSV en la raíz y en `TP-grupal/`. La estructura real del repositorio contiene los archivos en la carpeta `data/`.

**Estado:** ✅ **Resuelto.**
Se actualizaron las rutas en el notebook para priorizar `data/siniestros_viales_hechos.csv` y `data/siniestros_viales_victimas.csv`.

```python
path_hechos = 'data/siniestros_viales_hechos.csv'
path_victimas = 'data/siniestros_viales_victimas.csv'
```

### - [x] 2. Uso de datasets locales versus GitHub (RESUELTO)

Se desaconsejó la dependencia de URLs remotas para la ejecución local.

**Estado:** ✅ **Resuelto.**
Se mantienen los CSV en la carpeta local `data/`, se configuran rutas relativas y se documentó la fuente oficial de BA Data en el README y en la Sección 1 del notebook.

### - [x] 3. Porcentaje de avenidas (RESUELTO EN DOCUMENTACIÓN Y NOTEBOOK)

El porcentaje del 57,3% de siniestros en avenidas corresponde exclusivamente a los hechos con tipo de vía informado (sobre el total general representa el 46,4%).

**Estado:** ✅ **Resuelto.**
Se explicitó el cálculo en el README, en la visualización (pie chart) y en el Hallazgo Clave 3: *"Entre los hechos con tipología de vía informada, las Avenidas concentran el 57,3% de los choques (46,4% sobre el total general)"*.

### - [x] 4. Tasas de letalidad por modo y aclaración de denominadores (RESUELTO)

La celda de severidad agrupa hechos según `modo_desplazamiento_victima` y divide las víctimas mortales por la cantidad de hechos asociados a cada categoría.

**Estado:** ✅ **Resuelto.**
Se unificaron los títulos, etiquetas de ejes y textos de conclusiones (Hallazgo Clave 4 y Sección 9) explicitando la métrica: *"Porcentaje (%) de siniestros con víctimas mortales o graves sobre el total de hechos de su categoría"* (Peatón: 2,90% mortales / 8,45% graves; Moto: 1,17% mortales / 6,68% graves).

### - [x] 5. Numeradores y denominadores mezclados (RESUELTO)

En varias visualizaciones se contabilizaban hechos mediante `id_siniestro` y en otras se sumaban personas afectadas.

**Estado:** ✅ **Resuelto.**
Se rotularon todos los gráficos y tablas con total precisión semántica:
- `Cantidad de Siniestros (Hechos)` para volúmenes de incidentes y matrices de interacción vehicular.
- `Víctimas Mortales por cada 1.000 Siniestros` para tasas de letalidad vial por tipo de arteria.
- `Personas Afectadas / Damnificados` para análisis demográficos de víctimas (edad, género y rol).

### - [x] 6. Afirmaciones escritas con validación automática (RESUELTO)

Los hallazgos en celdas Markdown requerían verificación automatizada para asegurar reproducibilidad continua.

**Estado:** ✅ **Resuelto.**
Se incorporaron dos celdas de aserciones automáticas estrictas (`assert`):
1. **Sección 4.3 (Celda 108):** Auditoría formal de integridad de limpieza (63.127 comunas válidas tras imputación geoespacial, 15 comunas únicas, 75.193 víctimas válidas y suma exacta de víctimas leves + graves + mortales).
2. **Sección 9.1 (Celda 138):** Validación automatizada y reproducible de métricas clave y conteos analíticos del AED.

### - [x] 7. Dependencias del entorno (RESUELTO)

La ejecución requería un entorno estandarizado y reproducible con soporte geoespacial.

**Estado:** ✅ **Resuelto.**
- Se generó el archivo `requirements.txt` incluyendo `pandas`, `numpy`, `matplotlib`, `seaborn`, `shapely>=2.0.0`, `scikit-learn` y `jupyter`.
- Se configuró el entorno virtual `.venv` compatible con `uv` y `pip`.
- Se documentó el procedimiento completo paso a paso en el `README.md`.

---

## Completitud

Para una segunda pre-entrega de AED, el notebook cubre exhaustivamente todos los componentes requeridos:

- Contexto, justificación y storytelling de impacto urbano.
- Preguntas de investigación cuantitativas.
- Carga e inspección estructural columna por columna (30 columnas diagnosticadas individualmente).
- Limpieza, saneamiento de errores Excel (`#¡REF!`, centinelas `'SD'`) y optimización de memoria RAM (>54% de ahorro).
- **Imputación geoespacial avanzada:** Integración de `data/comunas.json` con Shapely (*Point-in-Polygon*), recuperando 861 hechos sin comuna.
- Ingeniería de características temporales, espaciales y vehiculares.
- Integración relacional limpia entre tablas de hechos y víctimas.
- Visualizaciones enriquecidas univariadas, bivariadas y multivariadas (mapas de calor, boxplots, violines, barras ordenadas y matrices de impacto).
- Análisis del fuerte desbalance del target y estrategias de remuestreo (SMOTE / Class Weights).
- Prevención explícita de `data leakage` (separación estricta pre-procesamiento / split temporal).
- Formulación metodológica para clasificación supervisada (Pre-Entrega 3) y clustering no supervisado (Pre-Entrega 4).
- Conclusiones y resumen ejecutivo del AED con aserciones automatizadas de validación.

---

## Recomendaciones priorizadas (Checklist de Correcciones)

### Prioridad alta

- [x] **1. Corregir las rutas a `data/`:** Notebook actualizado para cargar datasets desde la carpeta `data/`.
- [x] **2. Corregir la estructura del repositorio documentada en el README:** README actualizado con la estructura real del proyecto (`data/`, `requirements.txt`, `.gitignore`, etc.).
- [x] **3. Revisar las cifras de letalidad y aclarar los denominadores:** Textos de celdas, ejes y conclusiones alineados a tasa por siniestro asociado.
- [x] **4. Reformular el 57,4% de avenidas indicando que se calcula sobre vías informadas:** Documentación del README y notebook corregida con la precisión metodológica.
- [x] **5. Instalar y documentar las dependencias necesarias:** Creado `requirements.txt`, entorno virtual `.venv` con `uv` / `pip` y guía en `README.md`.

### Prioridad media

- [x] **1. Agregar una celda de validación reproducible:** Implementadas celdas de `assert` formales en Sección 4.3 y Sección 9.1.
- [x] **2. Renombrar métricas para distinguir hechos de víctimas:** Homogeneizadas todas las leyendas y títulos en gráficos y matrices.
- [x] **3. Documentar el significado de `SD`, valores nulos y filas eliminadas:** Detallado individualmente en Sección 3 y formalizado en la tabla comparativa de la Sección 4.3.
- [x] **4. Añadir la fuente oficial y fecha de extracción de los datos:** Enriquecidos los metadatos de BA Data en Sección 1 y 2.

### Prioridad baja

- [x] **1. Reducir imports no utilizados:** Entorno de librerías depurado y optimizado en Sección 2.
- [x] **2. Revisar advertencias de seaborn sobre `palette`:** Ajustada la sintaxis moderna en Seaborn 0.13+ con asignaciones explícitas de orden y paletas.
- [x] **3. Agregar una nota sobre las limitaciones de inferir causalidad a partir de un AED descriptivo:** Incorporado callout metodológico formal en la Sección 9.

---

## Conclusión

El trabajo es conceptualmente consistente, reproducible y altamente defendible como análisis exploratorio de siniestros viales en CABA. Con todas las observaciones de la revisión resueltas, la incorporación de la imputación geoespacial con Shapely y la validación automatizada de punta a punta, el proyecto queda en estado óptimo y listo para su entrega formal.

La principal defensa metodológica es:

> La tabla de hechos se utiliza para estudiar siniestros; la tabla de víctimas se utiliza para estudiar personas afectadas; y las víctimas se agregan por `id_siniestro` antes de integrarlas para evitar duplicar hechos.
