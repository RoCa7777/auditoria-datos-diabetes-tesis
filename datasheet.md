---

### 2. Archivo `datasheet.md`

```markdown
# Datasheet: Dataset Auditado de Diabetes Mellitus (`diabetes_v1.0.csv`)
*Estandarizado conforme a la metodología "Datasheets for Datasets" (Gebru et al.).*

---

## 1. Motivación
* **¿Para qué propósito fue creado este dataset?**  
  Para auditar la integridad de registros biométricos clínicos, eliminar sesgos derivados de valores faltantes mal codificados y servir como base reproducible para el análisis estadístico y modelado predictivo de riesgo metabólico en tesis de posgrado.
* **¿Quién financió o respaldó la recolección?**  
  Los datos clínicos provienen del repositorio público histórico del *National Institute of Diabetes and Digestive and Kidney Diseases (NIDDK)*, complementados para contraste epidemiológico con microdatos abiertos de la *Encuesta Nacional de Salud y Nutrición (ENSANUT)* de México.

---

## 2. Composición y Diccionario de Datos
El dataset curado (`v1.0`) contiene **$752$ registros únicos** (tras excluir $16$ registros sin medición válida de glucosa o IMC) y **$11$ columnas**:

| Variable | Tipo | Unidad / Valores | Descripción y Criterio Clínico |
| :--- | :--- | :--- | :--- |
| `id_paciente_hash` | `string` | Hash SHA-256 (16 hex) | Identificador único seudonimizado para trazabilidad sin exposición de PII. |
| `Embarazos` | `int` | Conteo ($\ge 0$) | Número de gestaciones previas registradas. |
| `Glucosa` | `float` | $\text{mg/dL}$ | Concentración plasmática de glucosa a las 2 horas en prueba de tolerancia oral. |
| `Presion` | `float` | $\text{mmHg}$ | Presión arterial diastólica. |
| `Grosor_Piel` | `float` | $\text{mm}$ | Espesor del pliegue cutáneo del tríceps (`NaN` si no fue medido). |
| `Insulina` | `float` | $\mu\text{U/mL}$ | Insulina sérica a las 2 horas (`NaN` si no fue medida). |
| `IMC` | `float` | $\text{kg/m}^2$ | Índice de Masa Corporal ($\text{peso}/\text{talla}^2$). |
| `Pedigri` | `float` | Índice continuo | Función de pedigrí de diabetes (carga hereditaria familiar). |
| `Edad` | `int` | Años ($\ge 21$) | Edad biológica del paciente al momento de la evaluación. |
| `Nivel_Glucosa_ADA` | `category` | `Normal`, `Prediabetes`, `Rango_Diabetico` | Estratificación ADA: $<100$, $100\text{–}125.9$, $\ge 126\text{ mg/dL}$. |
| `Nivel_IMC_OMS` | `category` | `Normal`, `Sobrepeso`, `Obesidad` | Estratificación OMS: $<25$, $25\text{–}29.9$, $\ge 30\text{ kg/m}^2$. |
| `Diagnostico` | `int` (Target) | `0` = Sano, `1` = Diabetes | **Ground Truth:** Diagnóstico confirmado dentro de un seguimiento de 5 años bajo criterios OMS. |

---

## 3. Solidez del Ground Truth
* **Definición de la etiqueta:** La variable objetivo `Diagnostico` proviene de confirmación clínica y bioquímica estandarizada (glucosa en ayunas $\ge 126\text{ mg/dL}$ o poscarga $\ge 200\text{ mg/dL}$).
* **Confiabilidad:** Al basarse en pruebas objetivas de laboratorio clínico y no en anotación subjetiva manual, no aplica el cálculo de concordancia inter-evaluador ($\kappa$ de Cohen). Se verificó la consistencia interna mediante cruce contra `Nivel_Glucosa_ADA`.

---

## 4. Preprocesamiento, Limpieza y Prevención de Data Leakage
* **Saneamiento de ceros inverosímiles:** En los datos crudos, los valores faltantes estaban codificados como `0`. Se transformaron a `NaN` verdadero en `Glucosa` ($5$ casos), `Presion` ($35$ casos), `Grosor_Piel` ($227$ casos), `Insulina` ($374$ casos) e `IMC` ($11$ casos) para evitar distorsión de medianas y correlaciones.
* **Prevención estricta de Data Leakage:**
  * El dataset `diabetes_v1.0.csv` **no contiene imputaciones precalculadas** en variables secundarias (`Presion`, `Insulina`, `Grosor_Piel`) ni escalamientos globales. Cualquier imputación (ej. `SimpleImputer(strategy="median")`) o estandarización debe ajustarse (`fit`) exclusivamente sobre la partición de entrenamiento (`X_train`) posterior a `train_test_split`.
  * La deduplicación previa por identificador garantiza ausencia de *Subject Leakage* entre particiones.

---

## 5. Evaluación de Sesgos (Bias) y Limitaciones
* **Sesgo de Selección Demográfico:** La cohorte clínica está compuesta exclusivamente por mujeres adultas ($\ge 21$ años) de ascendencia indígena Pima. Por consiguiente, presenta alta prevalencia basal ($34.9\%$) y no representa directamente a población masculina, pediátrica ni a otras etnias sin ajuste epidemiológico externo.
* **Sesgo de Medición:** Alta proporción de datos faltantes en `Insulina` ($48.7\%$) y `Grosor_Piel` ($29.6\%$), por lo que su uso en modelos predictivos requiere técnicas robustas de imputación o exclusión controlada.

---

## 6. Usos Recomendados y No Recomendados
* **Usos Recomendados:** Investigación epidemiológica, análisis exploratorio de factores de riesgo metabólico, docencia en pipelines reproducibles y prototipado de modelos de tamizaje preventivo.
* **Usos No Recomendados:** Diagnóstico médico clínico automatizado en pacientes individuales, toma de decisiones terapéuticas o despliegue en producción hospitalaria sin validación prospectiva multicéntrica.