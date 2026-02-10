# 🛡️ Monitor de Ciberseguridad & Threat Intelligence (GCBA 2025)

> **Un sistema de inteligencia de amenazas diseñado para transformar reportes estáticos en un Centro de Comando Dinámico, permitiendo la visualización de la evolución del crimen digital en la Ciudad de Buenos Aires (Comparativa 2024 vs. 2025).**

---

## 📋 Tabla de Contenidos
1. [Contexto del Negocio](#-contexto-del-negocio)
2. [Arquitectura de Datos (ETL)](#-arquitectura-de-datos-etl)
3. [Métricas Clave (KPIs)](#-métricas-clave-kpis)
4. [Análisis Visual y Storytelling](#-análisis-visual-y-storytelling)
5. [Implementación Técnica (DAX)](#-implementación-técnica-dax)
6. [Insights Descubiertos](#-insights-descubiertos)
7. [Stack Tecnológico](#-stack-tecnológico)

---

## 💼 Contexto del Negocio

**El Problema:**
La gestión de incidentes de ciberseguridad en el ámbito público suele depender de reportes mensuales estáticos (PDFs). Esta fragmentación de datos impedía:
- Visualizar la tendencia histórica de los ataques.
- Correlacionar el aumento de la demanda operativa con eventos específicos.
- Identificar la mutación de los vectores de ataque (ej. ¿Cuándo el Phishing supera a las Estafas?).

**La Solución:**
Desarrollar un **Dashboard de Threat Intelligence** en Power BI que consolide el ciclo fiscal completo (Enero - Diciembre), permitiendo comparar el desempeño de 2025 contra la línea base de 2024 para facilitar la toma de decisiones proactivas.

---

## 🔄 Arquitectura de Datos (ETL)

El proceso de ingeniería de datos se dividió en tres fases:

1.  **Extracción (Extract):** Recopilación de reportes mensuales no estructurados (PDF) emitidos por el organismo gubernamental.
2.  **Transformación (Transform):**
    * Limpieza de datos (Data Cleansing) para estandarizar categorías (ej. unificación de "Phising" a "Phishing").
    * Normalización de fechas para permitir funciones de Time Intelligence.
    * Creación de tablas de dimensiones para manejar el ordenamiento cronológico.
3.  **Carga (Load):** Ingesta en Power BI mediante archivos CSV estructurados.

---

## 📊 Métricas Clave (KPIs)

Para medir la salud del ecosistema digital, definí tres indicadores de alto nivel:

| KPI | Definición Técnica | Objetivo de Negocio |
| :--- | :--- | :--- |
| **Explosión de Demanda (YoY)** | `(Volumen Actual - Volumen Año Anterior) / Volumen Año Anterior` | Medir la aceleración de la presión operativa sobre el equipo de defensa (SOC). |
| **Cuota de Atención** | `% Share of Voice` de Ciberseguridad vs. otros temas. | Determinar la criticidad del área dentro de la agenda pública. |
| **Distribución Neta de Ataques** | Distribución % excluyendo "Consultas Administrativas". | **Metric Custom:** Aislar los ataques reales para evitar que el ruido operativo diluya la gravedad de las amenazas. |

---

## 📈 Análisis Visual y Storytelling

El tablero fue diseñado bajo los principios de **"Dark Mode UI"** para simular interfaces de monitoreo de seguridad (SOC), reduciendo la fatiga visual y resaltando las alertas críticas.

### Página 1: Radar Estratégico
* **Visión General:** Gráfico combinado (Líneas + Barras) que cruza el volumen de tickets con la aceleración interanual.
* **Composición:** Gráfico de anillo para entender la carga operativa (Burocracia vs. Ataques).

### Página 2: Inteligencia de Amenazas (Threat Intel)
* **Matriz de Calor (Heatmap):** Permite detectar "Zonas Calientes" temporales, identificando meses donde ciertos ataques se dispararon.
* **Ribbon Chart (Cintas):** Visualiza el **Ranking Cambiante**. Muestra cómo el "Enemigo #1" mutó de Estafas (Enero) a Ingeniería Social (Diciembre).
* **Semáforo de Riesgo:** Gráfico de barras con líneas de referencia (Threshold) para marcar alertas críticas cuando un ataque supera el 10% de incidencia.

---

## 💻 Implementación Técnica (DAX)

Se utilizó **DAX (Data Analysis Expressions)** avanzado para crear lógica de negocio dinámica.

**1. Semáforo de Riesgo (Lógica Condicional):**
Asigna colores automáticamente según la gravedad del incidente para alertas visuales.
```dax
Color Riesgo = 
SWITCH(
    TRUE(),
    SELECTEDVALUE('Data'[Tipo]) IN {"Estafas", "Suplantación"}, "#D92525", // Critical Red
    SELECTEDVALUE('Data'[Tipo]) IN {"Phishing", "Ingeniería Social"}, "#FF8C00", // Warning Orange
    "#CCCCCC" // Neutral Grey
)
```
**2. Distribución Neta (Normalización de Datos):**
Recalcula los porcentajes eliminando el ruido de las consultas generales ("Información")
```dax
% Distribucion Neta Ataques = 
VAR PorcentajeActual = SUM('Ciberseguridad - Buenos Aires'[Porcentaje_Incidente])
VAR PorcentajeInformacion = 
    CALCULATE(
        SUM('Ciberseguridad - Buenos Aires'[Porcentaje_Incidente]), 
        'Ciberseguridad - Buenos Aires'[Tipo_Incidente] = "Información (General)"
    )
VAR BaseNormalizada = 1 - PorcentajeInformacion

RETURN
    DIVIDE(PorcentajeActual, BaseNormalizada, 0)
```

## 🔍 Insights Descubiertos

Tras el análisis de los datos de 2025, se concluye:

* **Mutación de Vectores:** Existe una clara migración del cibercrimen. El año inició con Estafas Transaccionales como vector principal, pero cerró con un dominio de la Ingeniería Social, sugiriendo un aumento en la sofisticación de los atacantes.

* **Estacionalidad Crítica:** Se detectaron picos de estrés operativo en Febrero y Marzo, correlacionados con un crecimiento interanual superior al 300%.

* **Saturación del Sistema:** En meses pico, la Ciberseguridad representó más del 60% de toda la atención al ciudadano, validando la necesidad de escalar recursos.

## 🛠 Stack Tecnológico

* **Microsoft Power BI:** Modelado de datos y visualización.

* **DAX:** Lógica de cálculo y medidas.

* **Power Query (M):** Transformación y limpieza de datos (ETL).

* **Excel/CSV:** Estructuración de fuentes de datos.
