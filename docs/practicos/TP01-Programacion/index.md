---
title: Practica Uno
show:
  - toc
---

![Image](imagenes/featured.png){ width="750", align=center }

# **TP 1**. TP introducción a programación { markdown data-toc-label = 'TP 01' }

<!--
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-download: Materiales</span>](https://drive.google.com/file/d/1b74X8uGOYGTHt_OaJZbn9N385MjwWswV/view?usp=sharing){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-file-powerpoint: Slides</span>](https://docs.google.com/presentation/d/1Vb3GfjxVjIiaMuHPtCnXc1vxpQ3hG7AaOPPnJNm9Ew0/edit?usp=sharing){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-youtube: Clase grabada</span>](https://drive.google.com/){ .md-button }
-->

## Objetivos

1) Tener una primera aproximación a la programación y pensamiento computacional.  
2) Familiarizarse con la terminal y Bash para automatizar tareas básicas.  
3) Manipular y analizar data frames en R, incluyendo gráficos y normalización.

## Uso de programación en genómica y transcriptómica

En genómica y transcriptómica, la cantidad de datos generados por tecnologías como la secuenciación masiva (NGS) es enorme. Analizar estos datos requiere herramientas que permitan automatizar tareas, reproducir análisis y manejar grandes volúmenes de información de manera eficiente.  

La programación se convierte así en una herramienta esencial: permite desde tareas simples —como contar lecturas o filtrar archivos— hasta análisis complejos de expresión génica, variantes genómicas o ensamblajes.  

Además, usar lenguajes como **Bash** o **R** fomenta la **reproducibilidad científica**, ya que los análisis pueden documentarse, compartirse y ejecutarse nuevamente con los mismos resultados.

## Parte 1: Introducción a Linux / Ubuntu

Linux es un sistema operativo de código abierto, estable y muy utilizado en entornos de investigación y servidores.  
Ubuntu es una de sus distribuciones más populares, conocida por su facilidad de uso y amplia comunidad de soporte.  

En bioinformática y análisis de datos, Linux ofrece ventajas importantes: permite manejar grandes volúmenes de información, automatizar tareas mediante scripts, instalar software científico fácilmente y ejecutar pipelines de análisis de manera reproducible.  

A través de la terminal, los usuarios pueden interactuar directamente con el sistema operativo, ejecutar programas, gestionar archivos y carpetas, y combinar herramientas de manera flexible.


## Introducción a Bash

Bash (Bourne Again SHell) es un intérprete de comandos que permite interactuar con el sistema operativo mediante texto.  

Usando Bash, los usuarios pueden navegar entre carpetas, gestionar archivos, ejecutar programas, combinar comandos y automatizar tareas repetitivas mediante scripts.  

En bioinformática, Bash es fundamental para procesar grandes volúmenes de datos, lanzar pipelines de análisis y manipular archivos de secuenciación de manera eficiente sin necesidad de interfaces gráficas.

### Conceptos básicos al escribir comandos

- Los comandos se escriben en **minúsculas** y respetando espacios entre palabras.  
- Un comando suele tener la siguiente estructura:  

```
comando [opciones] [argumentos]
```

  - **comando:** lo que queremos ejecutar (por ejemplo `ls`, `cd`, `wc`).  
  - **opciones:** modifican el comportamiento del comando (por ejemplo `-l` para listar en detalle).  
  - **argumentos:** archivos o carpetas sobre los que actúa el comando.

- La terminal es **sensible a mayúsculas y minúsculas**, por lo que `File.txt` y `file.txt` son archivos distintos.  
- Se pueden usar **tabulaciones** para autocompletar nombres de archivos o carpetas, evitando errores de tipeo.  
- Los **comentarios** se escriben con `#` y la terminal los ignora, útil para documentar scripts.

### Comandos básicos de Bash

A continuación hay una lista de los comandos más usados en Bash, vamos a ir viendo los más importantes para este curso en la siguiente sección

| Comando | Qué hace | Ejemplo |
|---------|----------|---------|
| `pwd`   | Muestra la ruta del directorio actual | `pwd` → `/home/usuario/proyecto` |
| `ls`    | Lista los archivos y carpetas del directorio | `ls` → `archivo1.txt archivo2.txt` |
| `cd`    | Cambia de directorio | `cd datos/` → cambia al subdirectorio `datos` |
| `mkdir` | Crea un nuevo directorio | `mkdir resultados` → crea carpeta `resultados` |
| `rm`    | Elimina archivos | `rm archivo.txt` → borra `archivo.txt` |
| `cp`    | Copia archivos | `cp archivo.txt backup/` → copia archivo a `backup/` |
| `mv`    | Mueve o renombra archivos | `mv archivo.txt archivo_old.txt` → renombra archivo |
| `head`  | Muestra las primeras líneas de un archivo | `head sample.fastq` → primeras 10 líneas |
| `tail`  | Muestra las últimas líneas de un archivo | `tail sample.fastq` → últimas 10 líneas |
| `wc`    | Cuenta líneas, palabras o caracteres | `wc -l sample.fastq` → número de líneas |
| `cat`   | Muestra el contenido de un archivo | `cat archivo.txt` → imprime el archivo en pantalla |
| `less`  | Permite ver un archivo página por página | `less archivo.txt` → navegación interactiva |
| `grep`  | Busca texto dentro de un archivo | `grep "ATG" sample.fasta` → muestra líneas con "ATG" |
| `chmod` | Cambia permisos de archivo o carpeta | `chmod 755 script.sh` → permisos de ejecución |
| `./`    | Ejecuta un script o programa en el directorio actual | `./script.sh` → ejecuta `script.sh` |
| `|`     | Pipe: envía la salida de un comando como entrada de otro | `cat archivo.txt | grep "gene"` → filtra líneas con "gene" |
| `*`     | Comodín que representa cualquier cadena de caracteres | `ls *.fastq` → lista todos los archivos que terminan en `.fastq` |


### Navegación y cambio de directorio en Bash

En la terminal de Linux/Ubuntu, es fundamental saber **dónde estamos ubicados** y cómo movernos entre carpetas.  
Esto permite ejecutar comandos sobre los archivos correctos y organizar proyectos de manera eficiente.

#### Comando para ver la ubicación actual

```bash
pwd
```

- `pwd` (print working directory) muestra la ruta completa del directorio actual.  
- Saber en qué carpeta estamos evita errores al ejecutar comandos que modifican archivos.

#### Comando para cambiar de directorio

```bash
cd nombre_de_carpeta
```

- `cd` (change directory) cambia la ubicación actual a la carpeta indicada.  
- Se puede usar una **ruta relativa** (`cd subcarpeta`) o **ruta absoluta** (`cd /home/usuario/proyecto`).  
- Para subir un nivel en la jerarquía de carpetas se usa: `cd ..`  
- Para ir al directorio personal del usuario se usa: `cd ~`  

**Ejemplos prácticos**

```bash
cd datos/                 # va al subdirectorio "datos"
cd ../                     # sube un nivel en la jerarquía de carpetas
cd /home/usuario/proyecto  # va directamente a la ruta absoluta
```

**Buenas prácticas**

1. Siempre verificar la ubicación actual con `pwd` antes de cambiar de carpeta.  
2. Usar **tabulaciones** para autocompletar nombres de carpetas y evitar errores de tipeo.  
3. Documentar scripts indicando rutas relativas o absolutas para que sean reproducibles en otros equipos.

### Comandos `head` y `tail`

Los comandos `head` y `tail` permiten **ver partes de un archivo de texto** sin abrirlo completo, lo cual es útil para inspeccionar archivos grandes, como FASTQ o CSV.

#### `head`

- Muestra las primeras líneas de un archivo.  
- Por defecto, muestra las **primeras 10 líneas**, pero se puede cambiar con la opción `-n`.

```bash
# Ver las primeras 10 líneas de sample.fastq
head sample.fastq

# Ver las primeras 20 líneas
head -n 20 sample.fastq
```

#### `tail`

- Muestra las últimas líneas de un archivo.  
- También por defecto son 10 líneas, modificables con `-n`.

```bash
# Ver las últimas 10 líneas de sample.fastq
tail sample.fastq

# Ver las últimas 15 líneas
tail -n 15 sample.fastq
```

**Buenas prácticas**

1. Usar `head` para revisar rápidamente la estructura de archivos grandes.  
2. Usar `tail` para monitorear archivos que se actualizan continuamente (como logs).  

### Comando `wc -l` y uso de comodines `*`

En bioinformática es común tener **muchos archivos** de secuenciación (FASTQ, BAM, etc.).  
El comodín `*` permite seleccionar varios archivos a la vez, y `wc -l` sirve para contar líneas, útil para saber cuántas lecturas hay en un FASTQ (cada lectura ocupa 4 líneas).

#### Contar líneas de un archivo

```bash
# Contar líneas de un archivo FASTQ
wc -l sample1.fastq
```

- `wc` (word count) cuenta líneas, palabras y caracteres.  
- La opción `-l` muestra **solo las líneas**.  
- Cada lectura en un FASTQ tiene 4 líneas → número de lecturas = líneas ÷ 4.

#### Contar líneas en varios archivos con comodín `*`

```bash
# Contar líneas de todos los archivos que terminan en "1.fastq"
wc -l *1.fastq
```

- `*1.fastq` selecciona **todos los archivos cuyo nombre termina en "1.fastq"**.  
- Ejemplo de salida:
```text
16000 sample1_1.fastq
20000 sample2_1.fastq
36000 total
```
- Para calcular lecturas por archivo, dividir cada número por 4.

#### Ejemplo práctico para estudiantes

1. Ejecutar `wc -l *1.fastq` en un directorio con varios FASTQ.  
2. Crear una tabla `.tsv` con dos columnas: `archivo` y `n_lecturas`.  
3. Identificar el archivo con más lecturas y discutir posibles razones (mayor profundidad de secuenciación, etc.).

**Buenas prácticas**

- Revisar que los archivos seleccionados por el comodín sean los correctos con `ls *1.fastq` antes de ejecutar `wc -l`.  

### Uso de pipes (`|`) en Bash (con comandos básicos)

En Bash, el **pipe (`|`)** permite **conectar la salida de un comando con la entrada de otro**, lo que facilita procesar datos de manera eficiente sin generar archivos intermedios.

#### Concepto

- Cada comando en la terminal produce una **salida estándar** (stdout).  
- La pipe `|` toma esa salida y la pasa como **entrada estándar** (stdin) al siguiente comando.  
- Esto permite **encadenar comandos**, filtrando, contando o transformando información en un solo paso.

**Ejemplos usando comandos básicos**

```bash
# Contar cuántos archivos FASTQ hay en el directorio
ls *.fastq | wc -l
```

- `ls *.fastq` lista todos los archivos que terminan en `.fastq`.  
- `| wc -l` cuenta cuántas líneas produjo `ls`, es decir, cuántos archivos hay.

```bash
# Ver las primeras 10 líneas de sample1.fastq y luego contar cuántas líneas hay (debería ser 10)
head sample1.fastq | wc -l
```

```bash
# Ver las últimas 15 líneas de sample2.fastq y luego contar cuántas líneas hay (debería ser 15)
tail -n 15 sample2.fastq | wc -l
```

**Buenas prácticas**

1. Probar cada comando por separado antes de encadenarlos con `|`.  
2. Usar pipes para **evitar crear archivos temporales innecesarios**.  
3. Documentar scripts indicando claramente qué hace cada paso del pipe.


### Navegación de manuales

FastQC es una herramienta para **control de calidad de archivos FASTQ**.  
Antes de usar cualquier comando, es recomendable consultar su manual para entender todas las opciones disponibles.

#### Abrir el manual de FastQC

```bash
man fastqc
```

- `man` (manual) muestra la documentación de cualquier comando instalado en el sistema.  
- Permite leer todas las opciones, parámetros y ejemplos de uso del programa.

#### Navegación básica dentro de un manual (`man`)

- **Flechas arriba/abajo**: moverse línea por línea.  
- **Barra espaciadora**: avanzar una página completa.  
- **b**: retroceder una página.  
- **q**: salir del manual.  
- **/palabra**: buscar una palabra dentro del manual.  
- **n**: ir al siguiente resultado de la búsqueda.  

**Ejemplo práctico**

```bash
# Buscar rápidamente todas las opciones de salida de FastQC
man fastqc
# Dentro del manual, presionar / y escribir "output" y luego Enter
# Presionar n para navegar entre los resultados de la búsqueda
```

**Buenas prácticas**

1. Consultar siempre el manual de un comando antes de usarlo para evitar errores.  
2. Combinar la lectura del manual con ejemplos prácticos para entender mejor cada opción.  
3. Usar `/palabra` y `n` para buscar rápidamente secciones relevantes en manuales largos.

## Elaboración y ejecución de scripts en Bash

En bioinformática, muchas tareas se repiten o requieren procesar **grandes cantidades de archivos**.  
Los **scripts en Bash** permiten automatizar estas tareas, hacerlas reproducibles y ahorrar tiempo.

### Qué es un script

- Un **script** es un archivo de texto que contiene una serie de comandos de Bash que se ejecutan en secuencia.  
- Permite **automatizar análisis**, por ejemplo: contar lecturas, mover archivos o ejecutar pipelines completos.

### Crear un script

1. Abrir un editor de texto (nano, vim, gedit) y escribir los comandos:

```bash
#!/bin/bash
# Este script cuenta las líneas de todos los archivos *_1.fastq
wc -l *_1.fastq
```

- La primera línea `#!/bin/bash` indica al sistema que use Bash para ejecutar el script.  
- Los comentarios comienzan con `#` y son ignorados por Bash, útiles para documentar el script.

2. Guardar el archivo, por ejemplo como `contar_fastq.sh`.

### Uso de `echo` en scripts Bash

El comando `echo` permite **imprimir texto o variables en la terminal**.  
Es muy útil para:

- Mostrar mensajes explicativos dentro de un script.
- Mostrar resultados intermedios de un análisis.
- Depurar scripts para entender qué comandos se están ejecutando.

**Ejemplos**

```bash
# Mostrar un mensaje simple
echo "Iniciando el conteo de lecturas"

# Mostrar el contenido de una variable
FASTA_FILE="/ruta/a/tu/archivo.fasta"
echo "El archivo que se analizará es: $FASTA_FILE"
```

### Dar permisos de ejecución

```bash
chmod +x contar_fastq.sh
```

- `chmod +x` permite que el script se pueda ejecutar como un programa.  

### Ejecutar un script

```bash
./contar_fastq.sh
```

- `./` indica que el script se encuentra en el directorio actual.  

**Buenas prácticas**

1. Documentar cada comando dentro del script usando comentarios (`#`).  
2. Probar el script con **archivos de ejemplo** antes de usarlo con datos importantes.  
3. Usar nombres de archivos y rutas claras y consistentes.  
4. Mantener scripts reproducibles y organizados para poder compartirlos con otros.
5. Usar `echo` para documentar pasos importantes dentro de un script.  

## Ejercicio integrador - Parte 1

En este ejercicio, combinarás varios conceptos vistos en clase: scripts, rutas, comandos básicos y pipes.  
Resuelve los siguientes ítems paso a paso.

### Ítems
1. Crear con el block de notas un script en Bash llamado `buscar_ATG.sh`.
2. Indicar en el script la ruta de un archivo FASTA que quieras analizar.
3. Mostrar las primeras 10 líneas del archivo dentro del script.
4. Buscar el patrón "ATG" en el archivo desde el script.
5. Contar cuántas veces aparece "ATG" combinando comandos con un pipe.
6. Dar permisos de ejecución al script.
7. Ejecutar el script y verificar los resultados.

---
## Parte 2: Introducción a R

R es un lenguaje de programación y entorno estadístico diseñado para el análisis de datos, visualización y modelado.  

Es ampliamente utilizado en bioinformática, biología computacional y análisis de datos científicos debido a su capacidad para manejar grandes conjuntos de datos, realizar estadísticas avanzadas y generar gráficos de alta calidad.  

En R, los datos se pueden almacenar en estructuras como vectores, matrices, listas y data frames, lo que facilita la manipulación y el análisis de información experimental.  

Además, R cuenta con un ecosistema amplio de paquetes que extienden sus funcionalidades, incluyendo herramientas para gráficos con **ggplot2**, análisis de expresión génica, normalización de datos, PCA, y visualización de resultados complejos como volcano plots.

## Uso de RStudio

RStudio es un entorno de desarrollo integrado (IDE) para R que facilita la escritura de código, la ejecución de scripts, la visualización de datos y la depuración de programas.  

Ofrece una interfaz gráfica organizada en paneles: uno para el script o consola, otro para el entorno y variables, uno para gráficos y otro para archivos, paquetes y ayuda.  

RStudio ayuda a los usuarios a trabajar de manera más eficiente con R, combinando ejecución interactiva, edición de scripts, generación de gráficos y manejo de proyectos completos en un solo lugar.  

Es especialmente útil para bioinformática, donde los análisis requieren reproducibilidad y manejo de múltiples conjuntos de datos de manera organizada.

### Variables y tipos de datos

En R, las variables son **contenedores de datos**.  
Es importante conocer los **tipos de datos** y cómo crearlas, ya que esto determina qué operaciones se pueden realizar sobre ellas.

#### Ver tipo de datos de una variable

- Para conocer el tipo de una variable se usa la función `class()`.

```r
# Crear una variable
x <- 10

# Ver el tipo de la variable
class(x)
```

- Resultado: `"numeric"`

#### Generación de variables

- Se puede asignar valores a variables usando `<-` o `=`.

```r
# Números
edad <- 25
peso = 70.5

# Texto (character)
nombre <- "Mercedes"

# Valores lógicos (booleanos)
activo <- TRUE
```

#### Tipos de variables más comunes en R

| Tipo        | Ejemplo       | Descripción                                     |
|------------|---------------|------------------------------------------------|
| numeric    | 10, 3.14      | Números enteros o decimales                    |
| character  | "Hola"        | Texto o cadenas de caracteres                  |
| logical    | TRUE, FALSE   | Valores booleanos                              |
| factor     | factor(c("A","B")) | Variables categóricas                        |
| integer    | 5L            | Enteros (se especifica con L al final)        |

**Buenas prácticas**

1. Nombrar variables de forma clara y consistente.  
2. Usar `class()` y `str()` para verificar el tipo y la estructura de las variables.  
3. Convertir tipos de variables explícitamente si se necesita (`as.numeric()`, `as.character()`, `as.factor()`).

### Data frames y manejo de columnas

En R, un **data frame** es una estructura que permite almacenar datos en **filas y columnas**, similar a una tabla de Excel.  
Saber abrir y manipular un data frame es fundamental para analizar datos de manera eficiente.

#### Abrir un data frame

- Se puede crear directamente o leer desde un archivo CSV usando `read.csv()`.

```r
# Crear un data frame manualmente
df <- data.frame(
  nombre = c("Ana", "Luis", "María"),
  edad = c(25, 30, 28),
  peso = c(55.0, 70.5, 60.2)
)

# Leer un data frame desde un archivo CSV
df2 <- read.csv("datos.csv")
```

#### Ver las primeras y últimas filas

```r
head(df)  # Primeras 6 filas por defecto
tail(df)  # Últimas 6 filas por defecto
```

#### Acceder a columnas de un data frame

- Usando el símbolo `$`:

```r
# Acceder a la columna "edad"
df$edad
```

- Usando corchetes `[ , ]`:

```r
# Acceder a la columna "peso"
df[ , "peso"]

# Acceder a varias columnas
df[ , c("nombre", "edad")]
```

**Buenas prácticas**

1. Verificar la estructura del data frame con `str(df)` para conocer tipos de columnas.  
2. Usar nombres claros para las columnas y filas.  
3. Evitar modificar directamente columnas importantes sin crear copias de seguridad.

### Comandos más usados en R

| Comando / Función       | Qué hace                                           | Ejemplo                                         |
|-------------------------|--------------------------------------------------|------------------------------------------------|
| `read.csv()`            | Leer un archivo CSV y crear un data frame       | `df <- read.csv("datos.csv")`                 |
| `head()`                | Mostrar las primeras filas de un data frame     | `head(df)`                                    |
| `tail()`                | Mostrar las últimas filas de un data frame      | `tail(df)`                                    |
| `str()`                 | Mostrar la estructura de un objeto              | `str(df)`                                     |
| `class()`               | Ver el tipo de un objeto o variable             | `class(df$edad)`                              |
| `summary()`             | Resumen estadístico de columnas numéricas       | `summary(df$edad)`                             |
| `$`                     | Acceder a una columna de un data frame          | `df$nombre`                                   |
| `[ , ]`                 | Seleccionar filas y columnas                     | `df[ , "edad"]` o `df[1:5, c("edad","peso")]` |
| `as.numeric()`          | Convertir a tipo numérico                        | `as.numeric(df$edad)`                         |
| `as.character()`        | Convertir a tipo texto                            | `as.character(df$nombre)`                     |
| `as.factor()`           | Convertir a factor / categoría                  | `as.factor(df$grupo)`                         |
| `na.omit()`             | Eliminar filas con NA                            | `df_clean <- na.omit(df)`                     |

### Normalización y parseo de datos en R

En análisis de datos, especialmente en genómica y transcriptómica, es común trabajar con **datos de diferentes escalas** o con **archivos crudos** que necesitan ser adaptados para análisis posteriores.

#### Normalización de datos

- La normalización permite **poner los datos en una escala comparable**, evitando que valores muy grandes dominen los análisis.
- Métodos comunes:
  - Escalar entre 0 y 1
  - Transformación logarítmica
  - Z-score (media = 0, desviación estándar = 1)

```r
# Ejemplo: normalización de una columna numérica con min-max
df$edad_norm <- (df$edad - min(df$edad)) / (max(df$edad) - min(df$edad))

# Ejemplo: transformación logarítmica
df$peso_log <- log(df$peso)
```

#### Parseo de datos

- Parsear datos significa **procesar y transformar archivos crudos** en un formato que pueda ser usado para análisis.
- Incluye acciones como:
  - Separar columnas
  - Convertir tipos de datos
  - Eliminar o imputar valores faltantes

```r
# Convertir una columna a factor
df$nombre <- as.factor(df$nombre)

# Eliminar filas con valores NA
df_clean <- na.omit(df)

# Seleccionar solo columnas necesarias
df_subset <- df[ , c("nombre", "edad_norm")]
```

**Buenas prácticas**

1. Verificar siempre los tipos de datos con `str(df)` antes y después del parseo.  
2. Documentar cada paso de transformación para reproducibilidad.  
3. Normalizar solo las columnas que lo requieren según el análisis.

### Librerías y gráficos básicos

En R, las **librerías** son colecciones de funciones que amplían la funcionalidad básica del lenguaje.  
`ggplot2` es una librería muy utilizada para **visualización de datos**, permitiendo crear gráficos claros y personalizables.

#### Cargar una librería

```r
# Instalar ggplot2 si no está instalado
install.packages("ggplot2")

# Cargar la librería
library(ggplot2)
```

#### Crear gráficos básicos

###### 1. Histograma

```r
# Histograma de la columna "edad"
ggplot(df, aes(x = edad)) +
  geom_histogram(binwidth = 5, fill = "lightblue", color = "black") +
  labs(title = "Histograma de edades", x = "Edad", y = "Frecuencia")
```

###### 2. Scatter plot (gráfico de dispersión)

```r
# Relación entre edad y peso
ggplot(df, aes(x = edad, y = peso)) +
  geom_point(color = "red") +
  labs(title = "Edad vs Peso", x = "Edad", y = "Peso")
```

###### 3. Boxplot

```r
# Distribución del peso por nombre
ggplot(df, aes(x = nombre, y = peso)) +
  geom_boxplot(fill = "lightgreen") +
  labs(title = "Boxplot de peso por persona", x = "Nombre", y = "Peso")
```

**Buenas prácticas**

1. Verificar que la columna que se quiere graficar sea del tipo adecuado (numérica o factor).  
2. Usar títulos y etiquetas claras para facilitar la interpretación.  
3. Guardar los gráficos con `ggsave()` si se necesitan en reportes.

### Introducción a PCA y Volcano Plot

En análisis de datos biológicos, especialmente transcriptómicos:  

- **PCA (Principal Component Analysis)** se usa para **reducir la dimensionalidad** y visualizar patrones o agrupaciones entre muestras.  
- **Volcano Plot** se usa para **visualizar resultados de experimentos diferenciales**, mostrando significancia vs. cambio en expresión.

#### PCA en R

```r
# Suponiendo un data frame de expresión genética con filas = genes y columnas = muestras
# Normalizamos los datos previamente
expr_matrix <- as.matrix(df[ , -1])  # eliminar columna de nombres si existe

# Realizar PCA
pca_result <- prcomp(expr_matrix, scale. = TRUE)

# Ver resumen de la varianza explicada
summary(pca_result)

# Graficar los dos primeros componentes principales
plot(pca_result$x[,1], pca_result$x[,2], 
     xlab = "PC1", ylab = "PC2", main = "PCA de muestras", pch = 19)
```

#### Volcano Plot en R

- Se utiliza para visualizar **log2 fold-change vs. -log10(p-value)** de un análisis diferencial.

```r
# Suponiendo un data frame "res" con columnas log2FC y pvalue
res$negLogP <- -log10(res$pvalue)

# Graficar Volcano Plot
ggplot(res, aes(x = log2FC, y = negLogP)) +
  geom_point(alpha = 0.5) +
  geom_vline(xintercept = c(-1, 1), col = "red", linetype = "dashed") +
  geom_hline(yintercept = -log10(0.05), col = "blue", linetype = "dashed") +
  labs(title = "Volcano Plot", x = "log2 Fold Change", y = "-log10(p-value)")
```

**Buenas prácticas**

1. Normalizar los datos antes de PCA para que todas las variables tengan la misma escala.  
2. Revisar los valores atípicos antes de interpretar resultados.  
3. Usar colores y etiquetas en Volcano plots para resaltar genes significativos.

### Ejercicio integrador - Parte 2

En este ejercicio deberás combinar varios conceptos vistos en clase. Resuelve los siguientes pasos:

1) Abrí un data frame a partir de un archivo CSV usando `read.csv()`.  

2) Hacé un parseo de los datos: revisá los tipos de columnas y convertí a `numeric`, `character` o `factor` según corresponda.  
   Eliminá filas o columnas con valores faltantes si es necesario.  

3) Normalizá al menos una columna numérica del data frame utilizando un método de R base (por ejemplo, min-max o log).  

4) Generá un gráfico básico usando funciones de R base (`plot()`, `hist()` o `boxplot()`).  
   Agregá títulos y etiquetas de ejes.  

5) Calculá un PCA sobre las columnas numéricas del data frame usando `prcomp()`.  
   Graficá los dos primeros componentes principales con `plot()`.  


