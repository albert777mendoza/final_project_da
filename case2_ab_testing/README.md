# A/B Testing – Evalúa efectividad de un sistema de recomendaciones en tienda online

## 📌 Ficha Ejecutiva

- **Problema de negocio:** Evaluar si un nuevo sistema de recomendaciones en una tienda online mejora la tasa de conversión frente al sistema anterior.
- **Dataset utilizado:** Datos de comportamiento de usuarios en una prueba A/B, incluyendo eventos de navegación, conversiones y métricas por grupo (A y B).
- **Herramientas:** Python, Pandas, Seaborn, SciPy, Matplotlib.
- **Resultado principal:** El grupo B no mostró mejora estadísticamente significativa en la tasa de conversión ni en ingresos frente al grupo de control (A).
- **Recomendación final:** No implementar el cambio actual. Rediseñar el experimento corrigiendo desbalances entre grupos y posibles sesgos en la asignación.

---

## 🔗 Recursos

[📖 README](https://github.com/albert777mendoza/final_project_da/tree/main/case2_ab-testing)  
[📓 Notebook](https://github.com/albert777mendoza/final_project_da/blob/main/case2_ab_testing/notebooks/ab_testing.ipynb)

---

## 🎯 Objetivo

Evaluar si el sistema de recomendaciones recientemente implementado en la tienda online generó mejoras significativas en la tasa de conversión, comparando los resultados de un grupo de control y un grupo de prueba.

---

## 🛠 Herramientas y Tecnologías

Python | Pandas | Seaborn | SciPy | Matplotlib

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

1. ¿El cambio implementado en el grupo B genera mejora significativa en el embudo?  
2. ¿Es recomendable la implementación del cambio inicialmente sugerido a la tienda online?  
3. ¿La prueba estadística confirma que la diferencia es significativa?

---

## 📊 Metodología

1. **Preprocesamiento de datos:** limpieza, integración y estandarización, verificando inconsistencias, duplicados y valores faltantes.  
2. **Análisis Exploratorio de Datos (EDA):** comparación de tamaños de muestra, distribución de eventos por usuario y por día. Identificación de posibles anomalías.  
3. **Construcción del embudo de conversión:** se detectó desbalance inicial entre grupos (A tenía más usuarios en todas las etapas que B).  
4. **Ejecución de la prueba A/B:** B muestra ligera mejora en la etapa product_page → product_cart, pero pierde ventaja en la etapa final. La diferencia general no es significativa estadísticamente.

---

## 📸 Visualizaciones destacadas

- **Funnel comparativo:** muestra cómo los usuarios avanzan en cada etapa del proceso de conversión.
- 
![Funnel Comparativo](https://albert777mendoza.github.io/assets/img/funnel-comparativo.png)

- **Eventos por día:** evolución temporal de la actividad de los usuarios, detectando tendencias y picos de interacción.
- 
![Eventos por día](https://albert777mendoza.github.io/assets/img/eventos-dia.png)

- **Resumen de la prueba:** compara usuarios, conversiones e ingresos de ambos grupos.
- 
![Resumen de la prueba](https://albert777mendoza.github.io/assets/img/resumen-prueba.png)
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

👉 [Explorar notebook completo](notebooks/ab_testing.ipynb)  
👉 [Ver storytelling completo](storytelling.md)



