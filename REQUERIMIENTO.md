### Requerimiento Técnico: Plataforma Web de Análisis Estadístico (R + React)

#### 1. Objetivo General

Desarrollar una aplicación web para el análisis de datos y estadísticas avanzadas. La plataforma debe ser modular y extensible. El proyecto se dividirá en tres fases principales: **"El Core" (Fase 1)**, **Análisis Avanzado y Persistencia de Datos (Fase 2)**, y **Modelado de Machine Learning (Fase 3)**.

#### 2. Arquitectura y Diseño Técnico

Se utilizará una arquitectura de **frontend/backend desacoplada**. El frontend, construido con React, se encargará de la interfaz de usuario, la visualización interactiva y el manejo del estado local. El backend, en R, será el motor de procesamiento de datos y la API que exponga las funciones estadísticas.

* **Frontend (React):**
    * **Manejo del Estado:** Se utilizará un gestor de estado centralizado (como Redux o la Context API de React) para manejar el dataset y la configuración del análisis, asegurando que todos los componentes tengan acceso a la misma información.
    * **Comunicación:** Se utilizará la API `fetch` o librerías como `axios` para realizar peticiones `POST` a la API de R, enviando datos y recibiendo resultados en formato JSON.
    * **Visualización:** Para la Fase 1 se usará **Chart.js**, por su facilidad de uso, para renderizar gráficos comunes, recibiendo los datos y las configuraciones desde el backend de R.

* **Backend (R + Plumber):**
    * **API REST:** El framework **Plumber** se usará para construir la API. Plumber convierte funciones de R en *endpoints* REST, facilitando la comunicación con el frontend.
    * **Procesamiento de Datos:** Se utilizarán librerías de R como `dplyr` y `data.table` para la manipulación eficiente de datasets. Los modelos estadísticos se implementarán con las librerías nativas de R (`stats`) o paquetes especializados.
    * **Formato de Comunicación:** Todas las peticiones y respuestas se manejarán en formato **JSON**. El backend recibirá JSON, lo convertirá en un `data.frame` de R, realizará el análisis y devolverá los resultados como un objeto JSON.

---

### Fase 1: El Core de la Plataforma

#### 3. Requisitos Funcionales Detallados

* **Módulo de Carga de Datos:**
    * **RF-CD-1 (Carga de Archivos):**
        * **Frontend:** Componente de interfaz de usuario con una zona de arrastrar y soltar (`Drag and Drop`) y un botón para seleccionar archivos.
        * **Backend:** Endpoint `/upload` que reciba el archivo, lo guarde temporalmente y devuelva una previsualización de las primeras 10 filas.
    * **RF-CD-2 (Soporte de Formatos):**
        * **Frontend:** El componente de carga debe validar las extensiones de los archivos (`.csv`, `.txt`, `.xls`, `.xlsx`).
        * **Backend:** Funciones en R que utilicen librerías como `readr`, `readxl` o `data.table::fread` para leer los diferentes formatos. Se debe implementar lógica de detección de separadores para archivos de texto.
    * **RF-CD-3 (Conexión a Bases de Datos):**
        * **Frontend:** Un formulario con campos de texto para los parámetros de conexión (tipo de BBDD, host, puerto, usuario, contraseña, `query`).
        * **Backend:** Un endpoint `/db_connect` que reciba los parámetros y use paquetes de R como `DBI` o `RPostgreSQL` para establecer la conexión, ejecutar la consulta y devolver el `top 10` de los datos.

* **Módulo de Previsualización y Validación:**
    * **RF-PV-1 (Previsualización):**
        * **Frontend:** Un componente que muestre una tabla de React con las 10 primeras filas del dataset y los tipos de datos inferidos por R.
    * **RF-PV-2 (Validación):**
        * **Frontend:** Botones "Aceptar" y "Rechazar". "Aceptar" activa el siguiente paso y guarda el dataset en el estado global. "Rechazar" limpia el estado y regresa al módulo de carga.

* **Módulo de Resumen Estadístico Básico:**
    * **RF-RE-1 (Resumen `summary`):**
        * **Backend:** Un endpoint `/summary` que reciba el dataset, calcule métricas para variables numéricas (conteo, media, mediana, desviación estándar, cuartiles, min, max) y categóricas (conteo, moda).
        * **Frontend:** Un componente que renderice los resultados de R en una tabla con el estilo deseado, similar a la librería `STARGAZER`.

* **Módulo de Visualización Básica:**
    * **RF-VB-1 (Gráficos por Defecto):**
        * **Backend:** Un endpoint `/plot/default` que analice el tipo de datos de cada variable y devuelva una estructura de JSON compatible con **Chart.js** para generar un gráfico predeterminado (e.g., histograma para numéricas, gráfico de barras para categóricas).
    * **RF-VB-2 (Interactividad):**
        * **Frontend:** El componente de visualización debe inicializar los gráficos de **Chart.js** y configurarlos para permitir tooltips y zoom básico.

#### 4. Plan de Trabajo Detallado (Fase 1)

1.  **Configuración (Días 1-2):**
    * Configurar el proyecto de React y el servidor de R con Plumber.
    * Definir la estructura de carpetas y el sistema de gestión de paquetes (`npm` para React, `renv` para R).
    * Crear el gestor de estado en React para almacenar el dataset actual.
2.  **Módulo de Carga y Previsualización (Días 3-7):**
    * (Tarea React) Desarrollo del componente de carga con `Drag and Drop`.
    * (Tarea R) Creación del endpoint `/upload` con las funciones de *parsing* para todos los formatos de archivo.
    * (Tarea R) Implementación del endpoint `/db_connect`.
    * (Tarea React) Desarrollo del componente de previsualización y de los botones "Aceptar"/"Rechazar".
3.  **Módulo de Resumen Estadístico (Días 8-12):**
    * (Tarea R) Creación de la función `summary_stats()` y del endpoint `/summary`.
    * (Tarea React) Desarrollo de la interfaz de la tabla de resumen y manejo del `JSON` de R.
4.  **Módulo de Visualización Básica (Días 13-18):**
    * (Tarea R) Creación de las funciones de visualización que generen objetos de datos compatibles con **Chart.js** y del endpoint `/plot/default`.
    * (Tarea React) Desarrollo del componente de visualización de gráficos y su integración con **Chart.js**.

---

### Fase 2: Análisis Avanzado y Persistencia de Datos

#### 5. Requisitos Funcionales Detallados

* **RF-A-1 (Manipulación de Datos):**
    * **Frontend:** Componentes con controles de interfaz para filtrar filas, seleccionar/eliminar columnas y renombrar.
    * **Backend:** Endpoints dedicados (`/filter`, `/select_cols`, `/rename`) que reciban los parámetros del frontend y utilicen las funciones de `dplyr` en R para devolver el dataset modificado.
* **RF-A-2 (Tablas Dinámicas):**
    * **Frontend:** Un componente de "Tabla Dinámica" con áreas para arrastrar variables (`filas`, `columnas`, `valores`).
    * **Backend:** Un endpoint `/pivot_table` que reciba las variables seleccionadas y use la función `tidyr::pivot_wider()` en R para generar la tabla dinámica.
* **RF-A-3 (Persistencia de Sesiones):**
    * **Frontend:** Botones "Guardar" y "Cargar" en la interfaz. El botón "Guardar" envía el estado completo del análisis (dataset, filtros, gráficos) al backend.
    * **Backend:** Un endpoint `/save_session` que reciba el estado en JSON y lo guarde en el servidor en un archivo `.RData` o `.json`. Un endpoint `/load_session` que devuelva un archivo de sesión guardado.
* **RF-A-4 (Exportación):**
    * **Frontend:** Botones para descargar la tabla de resumen o los gráficos.
    * **Backend:** Endpoints (`/export/csv`, `/export/xlsx`, `/export/png`) que utilicen librerías de R (`readr`, `openxlsx`, `ggplot2`) para generar los archivos y los envíen al frontend como una respuesta binaria.

#### 6. Plan de Trabajo Detallado (Fase 2)

1.  **Refactorización y Persistencia (Días 1-4):**
    * (Tarea React) Actualizar el gestor de estado para manejar la lógica de guardado y carga de sesiones.
    * (Tarea R) Creación de los endpoints `/save_session` y `/load_session` para gestionar los archivos de sesión en el servidor.
2.  **Manipulación y Tablas Dinámicas (Días 5-13):**
    * (Tarea React) Desarrollo de los componentes de interfaz para la manipulación de datos.
    * (Tarea R) Implementación de los endpoints `/filter`, `/select_cols` y `/rename`.
    * (Tarea React) Creación del componente de la tabla dinámica.
    * (Tarea R) Implementación del endpoint `/pivot_table` con `tidyr`.
3.  **Implementación de Exportación (Días 14-16):**
    * (Tarea React) Creación de los botones de descarga.
    * (Tarea R) Creación de los endpoints de exportación para diferentes formatos.

---

### Fase 3: Modelado de Machine Learning

#### 7. Requisitos Funcionales Detallados

* **RF-ML-1 (Modelos de ML):**
    * **Frontend:** Una interfaz que permita al usuario seleccionar un modelo (Regresión Lineal, K-Means, PCA) y sus parámetros.
    * **Backend:** Un endpoint `/ml_model` que reciba el nombre del modelo y los parámetros, y utilice librerías de R como `stats` o `caret` para ejecutar el análisis y devolver los resultados.
* **RF-ML-2 (Visualización de Resultados de ML):**
    * **Frontend:** El componente de visualización debe ser capaz de renderizar los resultados de los modelos de ML, por ejemplo, un diagrama de dispersión con una línea de regresión.
* **RF-ML-3 (Visualización de Resultados de ML):**
    * **Backend:** Las funciones de R para los modelos de ML deben devolver, junto con los resultados numéricos, la información necesaria para que Chart.js o una biblioteca más avanzada como Plotly.js dibuje los gráficos correspondientes. Por ejemplo, los coeficientes de la línea de regresión o las coordenadas de los centroides de K-Means.

#### 8. Plan de Trabajo Detallado (Fase 3)

1.  **Desarrollo del Backend de R (Días 1-10):**
    * Integrar librerías de ML como `stats` (para Regresión), `cluster` (para K-Means) y `factoextra` (para visualización de resultados de PCA).
    * Construir el endpoint `/ml_model` que gestione la lógica para cada tipo de modelo.
2.  **Desarrollo del Frontend de Modelado (Días 11-18):**
    * Crear un componente de React con la interfaz para seleccionar los modelos de ML y sus variables.
    * Desarrollar la lógica de comunicación con el nuevo endpoint de la API.
3.  **Visualización de Resultados (Días 19-25):**
    * Actualizar el componente de visualización en React para que pueda interpretar los resultados de los modelos de ML y generar gráficos de dispersión, gráficos de clústeres, etc.
4.  **Pruebas de Integración y Rendimiento (Días 26-30):**
    * Realizar pruebas de extremo a extremo para asegurar la correcta comunicación entre los componentes de React y la API de R.