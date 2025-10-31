---
title: Practica Dos
#tags: 
#  - practicos
#  - genomica
show:
  - toc 
---

![Image](imagenes/featured.png){ width="750", align=center }

# **TP 2**. Del control de calidad a la detección de variantes genómicas { markdown data-toc-label = 'TP 02' }

[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-download: Materiales</span>](https://drive.google.com/file/d/1b74X8uGOYGTHt_OaJZbn9N385MjwWswV/view?usp=sharing){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-file-powerpoint: Slides</span>](https://docs.google.com/presentation/d/1Vb3GfjxVjIiaMuHPtCnXc1vxpQ3hG7AaOPPnJNm9Ew0/edit?usp=sharing){ .md-button }
<!--
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-youtube: Clase grabada</span>](https://drive.google.com/){ .md-button }
-->

## Objetivos

* Obtener lecturas de secuenciación de nueva generación (NGS) disponibles en bases de datos públicas
* Interpretar los formatos utilizados comúnmente en NGS
* Realizar controles de calidad de las lecturas e interpretar los resultados
* Mapear secuencias al genoma de referencia
* Visualizar e interpretar alteraciones genéticas

## Introducción

### _Drosophila melanogaster_

_D. melanogaster_ es un organismo modelo ampliamente utilizado en genética y biología del desarrollo. Su ciclo de vida corto, facilidad de cultivo en laboratorio y su genoma relativamente pequeño (aproximadamente 175 millones de pares de bases) lo convierten en un sistema ideal para estudiar procesos biológicos fundamentales.

El genoma de _D. melanogaster_ consiste en cuatro cromosomas principales (X, 2, 3 y 4) y un cromosoma sexual (Y). Fue uno de los primeros genomas de eucariotas en ser secuenciado completamente, habiendo sido publicado en el año 2000. Desde entonces, ha sido objeto de numerosos estudios genómicos que han proporcionado información valiosa sobre la genética, la evolución y la biología del desarrollo.

En este trabajo práctico, vamos a mapear lecturas de secuenciación de _D. melanogaster_ a su genoma de referencia en busca de variantes genéticas. Estas lecturas están disponibles en bases de datos públicas, y forman parte de un estudio titulado **"_Drosophila melanogaster_ strain: Canton S Genome sequencing"**. Pueden ver información sobre como se generaron estas lecturas en el diseño del experimento publicado en [**NCBI**](https://www.ncbi.nlm.nih.gov/sra/SRX28785043[accn]). 

**PONER ACÁ ALGO INTERESANTE DE LOS GENES QUE DESPUÉS VAMOS A MIRAR**

### Flujo de trabajo de secuenciación y mapeo 

![Image](imagenes/workflow-ilumina.png){ width="750", align=center }

La plataforma de secuenciación que se utilizó para esta secuenciación es DNBSEQ-T7, una plataforma de secuenciación por síntesis que genera lecturas cortas de alta calidad. Un pipeline típico de análisis de datos de secuenciación incluye los siguientes pasos:

**1. Obtención del ADN y preparación de la biblioteca:** Estos pasos son de _wet lab_, y consisten en la extracción del ADN genómico de las muestras biológicas, seguido de la fragmentación del ADN y la adición de adaptadores específicos para la plataforma de secuenciación.

**2. Amplificación:** El ADN fragmentado se amplifica mediante PCR para aumentar la cantidad de material disponible para la secuenciación.

**3. Secuenciación:** La biblioteca amplificada se carga en la plataforma de secuenciación, donde se generan las lecturas de secuenciación. Se obtienen archivos en formato **FASTQ** que contienen las secuencias de las lecturas junto con sus calidades.

**4. Alineamiento y análisis de datos:** Las lecturas generadas se alinean al genoma de referencia utilizando herramientas bioinformáticas. Posteriormente, se realizan análisis para identificar variantes genéticas, evaluar la calidad de las lecturas y otras características relevantes.

## Bases de datos genómicas

Existen numerosas bases de datos que albergan información genómica y genética de _D. melanogaster_. Algunas de las más relevantes son:

* **[FlyBase](https://flybase.org/)**: Es la base de datos principal para la genética y biología de _D. melanogaster_. Proporciona información sobre genes, mutaciones, fenotipos, secuencias genómicas y mucho más.

* **[Ensembl Metazoa](https://metazoa.ensembl.org/Drosophila_melanogaster/Info/Index)**: Ofrece acceso a datos genómicos de múltiples especies, incluyendo _D. melanogaster_. Permite la visualización y análisis de secuencias genómicas, anotaciones de genes y variantes. 

* **[NCBI](https://www.ncbi.nlm.nih.gov/)**: La base de datos del National Center for Biotechnology Information (NCBI) también alberga secuencias genómicas y datos relacionados con _D. melanogaster_. 


### Ejercicio 1: Exploración de bases de datos 

Dentro de la carpeta **Materiales** van a encontrar los archivos necesarios para realizar el práctico. El genoma de referencia y sus anotaciones fueron obtenidas de **FlyBase**. Explorando la web de Flybase (pestaña **Downloads**), respondan:

Aclarar mejor estos puntos

1. ¿Cuál es la versión del genoma de referencia de _D. melanogaster_ que vamos a utilizar? ¿Cuándo fue actualizada por última vez?

!!! tip "Pista"
    La versión del genoma de referencia se indica en el nombre del archivo .fasta que está en la carpeta. Para ver todas las versiones disponibles, hagan click [acá](https://flybase.org/genomes/).

2. ¿Qué diferencias hay entre los distintos archivos .fasta? ¿Cuál vamos a utilizar en el TP y por qué?

3. ¿Qué información contiene el archivo .gff? ¿Para qué lo vamos a utilizar en el TP? ¿Cuántos tipos de .gff hay disponibles para esta versión?

Agregar pregunta de info extra

!!! note "Info extra"  
    Tienen dos archivos, uno llamado all-no-analysis (descargado de FlyBase) y y otro dmel_5.57 (procesado). Para saber cual es la diferencia entre ambos, prueben inspeccionando el inicio y el final de cada archivo con los siguientes comandos:

    ```bash
    # Recuerden estar en la carpeta donde están los archivos .gff3

    head -n 5 dmel-all-no-analysis-r5.57.gff
    head -n 5 dmel_5.57.gff

    tail -n 5 dmel-all-no-analysis-r5.57.gff
    tail -n 5 dmel_5.57.gff
    ```


### Ejercicio 2: Inspección de archivos FASTQ

En la carpeta **Materiales** van a encontrar dos archivos FASTQ que comienzan con _subset_ y terminan con _.fastq_. Estos archivos contienen una selección de las lecturas de secuenciación que vamos a analizar. Si quisieran obtener las lecturas completas, pueden descargarlas desde el [SRA](https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&acc=SRR33554827&display=download). 

1. ¿Qué significan los sufijos _1_ y _2_ de los archivos .fastq?
   
2. ¿Cuántas líneas tiene cada lectura en un archivo FASTQ? ¿Y cuántas lecturas hay en cada archivo?
  ```bash
    # Recuerden estar en la carpeta donde están los archivos .fastq

    # El * representa comodín, en este caso es cualquier nombre que termine en 1.fastq

    head *1.fastq

    wc -l *1.fastq
  ```
![Image](imagenes/fastq_file.png){ width="600", align=center }

1. Comparen las primeras dos lecturas de ambos archivos. ¿Qué diferencias y similitudes encuentran?

## Métricas de calidad

La calidad de las lecturas de secuenciación es un aspecto crucial en los análisis genómicos. Las métricas de calidad nos permiten evaluar la confiabilidad de las secuencias obtenidas y tomar decisiones informadas sobre su uso en análisis posteriores. Un programa muy usado para evaluar la calidad de las lecturas es **FastQC**. Este software genera un informe detallado que incluye varias métricas clave, tales como:


### Ejercicio 3: Control de calidad de las lecturas

Para evaluar la calidad de las lecturas que tenemos en la carpeta *Materiales*, vamos a utilizar **FastQC**. Corran en la terminal:

Ver el manual de fastqc antes de correr

```bash

# Recuerden estar en la carpeta donde están los archivos .fastq

# -o indica la carpeta donde se van a guardar los resultados, si no existe, creenla utilizando mkdir
# El * es un comodín que indica que se van a analizar todos los archivos que terminen en .fastq

fastqc -o ../Outputs/resultados_fastqc *fastq

```
1. ¿Cuántos archivos se generaron en la carpeta de resultados? ¿Qué tipo de archivos son?

Los archivos .html contienen el informe de FastQC, mientras que los archivos .zip contienen los datos en bruto utilizados para generar el informe. Abran ambos archivos .html y exploren las diferentes secciones del informe. Algunas métricas clave a observar son:

  1. **Per base sequence quality**: Este histograma muestra la distribución de las calidades de las bases a lo largo de las lecturas. La calidad se mide en una escala logarítmica llamada puntaje Phred, donde valores más altos indican mayor confianza en la base llamada. Idealmente, queremos que la mayoría de las bases tengan un puntaje Phred alto (por ejemplo, >30).

  2. **Per sequence quality scores**: Este gráfico muestra la distribución de las calidades promedio de las lecturas. Nos permite identificar si hay un subconjunto de lecturas con baja calidad que podría afectar los análisis posteriores.

  3. **Per base sequence content**: Este gráfico muestra la proporción de cada base (A, T, C, G) en cada posición a lo largo de las lecturas. En una secuencia aleatoria, esperaríamos ver una distribución aproximadamente uniforme de las bases. Desviaciones significativas pueden indicar sesgos en la secuenciación o problemas técnicos.

  4. **Adapter Content**: Este gráfico muestra la presencia de secuencias de adaptadores en las lecturas. La presencia de adaptadores puede interferir con el mapeo y otros análisis, por lo que es importante identificarlos y eliminarlos si es necesario.

Si bien nosotros estamos trabajando con solo dos archivos de lecturas, es usual que cuando trabajamos con ómicas tengamos muchos mas para analizar. Por lo tanto, para facilitar la interpretación de los resultados de FastQC cuando se tienen múltiples archivos, es común utilizar una herramienta llamada **MultiQC**. Esta herramienta agrega los resultados de múltiples análisis de calidad en un solo informe consolidado.

Para obtener el informe de MultiQC corran en la terminal:

```bash
# multiqc necesita que le indiquemos la carpeta donde estan los reportes de fastqc y la carpeta donde va a guardar el reporte final

multiqc resultados_fastqc/ -o resultados_multiqc

```

2. Viendo los reportes de calidad, ¿Qué opinan de los datos? ¿Los usarían para análisis posteriores?. Pueden comparar con este ejemplo de [Reporte de FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/bad_sequence_fastqc.html). ¿Hay alguna métrica que no hayamos visto en el reporte de nuestras lecturas?

## Alineamiento de lecturas al genoma de referencia

Ahora que tenemos nuestras lecturas de secuenciación y hemos evaluado su calidad, el siguiente paso es alinearlas al genoma de referencia de _D. melanogaster_. Durante el alineamiento, lo que hacemos es encontrar la posición en el genoma donde cada lectura se corresponde mejor, teniendo en cuenta posibles errores de secuenciación y variaciones genéticas.

Existen varias herramientas para realizar el alineamiento de lecturas, entre las más populares se encuentran **BWA** y **Bowtie2**. La elección del alineador depende del tipo y largo de las lecturas, del hardware con el que contemos y del balance entre velocidad/exactitud que sea útil para nuestro análisis.En este práctico, vamos a utilizar **BWA-MEM** (un algoritmo de alineamiento de BWA) debido a su eficiencia y precisión en el alineamiento de lecturas cortas.

### Ejercicio 4: Indexado del genoma de referencia

Antes de alinear las lecturas, necesitamos preparar el genoma de referencia para que pueda ser utilizado por el alineador. Este proceso se llama indexado y crea una estructura de datos que permite búsquedas rápidas durante el alineamiento. Para indexar el genoma de referencia, corran en la terminal:

```bash

# Recuerden estar en la carpeta donde está el archivo .fasta del genoma de referencia

bwa index dmel-all-chromosome-r5.57.fasta
```
1. ¿Cuántos archivos nuevos tienen ahora en la carpeta Materiales?

  Los archivos generados son índices que permiten a BWA realizar búsquedas rápidas en el genoma de referencia durante el proceso de alineamiento. Cada archivo tiene un propósito específico:

  * **.amb**: Contiene información sobre las posiciones ambiguas en el genoma.
  * **.ann**: Contiene anotaciones del genoma, como la longitud de cada
  * **.bwt**: Es el índice Burrows-Wheeler Transform, que permite búsquedas rápidas de patrones en el genoma.
  * **.pac**: Contiene la secuencia del genoma en un formato comprimido
  * **.sa**: Es el índice de sufijos, que también facilita la búsqueda de patrones en el genoma.

### Ejercicio 5: Alineamiento de las lecturas

Ahora que tenemos el genoma de referencia indexado, podemos proceder a alinear nuestras lecturas de secuenciación. Utilizaremos el comando `bwa mem` para este propósito. Corran en la terminal:

```bash
# Recuerden estar en la carpeta donde están los archivos .fastq y el archivo .fasta del genoma de referencia

# Tengan paciencia, tarda unos 10 minutos en correr

bwa mem dmel-all-chromosome-r5.57.fasta *1.fastq *2.fastq > ../Outputs/subset_SRR33554828_bwa.sam
```

2. ¿Qué tipo de archivo se generó en la carpeta Outputs? ¿Qué información contiene este archivo? Pueden inspeccionarlo con `head` y `tail`.

El archivo SAM puede ser bastante grande y no es eficiente para su almacenamiento y procesamiento. Por lo tanto, es común convertirlo a un formato binario más compacto llamado BAM (Binary Alignment/Map). Además, los archivos BAM suelen ordenarse por la posición en el genoma para facilitar el acceso y análisis de las lecturas alineadas.

Vamos a realizar la conversión y ordenamiento del archivo SAM a BAM utilizando la herramienta **samtools**. Corran en la terminal:

```bash
# Recuerden estar en la carpeta Outputs donde está el archivo .sam

samtools view -Sb subset_SRR33554828_bwa.sam | samtools sort -o subset_SRR33554828_bwa_sorted.bam

```
3. ¿Qué diferencias hay entre los archivos .sam y .bam (prueben explorar con `head` el archivo .bam)? ¿Cuál es el factor de compresión?