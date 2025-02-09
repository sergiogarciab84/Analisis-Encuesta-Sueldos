# Proyecto: Análisis de la Encuesta "How Much Money Do You Make?"

**Fuente:** Ask A Manager Salary Survey 2021  
**Fecha de extracción de TRM:** 9 de febrero de 2025  

---
## Introducción
Este proyecto analiza los resultados de la encuesta "How Much Money Do You Make?" recolectada por *Ask A Manager*. La encuesta recopila datos sobre los salarios de profesionales en diferentes disciplinas y países, principalmente en Estados Unidos. 

---
## Objetivo del Proyecto
Preparar, limpiar y modelar los datos para visualizarlos en un **Dashboard interactivo**. Además, la documentación describe los pasos para replicar el proceso de modelado y análisis.

---
## Variables en la Base de Datos Original
| Variable                              | Tipo      | Descripción                                                                                 |
|---------------------------------------|-----------|---------------------------------------------------------------------------------------------|
| Timestamp                             | Fecha     | Fecha y hora en la que se completó la encuesta.                                             |
| How old are you?                      | Texto     | Rango de edad del encuestado.                                                               |
| What industry do you work in?         | Texto     | Industria en la que trabaja el encuestado.                                                  |
| Job title                             | Texto     | Título del cargo del encuestado.                                                            |
| What is your annual salary?           | Texto     | Salario anual reportado por el encuestado.                                                  |
| Please indicate the currency          | Texto     | Moneda en la que está expresado el salario anual.                                           |
| What country do you work in?          | Texto     | País donde trabaja el encuestado.                                                           |
| How many years of professional work experience do you have overall? | Texto | Años de experiencia profesional general.                                                    |
| What is your highest level of education completed?  | Texto  | Nivel de estudios completado por el encuestado.                                             |

---
## Variables Luego de Modeladas
Durante el proceso de modelado, se crearon nuevas variables para facilitar el análisis de la información.

| Variable            | Tipo      | Descripción                                                                                  |
|---------------------|-----------|----------------------------------------------------------------------------------------------|
| Salario_COP         | Numérico  | Salario anual convertido a pesos colombianos.                                                 |
| Ingreso_Adicional_COP | Numérico  | Compensación adicional anual en pesos colombianos.                                           |
| Ingreso_Total_COP   | Numérico  | Suma del salario anual y las compensaciones en pesos colombianos.                            |
| Rango de edad       | Texto     | Clasificación del rango de edad del encuestado.                                              |
| Tiempo de experiencia | Texto   | Clasificación del tiempo de experiencia profesional del encuestado.                           |

---
## Resumen del Modelado y Análisis de Datos
A lo largo de este proceso, trabajamos con los datos de la encuesta alojada en **Google Sheets** y llevamos a cabo una serie de pasos para preparar y analizar la información. 

### 1. Carga y Exploración de los Datos
Utilizamos Python y la biblioteca `pandas` para cargar y explorar los datos.
```python
import pandas as pd
df = pd.read_csv("encuesta.csv")
print(df.head())
```
### 2. Estandarización de los Campos de País y Ciudad
Aplicamos funciones para estandarizar las columnas `Country` y `City` asegurando que los valores fueran homogéneos.
```python
df['Country'] = df['Country'].str.strip().str.title()
df['Country'].replace({'United States of America': 'United States', 'UK': 'United Kingdom'}, inplace=True)
```
### 3. Conversión de Salarios a Tipo de Dato Numérico
```python
df['Annual Salary'] = pd.to_numeric(df['Annual Salary'].str.replace(',', ''), errors='coerce')
```
### 4. Conversión de Moneda a Pesos Colombianos
Utilizamos un diccionario con tasas de cambio para convertir los salarios a pesos colombianos.
```python
tasas_cambio = {'USD': 4113.7, 'EUR': 4500.0, 'GBP': 5100.0, 'COP': 1.0}
df['Salario_COP'] = df.apply(lambda row: row['Annual Salary'] * tasas_cambio.get(row['Currency'], 1) if pd.notna(row['Annual Salary']) else 0, axis=1)
```
### 5. Cálculo del Ingreso Total
```python
df['Ingreso_Total_COP'] = df['Salario_COP'] + df['Compensacion_Adicional_COP']
```
### 6. Visualización y Dashboard en Looker Studio
Exportamos los datos procesados a un archivo Excel y creamos un Dashboard interactivo en **Looker Studio** con los siguientes módulos:
- **Contador de Total de Encuestados**.
- **Mapa Interactivo** para visualizar los países donde trabajan los encuestados.
- **Gráficos** para analizar la relación entre industria, formación académica y salario.

### 7. Exportación a Excel
```python
df.to_excel("datos_modelados.xlsx", index=False)
```
---
## Visualización del Dashboard
### Capturas del Dashboard:
#### **Portada**
![Portada](https://github.com/sergiogarciab84/Analisis-Encuesta-Sueldos/blob/main/portada.png)

#### **Total de Encuestados y Distribución por País**
![Dashboard Slide 1](https://github.com/sergiogarciab84/Analisis-Encuesta-Sueldos/blob/main/1.png)

#### **Relación Industria vs. Salario**
![Dashboard Slide 2](https://github.com/sergiogarciab84/Analisis-Encuesta-Sueldos/blob/main/2.png)

#### **Salario Anual vs Tiempo de Experiencia**
![Dashboard Slide 3](https://github.com/sergiogarciab84/Analisis-Encuesta-Sueldos/blob/main/3.png)


---
## Conclusión
Este proceso permitió limpiar y estructurar los datos de manera efectiva, asegurando coherencia en los campos clave y facilitando su análisis. La conversión a pesos colombianos fue clave para contextualizar los datos en nuestro entorno.

---
## Enlace al Dashboard
[Acceder al Dashboard en Looker Studio](https://lookerstudio.google.com/reporting/4b8d8ec0-784b-44e9-a74b-0f82e94bc406)
