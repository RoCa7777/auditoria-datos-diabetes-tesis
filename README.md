# Auditoría y Reingeniería de Datos: Determinantes Biométricos y Epidemiológicos de Diabetes Mellitus

**Asignatura:** Recolección y Clasificación de Datos — Ciencia de Datos e IA Aplicada  
**Universidad Autónoma de Guerrero**  

---

## 1. Descripción y Pregunta de Investigación
Este repositorio implementa un pipeline reproducible de extracción, curación, anonimización y auditoría de datos clínicos y poblacionales para evaluar la relación entre indicadores biométricos (glucosa plasmática, índice de masa corporal, presión arterial diastólica y edad) y el diagnóstico de **Diabetes Mellitus Tipo 2**, contrastando una cohorte clínica ($n = 768$) con microdatos poblacionales de la **ENSANUT** ($N = 6,772$; submuestra regional de Guerrero, $n = 33$).

---

## 2. Rigor de Adquisición y Justificación del Tamaño de Muestra

* **Tipo de Investigación:** Cuantitativa, observacional, transversal y descriptivo-correlacional con enfoque deductivo basado en los puntos de corte diagnósticos de la **Asociación Americana de Diabetes (ADA)** y la **Organización Mundial de la Salud (OMS)**.
* **Diseño Muestral:**
  * *Cohorte Clínica (`diabetes_raw.csv`):* Muestreo no probabilístico intencional de pacientes femeninas adultas ($\ge 21$ años).
  * *Encuesta Poblacional (`adultos_salud.csv`):* Muestreo probabilístico, estratificado y polietápico con factores de expansión (`ponde_f`) para inferencia nacional y estatal.
* **Cálculo Estadístico del Tamaño de Muestra:**
  Considerando una prevalencia esperada de diabetes en adultos en México ($p = 0.14$), un nivel de confianza del $95\%$ ($Z = 1.96$) y un margen de error máximo tolerable del $3\%$ ($E = 0.03$):

  $$n = \frac{Z^2 \cdot p \cdot (1 - p)}{E^2} = \frac{(1.96)^2 \cdot 0.14 \cdot (1 - 0.14)}{(0.03)^2} \approx 514 \text{ registros mínimos}$$

  La cohorte clínica auditada dispone de **$768$ registros brutos** ($752$ registros válidos tras depuración) y la encuesta poblacional cuenta con **$6,772$ observaciones**, superando en ambos casos el tamaño de muestra mínimo requerido para garantizar potencia estadística.

---

## 3. Estructura del Repositorio

```text
auditoria-datos-diabetes-tesis/
├── datos_crudos/
│   └── diabetes_raw.csv              # Dataset original inmutable
├── datos_curados/
│   └── diabetes_v1.0.csv             # Dataset limpio y versionado generado por el pipeline
├── src/
│   └── pipeline_tesis.py             # Script ETL reproducible (Extract, Transform, Load)
├── notebooks/
│   └── auditoria_exploratoria.ipynb  # Validación de leakage, sesgos y análisis estadístico
├── README.md                         # Documentación general y guía de ejecución
├── datasheet.md                      # Ficha técnica estandarizada (Gebru et al.)
├── requirements.txt                  # Dependencias exactas del entorno
└── .gitignore                        # Exclusión de archivos temporales y caché