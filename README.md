# Proyecto Final de Ciencia de Datos: Seguridad Vial en CABA

**Programa:** EnergIA Digital — Fundación YPF  
**Docentes:** Benjamin Aseretto / Gabriel Acosta · **Tutora:** Belén Laresca  
**Grupo 8:** Diego Bianqui, Luida Medina y Matías Lorenzo

## Origen de los datos
Se utilizan los registros oficiales de siniestros viales y víctimas publicados por el Observatorio de Movilidad y Seguridad Vial (OMSV) del Gobierno de la Ciudad de Buenos Aires, disponibles en [BA Data](https://data.buenosaires.gob.ar/). El repositorio incluye 65.818 hechos, 75.197 registros de víctimas y el GeoJSON oficial de las 15 comunas de CABA.

## Resumen del proyecto
El trabajo analiza la siniestralidad vial de CABA entre 2019 y 2025 mediante limpieza, integración relacional, ingeniería de variables temporales y territoriales, imputación geoespacial y modelado supervisado y no supervisado. El propósito es descubrir patrones útiles para comprender perfiles de riesgo y orientar preguntas de planificación vial y de emergencias.

## Objetivo
Investigar qué características temporales, territoriales y de interacción entre actores aparecen asociadas con la gravedad de los siniestros; construir modelos para clasificar hechos graves o mortales, estimar la cantidad de víctimas y segmentar tipologías de siniestros sin utilizar la gravedad como variable de clustering.

## Hallazgos del análisis exploratorio
Entre los principales hallazgos se encuentran la caída aproximada del 40,3% en los hechos registrados durante 2020, la concentración de siniestros en días hábiles entre el mediodía y las 18:00, diferencias entre comunas en volumen y severidad relativa, mayor letalidad por siniestro en autopistas y una fuerte vulnerabilidad de motociclistas y peatones. La variable de gravedad presenta un desbalance marcado: 94,46% leves, 4,49% graves y 1,05% mortales.

## Modelos ajustados
La clasificación compara cinco algoritmos y selecciona HistGradientBoostingClassifier. En el test no visto de 13.164 hechos obtuvo ROC-AUC 0,7900, PR-AUC 0,2256, Balanced Accuracy 0,7055 y Recall de la clase grave/mortal de 76,95% con umbral 0,50. El notebook supervisado incorpora además una comparación y optimización de regresores para estimar el número total de víctimas; el notebook no supervisado compara MiniBatch K-Means y DBSCAN para identificar grupos de siniestros. **Las métricas finales de regresión y clustering deben completarse a partir de la ejecución íntegra de esos notebooks; no se consignan resultados no verificados.**

## Estructura del repositorio
- `data/`: datasets oficiales y polígonos GeoJSON de comunas.
- [Notebook AED — Entrega 2](Entrega_2_Analisis_Exploratorio_Siniestros_Viales_CABA.ipynb).
- [Notebook supervisado — clasificación y regresión](Entrega_3_Modelos_Supervisados_Siniestros_Viales_CABA.ipynb).
- [Notebook no supervisado — clustering](Entrega_4_Modelos_No_Supervisados_Clustering_Siniestros_Viales_CABA.ipynb).
- `requirements.txt`: dependencias reproducibles.

## Instalación y ejecución
Desde la raíz del repositorio, crear y activar un entorno virtual con Python 3.10 o superior, instalar las dependencias mediante `pip install -r requirements.txt` y abrir los notebooks con Jupyter o Visual Studio Code. Ejecutar cada notebook en orden, manteniendo la carpeta `data/` en la ubicación original.

## Alcance y limitaciones
Los modelos son ejercicios retrospectivos sobre registros históricos. Sus asociaciones no demuestran causalidad y no constituyen sistemas validados para decisiones clínicas ni para despacho autónomo del SAME. Un uso operativo requeriría validación temporal y externa, calibración, análisis de sesgos y evaluación de costos de error con especialistas.
