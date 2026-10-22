# Regiones con mayor canon minero y tasa de criminalidad
# Regiones con mayor canon minero y tasa de criminalidad

Proyecto del curso de Python (Grupo 3) que estudia si las regiones del Perú que reciben más **canon minero** tienen también mayores niveles de **criminalidad**. Se analiza un panel de 25 departamentos entre 2018 y 2025, con la tasa de victimización por 100 mil habitantes como medida de inseguridad y el canon minero per cápita como medida de la presencia de la minería formal.

**Integrantes**
- Joe Anderson Chuque Vergara
- Roberto Alonso Briceño Alva
- Luis Alejandro Zegarra Misajel

## Pregunta de investigación e hipótesis

**Pregunta:** ¿Cuál es la asociación entre un mayor canon minero per cápita, rezagado un año, y la tasa de victimización por 100 mil habitantes en las regiones del Perú?

- **H0:** el canon minero per cápita no se asocia con la victimización (β₁ = 0).
- **H1a:** un mayor canon se asocia con **menor** victimización (β₁ < 0), por mayor gasto público, empleo y provisión de servicios.
- **H1b:** un mayor canon se asocia con **mayor** victimización (β₁ > 0), por migración, desigualdad, conflictos por recursos y mayor atracción de economías ilegales y organizaciones criminales.

Los resultados se interpretan como **asociaciones condicionales, no como efectos causales**, porque son datos observacionales a nivel regional.

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `cod_final.ipynb` | Notebook principal: introducción, hipótesis, análisis descriptivo con gráficos y mapas, regresión lineal múltiple, análisis predictivo y conclusiones. |
| `Base_de_datos.ipynb` | Construcción de la base: lee las fuentes originales, calcula cada variable y genera `base_final.csv`. |
| `base_final.csv` | Panel final (25 departamentos × 8 años = 200 observaciones, 29 columnas). Es la única base que necesita `cod_final.ipynb`. |

## Datos

El panel combina las siguientes fuentes, procesadas en `Base_de_datos.ipynb`:

| Tema | Base utilizada |
|---|---|
| Victimización | ENAPRES 2018-2025 (INEI) |
| Pobreza, migración y desempleo | Módulos de la ENAHO 2018-2025 (INEI) |
| Población | Población proyectada por departamento al 30 de junio (INEI) |
| Gasto en seguridad | Gasto por departamento, 2018-2025 |
| Canon minero | Canon regional y canon local por departamento, 2018-2025 |
| Valor agregado bruto (VAB) | VAB total y VAB minero por departamento |
| Precios | Índice de precios al consumidor, para pasar a soles reales (base 2025 = 1) |

**Variables principales de `base_final.csv`**

| Variable | Descripción |
|---|---|
| `depto`, `anio` | Departamento (25, con Callao separado de Lima) y año |
| `victim_100k` | Tasa de victimización por 100 mil habitantes (variable a explicar) |
| `canon_pc_real` | Canon total (regional + local) per cápita en soles reales |
| `canon_pc_real_l1`, `ln_canon_l1` | Canon per cápita rezagado un año y su logaritmo (`log(1 + x)`, porque hay departamentos sin canon) |
| `pobreza`, `pobreza_extrema` | Porcentaje de población en pobreza y en pobreza extrema |
| `desempleo` | Porcentaje de la PEA desocupada |
| `migracion` | Porcentaje de personas que vivían en otro departamento hace 5 años |
| `gasto_seg_pc_real` | Gasto en seguridad per cápita en soles reales |
| `pbi_sinmin_pc`, `prod_min_pc` | VAB sin minería y VAB minero per cápita |
| `region_natural` | Costa, Sierra o Selva |

Como el rezago del canon no existe para 2018, el análisis con esa variable usa **175 observaciones**.

## Metodología

1. **Análisis descriptivo:** estadísticas por región natural, evolución temporal, ranking de departamentos, diagramas de dispersión, mapas y mapas de calor.
2. **Análisis estadístico:** regresión lineal múltiple (MCO) con muestra 2021-2025 (125 observaciones):

   `victimización = β₀ + β₁·ln(canon pc real, rezagado 1 año) + β₂·pobreza + β₃·desempleo + β₄·gasto en seguridad pc + β₅·migración + ε`
3. **Análisis predictivo:** el mismo modelo se entrena con 2021-2024 (100 observaciones) y se evalúa prediciendo 2025 (25 departamentos), con MAE, RMSE y R².

## Principales resultados

**Descriptivo**
- La victimización promedio es de unos 22 mil casos por 100 mil habitantes y es muy desigual: los promedios 2018-2025 van de 13 567 (Cajamarca) a 33 276 (Puno).
- Mayor victimización: Puno, Cusco, Junín, Tacna y Madre de Dios. Menor victimización: Cajamarca, San Martín, Huánuco, Lambayeque y Tumbes.
- Por región natural, la Sierra tiene la victimización promedio más alta (23 830), seguida de la Costa (21 661) y la Selva (19 733).
- El canon está muy concentrado en pocas regiones (Moquegua, Áncash, Tacna, Arequipa e Ica), y seis departamentos no reciben canon en ningún año.
- La victimización cae en 2021 en casi todas las regiones (es el mínimo en 23 de 25) y luego se recupera hasta 2024.
- La correlación entre el logaritmo del canon rezagado y la victimización es **0.30**: positiva pero débil.

**Regresión**
- El coeficiente del canon es **β₁ ≈ 845** (p < 0.001): al multiplicarse el canon per cápita por ≈ 2.7, la victimización aumenta en promedio unos 845 casos por 100 mil habitantes, manteniendo lo demás constante. La evidencia es consistente con **H1b**.
- El desempleo también es significativo y positivo (β ≈ 1 017). Pobreza, gasto en seguridad y migración no son significativos.
- El modelo explica el 18.4 % de la variación (R² ajustado 0.150): capacidad explicativa baja.

**Predicción 2025**
- MAE ≈ 4 280, RMSE ≈ 5 197 y R² ≈ 0.168. El modelo acierta en algunos departamentos (Piura, San Martín, Amazonas, Ucayali) pero subestima a los de mayor victimización (Cusco, Madre de Dios, Junín, Ayacucho).

## Limitaciones

- **Minería ilegal e informal:** el canon solo refleja la minería formal. Regiones como Puno y Madre de Dios tienen alta victimización con poco canon, y no se pudo incorporar una variable de minería ilegal por falta de datos regionales anuales comparables.
- **Sin efectos fijos:** el modelo no controla por características fijas de cada departamento ni por tendencias comunes entre años, y los errores estándar no son robustos (Durbin-Watson ≈ 0.95), por lo que los p-valores pueden ser demasiado optimistas.
- **Asociación, no causalidad:** la capacidad predictiva es limitada y una buena predicción no implica una relación causal.

## Cómo ejecutar

**Para replicar el análisis** solo se necesita `cod_final.ipynb`:

1. Instala las librerías: `pip install pandas numpy matplotlib seaborn plotly statsmodels scikit-learn requests jupyter`
2. Abre `cod_final.ipynb` en Jupyter o en Google Colab y ejecuta las celdas en orden.

El notebook lee `base_final.csv` desde la misma carpeta y, si no lo encuentra, lo descarga automáticamente de este repositorio. Los dos mapas descargan un archivo geográfico de internet, por lo que requieren conexión.

**Para reconstruir la base desde cero** (opcional) usa `Base_de_datos.ipynb`:

1. Descarga los archivos originales y organízalos en una carpeta con la estructura que espera el código (ver `CARPETAS` en la primera parte del notebook).
2. Instala además `pip install pyreadstat openpyxl lxml`.
3. Ejecuta el notebook e indica la ruta de esa carpeta cuando la pida. Al final se guarda `base_final.csv`.
