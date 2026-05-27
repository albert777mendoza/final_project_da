# Caso 1 – Análisis de Telecomunicaciones

[![CallMeMaybe Dashboard](https://albert777mendoza.github.io/assets/img/dashboard.png)](https://public.tableau.com/views/Case1_TelecomFinalProyectD_A/Hoja1?:language=es-ES&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

---

# 📌 Descripción del Proyecto

Proyecto de análisis de datos enfocado en identificar operadores ineficaces del servicio de telefonía virtual CallMeMaybe mediante análisis exploratorio, métricas operativas y validación estadística.

---

# 🎯 Objetivo

Identificar operadores ineficaces del servicio de telefonía virtual **CallMeMaybe** mediante análisis exploratorio y métricas de eficiencia operacional.

---

# 📂 Dataset

## Fuente
Registros operativos del servicio de telefonía virtual CallMeMaybe.

## Variables principales

- `operator_id`
- `call_duration`
- `missed_call`
- `direction`
- `internal`
- `date`

## Información analizada

- llamadas entrantes
- llamadas salientes
- llamadas internas
- duración de llamadas
- desempeño operativo por operador

---

# 🛠 Herramientas y Tecnologías

- Python
- Pandas
- Seaborn
- Matplotlib
- SciPy
- Tableau
- Jupyter Notebook

---

# 🔎 Metodología

- Limpieza y transformación de datos
- Análisis Exploratorio de Datos (EDA)
- Segmentación de operadores
- Sistema de semaforización
- Validación estadística
- Construcción de dashboard operativo

---

# 📊 Hallazgos Clave

## 📉 Ineficiencia en llamadas salientes

Las llamadas salientes presentaron mayor tasa de pérdida:

- Salientes: **52.56%**
- Entrantes: **41.46%**

---

## ⏱ Baja efectividad operativa

La mediana de duración de llamadas salientes fue:

### **0 minutos**

Esto evidencia baja capacidad de contacto efectivo.

---

## 🔄 Problemas en llamadas internas

Las llamadas internas presentan alta pérdida incluso con operador:

### **45.49%**

---

## 🤖 Sistema automático ineficiente

El sistema automático sin operador presentó aproximadamente:

### **99% de llamadas perdidas**

---

# 💼 Impacto Potencial de Negocio

- Se estimaron pérdidas potenciales de productividad cercanas a **$6K USD mensuales** asociadas a tiempos de espera operacionales en operadores críticos.

- Si la tasa de llamadas perdidas se redujera desde **37%** hasta el objetivo operacional del **5%**, la operación podría recuperar más de **10,000 interacciones adicionales con clientes**.

- Los resultados permiten priorizar acciones sobre operadores críticos, optimizar procesos de llamadas salientes y mejorar la capacidad operativa del contact center.

---

# 📸 Visualizaciones Destacadas

## 📊 Dashboard Operativo

[![CallMeMaybe Dashboard](https://albert777mendoza.github.io/assets/img/dashboard.png)](https://public.tableau.com/views/Case1_TelecomFinalProyectD_A/Hoja1?:language=es-ES&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

Dashboard interactivo desarrollado en Tableau para monitorear:

- llamadas perdidas
- desempeño operativo
- duración promedio
- operadores críticos

---

## 🔥 Correlación entre variables operativas

![Correlation](https://albert777mendoza.github.io/assets/img/correlation.png)

La matriz de correlación permitió identificar relaciones relevantes entre:

- volumen de llamadas
- duración
- pérdida operativa
- score de ineficiencia

---

## 📈 Distribución y eficiencia operacional

![Distribución](https://albert777mendoza.github.io/assets/img/distribution.png)

Visualización utilizada para detectar:

- operadores críticos
- patrones de ineficiencia
- comportamiento típico vs outliers

---

# ✅ Estrategias Recomendadas

- Optimizar bases de datos y horarios de contacto
- Implementar dashboards de monitoreo
- Fortalecer procesos de capacitación y gestión de tiempos
- Rediseñar procesos internos y mejorar el sistema automático

---

# 🚀 Cómo Ejecutar el Proyecto

## 1. Clonar repositorio

```bash
git clone https://github.com/albert777mendoza/final_project_da.git


# 👤 Autor

**Albert Mendoza**  
Data Analyst Jr. | Python | SQL | Tableau
