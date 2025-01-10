Herramienta de Modelación para la Distribución de Servicios y  Áreas Verdes en Sitios Eriazos
DAG_DyDAT_2s_2024_SEMESTRAL
<h2 align="center">
  <div>
  <img width="200" src="https://upload.wikimedia.org/wikipedia/commons/d/d9/Usach_P1.png" alt="logo Usach">
  </div>
 
  <img width= "300" src="![Imagen de WhatsApp 2025-01-07 a las 18 43 05_e50f79a8](https://github.com/user-attachments/assets/3353defe-7daf-492e-9992-6e5b67625118)
" alt= "terrabits">

  <p>Universidad de Santiago de Chile
<p>Facultad de Ingeniería
<p>Departamento de Ingeniería Geoespacial
<p> Terrabits

# DAG_DyDAT_2s_2024_SEMESTRAL
# Herramienta de Modelación para la Distribución de Servicios y  Áreas Verdes en Sitios Eriazos
## Descripción 📋
<p>La Herramienta de Modelación para la Distribución de Servicios y Áreas Verdes en Sitios Eriazos es una solución diseñada para estimar y asignar el uso de suelo a sitios eriazos dentro de una comuna específica. Este modelo tiene como objetivo optimizar la distribución de servicios y áreas verdes, mejorando la planificación urbana y la calidad de vida de los habitantes
</p>

## Requisitos 🛠️
Entre los requisitos, requiere la instalación del software ArcGIS PRO y colocar la GDB respectiva disponible en este **Repositorio**

## Capas que utiliza el Script 🗺️

### Script 1. Cobertura de Servicios con Network
Este script utiliza las siguientes capas geoespaciales para calcular la cobertura de servicios mediante análisis de redes:
* Vialidad (Línea) 🚙
* Bomberos (Punto) 🧑‍🚒
* Carabineros (Punto) 👮‍♂️
* Colegios (Punto) 🏫 
* Salud (Punto) 🏥
* Conservador de Bienes Raíces (Punto) 🏢
* Jardines Infantiles (Punto) 🧸
* Registro Civil (Punto) 📝
* Área Verde (Punto) 🏞️
* Espacio de Trabajo (GDB) 📂

### Script 2. Area Mínima requerida para Destinos.
Este script evalúa la proximidad y el área mínima necesaria para asignar destinos, utilizando las siguientes capas:
* Capa Predios (Polígono)
* Capa Sitio Eriazos (Punto)
* Capa Salud (Punto)
* Capa Carabineros (Punto)
* Capa Bomberos (Punto)
* Capa Colegios (Punto)
* Capa Conservador de Bienes Raices (Punto)
* Capa Jardines Infantiles (Punto)
* Capa Registro Civil (Punto)
* Capa Área Verde (Polígono)
* Clase de Entidad de Salida (Output) (Polígono)
### Script 3. Calculo Modelo
Este script se utiliza para calcular el modelo final, empleando las siguientes capas geoespaciales:
* Capa Sitio Eriazos (Polígono) :Output Anterior
* Capa Censo manzana (Polígono)
* Capa PRC (Polígono)
* Clase de Entidad de Salida (Output) (Polígono)

  
  
## Aplicación 🖥️


## Aplicación 🖥️
La aplicación comienza con la ejecución del primer script, ***Script 1: Cobertura de Servicios con Network***, donde se configuran los parámetros previamente mencionados. Este script realiza un análisis de red para cada capa de servicio (``Salud, Bomberos, Carabineros, etc.``), generando resultados que miden la cobertura de los servicios mediante el tiempo de caminata de 5, 10 y 15 minutos desde cada punto de servicio.

Una vez realizado este análisis, se aplica a las capas de predios, creando un nuevo campo denominado COBERTURA_{``nombre del servicio``}. El cálculo realizado consiste en una selección por atributo en la que, si las entidades de servicio generadas por el análisis de red intersectan con los predios, el valor asignado en el campo es `0`; en caso contrario, el valor es `1`. Estos valores son utilizados como variables booleanas para el modelo.

Posteriormente, se ejecuta el ***Script 2: Área Mínima Requerida para Destinos ***, cuyo objetivo es evaluar la viabilidad de la construcción de los servicios en función del área del predio. Para ello, se aplica un filtro de superficie, ya que cada tipo de servicio requiere una superficie mínima específica para su construcción (por ejemplo, algunas instalaciones, como las de Parque o Salud, necesitan más espacio que otras). Este script genera los campos FA_{``nombre del servicio``} como variables booleanas que se integran en el modelo.

El último paso corresponde a ***Script 3: Cálculo del Modelo***. Este script toma como entrada los resultados generados en los pasos anteriores y realiza un conteo de la población en el área de influencia de cada servicio. A continuación, se aplica un join espacial entre los sitios eriazos y su respectiva capa `PRC` (Plan Regulador Comunal) para determinar la zonificación permitida del área. Luego, se realiza otro join espacial entre los sitios eriazos y las capas de población para calcular cuánta población se encuentra alrededor de cada predio. Los resultados se almacenan en la clase de entidad SITIO_ERIAZO_{Comuna}, creando los campos TOTAL_PERS_{`nombre del servicio`}.

Una vez calculada la población, los valores se normalizan utilizando el Método Min-Max, generando los campos TOTAL_PERS_{`nombre del servicio`}_NORM.

Además, este script evalúa el puntaje del área en función de su adecuación al tamaño óptimo para cada tipo de servicio. Por ejemplo, si para ubicar una comisaría se requiere un terreno de entre 200 y 500 m², y un sitio eriazo tiene 1000 m², el predio será penalizado en su ponderación final debido a que su superficie excede la necesidad óptima para ese uso específico.

Finalmente, se calcula el modelo completo y se crean los campos Índice_{``nombre del servicio``} para cada servicio, los cuales reflejan la idoneidad del predio para el uso específico. La fórmula para calcular el índice se basa en los datos obtenidos en los pasos previos.

<h2 align="center">
  <img src="https://github.com/user-attachments/assets/3633c964-00f4-42e4-819b-597715472573" alt="fórmula" />
</h2>




El script también genera los campos Prioridad1, Prioridad2 y Prioridad3, los cuales jerarquizan los valores de los Índice_{``nombre del servicio``}. Esta jerarquización permite clasificar los predios según su idoneidad para la ubicación de cada tipo de servicio, como se muestra en la siguiente tabla:
<h2 align="center">
  <img src="https://github.com/user-attachments/assets/e5fa9201-af01-4338-9cf4-8c6903e1e1e6" />
</h2>



## GUIA DE APLICACIÓN DE LA HERRAMIENTA
https://usachcl-my.sharepoint.com/:v:/g/personal/noemi_concha_usach_cl/ESD54E-215xCnVVMxcCAM80BQ9W3KTEjeaxkEp7QA7Vcng?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=SGNiVC
