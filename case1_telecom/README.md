[![CallMeMaybe Dashboard](https://albert777mendoza.github.io/assets/img/dashboard.png)](https://public.tableau.com/views/Case1_TelecomFinalProyectD_A/Hoja1?:language=es-ES&:display_count=n&:origin=viz_share_link)

# 📞 Caso 1 – Análisis de Telecomunicaciones

## 📌 Descripción del Proyecto

Proyecto de análisis de datos enfocado en identificar operadores ineficaces del servicio de telefonía virtual **CallMeMaybe** mediante análisis exploratorio, métricas operativas y validación estadística.

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
- llamadas entrantes,
- llamadas salientes,
- llamadas internas,
- duración de llamadas,
- tiempos de espera,
- desempeño operativo por operador.

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

# ⚙️ Metodología

- Limpieza y transformación de datos
- Análisis Exploratorio de Datos (EDA)
- Segmentación de operadores
- Sistema de scoring y semaforización
- Validación estadística
- Construcción de dashboard operativo

---

# 🔍 Hallazgos Clave

## 📉 Ineficiencia en llamadas salientes

Las llamadas salientes presentaron una tasa significativamente mayor de pérdida frente a las llamadas entrantes:

- Salientes: **52.56%**
- Entrantes: **41.46%**

La diferencia fue validada mediante prueba estadística.

### Visualización
![Distribución llamadas](https://albert777mendoza.github.io/assets/img/outbound_calls.png)

---

## ⏱ Baja efectividad operativa

La mediana de duración de llamadas salientes fue:

- **0 minutos**

Esto evidenció baja capacidad de contacto efectivo y posibles fallas estructurales en la gestión de llamadas salientes.

### Visualización
![Duración llamadas](https://albert777mendoza.github.io/assets/img/call_duration.png)

---

## 🔄 Problemas en llamadas internas

Las llamadas internas presentaron alta pérdida incluso con operador:

- **45.49%**

Lo anterior sugiere posibles problemas de coordinación operativa interna.

### Visualización
![Llamadas internas](https://albert777mendoza.github.io/assets/img/internal_calls.png)

---

## 🤖 Sistema automático ineficiente

El sistema automático sin operador presentó aproximadamente:

- **99% de llamadas perdidas**

Lo que evidenció una fuerte dependencia del operador humano dentro de la operación.

### Visualización
![Sistema automático](https://albert777mendoza.github.io/assets/img/no_operator.png)

---

# 📊 Dashboard Operativo

Dashboard interactivo desarrollado en Tableau para monitorear:

- llamadas perdidas,
- desempeño operativo,
- duración promedio,
- operadores críticos,
- eficiencia operacional.

[![CallMeMaybe Dashboard](https://albert777mendoza.github.io/assets/img/dashboard.png)](https://public.tableau.com/views/Case1_TelecomFinalProyectD_A/Hoja1?:language=es-ES&:display_count=n&:origin=viz_share_link)

🔗 **Ver Dashboard Tableau:**  
https://public.tableau.com/views/Case1_TelecomFinalProyectD_A/Hoja1?:language=es-ES&:display_count=n&:origin=viz_share_link

---

# 📈 Correlación entre Variables Operativas

![Correlation](https://albert777mendoza.github.io/assets/img/correlation.png)

La matriz de correlación permitió identificar relaciones relevantes entre:

- volumen de llamadas,
- duración total,
- llamadas perdidas,
- score de ineficiencia operacional.

---

# 📊 Distribución y Eficiencia Operacional

![Distribución](https://albert777mendoza.github.io/assets/img/distribucion.png)

La segmentación operacional permitió identificar operadores críticos mediante un sistema de scoring y semaforización basado en métricas de productividad y eficiencia.

---

# 💡 Estrategias Recomendadas

- Optimizar bases de datos y horarios de contacto.
- Implementar dashboards de monitoreo operativo.
- Fortalecer procesos de capacitación y gestión de tiempos.
- Rediseñar procesos internos y mejorar el sistema automático.

---

# 🚀 Cómo Ejecutar el Proyecto

## 1️⃣ Clonar repositorio

```bash
git clone https://github.com/albert777mendoza/final_project_da.git
```

## 2️⃣ Instalar dependencias

```bash
pip install -r requirements.txt
```

## 3️⃣ Ejecutar notebook

```bash
jupyter notebook
```

---

# 🔗 Recursos

## 📓 Notebook completo
👉 Explorar notebook completo

## 📊 Dashboard Tableau
👉 Ver Dashboard Tableau

## 📖 Storytelling del proyecto
👉 Ver storytelling completo

---

# 👤 Autor

**Albert Mendoza**  
Data Analyst Jr. | Python | SQL | Tableau
