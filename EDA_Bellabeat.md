---
title: "Análisis Exploratorio de Datos para Bellabeat"
output:
  html_document: 
    keep_md: true
  html_notebook: default
editor_options:
  markdown:
    wrap: sentence
---
# **¿CÓMO PUEDE HACER UNA EMPRESA DE TECNOLOGÍA PARA EL BIENESTAR TOMAR DECISIONES INTELIGENTES?**

# **INTRODUCCIÓN**

El presente caso practico forma parte del proyecto final del curso de Google Data Analytics.
El análisis desarrollado se basa en datos de Bellabeat, un fabricante de productos de alta tecnología orientados a la salud de la mujer, los cuales fueron proporcionados de Kaggle.

La base de datos cuenta con registros de actividad física como el total de pasos y distancia recorrida, cantidad de calorías quemadas, intensidad de la actividad física, frecuencia cardíaca, MET (Equivalentes Metabólicos de Trabajo), tiempo de sueño y peso.

El objetivo de este análisis es identificar tendencias de uso de los dispositivos inteligentes que pueden ser aplicables a los clientes de Bellabeat. La exploración de estos datos permitió conocer mejor los comportamientos de los usuarios de dispositivos similares y cómo Bellabeat puede aplicar estos conocimientos para mejorar sus productos y estrategias de marketing.

# **DESAFÍO EMPRESARIAL**

El objetivo del proyecto fue realizar un análisis de los datos para conocer como los consumidores usan los dispositivos inteligentes que no son de Bellabeat para revelar nuevas oportunidades de crecimiento a través de estrategias de marketing.
El análisis se baso en las siguientes interrogantes:

1.  ¿Cuáles son algunas tendencias de uso de los dispositivos inteligentes?

2.  ¿Cómo se podrían aplicar estas tendencias a los clientes de Bellabeat?

3.  ¿Cómo podrían ayudar estas tendencias a influir en la estrategia de marketing de Bellabeat?

# **DISPOSICIÓN DE LOS DATOS**

## **Abastecimiento de datos**

Para el análisis se sugirió el uso de fuentes externas y públicas de información.
En específico para el caso se emplea el siguiente conjunto de datos:

**Datos de seguimiento de actividad física de Fitbit** (CC0: Dominio público, conjunto de datos disponibles a través de Möbius) disponible en https://www.kaggle.com/arashnic/fitbit.

Este conjunto de datos de Kaggle incluye el seguimiento de la actividad física de treinta usuarios de Fitbit.
Treinta usuarios seleccionados de Fitbit dieron su consentimiento para compartir datos personales de seguimiento. Con respecto a la privacidad, esta se mantiene ya que no incluye información que pueda identificar a los participantes. En cuanto a la seguridad, los datos se almacenaron en un lugar seguro.

## **Credibilidad y sesgo de los datos**

La credibilidad de los datos se ve afectada por varios factores, incluyendo la representatividad de la muestra, la calidad de los datos y la validez de las medidas. En este caso, la representatividad de la muestra es limitada, ya que los participantes fueron reclutados a través de Amazon Mechanical Turk, lo que puede introducir sesgos de selección. Además, la calidad de los datos puede verse afectada por la precisión de los monitores Fitbit y la fiabilidad de los informes de los participantes. Por último, la validez de las medidas puede verse afectada por la definición de las variables y la forma en que se midió.

### ***Librerías empleadas***


```r
library(dplyr) # para usar distinct() y eliminar duplicados
library(lubridate) # para manejar fechas y horas
library(readr) # permite leer archivos con formato .xlsx
library(skimr) # proporcionar estadísticas resumidas sobre variables
library(data.table)
library(janitor)
library(tidyverse) # para la importación y gestión de datos
library(rmarkdown) # brinda formato mardown al archivo
library(scales) # manejar las escalas en los gráficos
library(ggplot2) # permite crear gráficos
library(naniar) # permite crear gráficos de datos faltantes
library(purrr) # 
library(gridExtra) #pemite unir gráficos
library(scales) # manejar las escalas en los gráficos
library(ggnewscale) # permite aplicar múltiples escalas de color dentro de un mismo gráfico
```

### ***Subida de datos***

A continuación se indica el procedimiento aplicado para subir los datos para su posterior uso.


```r
#Se crea una función personalizada para leer los archivos
leer_archivo_csv <- function(ruta_archivo){
  # leer el archivo en un marco de datos
  data_clean <- read.csv(ruta_archivo) %>%
    distinct() %>% # elimina duplicados
    clean_names()  # Limpia los nombres de las columnas para facilitar su uso
  # devolver el marco de datos
  return(data_clean)}

rutas_archivos <- list(
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/dailyActivity-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/dailyActivity-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/dailyCalories-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/dailyIntensities-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/dailySteps-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/heartrate_seconds-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/heartrate_seconds-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/hourlyCalories-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/hourlyCalories-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/hourlyIntensities-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/hourlyIntensities-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/hourlySteps-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/hourlySteps-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteCaloriesNarrow-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteCaloriesNarrow-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteCaloriesWide-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteIntensitiesNarrow-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteIntensitiesNarrow-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteIntensitiesWide-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteMETsNarrow-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteMETsNarrow-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteSleep-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteSleep-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteStepsNarrow-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteStepsNarrow-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/minuteStepsWide-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/sleepDay-16_04_12-16_05_12.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/weightLogInfo-16_03_12-16_04_11.csv",
  "C:/Users/Dell/Documents/Analisis_Datos/Caso01-Bellabeat/Bellabeat/Datos_bellabeat/weightLogInfo-16_04_12-16_05_12.csv"
)

# Leer los archivos CSV y asignar nombres únicos a cada marco de datos
# Asegúrate de que rutas_archivos sea un vector de caracteres
data_clean <- lapply(rutas_archivos, leer_archivo_csv) %>%
  # Asignar nombres a cada marco de datos
  setNames(basename(unlist(rutas_archivos)) %>% 
             # Eliminar la extensión del archivo
             tools::file_path_sans_ext())
```

## **Revisión de los datos**

### ***Descripción de datos***

Como primer paso de exploración de los datos se observo las diferentes columnas que disponen, con el fin de identificar como están organizados.


```r
## Extrae los nombres de las columnas de cada dataframe en la lista data_clean para compararlos
for (nombres_archivos in names(lapply(data_clean, names))) {
  cat("Nombres de columnas en", nombres_archivos, ":\n")
  print(lapply(data_clean, names)[[nombres_archivos]])
  cat("\n")}
```

```
## Nombres de columnas en dailyActivity-16_03_12-16_04_11 :
##  [1] "id"                         "activity_date"             
##  [3] "total_steps"                "total_distance"            
##  [5] "tracker_distance"           "logged_activities_distance"
##  [7] "very_active_distance"       "moderately_active_distance"
##  [9] "light_active_distance"      "sedentary_active_distance" 
## [11] "very_active_minutes"        "fairly_active_minutes"     
## [13] "lightly_active_minutes"     "sedentary_minutes"         
## [15] "calories"                  
## 
## Nombres de columnas en dailyActivity-16_04_12-16_05_12 :
##  [1] "id"                         "activity_date"             
##  [3] "total_steps"                "total_distance"            
##  [5] "tracker_distance"           "logged_activities_distance"
##  [7] "very_active_distance"       "moderately_active_distance"
##  [9] "light_active_distance"      "sedentary_active_distance" 
## [11] "very_active_minutes"        "fairly_active_minutes"     
## [13] "lightly_active_minutes"     "sedentary_minutes"         
## [15] "calories"                  
## 
## Nombres de columnas en dailyCalories-16_04_12-16_05_12 :
## [1] "id"           "activity_day" "calories"    
## 
## Nombres de columnas en dailyIntensities-16_04_12-16_05_12 :
##  [1] "id"                         "activity_day"              
##  [3] "sedentary_minutes"          "lightly_active_minutes"    
##  [5] "fairly_active_minutes"      "very_active_minutes"       
##  [7] "sedentary_active_distance"  "light_active_distance"     
##  [9] "moderately_active_distance" "very_active_distance"      
## 
## Nombres de columnas en dailySteps-16_04_12-16_05_12 :
## [1] "id"           "activity_day" "step_total"  
## 
## Nombres de columnas en heartrate_seconds-16_03_12-16_04_11 :
## [1] "id"    "time"  "value"
## 
## Nombres de columnas en heartrate_seconds-16_04_12-16_05_12 :
## [1] "id"    "time"  "value"
## 
## Nombres de columnas en hourlyCalories-16_03_12-16_04_11 :
## [1] "id"            "activity_hour" "calories"     
## 
## Nombres de columnas en hourlyCalories-16_04_12-16_05_12 :
## [1] "id"            "activity_hour" "calories"     
## 
## Nombres de columnas en hourlyIntensities-16_03_12-16_04_11 :
## [1] "id"                "activity_hour"     "total_intensity"  
## [4] "average_intensity"
## 
## Nombres de columnas en hourlyIntensities-16_04_12-16_05_12 :
## [1] "id"                "activity_hour"     "total_intensity"  
## [4] "average_intensity"
## 
## Nombres de columnas en hourlySteps-16_03_12-16_04_11 :
## [1] "id"            "activity_hour" "step_total"   
## 
## Nombres de columnas en hourlySteps-16_04_12-16_05_12 :
## [1] "id"            "activity_hour" "step_total"   
## 
## Nombres de columnas en minuteCaloriesNarrow-16_03_12-16_04_11 :
## [1] "id"              "activity_minute" "calories"       
## 
## Nombres de columnas en minuteCaloriesNarrow-16_04_12-16_05_12 :
## [1] "id"              "activity_minute" "calories"       
## 
## Nombres de columnas en minuteCaloriesWide-16_04_12-16_05_12 :
##  [1] "id"            "activity_hour" "calories00"    "calories01"   
##  [5] "calories02"    "calories03"    "calories04"    "calories05"   
##  [9] "calories06"    "calories07"    "calories08"    "calories09"   
## [13] "calories10"    "calories11"    "calories12"    "calories13"   
## [17] "calories14"    "calories15"    "calories16"    "calories17"   
## [21] "calories18"    "calories19"    "calories20"    "calories21"   
## [25] "calories22"    "calories23"    "calories24"    "calories25"   
## [29] "calories26"    "calories27"    "calories28"    "calories29"   
## [33] "calories30"    "calories31"    "calories32"    "calories33"   
## [37] "calories34"    "calories35"    "calories36"    "calories37"   
## [41] "calories38"    "calories39"    "calories40"    "calories41"   
## [45] "calories42"    "calories43"    "calories44"    "calories45"   
## [49] "calories46"    "calories47"    "calories48"    "calories49"   
## [53] "calories50"    "calories51"    "calories52"    "calories53"   
## [57] "calories54"    "calories55"    "calories56"    "calories57"   
## [61] "calories58"    "calories59"   
## 
## Nombres de columnas en minuteIntensitiesNarrow-16_03_12-16_04_11 :
## [1] "id"              "activity_minute" "intensity"      
## 
## Nombres de columnas en minuteIntensitiesNarrow-16_04_12-16_05_12 :
## [1] "id"              "activity_minute" "intensity"      
## 
## Nombres de columnas en minuteIntensitiesWide-16_04_12-16_05_12 :
##  [1] "id"            "activity_hour" "intensity00"   "intensity01"  
##  [5] "intensity02"   "intensity03"   "intensity04"   "intensity05"  
##  [9] "intensity06"   "intensity07"   "intensity08"   "intensity09"  
## [13] "intensity10"   "intensity11"   "intensity12"   "intensity13"  
## [17] "intensity14"   "intensity15"   "intensity16"   "intensity17"  
## [21] "intensity18"   "intensity19"   "intensity20"   "intensity21"  
## [25] "intensity22"   "intensity23"   "intensity24"   "intensity25"  
## [29] "intensity26"   "intensity27"   "intensity28"   "intensity29"  
## [33] "intensity30"   "intensity31"   "intensity32"   "intensity33"  
## [37] "intensity34"   "intensity35"   "intensity36"   "intensity37"  
## [41] "intensity38"   "intensity39"   "intensity40"   "intensity41"  
## [45] "intensity42"   "intensity43"   "intensity44"   "intensity45"  
## [49] "intensity46"   "intensity47"   "intensity48"   "intensity49"  
## [53] "intensity50"   "intensity51"   "intensity52"   "intensity53"  
## [57] "intensity54"   "intensity55"   "intensity56"   "intensity57"  
## [61] "intensity58"   "intensity59"  
## 
## Nombres de columnas en minuteMETsNarrow-16_03_12-16_04_11 :
## [1] "id"              "activity_minute" "me_ts"          
## 
## Nombres de columnas en minuteMETsNarrow-16_04_12-16_05_12 :
## [1] "id"              "activity_minute" "me_ts"          
## 
## Nombres de columnas en minuteSleep-16_03_12-16_04_11 :
## [1] "id"     "date"   "value"  "log_id"
## 
## Nombres de columnas en minuteSleep-16_04_12-16_05_12 :
## [1] "id"     "date"   "value"  "log_id"
## 
## Nombres de columnas en minuteStepsNarrow-16_03_12-16_04_11 :
## [1] "id"              "activity_minute" "steps"          
## 
## Nombres de columnas en minuteStepsNarrow-16_04_12-16_05_12 :
## [1] "id"              "activity_minute" "steps"          
## 
## Nombres de columnas en minuteStepsWide-16_04_12-16_05_12 :
##  [1] "id"            "activity_hour" "steps00"       "steps01"      
##  [5] "steps02"       "steps03"       "steps04"       "steps05"      
##  [9] "steps06"       "steps07"       "steps08"       "steps09"      
## [13] "steps10"       "steps11"       "steps12"       "steps13"      
## [17] "steps14"       "steps15"       "steps16"       "steps17"      
## [21] "steps18"       "steps19"       "steps20"       "steps21"      
## [25] "steps22"       "steps23"       "steps24"       "steps25"      
## [29] "steps26"       "steps27"       "steps28"       "steps29"      
## [33] "steps30"       "steps31"       "steps32"       "steps33"      
## [37] "steps34"       "steps35"       "steps36"       "steps37"      
## [41] "steps38"       "steps39"       "steps40"       "steps41"      
## [45] "steps42"       "steps43"       "steps44"       "steps45"      
## [49] "steps46"       "steps47"       "steps48"       "steps49"      
## [53] "steps50"       "steps51"       "steps52"       "steps53"      
## [57] "steps54"       "steps55"       "steps56"       "steps57"      
## [61] "steps58"       "steps59"      
## 
## Nombres de columnas en sleepDay-16_04_12-16_05_12 :
## [1] "id"                   "sleep_day"            "total_sleep_records" 
## [4] "total_minutes_asleep" "total_time_in_bed"   
## 
## Nombres de columnas en weightLogInfo-16_03_12-16_04_11 :
## [1] "id"               "date"             "weight_kg"        "weight_pounds"   
## [5] "fat"              "bmi"              "is_manual_report" "log_id"          
## 
## Nombres de columnas en weightLogInfo-16_04_12-16_05_12 :
## [1] "id"               "date"             "weight_kg"        "weight_pounds"   
## [5] "fat"              "bmi"              "is_manual_report" "log_id"
```

Se observo que varios conjuntos de datos corresponde al mismo tipo de variable pero en un mes diferente, además, cada uno cuanto con una columna de identificación "id" y fecha que permitirá relacionarlos con mayor facilidad.

#### *Cantidad de voluntarios por tipo de variable*

Para decidir como relacionar las variables del mismo tipo pero de fecha diferente se procedió a determinar la cantidad de "id" diferentes para cada conjuntos de datos con el fin de decidir la forma en que se los agrupara.


```r
data_clean %>%
  lapply(function(df) {
    list(
      Registros = nrow(df),
      Voluntarios = length(unique(df$id))
      )
    })
```

```
## $`dailyActivity-16_03_12-16_04_11`
## $`dailyActivity-16_03_12-16_04_11`$Registros
## [1] 457
## 
## $`dailyActivity-16_03_12-16_04_11`$Voluntarios
## [1] 35
## 
## 
## $`dailyActivity-16_04_12-16_05_12`
## $`dailyActivity-16_04_12-16_05_12`$Registros
## [1] 940
## 
## $`dailyActivity-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`dailyCalories-16_04_12-16_05_12`
## $`dailyCalories-16_04_12-16_05_12`$Registros
## [1] 940
## 
## $`dailyCalories-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`dailyIntensities-16_04_12-16_05_12`
## $`dailyIntensities-16_04_12-16_05_12`$Registros
## [1] 940
## 
## $`dailyIntensities-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`dailySteps-16_04_12-16_05_12`
## $`dailySteps-16_04_12-16_05_12`$Registros
## [1] 940
## 
## $`dailySteps-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`heartrate_seconds-16_03_12-16_04_11`
## $`heartrate_seconds-16_03_12-16_04_11`$Registros
## [1] 1154681
## 
## $`heartrate_seconds-16_03_12-16_04_11`$Voluntarios
## [1] 14
## 
## 
## $`heartrate_seconds-16_04_12-16_05_12`
## $`heartrate_seconds-16_04_12-16_05_12`$Registros
## [1] 2483658
## 
## $`heartrate_seconds-16_04_12-16_05_12`$Voluntarios
## [1] 14
## 
## 
## $`hourlyCalories-16_03_12-16_04_11`
## $`hourlyCalories-16_03_12-16_04_11`$Registros
## [1] 24084
## 
## $`hourlyCalories-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`hourlyCalories-16_04_12-16_05_12`
## $`hourlyCalories-16_04_12-16_05_12`$Registros
## [1] 22099
## 
## $`hourlyCalories-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`hourlyIntensities-16_03_12-16_04_11`
## $`hourlyIntensities-16_03_12-16_04_11`$Registros
## [1] 24084
## 
## $`hourlyIntensities-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`hourlyIntensities-16_04_12-16_05_12`
## $`hourlyIntensities-16_04_12-16_05_12`$Registros
## [1] 22099
## 
## $`hourlyIntensities-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`hourlySteps-16_03_12-16_04_11`
## $`hourlySteps-16_03_12-16_04_11`$Registros
## [1] 24084
## 
## $`hourlySteps-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`hourlySteps-16_04_12-16_05_12`
## $`hourlySteps-16_04_12-16_05_12`$Registros
## [1] 22099
## 
## $`hourlySteps-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteCaloriesNarrow-16_03_12-16_04_11`
## $`minuteCaloriesNarrow-16_03_12-16_04_11`$Registros
## [1] 1445040
## 
## $`minuteCaloriesNarrow-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`minuteCaloriesNarrow-16_04_12-16_05_12`
## $`minuteCaloriesNarrow-16_04_12-16_05_12`$Registros
## [1] 1325580
## 
## $`minuteCaloriesNarrow-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteCaloriesWide-16_04_12-16_05_12`
## $`minuteCaloriesWide-16_04_12-16_05_12`$Registros
## [1] 21645
## 
## $`minuteCaloriesWide-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteIntensitiesNarrow-16_03_12-16_04_11`
## $`minuteIntensitiesNarrow-16_03_12-16_04_11`$Registros
## [1] 1445040
## 
## $`minuteIntensitiesNarrow-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`minuteIntensitiesNarrow-16_04_12-16_05_12`
## $`minuteIntensitiesNarrow-16_04_12-16_05_12`$Registros
## [1] 1325580
## 
## $`minuteIntensitiesNarrow-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteIntensitiesWide-16_04_12-16_05_12`
## $`minuteIntensitiesWide-16_04_12-16_05_12`$Registros
## [1] 21645
## 
## $`minuteIntensitiesWide-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteMETsNarrow-16_03_12-16_04_11`
## $`minuteMETsNarrow-16_03_12-16_04_11`$Registros
## [1] 1445040
## 
## $`minuteMETsNarrow-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`minuteMETsNarrow-16_04_12-16_05_12`
## $`minuteMETsNarrow-16_04_12-16_05_12`$Registros
## [1] 1325580
## 
## $`minuteMETsNarrow-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteSleep-16_03_12-16_04_11`
## $`minuteSleep-16_03_12-16_04_11`$Registros
## [1] 198034
## 
## $`minuteSleep-16_03_12-16_04_11`$Voluntarios
## [1] 23
## 
## 
## $`minuteSleep-16_04_12-16_05_12`
## $`minuteSleep-16_04_12-16_05_12`$Registros
## [1] 187978
## 
## $`minuteSleep-16_04_12-16_05_12`$Voluntarios
## [1] 24
## 
## 
## $`minuteStepsNarrow-16_03_12-16_04_11`
## $`minuteStepsNarrow-16_03_12-16_04_11`$Registros
## [1] 1445040
## 
## $`minuteStepsNarrow-16_03_12-16_04_11`$Voluntarios
## [1] 34
## 
## 
## $`minuteStepsNarrow-16_04_12-16_05_12`
## $`minuteStepsNarrow-16_04_12-16_05_12`$Registros
## [1] 1325580
## 
## $`minuteStepsNarrow-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`minuteStepsWide-16_04_12-16_05_12`
## $`minuteStepsWide-16_04_12-16_05_12`$Registros
## [1] 21645
## 
## $`minuteStepsWide-16_04_12-16_05_12`$Voluntarios
## [1] 33
## 
## 
## $`sleepDay-16_04_12-16_05_12`
## $`sleepDay-16_04_12-16_05_12`$Registros
## [1] 410
## 
## $`sleepDay-16_04_12-16_05_12`$Voluntarios
## [1] 24
## 
## 
## $`weightLogInfo-16_03_12-16_04_11`
## $`weightLogInfo-16_03_12-16_04_11`$Registros
## [1] 33
## 
## $`weightLogInfo-16_03_12-16_04_11`$Voluntarios
## [1] 11
## 
## 
## $`weightLogInfo-16_04_12-16_05_12`
## $`weightLogInfo-16_04_12-16_05_12`$Registros
## [1] 67
## 
## $`weightLogInfo-16_04_12-16_05_12`$Voluntarios
## [1] 8
```

# **PROCESAMIENTO DE LOS DATOS**

## **Unión de dataframes del mismo tipo de variable**

-   Dataframe de sueño diario


```r
sueño_diario <- data_clean$`sleepDay-16_04_12-16_05_12` %>%
    mutate(activity_day = date(mdy_hms(sleep_day)))
```

-   Usando el "id" se unió los dataframe que corresponden a 2 meses de datos de la lista de archivos y cuyo nombre son similares.


```r
# Combinar dataframes por filas para cada conjunto de datos mensuales

actividad_diaria <- bind_rows(distinct(data_clean$`dailyActivity-16_03_12-16_04_11`),
                              distinct(data_clean$`dailyActivity-16_04_12-16_05_12`))

frecuencia_cardiaca_seg <- bind_rows(distinct(data_clean$`heartrate_seconds-16_03_12-16_04_11`),
                                     distinct(data_clean$`heartrate_seconds-16_04_12-16_05_12`))

sueño_minuto <- bind_rows(distinct(data_clean$`minuteSleep-16_03_12-16_04_11`),
                          distinct(data_clean$`minuteSleep-16_04_12-16_05_12`))

peso_informe <- bind_rows(distinct(data_clean$`weightLogInfo-16_03_12-16_04_11`),
                          distinct(data_clean$`weightLogInfo-16_04_12-16_05_12`))
```

-   Dataframe uniendo variables por minuto


```r
# Unión de los dataframe
datos_minuto <- list(
  bind_rows(
    distinct(data_clean$`minuteCaloriesNarrow-16_03_12-16_04_11`),
    distinct(data_clean$`minuteCaloriesNarrow-16_04_12-16_05_12`)
  ),
  
  bind_rows(
    distinct(data_clean$`minuteIntensitiesNarrow-16_03_12-16_04_11`),
    distinct(data_clean$`minuteIntensitiesNarrow-16_04_12-16_05_12`)
  ),
  
  bind_rows(
    distinct(data_clean$`minuteMETsNarrow-16_03_12-16_04_11`),
    distinct(data_clean$`minuteMETsNarrow-16_04_12-16_05_12`)
  ),
  
  bind_rows(
    distinct(data_clean$`minuteStepsNarrow-16_03_12-16_04_11`),
    distinct(data_clean$`minuteStepsNarrow-16_04_12-16_05_12`)
  )
) %>%
  reduce(function(x, y) full_join(x, y, by = c("id", "activity_minute"))) %>%
mutate(activity_minute = mdy_hms(activity_minute),
       hora = hour(activity_minute))
```

-   Dataframe uniendo variables por hora


```r
# Unión de los dataframe
datos_hora <- list(
  bind_rows(distinct(data_clean$`hourlyCalories-16_03_12-16_04_11`),
            distinct(data_clean$`hourlyCalories-16_04_12-16_05_12`)),
  
  bind_rows(
    distinct(data_clean$`hourlyIntensities-16_03_12-16_04_11`),
    distinct(data_clean$`hourlyIntensities-16_04_12-16_05_12`)),
  
  bind_rows(distinct(data_clean$`hourlySteps-16_03_12-16_04_11`),
            distinct(data_clean$`hourlySteps-16_04_12-16_05_12`))
) %>%
  reduce(function(x, y) full_join(x, y, by = c("id", "activity_hour"))) %>%
mutate(activity_hour = mdy_hms(activity_hour),
       fecha = date(activity_hour),
       dia = factor(weekdays(fecha), levels = c("lunes","martes","miércoles","jueves","viernes","sábado","domingo")))
```

## **Datos faltantes**

Una vez organizados los datos se procedió a revisar datos faltantes.


```r
# Aplicar gg_miss_var a cada dataframe utilizando %>% y evitando la creación de nuevas variables
list(
  actividad_diaria = actividad_diaria,
  datos_hora = datos_hora,
  datos_minuto = datos_minuto,
  frecuencia_cardiaca_seg = frecuencia_cardiaca_seg,
  sueño_diario = sueño_diario,
  sueño_minuto = sueño_minuto,
  peso_informe = peso_informe
) %>%
  # Se utiliza imap para iterar sobre la lista de dataframes y sus nombres simultáneamente
  imap(~ {
    .x %>% 
      gg_miss_var() +
      labs(
        title = "Cantidad de Datos Faltantes por Variable",
        subtitle = paste("DataFrame:", .y),  # Agrega el nombre del dataframe como subtítulo
        x = "Variables",
        y = "Número de Datos Faltantes"
      ) +
      theme_minimal() +
      theme(axis.text.x = element_text(angle = 90, hjust = 1))
  }) %>% print()
```

```
## $actividad_diaria
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```
## 
## $datos_hora
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-2.png)<!-- -->

```
## 
## $datos_minuto
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-3.png)<!-- -->

```
## 
## $frecuencia_cardiaca_seg
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-4.png)<!-- -->

```
## 
## $sueño_diario
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-5.png)<!-- -->

```
## 
## $sueño_minuto
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-6.png)<!-- -->

```
## 
## $peso_informe
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-9-7.png)<!-- -->

## **Eliminación de columnas irrelevantes**

Se eliminó la columna "fat" del dataframe "peso_informe" ya que en su gran mayoría los registros estaban vacíos.


```r
peso_informe <- peso_informe %>% select(-fat)
```

# **ANÁLISIS DE DATOS**

Una vez hecho el procesamiento de los datos se prosiguió con el análisis de las diferentes variables para poder responder a las incógnitas que guiaron el proyecto.

## **Análisis de las Tendencias de Uso de los Dispositivos Inteligentes**

### ***Correlación de datos***


```r
# Pasos vs calorías
datos_hora %>% ggplot(aes(x = step_total, y = calories, color = average_intensity)) +
    geom_point() + geom_smooth() +
    labs(
      title = "Pasos vs Calorías",
      x = "Pasos",
      y = "calorias",
      color = "Intensidad Ejercicio"
    ) +
  scale_color_gradient(low = "#4df505", high ="#f50505") +
  theme_minimal()
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

Este gráfico nos indica la relación entre la cantidad de pasos y la calorías quemadas, y como la intensidad de ejercicio es un factor importante en la quema calorica.

Se observo como las *personas que realizan un mayor número de pasos a alta intensidad tienden a quemar muchas más calorías* en comparación con aquellos que hacen el mismo número de pasos a una intensidad más baja.

### ***Análisis Temporal***

-   **Datos por Minuto/Hora** En esta parte se exploró como varían las calorías, la intensidad y los pasos minuto a minuto y hora a hora con el fin de encontrar tendencias en estos datos.


```r
datos_minuto %>% group_by(hora) %>%
  summarise(
    Calorias = mean(calories, na.rm = TRUE),
    Intensidad = mean(intensity, na.rm = TRUE),
    Pasos = mean(steps, na.rm = TRUE)
    ) %>%
  gather(key = "Métrica", value = "Valor",Calorias, Intensidad, Pasos) %>%
  ggplot(aes(x = hora, y = Valor, color = Métrica)) +
  geom_line(lwd = 1) +
  coord_polar() +
  scale_x_continuous(breaks = seq(0, 23, by = 1), labels = c(1:23,0)) +
    facet_wrap(~ Métrica, scales = "free_y", ) +  # Cada gráfico con su propia escala
    labs(title = "Tendencias Promedio por Hora", x = "Hora del Día") +
    theme_minimal() +
    theme(axis.text.x = element_text(angle = 0, hjust = 1))
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

Con respecto a las horas del día en que se registran una mayor cantidad de pasos, quema de caloría he intensidad del ejercicio estos se registran alrededor del mediodía y a primeras horas de la tarde, lo que sugiere un patrón de actividad física vinculado a desplazamientos o ejercicio en horas laborales, así como indicadores de que los usuarios realizan actividades más vigorosas en estos periodos.


```r
datos_hora %>% group_by(id, fecha) %>%
    mutate(
      Calorias = calories,
      Intensidad = total_intensity,
      Pasos = step_total) %>%
  group_by(dia) %>%
  summarise(
    Calorias = mean(calories, na.rm = TRUE),
    Intensidad = mean(total_intensity, na.rm = TRUE),
    Pasos = mean(step_total, na.rm = TRUE)) %>%
  gather(key = "Métrica", value = "Valor",Calorias, Intensidad, Pasos) %>%
  ggplot(aes(x = dia, y = Valor, fill= Métrica)) +
  geom_col() +
  facet_wrap(~ Métrica, scales = "free_y") +  # Cada gráfico con su propia escala
  labs(title = "Tendencias Promedio por Día", x = "dia") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

Así mismo,se puede observar una tendencia a los largo de los diferentes días de la semana lo cual muestra que Bellabeat podría enfocar sus esfuerzos de marketing en promover el uso de sus dispositivos durante horas pico de actividad diaria, ofreciendo incentivos para que los usuarios se mantengan activos. Al demostrar como la intensidad de la actividad física se correlaciona tanto con la cantidad de pasos como la quema calórica.

-   **Frecuencia cardiaca** Se analizaron datos de frecuencia cardíaca por segundo, evaluando la variabilidad de las pulsaciones a lo largo del día en relación con las calorías quemadas y la intensidad de la actividad física.


```r
frecuencia_cardiaca_seg %>%
  mutate(
    Fecha = date(mdy_hms(time)),
    Hora = hour(mdy_hms(time))) %>%
  group_by(id,Fecha,Hora) %>%
  mutate(Frecuencia = mean(value, na.rm = TRUE)) %>%
  group_by(Hora) %>%
  summarise(Frecuencia = mean(value, na.rm = TRUE)) %>%
  ggplot(aes(x = Hora, y = Frecuencia)) +
  geom_line(lwd = 1) +
  coord_polar() +
  scale_x_continuous(breaks = seq(0, 23, by = 1), labels = c(1:23,0)) +
  labs(title = "Frecuencia cardiaca promedio por hora") +
  theme_minimal()
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

La frecuencia cardíaca muestra picos en horas de alta actividad física, lo que está en línea con el aumento de pasos e intensidad observados en los datos de actividad física. Esto refuerza la idea de que los usuarios están realizando ejercicio intenso o actividades extenuantes durante esos periodos.

En base a lo cual Bellabeat podría desarrollar nuevas características en sus dispositivos para alertar a los usuarios cuando su frecuencia cardíaca está por encima de un umbral saludable durante sus entrenamientos.

### ***Segmentación por actividad física***

A continuación se relaciono el nivel de actividad con dos variables clave: calorías quemadas y número de pasos.


```r
actividad_diaria %>%
  mutate(Nivel = case_when(
    very_active_minutes > 60 ~ "Muy Activo",
    fairly_active_minutes > 30 ~ "Moderadamente Activo",
    lightly_active_minutes > 0 ~ "Ligeramente Activo",
    TRUE ~ "Sedentario")) %>%
  group_by(Nivel) %>%
  summarize(
    Calorias = mean(calories),
    Pasos = mean(total_steps)) %>%
  gather(key = "Métrica", value = "Valor", Calorias, Pasos) %>%
  ggplot() +
  geom_col(aes(x = Valor, y = Nivel, fill = Valor), data = . %>% filter(Métrica == "Calorias")) +
  scale_fill_gradient(low = "#4df505", high = "#f50505") +
  
  ggnewscale::new_scale_fill() +  # Añade una nueva escala de color
  
  geom_col(aes(x = Valor, y = Nivel, fill = Valor), data = . %>% filter(Métrica == "Pasos")) +
  scale_fill_gradient(low = "#4df505", high = "#f50505") +
  
  facet_wrap(~ Métrica, nrow = 2, scales = "free") +
  theme_minimal()
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

Donde se observa que existe una correlación positiva entre el nivel de actividad y ambas variables (calorías quemadas y número de pasos). Las personas con mayor nivel de actividad física no solo queman más calorías, sino que también realizan más pasos.
El gráfico ilustra claramente la diferencia significativa entre los niveles de actividad y cómo impactan en la salud física, con los niveles más altos de actividad asociados a un mayor gasto energético y un mayor movimiento físico.

Bellabeat podría aprovechar esta tendencia incentivando a las usuarias a ser más activas mediante recompensas o desafíos personalizados, fomentando el uso de sus dispositivos. Además, campañas dirigidas a mujeres con niveles de actividad más bajos podrían motivarlas a adoptar hábitos más saludables, alineándose con la misión de Bellabeat de mejorar el bienestar femenino.

### ***Análisis de Indice de Masa Corporal***


```r
peso_informe <- peso_informe %>% 
  mutate(fecha =  date(mdy_hms(date)))

actividad_diaria <- actividad_diaria %>%
  mutate(fecha =  mdy(activity_date))
```


```r
grid.arrange(
    ggplot(data = left_join(peso_informe, actividad_diaria, by = c("id", "fecha")), aes(x = very_active_minutes, y = bmi, color = calories)) +
      geom_point() + geom_smooth() +
      labs(title = "Relación entre minutos muy activos e IMC",
           y = "IMC",
           x = "Minutos muy activos",
           color = "Calorías") +
      scale_color_gradient(low = "#4df505", high = "#f50505") +
      theme_minimal() +
      theme(
        plot.title = element_text(size = 10, face = "bold"),
        axis.title = element_text(size = 9),
        axis.text = element_text(size = 9)),

    ggplot(data = left_join(peso_informe, actividad_diaria, by = c("id", "fecha")), aes(x = fairly_active_minutes, y = bmi, color = calories)) +
      geom_point() + geom_smooth() +
      labs(title = "Relación entre minutos bastante activos e IMC",
           y = "IMC",
           x = "Minutos bastante activos",
           color = "Calorías") +
      scale_color_gradient(low = "#4df505", high = "#f50505") +
      theme_minimal() +
    theme(
        plot.title = element_text(size = 10, face = "bold"),
        axis.title = element_text(size = 9),
        axis.text = element_text(size = 9)),

    ggplot(data = left_join(peso_informe, actividad_diaria, by = c("id", "fecha")), aes(x = lightly_active_minutes, y = bmi, color = calories)) +
      geom_point() + geom_smooth() +
      labs(title = "Relación entre minutos ligeramente activos e IMC",
           y = "IMC",
           x = "Minutos ligeramente activos",
           color = "Calorías") +
      scale_color_gradient(low = "#4df505", high = "#f50505") +
      theme_minimal() +
      theme(
        plot.title = element_text(size = 10, face = "bold"),
        axis.title = element_text(size = 9),
        axis.text = element_text(size = 9)),

    ggplot(data = left_join(peso_informe, actividad_diaria, by = c("id", "fecha")), aes(x = sedentary_minutes, y = bmi, color = calories)) +
      geom_point() + geom_smooth() +
      labs(title = "Relación entre minutos sedentarios e IMC",
           y = "IMC",
           x = "Minutos sedentarios",
           color = "Calorías") +
      scale_color_gradient(low = "#4df505", high = "#f50505") +
      theme_minimal() +
      theme(
        plot.title = element_text(size = 10, face = "bold"),
        axis.title = element_text(size = 9),
        axis.text = element_text(size = 9)),

    nrow = 2,
    ncol = 2
  )
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

*Actividad física y IMC:* Los gráficos indican que más minutos de actividad, especialmente ligera y muy activa, tienden a estar asociados con un IMC más bajo, lo que sugiere un impacto positivo en la salud.
En particular, la actividad ligera parece tener un efecto más constante en la reducción del IMC a medida que aumenta la cantidad de minutos.

*Intensidad y quema calórica:* Aunque la intensidad del ejercicio es clave para quemar más calorías (como se ve en los puntos rojos en actividades más intensas), la actividad ligera también tiene un impacto positivo en la reducción del IMC, probablemente debido a su mayor sostenibilidad a lo largo del tiempo.

*Sedentarismo:* Los minutos sedentarios tienen una clara correlación positiva con un IMC más alto, destacando los efectos negativos del sedentarismo en la salud.

Bellabeat puede diseñar campañas que promuevan actividades ligeras y sostenibles, como caminatas o ejercicios de baja intensidad, dado que estas tienen un impacto constante en la reducción del IMC y son más accesibles para mujeres con rutinas ocupadas.

Además, Bellabeat podría desarrollar funcionalidades en sus dispositivos que monitoreen y alienten a reducir los períodos sedentarios, aprovechando la correlación negativa entre el sedentarismo y la salud. Al ofrecer programas personalizados que equilibren intensidad y sostenibilidad, Bellabeat puede ayudar a sus usuarias a alcanzar metas de salud a largo plazo, mejorando su bienestar y fidelidad a la marca.

### ***Análisis de sueño***


```r
grid.arrange(
sueño_minuto %>% distinct(id, date, .keep_all = TRUE) %>%
  mutate(
    dia = factor(weekdays(mdy_hms(date)),levels = c("lunes","martes","miércoles","jueves","viernes","sábado","domingo")),
    fecha_hora= mdy_hms(date),
    fecha = date(fecha_hora))%>%
  # horas promedio de sueño
  group_by(id, fecha) %>%
  mutate(
    tiempo_sueño = (sum(value, na.rm = TRUE)/60)) %>%
  group_by(dia) %>%
  summarise(tiempo_sueño = mean(tiempo_sueño)) %>%
  ggplot(aes(x = dia, y = tiempo_sueño)) +
  geom_col(aes( fill = tiempo_sueño)) +
  labs(title = "Horas de sueño promedio por día",
       x = NULL,
       y = "Horas") +
  scale_fill_gradient(low = "#054bf0", high = "#f50505") +
  theme_minimal() +
  labs(fill = "Horas"),

sueño_diario %>% mutate(dia = factor(weekdays(date(activity_day)),levels = c("lunes","martes","miércoles","jueves","viernes","sábado","domingo"))) %>%
    group_by(dia) %>%
    summarise(
      tiempo_cama = mean(total_time_in_bed - total_minutes_asleep)) %>%
    ggplot(aes(x = dia, y= tiempo_cama)) +
    geom_col(aes( fill = tiempo_cama)) + 
    labs (
      title = "Tiempo en cama despierto",
      x = NULL,
      y = "Minutos") +
  scale_fill_gradient(low = "#054bf0", high = "#f50505") +
  theme_minimal() +
  labs(fill = "Minutos"),
nrow = 2
)
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

Este gráfico muestra que las personas tienen un promedio constante de 7 a 8 horas de sueño por día, pero pasan más tiempo despiertas en la cama los fines de semana. Bellabeat puede aprovechar esta información creando alertas personalizadas que ayuden a mejorar la calidad del sueño, especialmente los fines de semana, y ofreciendo recomendaciones para reducir el tiempo despierto en la cama, optimizando el descanso. Esto reforzaría el enfoque de la empresa en el bienestar integral de la mujer.


```r
sueño_minuto %>% distinct(id, date, .keep_all = TRUE) %>%
  
  mutate(
    dia = factor(weekdays(mdy_hms(date)),levels = c("lunes","martes","miércoles","jueves","viernes","sábado","domingo")),
    fecha_hora= mdy_hms(date)) %>%
  
  group_by(Hora = hour(fecha_hora)) %>%
  
  summarize(sueño = sum(value, na.rm = TRUE)) %>%
  
  ggplot(aes(x = Hora, y = sueño)) +
  geom_col(aes(fill = sueño)) +
  coord_polar() +
  scale_x_continuous(breaks = seq(0, 23, by = 1), labels = c(1:23,0)) +
  labs(title = "Horario de sueño promedio",
       y = "Registros") +
  scale_fill_gradient(low = "#054bf0", high = "#f50505") +
  theme_minimal() +
  labs(fill = "Minutos")
```

![](EDA_Bellabeat_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

Este gráfico polar indica que la mayor parte del sueño ocurre entre las 22:00 y las 6:00, alineado con un ciclo de descanso típico. Combinado con la información anterior, Bellabeat puede desarrollar funciones que optimicen la calidad del sueño dentro de este horario clave. Al ofrecer recordatorios personalizados para preparar el sueño o ajustar rutinas, especialmente los fines de semana cuando el tiempo despierto en cama aumenta, Bellabeat puede ayudar a sus usuarias a mejorar sus hábitos de descanso, promoviendo una mejor salud y bienestar.

## **Identificación de Tendencias y Aplicaciones para Bellabeat**

- Patrones de Actividad Física: Los usuarios muestran una mayor actividad durante horas específicas del día, particularmente alrededor del mediodía y la tarde.

- Frecuencia Cardíaca: La frecuencia cardíaca está directamente correlacionada con los niveles de actividad física.

- Patrones de Sueño: Los usuarios tienden a dormir más los fines de semana, lo que sugiere que los dispositivos Bellabeat podrían enfocarse en ofrecer recomendaciones para mejorar la calidad del sueño durante la semana.

## **Estrategias de Marketing Basadas en las Tendencias Identificadas**

- *Campañas de Marketing Dirigidas:* Basándose en los patrones de actividad identificados, Bellabeat podría desarrollar campañas específicas que promuevan el uso de sus dispositivos durante horas de mayor actividad física.

- *Nuevas Funcionalidades:* Desarrollar alertas basadas en la frecuencia cardíaca para mejorar la experiencia de usuario durante los entrenamientos. Y alertar que permitan reducir los períodos sedentarios con el fin de fomentar la actividad física.

- *Promoción del Sueño:* Integrar recomendaciones de sueño personalizadas para ayudar a los usuarios a mejorar su descanso, una funcionalidad que podría diferenciar a Bellabeat de otros dispositivos inteligentes.
