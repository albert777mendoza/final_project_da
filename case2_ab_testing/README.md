# A/B Testing – Evalúa efectividad de un sistema de recomendaciones en tienda online

## 📌 Descripción del Proyecto

Proyecto de análisis de datos enfocado en evaluar la efectividad de un nuevo sistema de recomendaciones implementado en una tienda online mediante una prueba A/B.

El análisis incluyó exploración de datos, construcción de embudo de conversión, validación estadística y evaluación de impacto de negocio.

---
# 🎯 Objetivo

Evaluar si el nuevo sistema de recomendaciones mejora significativamente la tasa de conversión del funnel respecto al sistema anterior.

---
# 📂 Dataset

## Fuente
Datos operativos de una prueba A/B realizada en una tienda online.

## Variables principales
- user_id
- event_name
- group
- details
- dt

---
## 🛠 Herramientas y Tecnologías

Python | Pandas | Seaborn | SciPy | Matplotlib

---


## 🔗 Recursos

<div class="project-links">
  <a href="https://github.com/albert777mendoza/final_project_da/tree/main/case2_ab-testing" class="btn" target="_blank">📖 README</a>
  <a href="https://github.com/albert777mendoza/final_project_da/blob/main/case2_ab_testing/notebooks/ab_testing.ipynb" class="btn" target="_blank">📓 Notebook</a>
</div>

---


---


## ⚙ Procesos desarrollados

- Limpieza de datos  
- Transformación de datos  
- Análisis Exploratorio de Datos (EDA)  
- Construcción de embudo de conversión  
- Ejecución de prueba A/B  
- Evaluación de resultados

---

## ❓ Preguntas Clave

- ¿El cambio implementado en el grupo B genera mejora significativa en el embudo?  
- ¿Es recomendable la implementación del cambio inicialmente sugerido a la tienda online?  
- ¿La prueba estadística confirma que la diferencia es significativa?

---

## 📊 Metodología

1. **Preprocesamiento de datos:** limpieza, integración y estandarización, verificando inconsistencias, duplicados y valores faltantes.  
2. **Análisis Exploratorio de Datos (EDA):** comparación de tamaños de muestra, distribución de eventos por usuario y por día. Identificación de posibles anomalías.  
3. **Construcción del embudo de conversión:** se detectó desbalance inicial entre grupos (A tenía más usuarios en todas las etapas que B).  
4. **Ejecución de la prueba A/B:** B muestra ligera mejora en la etapa product_page → product_cart, pero pierde ventaja en la etapa final. La diferencia general no es significativa estadísticamente.

---

## 📸 Visualizaciones destacadas

**Funnel comparativo:** muestra cómo los usuarios avanzan en cada etapa del proceso de conversión.  
![Funnel Comparativo](https://albert777mendoza.github.io/assets/img/funnel-comparativo.png)

**Eventos por día:** evolución temporal de la actividad de los usuarios, detectando tendencias y picos de interacción.  
![Eventos por día](https://albert777mendoza.github.io/assets/img/eventos-dia.png)

**Resumen de la prueba:** compara usuarios, conversiones e ingresos de ambos grupos.  
![Resumen de la prueba](https://albert777mendoza.github.io/assets/img/resumen-prueba.png)

---

---

## ⚠️ Anomalías detectadas

- Contaminación de usuarios entre grupos
- Desbalance de muestras
- Sesgo temporal
- Desviaciones en asignación del experimento

---

# 💼 Impacto de negocio

- Se evitó la implementación de un cambio sin evidencia estadística.
- Se identificaron riesgos en el diseño experimental.
- Se mejoró la capacidad de toma de decisiones basada en datos.
- Se redujo el riesgo de decisiones sesgadas por mala estructura de experimento.

---

# 🚀 Próximos pasos

- Repetir el experimento con diseño balanceado.
- Incrementar tamaño de muestra.
- Agregar métricas de negocio (revenue, retención, ticket promedio).
- Mejorar control de asignación de usuarios.
- Monitorear calidad del experimento en tiempo real.

---


## 📝 Conclusiones y Recomendaciones

- El grupo B no logra superar al grupo A en el rendimiento del funnel, mostrando solo ligera mejora inicial.  
- El cambio implementado en B no genera mejora significativa. Diferencia observada mínima (-1,1%).  
- La prueba estadística confirma que la diferencia no es significativa (p-value = 0.3024).  
- **Recomendación:** NO implementar el cambio hasta realizar un nuevo experimento con mejor diseño, corrigiendo desbalances y sesgos.

---

## 🚀 Cómo Ejecutar el Proyecto

```bash
git clone https://github.com/albert777mendoza/final_project_da.git
pip install -r requirements.txt
jupyter notebook

## 👤 Autor

Albert Mendoza
Data Analyst Jr. | Python | SQL | Tableau
