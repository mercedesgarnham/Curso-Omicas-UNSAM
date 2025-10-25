---
title: Practica Tres
tags: 
  - practicos
  - genomica
---

![Image](imagenes/featured.png){ width="750", align=center }

# **TP 3**. Transcriptómica { markdown data-toc-label = 'TP 03' }

<!--
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-download: Materiales</span>](https://drive.google.com/file/d/1b74X8uGOYGTHt_OaJZbn9N385MjwWswV/view?usp=sharing){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-file-powerpoint: Slides</span>](https://docs.google.com/presentation/d/1Vb3GfjxVjIiaMuHPtCnXc1vxpQ3hG7AaOPPnJNm9Ew0/edit?usp=sharing){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-youtube: Clase grabada</span>](https://drive.google.com/){ .md-button }
-->

En esta clase analizaremos **la calidad de las lecturas crudas y filtradas** de un experimento real de *Drosophila melanogaster*, tomado de un paper reciente que explora diferencias transcriptómicas entre fenotipos de ojo rojo y ojo blanco.

---

## 📄 Paper base

**More than meets the eye: mutation of the *white* gene in *Drosophila* has broad phenotypic and transcriptomic effects**  
*April Rickle, Krittika Sudhakar, Alix Booms, Ellen Stirtz, Adelheid Lempradl*  
📘 *Genetics*, Volume 230, Issue 3, July 2025, iyaf097  
🔗 [DOI: 10.1093/genetics/iyaf097](https://doi.org/10.1093/genetics/iyaf097)

!!! abstract
    En este trabajo, las autoras analizan cómo la mutación del gen **white** en *Drosophila melanogaster* no sólo afecta la pigmentación ocular, sino que también genera amplias modificaciones en la expresión génica.  
    En el presente práctico utilizaremos las bases de datos generadas en este estudio para realizar el **control de calidad** previo al análisis transcriptómico.

---

## Hands-on!

La primera parte de un pipeline adecuado en transcriptómica, comienza evaluando la **calidad de las lecturas RNA-seq** antes y después del trimeado utilizando, en este caso, las herramientas **FastQC**, **Trim Galore** y **MultiQC**.

---

## 🧬 Datos experimentales

Antes de comenzar el control de calidad, es importante **caracterizar las muestras**:  
¿De qué organismo provienen? ¿Qué tipo de secuenciación se usó? ¿Cuál es la longitud de las lecturas?

En esta parte del práctico, el objetivo es que **ustedes mismos obtengan esta información** a partir de los datos crudos y los metadatos del paper.

### 🕵️‍♀️ 1️⃣ Exploración de los archivos crudos

Cada muestra está disponible como un archivo comprimido `.fastq.gz` dentro del directorio del proyecto.

Comencemos inspeccionando la **estructura y el encabezado** de uno de ellos:

```bash
ls Drosophila_RNAseq_PRJNA875952/
zcat Drosophila_RNAseq_PRJNA875952/SRR34068709_1.fastq.gz | head -4

!!! question "Preguntas"
    - ¿Qué información podés obtener del encabezado (@SRR...)?
    - ¿Podés identificar si la lectura es paired-end o single-end?
    - ¿Qué longitud tienen las secuencias?

🧾 2️⃣ Consultar los metadatos del estudio

Los metadatos asociados al experimento pueden obtenerse desde el NCBI SRA o desde el paper original.

Buscá el BioProject o BioSample correspondiente al estudio:

🔗 PRJNA875952 – Drosophila RNA-seq study

!!! tip
    En la pestaña Runs podés ver detalles como:
    - Plataforma de secuenciación (Instrument Model)
    - Layout (Paired / Single)
    - Read length (por ejemplo, 75 bp)
    - Tipo de librería (mRNA, Total RNA, etc.)


??? success "💡 Ver respuesta esperada"
    | Característica | Descripción |
    |-----------------|--------------|
    | **Organismo** | *Drosophila melanogaster* |
    | **Plataforma de secuenciación** | Element Biosciences AVITI |
    | **Tipo de lectura** | Paired-end |
    | **Longitud** | 2 × 75 bp |
    | **Kit de preparación** | KAPA mRNA HyperPrep Kit |
    | **Tipo de librería** | mRNA poliA estándar (adaptadores Illumina) |
    | **Número de muestras** | 10 (5 ojo blanco y 5 ojo rojo) |

**Distribución de muestras:**
    - 🧪 *White-eye*: muestras 28 a 32  
    - 🧬 *Red-eye*: muestras 38 a 42  

!!! note "Archivos de entrada"
        Los archivos `.fastq.gz` ya están disponibles en la carpeta compartida del curso.

---

## 🧩 Parte 1: Análisis de calidad inicial

=== "1️⃣ Ejecutar FastQC"

    #FastQC permite evaluar la calidad de las lecturas crudas (`.fastq.gz`).

    ```bash
    mkdir -p fastqc_results
    fastqc Drosophila_RNAseq_PRJNA875952/*.fastq.gz -o fastqc_results
    ```

!!! tip
        Cada muestra generará un archivo `.html` con los gráficos de calidad y un archivo `.zip` con los datos resumidos.

=== "2️⃣ Explorar resultados"

    #Abrir uno de los reportes HTML (por ejemplo, en un navegador web local):

    ```bash
    xdg-open fastqc_results/SRR34068709_fastqc.html
    ```

    Revisar los siguientes módulos principales:
    - **Per base sequence quality**
    - **Per sequence GC content**
    - **Adapter content**
    - **Sequence length distribution**

!!! question "Preguntas para reflexionar"
        - ¿Qué patrones de calidad observas hacia el final de las lecturas?  
        - ¿Existen adaptadores detectados?  
        - ¿Hay diferencias notorias entre muestras?

---

## ✂️ Parte 2: Trimeado de lecturas

=== "1️⃣ Ejecutar Trim Galore"

    #Trim Galore combina **Cutadapt** y **FastQC** para recortar adaptadores y bases de baja calidad.

    ```bash
    mkdir -p trimmed_reads

    for sample in Drosophila_RNAseq_PRJNA875952/*_1.fastq.gz
    do
        base=$(basename $sample "_1.fastq.gz")
        trim_galore --paired \
            Drosophila_RNAseq_PRJNA875952/${base}_1.fastq.gz \
            Drosophila_RNAseq_PRJNA875952/${base}_2.fastq.gz \
            -o trimmed_reads
    done
    ```

!!! info
        Trim Galore automáticamente genera nuevos archivos `*_val_1.fq.gz` y `*_val_2.fq.gz` con las lecturas filtradas.

=== "2️⃣ Evaluar calidad post-trimming"

    ```bash
    mkdir -p fastqc_trimmed
    fastqc trimmed_reads/*.fq.gz -o fastqc_trimmed
    ```

 Comparar con los resultados previos:

    - ¿Mejoró la calidad promedio por base?
    - ¿Disminuyó el contenido de adaptadores?

---

## 📊 Parte 3: Resumen con **MultiQC**

=== "1️⃣ Generar el reporte"

    #MultiQC consolida todos los reportes de FastQC y Trim Galore en un solo archivo HTML.

    ```bash
    multiqc . -o multiqc_report
    ```

=== "2️⃣ Visualizar resultados"

    #Abrir el reporte:

    ```bash
    xdg-open multiqc_report/multiqc_report.html
    ```

!!! success
        Este reporte permite **comparar todas las muestras** simultáneamente, facilitando la identificación de anomalías o diferencias entre grupos experimentales.

---

## 🧠 Conclusión de este segmento 

- Las herramientas **FastQC**, **Trim Galore** y **MultiQC** constituyen la base del **control de calidad en RNA-seq**.  
- Es fundamental inspeccionar manualmente los reportes y confirmar que no haya sesgos sistemáticos entre muestras.  
- La calidad de las lecturas impacta directamente en los pasos posteriores de **mapeo, cuantificación y análisis diferencial**.

---

## 📚 Recursos adicionales

- 🔗 [FastQC Manual](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
- 🔗 [Trim Galore Documentation](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/)
- 🔗 [MultiQC Guide](https://multiqc.info/)
