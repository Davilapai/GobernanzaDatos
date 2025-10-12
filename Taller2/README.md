# **POLÍTICA DE GOBIERNO DE DATOS PARA PREVENIR RECURRENCIA**

## Sistema de Medidores de Agua - Maddalena S.P.A


### **1. PROPÓSITO Y ALCANCE**

El propósito de esta política es establecer los **lineamientos, responsabilidades y procedimientos** necesarios para garantizar la **calidad, consistencia, integridad y trazabilidad** de los datos generados por el sistema de medición de agua de **Maddalena S.P.A**.
Esta política surge como respuesta a los hallazgos obtenidos en el proyecto de implementación del modelo predictivo, donde se identificaron **fallas recurrentes en la calidad de los datos** que afectaban la precisión de los resultados analíticos.

Con estas medidas, se busca no solo **corregir errores existentes**, sino también **prevenir su recurrencia**, estableciendo un sistema robusto de **gobernanza de datos** que asegure decisiones confiables, sostenibles y basadas en evidencia.

**Alcance:**
Aplica a todos los conjuntos de datos relacionados con:

* Mediciones y lecturas de contadores de agua.
* Datos maestros de clientes y contratos.
* Fuentes de datos operativas y analíticas derivadas del sistema de medición.


### **2. ROLES Y RESPONSABILIDADES**

La gobernanza de datos depende de una estructura clara de roles que asegure la **responsabilidad compartida** y la **rendición de cuentas** en cada etapa del ciclo de vida de los datos.

#### **2.1 Dueño de Datos (Data Owner)**

**Responsable:** Gerente de Operaciones
**Rol estratégico:** Define la visión de calidad y las políticas de gestión de datos.

**Funciones:**

* Aprobar los estándares de calidad de datos.
* Autorizar modificaciones en la estructura o flujo de datos.
* Resolver conflictos o discrepancias entre áreas.
* Supervisar la alineación entre los objetivos de negocio y la gestión de datos.

El objetivo de este rol es asegurar que la **calidad de los datos esté alineada con las metas operativas**, evitando decisiones basadas en información defectuosa.

#### **2.2 Steward de Datos (Data Steward)**

**Responsable:** Supervisor de Lecturas
**Rol operativo:** Garantiza el cumplimiento diario de los estándares de calidad.

**Funciones:**

* Monitorear y validar la calidad de los datos en tiempo real.
* Implementar procesos correctivos inmediatos.
* Coordinar la capacitación del personal que realiza las lecturas.
* Registrar y documentar todas las acciones correctivas y sus resultados.

El Data Steward es clave para **prevenir la recurrencia** de errores, al actuar como primer filtro operativo en la cadena de generación de datos.

#### **2.3 Científico de Datos (Data Scientist)**

**Responsable:** Equipo de Analytics
**Rol analítico:** Diseña los mecanismos automáticos de control y mejora continua.

**Funciones:**

* Crear y actualizar los checks automáticos de calidad.
* Desarrollar modelos predictivos e imputaciones para valores faltantes.
* Implementar dashboards y reportes de monitorización.
* Analizar tendencias y proponer estrategias preventivas.

El equipo de análisis es fundamental para **traducir la calidad de datos en información accionable**, fortaleciendo la toma de decisiones informadas.


### **3. ESTÁNDARES DE CALIDAD DE DATOS**

El establecimiento de estándares formales permite definir **niveles mínimos aceptables de calidad** y medir de manera cuantitativa el desempeño del sistema.

#### **3.1 Umbrales Mínimos de Aceptabilidad**

| Dimensión    | Umbral Mínimo | Frecuencia de Medición |
| ------------ | ------------- | ---------------------- |
| Completitud  | 95%           | Diaria                 |
| Exactitud    | 90%           | Semanal                |
| Consistencia | 98%           | Mensual                |
| Validez      | 99%           | En tiempo real         |
| Unicidad     | 100%          | Diaria                 |

Estos umbrales fueron definidos tras analizar los fallos más recurrentes (facilmente ubicables en el reporte EDA) y buscan asegurar que **los errores no se acumulen ni repitan** en el tiempo.

#### **3.2 Estándares Específicos por Campo**

**Water Meter ID:**

* Formato: Alfanumérico sin espacios
* Longitud: 6–12 caracteres
* Unicidad: 100% garantizada

**Reading Value:**

* Rango: 0–10,000 m³
* Precisión: 2 decimales
* Valores negativos no permitidos

**Reading Date:**

* Formato: YYYY-MM-DD
* No se permiten fechas futuras
* Consistencia temporal entre lecturas consecutivas

Estos criterios fueron definidos con base en los errores más frecuentes detectados, con el objetivo de **estandarizar la entrada de datos y minimizar desviaciones operativas**.

---

### **4. PROCESOS DE GESTIÓN DE CALIDAD**

#### **4.1 Fase de Ingesta – Checks Automatizados**

Los procesos de ingesta consideramos deberian de contar **validaciones automáticas** que garantizan la integridad de los datos desde su origen.
Estas verificaciones detectan errores de formato, valores fuera de rango y categorías inválidas antes de su almacenamiento (bajo esto podria decirse que se orienta un poco más hacia la metodologia ETL)

```python
# Ejemplo de checks automaticos hechos por IA
def validar_ingesta_datos(nuevos_datos):
    """Validaciones en tiempo de ingesta"""
    
    checks = {
        'completitud': nuevos_datos.isnull().sum().sum() == 0,
        'formato_fechas': pd.to_datetime(nuevos_datos['Reading Date'], errors='coerce').notna().all(),
        'rangos_numericos': (nuevos_datos['Reading Value'] >= 0).all() & 
                           (nuevos_datos['Reading Value'] <= 10000).all(),
        'categorias_validas': nuevos_datos['Type of Contract'].isin([
            'residencial', 'comercial', 'industrial', 'other'
        ]).all()
    }
    
    return all(checks.values()), checks
```

#### **4.2 Sistema de Scoring en Tiempo Real**

Permite evaluar automaticamente el estado de la calidad de los datos a emdida que entran o se actualizan en el sistema, ante situaciones inesperadas surge un indicador que alerta al equipo ante degradaciones en la información.


### **5. PROTOCOLOS DE ACTUACIÓN**

#### **5.1 Flujo de Corrección de Datos**

1. **Detección:** Sistema identifica anomalía automáticamente.
2. **Clasificación:** Se evalúa la severidad (Alta, Media, Baja).
3. **Notificación:** Alerta al Data Steward responsable.
4. **Corrección:** Aplicación del protocolo correspondiente.
5. **Verificación:** Validación posterior por el Data Owner.
6. **Documentación:** Registro en la bitácora de calidad.

Este flujo asegura que **cada error sea tratado y documentado**, garantizando trazabilidad y mejora continua.

#### **5.2 Estrategias por Tipo de Problema**

**Valores Nulos:**

* <5% → Imputación por mediana
* 5–20% → Modelo predictivo de imputación
* -20% → Recolección manual

**Outliers:**

* Detección de valores atipicos usando cuartiles (método IQR)
* Verificación con el Data Owner
* Ajuste de valores extremos a limites permitidos (esto se conoce como capado, su implementacion requiere de extensa revision)

**Inconsistencias Categóricas:**

* Uso de diccionario de estandarización
* Corrección automática mediante mapeo
* Registro de cambios aplicados



### **6. MONITORIZACIÓN Y REPORTING**

#### **6.1 Dashboard de Monitorización**

Implementado con **YData**, el dashboard permite visualizar:

* Score general de calidad.
* Tendencias históricas.
* Alertas proactivas.
* Métricas por dimensión.

#### **6.2 Reportes Periódicos**

* **Diario:** Score de calidad y alertas críticas.
* **Semanal:** Tendencias y problemas recurrentes.
* **Mensual:** Reporte ejecutivo con recomendaciones.

#### **6.3 Métricas de Monitorización Continua**

* Tiempo de respuesta de alertas: ≤ 2 horas
* Exactitud de las lecturas: ≥ 98%
* Completitud de los datos: ≥ 95%
* Satisfaccion de los usuarios: ≥ 4/5

### **7. GARANTÍAS DE CUMPLIMIENTO**

#### **7.1 Auditorías Periódicas**

* Trimestral: Auditoría interna de procesos.
* Semestral: Auditoría externa.
* Anual: Revisión integral del sistema de calidad.

#### **7.2 Capacitación Continua**

* Mensual: Formación en buenas prácticas de lectura.
* Trimestral: Actualización de procesos de control.
* Anual: Certificación del equipo de datos.

#### **7.3 Mejora Continua**

* Revisión mensual de métricas.
* Ajuste trimestral de umbrales.
* Incorporación anual de innovaciones tecnológicas.

### **8. DOCUMENTACIÓN Y VERSIONAMIENTO**

#### **8.1 Documentación Obligatoria**

* Diccionario de datos actualizado.
* Registro de cambios y excepciones.
* Bitácora de correcciones.
* Reportes de auditoría.

#### **8.2 Control de Versiones**

* Versionado semántico de estándares.
* Historial de modificaciones.
* Backups automáticos de configuraciones.
