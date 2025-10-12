# Taller 2 – Calidad de los datos

Pontificia Universidad Javeriana

Autores: Juan Pablo Baquero Velandia, David Alejandro Davila Guaranguay

---

## Cuestionario y entregables

Este repositorio contiene el trabajo del Taller 2 de Curaduría y Gobernanza de Datos: análisis y plan de limpieza de calidad sobre los conjuntos de datos de medidores de agua.

Estructura del entregable solicitado:

- PARTE 1 - ANÁLISIS EXPLORATORIO DE DIMENSIONES DE CALIDAD
  1. Realizar un análisis exploratorio completo que identifique y cuantifique problemas en las 7 dimensiones de calidad de datos a lo largo de todo el dataset.
  2. Presentar un dashboard de métricas de calidad que incluya:
     - Score general de calidad por dimensión.
     - Escala de los cinco problemas más frecuentes (frecuencia y severidad).
     - Estimación del impacto económico de los problemas identificados (metodología y supuestos).

- PARTE 2 - PLAN DE LIMPIEZA DE DATOS Y GOBERNANZA
  - Unificación e integración de los 10 conjuntos de datos.
  - Diseñar e implementar un plan integral de limpieza y gobierno de datos que incluya las siguientes fases:

    Fase de Diagnóstico:
    - Priorizar problemas por impacto en negocio y frecuencia.
    - Establecer umbrales de aceptabilidad para cada dimensión de calidad.
    - Identificar las raíces de los problemas más críticos.

    Fase de Corrección:
    - Valores atípicos: proponer estrategia por variable (eliminación, imputación, capado) y justificarla.
    - Datos faltantes: proponer imputación por mediana, moda, o modelos predictivos según el caso.
    - Inconsistencias categóricas: desarrollar diccionarios de estandarización y reglas de mapeo.
    - Duplicados: implementar función para detección y unificación (reconciliación de registros).

    Fase de Prevención:
    - Diseñar checks de calidad automatizados para la ingesta futura (validaciones, rangos, formatos).
    - Proponer un sistema de scoring de calidad en tiempo real (cómo se calcula, umbrales y acciones).
    - Establecer protocolos de actuación para cada tipo de problema (quién, cuándo, cómo).
    - Definir métricas de monitorización continua y su periodicidad.

- Entrega final requerida:
  - Dataset limpio con documentación de transformaciones aplicadas (CSV/XLSX y un README de transformaciones).
  - Código reproducible del proceso de limpieza (notebook(s) y/o scripts). Incluye instrucciones para ejecutar.
  - Dashboard de monitorización de calidad (archivo HTML, notebook con visualizaciones o enlace a dashboard interactivo).
  - Política de gobierno de datos para prevenir recurrencia (documento breve con roles, responsabilidades y flujos).

---

## Guía práctica y estructura propuesta para el trabajo (reproducible)

1. Preparación y organización
   - Carpeta `Datos_MedidoresAgua/` contiene los archivos originales (txt/xlsx). Mantener un `raw/` con datos sin tocar.
   - Carpeta `notebooks/` con notebooks ordenados: `01_exploracion.ipynb`, `02_limpieza.ipynb`, `03_dashboard.ipynb`.
   - Carpeta `scripts/` para funciones reutilizables: limpieza, validación, scoring.
   - Carpeta `outputs/` para datos producidos y reportes.

2. PARTE 1 — Análisis exploratorio de calidad
   - Cargar y unificar los 10 archivos (ver `notebooks/01_exploracion.ipynb`).
   - Calcular métricas por dimensión (ejemplos):
     - Integridad: % valores faltantes por columna / fila.
     - Exactitud: validaciones de formato y rango (ej., IDs que no cumplen regex, lecturas negativas).
     - Consistencia: valores contradictorios entre columnas (p. ej. fecha de lectura posterior a fecha previa).
     - Uniformidad: variaciones en formatos/idiomas de categorías.
     - Puntualidad: retrasos o inconsistencias en timestamps (si aplica).
     - Conformidad: cumplimiento de esquemas (tipos de datos esperados).
     - Disponibilidad: número de registros por periodo / cobertura.
   - Generar tablas y gráficos por dimensión, y un score por dimensión — por ejemplo:
     - score_dim = 100 - (peso_faltantes * %faltantes + peso_errores * %errores + ...)
   - Identificar y listar los 5 problemas más frecuentes con frecuencia y costo estimado.

3. PARTE 2 — Plan de limpieza y gobernanza
   - Diagnóstico: tabla priorizada (problema, dimensión, frecuencia, impacto estimado, prioridad).
   - Corrección: scripts/notebook que apliquen:
     - Reglas de imputación (con explicación y pruebas A/B si aplica).
     - Funciones de normalización de texto y diccionarios de mapeo (incluir `reemplazos` ejemplo en notebook).
     - Detección y unificación de duplicados (matching por ID y heurística para conflictos).
   - Prevención: conjunto de checks automatizados (ejemplos de implementaciones en `scripts/quality_checks.py`).

4. Entregables y reproducibilidad
   - `README.md` (este archivo) con instrucciones para reproducir.
   - `notebooks/` con pasos claramente numerados para reproducir análisis y limpieza.
   - `requirements.txt` con dependencias (pandas, numpy, ydata-profiling, plotly, etc.).
   - Comandos para crear entorno e instalar dependencias:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

5. Metodología para estimación de impacto económico (ejemplo)
   - Definir hipótesis: por ejemplo, % lecturas erradas multiplicado por costo promedio por lectora/operación, o pérdidas por facturación estimada.
   - Mostrar cálculo reproducible en `notebooks/impacto_economico.ipynb`.

---

## Plantillas y fragmentos útiles (quick start)

- Detección de nulos por columna:

```python
print('\n===== FALTANTES =====')
print(df.isna().sum())
```

- Resumen de duplicados:

```python
print('Duplicados exactos:', df.duplicated().sum())
```

- Ejemplo de diccionario de reemplazo (usar y adaptar según columnas):

```python
reemplazos = {
    'Reading Frequency': {'mensual': 'mensual', 'monthly': 'mensual', 'bimonthly': 'bimestral'},
    # ...
}
```

---

## Checklist de entrega

- [ ] Dataset original (`raw/`) incluido
- [ ] Dataset limpio (`outputs/DatosLimpios.xlsx`) incluido
- [ ] Notebooks reproducibles en `notebooks/`
- [ ] Código reutilizable en `scripts/`
- [ ] Dashboard (`reporte_eda.html` o similar)
- [ ] Documentación de transformaciones (archivo `transformations.md` o similar)
- [ ] Política de gobernanza (archivo `GOBERNANZA.md`)

---

## Notas finales

- Mantener trazabilidad: cada transformación debe ir acompañada de una breve justificación y una función idempotente (si se ejecuta dos veces, el resultado no cambia).
- Priorizar la creación de tests unitarios para las funciones de curación de datos más críticas.

---

Fecha: 2025-10-11
