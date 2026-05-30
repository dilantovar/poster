# Modelación ARIMA del Precio Internacional del Aluminio

**Trabajo 1 — Econometría II (2016003) — 2026-1S**
**Universidad Nacional de Colombia — Facultad de Ciencias Económicas**

**Integrantes:** Santiago Vargas, Dilan Tovar, María Orjuela
**Docentes:** Milena Hoyos, Luis Luna, Germán Rodríguez

---

## 1. Resumen del proyecto

Este repositorio aplica la **metodología Box–Jenkins** (Identificación → Estimación → Validación → Pronóstico) a la serie mensual del **Precio Internacional del Aluminio** (FMI vía FRED, código `PALUMUSDM`) entre marzo 2016 y marzo 2026, con un pronóstico a 10 períodos adelante (abril 2026 – enero 2027).

- **Serie modelada:** $y_t = \log(P_t)$, precio internacional del aluminio en USD/tonelada métrica.
- **Observaciones:** 121 (mensuales).
- **Fuente:** Federal Reserve Economic Data (FRED), serie `PALUMUSDM` del Fondo Monetario Internacional.
- **Lenguaje:** Python 3.12.
- **Bibliotecas principales:** `pandas`, `numpy`, `statsmodels`, `matplotlib`, `scipy`.

---

## 2. Estructura del repositorio

```
poster/
│
├── README.md                          # Este archivo
├── requirements.txt                   # Versiones exactas de dependencias
├── .gitignore                         # Archivos ignorados por Git
│
├── data/
│   ├── raw/
│   │   └── PALUMUSDM.csv              # Datos crudos del FMI vía FRED
│   └── processed/
│       └── aluminio_precio.csv        # Datos limpios listos para modelar
│
├── notebooks/
│   └── 01_box_jenkins_aluminio.ipynb  # Cuaderno principal del análisis
│
└── outputs/
    ├── figures/                       # Figuras PNG generadas por el notebook
    │   ├── 01_serie_niveles.png
    │   ├── 02_niveles_vs_log.png
    │   ├── 03_retornos_log.png
    │   ├── 04_estacionalidad_check.png
    │   ├── 05_log_diff.png
    │   ├── 06_fac_facp_log_niveles.png
    │   ├── 07_fac_facp_retornos.png
    │   ├── 08_ajuste_in_sample.png
    │   └── ... (resto de figuras)
    └── tables/                        # Tablas CSV generadas por el notebook
        ├── 01_dickey_fuller.csv
        ├── 02_comparacion_modelos_aluminio.csv
        └── ...
```

---

## 3. Requisitos previos

Para reproducir este proyecto se requiere:

1. **Sistema operativo:** Windows, macOS o Linux.
2. **Anaconda Distribution** (recomendada) o Miniconda. Versión 2023 o superior.
   - Descarga: <https://www.anaconda.com/download>
3. **Git** para clonar el repositorio.
   - Descarga: <https://git-scm.com/downloads>
4. **Conexión a internet** para crear el entorno e instalar dependencias la primera vez.

> **Nota sobre Python:** este proyecto fue desarrollado con **Python 3.12**. Versiones de Python 3.10 a 3.12 deberían funcionar sin problemas.

---

## 4. Clonación del repositorio

Abra una terminal (Anaconda Prompt en Windows, Terminal en macOS/Linux) y ejecute:

```bash
git clone https://github.com/dilantovar/poster.git
cd poster
```

> Si no tiene Git instalado, puede descargar el ZIP directamente desde GitHub (botón verde "Code" → "Download ZIP") y descomprimirlo.

---

## 5. Creación del entorno conda

El proyecto se desarrolló en un entorno conda aislado llamado `econometria2`. Recomendamos recrearlo idéntico para garantizar reproducibilidad.

### Opción A — Crear desde cero (recomendada)

Desde la raíz del proyecto, ejecute en la terminal:

```bash
conda create -n econometria2 python=3.12 -y
conda activate econometria2
pip install -r requirements.txt
```

Verifique que el entorno se creó correctamente:

```bash
python -c "import pandas, numpy, statsmodels, matplotlib, scipy; print('Todo OK')"
```

Debe imprimir `Todo OK`. Si aparece algún error de importación, revise la sección **9. Resolución de problemas**.

### Opción B — Instalación rápida sin recrear el entorno

Si ya tiene un entorno Python 3.10+ con `pip` configurado y prefiere no crear uno nuevo:

```bash
pip install -r requirements.txt
```

> **Advertencia:** instalar globalmente puede generar conflictos con otros proyectos. La Opción A es más segura.

---

## 6. Ejecución del notebook

### 6.1. Configurar Jupyter para usar el entorno

Con `econometria2` activo, registre el kernel para que Jupyter lo reconozca:

```bash
python -m ipykernel install --user --name=econometria2 --display-name="Python (econometria2)"
```

### 6.2. Abrir el notebook

Tiene **dos opciones equivalentes** para abrirlo:

**Opción A — VSCode (recomendada para visualización completa):**

1. Abra Visual Studio Code.
2. **File → Open Folder** y seleccione la carpeta `poster`.
3. Instale las extensiones de **Python** y **Jupyter** de Microsoft si no las tiene.
4. Abra `notebooks/01_box_jenkins_aluminio.ipynb`.
5. En la esquina superior derecha del notebook, haga clic en **"Select Kernel"** y elija `econometria2 (Python 3.12)`.

**Opción B — Jupyter Notebook clásico:**

```bash
conda activate econometria2
jupyter notebook
```

Navegue a `notebooks/01_box_jenkins_aluminio.ipynb` y ábralo. Verifique que el kernel activo sea `Python (econometria2)`.

### 6.3. Ejecutar el notebook completo

Una vez abierto, ejecute todas las celdas en orden:

- **VSCode:** botón **"Run All"** en la barra superior del notebook.
- **Jupyter:** menú **Cell → Run All**.

El notebook tomará aproximadamente **1–2 minutos** en ejecutarse completamente y generará automáticamente:

- Todas las figuras PNG en `outputs/figures/`.
- Todas las tablas CSV en `outputs/tables/`.

---

## 7. Reproducibilidad mínima (solo notebook + CSV)

El notebook está diseñado para ser **completamente autocontenido**. Si solo dispone de:

- `01_box_jenkins_aluminio.ipynb`
- `PALUMUSDM.csv`

puede reproducir el análisis completo siguiendo estos pasos:

1. Cree una carpeta de trabajo, por ejemplo `aluminio/`.
2. Dentro de ella, cree la siguiente estructura:

   ```
   aluminio/
   ├── data/
   │   └── raw/
   │       └── PALUMUSDM.csv
   └── notebooks/
       └── 01_box_jenkins_aluminio.ipynb
   ```

3. Active un entorno con las dependencias del `requirements.txt`.
4. Abra y ejecute el notebook.

El notebook **detecta automáticamente la raíz del proyecto** mediante `pathlib` y crea las subcarpetas `data/processed/`, `outputs/figures/` y `outputs/tables/` la primera vez que se ejecuta.

### Diseño de rutas relativas

El bloque 0 del notebook contiene la siguiente lógica de detección automática:

```python
from pathlib import Path

notebook_path = Path.cwd()
if notebook_path.name == "notebooks":
    PROJECT_ROOT = notebook_path.parent
else:
    PROJECT_ROOT = notebook_path

DATA_RAW = PROJECT_ROOT / "data" / "raw"
DATA_PROCESSED = PROJECT_ROOT / "data" / "processed"
FIGURES = PROJECT_ROOT / "outputs" / "figures"
TABLES = PROJECT_ROOT / "outputs" / "tables"

for d in [DATA_PROCESSED, FIGURES, TABLES]:
    d.mkdir(parents=True, exist_ok=True)
```

Esto garantiza que el código funcione en **Windows, macOS y Linux** sin modificar manualmente ninguna ruta absoluta.

---

## 8. Dependencias completas

El archivo `requirements.txt` lista todas las dependencias con versiones compatibles:

| Paquete | Versión mínima | Uso en el proyecto |
|---|---|---|
| `pandas` | 2.0+ | Manipulación de la serie temporal y CSV |
| `numpy` | 1.24+ | Operaciones numéricas, logaritmos |
| `matplotlib` | 3.7+ | Generación de todas las figuras |
| `seaborn` | 0.12+ | Estilo de gráficos |
| `scipy` | 1.10+ | Pruebas estadísticas (Jarque-Bera, Q-Q plot) |
| `statsmodels` | 0.14+ | `SARIMAX`, ADF, Ljung-Box, ARCH-LM, FAC/FACP |
| `openpyxl` | 3.1+ | Lectura auxiliar de archivos `.xlsx` |
| `jupyter` | 1.0+ | Entorno de notebooks |
| `ipykernel` | 6.0+ | Conexión Jupyter–entorno conda |

> **Nota:** este proyecto **no utiliza** `pmdarima` ni `auto_arima`. La selección del modelo ARIMA se realizó manualmente siguiendo la metodología Box–Jenkins exigida por el curso.

---

## 9. Resolución de problemas comunes

### Error: `ModuleNotFoundError: No module named 'statsmodels'`

El entorno no está activo o no se instaló la dependencia. Active el entorno y reinstale:

```bash
conda activate econometria2
pip install -r requirements.txt
```

### Error: `FileNotFoundError: data/raw/PALUMUSDM.csv`

El archivo CSV no está en la ruta esperada. Verifique que:

1. El CSV se llama exactamente `PALUMUSDM.csv` (mayúsculas y minúsculas importan).
2. Está ubicado en `data/raw/` relativo a la raíz del proyecto.
3. Está ejecutando el notebook desde la carpeta `notebooks/` o desde la raíz del proyecto.

### El kernel `econometria2` no aparece en VSCode/Jupyter

Después de crear el entorno, debe registrarlo como kernel de Jupyter:

```bash
conda activate econometria2
python -m ipykernel install --user --name=econometria2 --display-name="Python (econometria2)"
```

Reinicie VSCode/Jupyter y el kernel aparecerá en la lista de selección.

### El notebook muestra figuras pero no se guardan en `outputs/figures/`

Verifique que el bloque 0 (configuración de rutas) se haya ejecutado correctamente. Las carpetas `outputs/figures/` y `outputs/tables/` se crean automáticamente solo si ese bloque se ejecutó al menos una vez.

### Diferencias menores en los resultados al re-ejecutar

`SARIMAX` utiliza optimización numérica con condiciones iniciales aleatorias en algunos casos. Las estimaciones pueden diferir en los últimos dos o tres decimales pero los resultados cualitativos (significancia, AIC/BIC, modelo seleccionado) son estables.

---

## 10. Estructura del análisis (resumen de los bloques del notebook)

| Bloque | Contenido | Salidas principales |
|---|---|---|
| **0. Configuración** | Importación de librerías, definición de rutas relativas con `pathlib`, configuración de `matplotlib`. | — |
| **1. Carga de datos** | Lectura del CSV crudo, construcción del índice temporal mensual (`MS`), generación del archivo limpio `aluminio_precio.csv`. | `data/processed/aluminio_precio.csv` |
| **2. Análisis exploratorio** | Gráficos de la serie en niveles y logaritmos, retornos logarítmicos, verificación visual de estacionalidad. | Figuras 01–04 |
| **3. Identificación** | Pruebas Dickey-Fuller (DF) y Dickey-Fuller Aumentada (ADF) sobre niveles y retornos; FAC y FACP en ambos casos. | Figuras 05–07, tabla `01_dickey_fuller.csv` |
| **4. Estimación** | Estimación de cuatro modelos ARIMA candidatos por máxima verosimilitud, comparación por AIC/BIC, análisis de robustez con/sin deriva. | Figura 08, tabla `02_comparacion_modelos_aluminio.csv` |
| **5. Validación** | Diagnóstico de residuos: FAC, histograma, Q-Q plot, pruebas Ljung-Box, Jarque-Bera y ARCH-LM. | Figuras de diagnóstico, tabla de p-valores |
| **6. Pronóstico** | Pronóstico a 10 períodos en escala logarítmica, re-transformación a niveles con corrección de sesgo, intervalo de confianza al 95%. | Figura del pronóstico, tabla de valores puntuales |

---

## 11. Verificación rápida de reproducibilidad

Para confirmar que su entorno reproduce exactamente nuestros resultados, después de ejecutar el notebook completo verifique:

1. El archivo `data/processed/aluminio_precio.csv` tiene **121 filas** (excluyendo encabezado).
2. La carpeta `outputs/figures/` contiene **todas las figuras PNG** numeradas del 01 en adelante.
3. La tabla `outputs/tables/02_comparacion_modelos_aluminio.csv` muestra los cuatro modelos ARIMA estimados con sus respectivos AIC y BIC.
4. El pronóstico a 10 períodos genera valores en escala de precio (USD/tonelada) en el rango razonable de la serie histórica reciente.

---

## 12. Referencias

- **Box, G. E. P., & Jenkins, G. M. (1976).** *Time Series Analysis: Forecasting and Control*. Holden-Day.
- **Enders, W. (2014).** *Applied Econometric Time Series* (4th ed.). Wiley.
- **Hamilton, J. D. (1994).** *Time Series Analysis*. Princeton University Press.
- **Hyndman, R. J., & Athanasopoulos, G. (2021).** *Forecasting: Principles and Practice* (3rd ed.). OTexts. <https://otexts.com/fpp3/>
- **International Monetary Fund (IMF).** *Global price of Aluminum* [PALUMUSDM]. Retrieved from FRED, Federal Reserve Bank of St. Louis. <https://fred.stlouisfed.org/series/PALUMUSDM>
- **Seabold, S., & Perktold, J. (2010).** Statsmodels: Econometric and statistical modeling with Python. *Proceedings of the 9th Python in Science Conference*.

---

## 13. Contacto

Para preguntas sobre la reproducibilidad del proyecto, contacte al equipo a través del repositorio:

<https://github.com/dilantovar/poster>

---

*Última actualización: mayo 2026.*
