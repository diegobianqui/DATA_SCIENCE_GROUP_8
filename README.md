# **Proyecto Final de Ciencia de Datos: Seguridad Vial en CABA**
### **Programa EnergIA Digital — Fundación YPF**
**Docente:** Gabriel Acosta | **Tutora:** Belén Laresca  
**Grupo 8:** Diego Bianqui, Luida Medina, Matías Lorenzo  

---

## 📌 **Origen del Dataset**
Los datos fueron obtenidos del portal oficial de datos abiertos del Gobierno de la Ciudad Autónoma de Buenos Aires (**[BA Data](https://data.buenosaires.gob.ar/)**), provistos por el *Observatorio de Movilidad y Seguridad Vial (OMSV)* perteneciente a la Secretaría de Transporte y el Ministerio de Infraestructura y Movilidad de la Ciudad. 

El conjunto está integrado por dos recursos relacionales:
- `siniestros_viales_hechos.csv`: **65.818 hechos** registrados con variables temporales, geográficas y dinámicas de colisión.
- `siniestros_viales_victimas.csv`: **75.197 víctimas** con atributos demográficos y desenlace médico auditado en los 14 hospitales de agudos y seguimiento a 30 días posteriores al siniestro.
- `comunas.json`: Polígonos geográficos oficiales de las **15 comunas de CABA** en formato GeoJSON (WGS84) provistos por el Catastro de CABA.

---

## 📖 **Resumen del Proyecto**
Este proyecto aborda la problemática de la seguridad vial urbana en la Ciudad Autónoma de Buenos Aires entre los años 2019 y 2025 desde un enfoque integral de Ciencia de Datos. Mediante técnicas de limpieza, normalización de variables, ingeniería de características espaciotemporales, imputación geoespacial automatizada (*Point-in-Polygon*), integración relacional y visualizaciones con storytelling, se analizan los patrones de siniestralidad para caracterizar los perfiles de mayor riesgo y sentar las bases de modelos predictivos y de segmentación no supervisada que sirvan de insumo para la toma de decisiones en políticas públicas de tránsito y respuesta de emergencias médicas (SAME).

---

## 🎯 **Objetivo Propuesto**
El objetivo central consiste en responder:  
> *¿Qué factores temporales, territoriales y de interacción vehicular explican y anticipan la gravedad de los siniestros viales en CABA, y cómo podemos identificar agrupamientos naturales (clusters) de accidentes para intervenir preventivamente?*

Para ello, el proyecto se estructura en etapas:
1. **Pre-Entrega 2:** Análisis Exploratorio de Datos (AED), diagnóstico columna por columna (30 variables), limpieza, imputación geoespacial con Shapely, ingeniería de features y visualizaciones.
2. **Pre-Entrega 3:** Modelo de clasificación multiclase supervisado para predecir la gravedad del hecho sin incurrir en *data leakage*.
3. **Pre-Entrega 4:** Modelo de clustering no supervisado para segmentar tipologías de siniestralidad.

---

## 🧹 **Pipeline de Limpieza, Optimización e Imputación Geoespacial**

El notebook implementa un pipeline de preprocesamiento estructurado en dos grandes fases:
1. **Diagnóstico Granular Columna por Columna (Sección 3):**
   - Inspección individual de las **21 columnas de Hechos** y las **9 columnas de Víctimas** mediante celdas de código dedicadas y análisis documental.
   - Saneamiento exhaustivo de centinelas de texto (`'SD'`, `'sd'`, `'SIN DATO'`) y errores de fórmulas rotas de Excel (`#¡REF!`).
   - Descarte formal de **4 filas 100% vacías** al final de la tabla de víctimas (quedando **75.193 filas válidas exactas**).
   - Reconstrucción matemática del total de víctimas (`numero_victimas_leve + grave + mortal`) eliminando 3.276 registros faltantes.
2. **Imputación Geoespacial de Comunas con Shapely (Sección 4):**
   - Para los 3.552 siniestros que carecían originalmente de comuna identificada (`NaN` o `'SD'`), se cruzaron sus coordenadas GPS (`longitud`, `latitud`) contra los polígonos oficiales de `data/comunas.json` mediante consultas espaciales aceleradas (`shapely.prepared.prep`).
   - Se recuperaron con éxito **861 siniestros** (96,5% de los que disponían de coordenadas válidas), alcanzando un **95,9% de cobertura comunal efectiva** (63.127 hechos con comuna identificada y solo 2.691 remanentes sin GPS).
3. **Optimización de Memoria RAM (>54% de Ahorro):**
   - Downcasting de enteros (`int16`, `uint8`), enteros nulleables (`Int8` para comunas y horas) y variables categóricas optimizadas (`category`), reduciendo el consumo de RAM de **58,23 MB a 26,46 MB**.
4. **Validación Automatizada Continua:**
   - Celdas de auditoría formal con aserciones automáticas (`assert`) en Sección 4.3 y Sección 9.1 para garantizar integridad referencial y reproducibilidad matemática.

---

## 📊 **Hallazgos Principales del Análisis Exploratorio (AED)**

1. **Dinámica Temporal y Resiliencia:** Tras una caída del 40,3% en 2020 por el confinamiento de la pandemia COVID-19, la siniestralidad creció sostenidamente hasta registrar 11.752 hechos anuales en 2025, mientras que la mortalidad se mantuvo estable en ~100–110 muertes anuales.
2. **Patrón Horario y Semanal:** De Lunes a Viernes la siniestralidad diurna se intensifica sostenidamente entre las **12:00 y las 18:00 hs**, alcanzando su máxima criticidad a las **17:00 hs** (cerca de 800 colisiones acumuladas) por la salida laboral y congestión vehicular. Los fines de semana el volumen total cae un 45% y se reparte en la tarde y noche (12:00 a 21:00 hs), mientras que las madrugadas registran el menor volumen absoluto de siniestros.
3. **Contraste Territorial y Vías Críticas:** La **Comuna 1** lidera ampliamente en volumen absoluto con **7.343 siniestros (11,16%)**, seguida por **Comuna 15 (5.269 hechos)** y **Comuna 3 (4.913 hechos)**. Sin embargo, las tasas de severidad (% graves o mortales) alcanzan sus máximos en **Comuna 14 (6,78%)**, **Comuna 13 (6,60%)** y **Comuna 12 (6,54%)**, mientras que el sur (**Comunas 4 y 8**) acumula el mayor saldo letal absoluto (140 víctimas mortales combinadas).
4. **Peligrosidad Extrema de Vías Rápidas:** Las **Avenidas** concentran el **57,3%** de los choques con vía informada (46,4% sobre el total general) y las **Calles** el **35,3%**. Sin embargo, las **Autopistas** son la vía con mayor letalidad relativa, registrando **68,2 muertes por cada 1.000 siniestros** (casi 5 veces más que las Avenidas: 14,0/mil y 8,5 veces más que las Calles: 8,0/mil), como consecuencia directa de la alta velocidad.
5. **Vulnerabilidad de Motociclistas y Peatones:** La colisión más frecuente es **Moto vs. Auto (16.414 hechos)**, seguida por **Peatón vs. Auto (5.047 hechos)** y **Auto vs. Auto (4.592 hechos)**. Los **peatones** (con fuerte concentración en adultos mayores >60 años) sufren la mayor tasa de mortalidad individual (**2,90%** de muertes y **8,45%** de lesiones graves). El automóvil actúa como contraparte en el 63,0% de los hechos, pero los usuarios vulnerables (motos, peatones, ciclistas) absorben el **87,0%** del daño corporal severo y letal.
6. **Fuerte Desbalance de la Variable Objetivo:** La severidad se distribuye en **94,46% Leves**, **4,49% Graves** y **1,05% Mortales**, justificando el uso de técnicas de remuestreo/pesado (`class_weight='balanced'`) y métricas especializadas (Macro F1-score, PR-AUC).

---

## 🤖 **Detalle de los Modelos (Hoja de Ruta Pre-Entregas 3 y 4)**
- **Modelo Supervisado (Pre-Entrega 3 - Clasificación de Severidad):** Se entrenará un modelo multiclase para predecir `gravedad_siniestro` comparando `DummyClassifier` (baseline), `LogisticRegression`, `DecisionTree`, `RandomForest` y `HistGradientBoostingClassifier`. Se aplica una política estricta de prevención de **Data Leakage**, excluyendo variables conocidas exclusivamente post-siniestro.
- **Modelo No Supervisado (Pre-Entrega 4 - Clustering de Siniestros):** Se aplicarán algoritmos `K-Means` y `DBSCAN` sobre características ex-ante (horario, día de semana, tipo de arteria, comuna y actores vehiculares involucrados), utilizando el Método del Codo y el Coeficiente de Silueta para descubrir perfiles de riesgo vial.

---

## 📂 **Estructura del Repositorio**
```text
DATA_SCIENCE_GROUP_8/
│
├── data/
│   ├── siniestros_viales_hechos.csv                    # Dataset oficial de Hechos (OMSV)
│   ├── siniestros_viales_victimas.csv                  # Dataset oficial de Víctimas (OMSV)
│   └── comunas.json                                    # Polígonos GeoJSON oficiales de las 15 comunas de CABA
│
├── Entrega_2_Analisis_Exploratorio_Siniestros_Viales_CABA.ipynb  # Notebook principal de la 2ª Pre-entrega (138 celdas)
├── requirements.txt                                    # Dependencias del proyecto (incluye shapely>=2.0.0)
├── .gitignore                                          # Exclusiones de Git (.venv, checkpoints, scratch/, etc.)
├── revision.md                                         # Auditoría técnica, diagnósticos y checklist de mejoras resueltas
└── README.md                                           # Documentación general y guía de ejecución
```

---

## ⚡ **Instalación y Configuración del Entorno Virtual**

Para garantizar la reproducibilidad del proyecto se proveen dos alternativas de instalación: la opción **ultrarrápida con `uv`** (recomendada) y la opción tradicional con **Python `venv` + `pip`**.

### **Opción 1: Instalación Ultrarrápida con `uv` (Recomendada)**

[`uv`](https://github.com/astral-sh/uv) es un administrador y resolutor de paquetes de Python desarrollado en Rust, entre 10 y 100 veces más rápido que `pip`.

#### 1. Instalar `uv` (si aún no lo tienes instalado):
- **Windows (PowerShell):**
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```
- **macOS / Linux:**
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
- **Alternativa con pip:**
  ```bash
  pip install uv
  ```

#### 2. Clonar el repositorio y crear el entorno:
```bash
# 1. Navegar a la carpeta del proyecto
cd DATA_SCIENCE_GROUP_8

# 2. Crear el entorno virtual con uv
uv venv .venv

# 3. Activar el entorno virtual:
# En Windows (PowerShell):
.venv\Scripts\Activate.ps1
# En Windows (CMD):
.venv\Scripts\activate.bat
# En Linux / macOS:
source .venv/bin/activate

# 4. Instalar todas las dependencias en segundos:
uv pip install -r requirements.txt
```

---

### **Opción 2: Instalación Estándar con `venv` y `pip`**

Si prefieres usar únicamente las herramientas nativas de Python:

```bash
# 1. Navegar a la carpeta del proyecto
cd DATA_SCIENCE_GROUP_8

# 2. Crear el entorno virtual nativo
python -m venv .venv

# 3. Activar el entorno:
# En Windows (PowerShell):
.venv\Scripts\Activate.ps1
# En Windows (CMD):
.venv\Scripts\activate.bat
# En Linux / macOS:
source .venv/bin/activate

# 4. Actualizar pip e instalar requerimientos
python -m pip install --upgrade pip
pip install -r requirements.txt
```

---

## 🧩 **Extensiones Recomendadas para Visual Studio Code**

Para trabajar de forma óptima con este proyecto en **VS Code**, se sugiere instalar las siguientes extensiones desde el panel de extensiones (`Ctrl + Shift + X` o `Cmd + Shift + X`):

| Extensión | ID de Extensión | Descripción |
| :--- | :--- | :--- |
| **Python** | `ms-python.python` | Soporte para autocompletado, linting, formateo y depuración de Python |
| **Jupyter** | `ms-toolsai.jupyter` | Visualización y ejecución interactiva de archivos `.ipynb` dentro del editor |
| **Jupyter Keymap** | `ms-toolsai.jupyter-keymap` | Atajos de teclado estándar de Jupyter (Modo Comando/Edición) |
| **Jupyter Cell Tags** | `ms-toolsai.vscode-jupyter-cell-tags` | Gestión de etiquetas y metadatos en celdas de Jupyter |
| **Pylance** | `ms-python.vscode-pylance` | Motor de análisis estático inteligente y tipado para Python |

---

## 🚀 **Cómo Correr el Código y Notebooks**

### **A. Ejecución en Visual Studio Code (Recomendado)**

1. Abre la carpeta del repositorio en VS Code (`File > Open Folder...` y selecciona `DATA_SCIENCE_GROUP_8`).
2. Abre el archivo [`Entrega_2_Analisis_Exploratorio_Siniestros_Viales_CABA.ipynb`](file:///c:/Users/Matias/Desktop/CURSOS%202026/ciencia_datos/DATA_SCIENCE_GROUP_8/Entrega_2_Analisis_Exploratorio_Siniestros_Viales_CABA.ipynb).
3. En la esquina superior derecha del notebook, haz clic en **Select Kernel** (o `Cambiar Kernel`).
4. Selecciona **Python Environments...** y elige el entorno virtual recién creado: **`.venv` (Python 3.x)**.
5. Ejecuta las celdas secuencialmente presionando `Shift + Enter` o haz clic en **Run All** en la barra superior del notebook.

### **B. Ejecución en Jupyter Lab / Notebook Clásico (Navegador Web)**

Si prefieres trabajar desde la interfaz web de Jupyter:

```bash
# 1. Asegúrate de tener el entorno activado
.venv\Scripts\activate

# 2. Iniciar Jupyter Lab
jupyter lab

# O iniciar Jupyter Notebook clásico
jupyter notebook
```
El navegador se abrirá automáticamente en `http://localhost:8888`. Abre el archivo `Entrega_2_Analisis_Exploratorio_Siniestros_Viales_CABA.ipynb` y ejecuta las celdas.

---

## 📋 **Control de Calidad y Validaciones**
El archivo [`revision.md`](file:///c:/Users/Matias/Desktop/CURSOS%202026/ciencia_datos/DATA_SCIENCE_GROUP_8/revision.md) contiene la auditoría técnica detallada del trabajo práctico, incluyendo el diagnóstico de integridad referencial, consistencia de datos, prevención de data leakage y la lista de control de todas las mejoras resueltas.
