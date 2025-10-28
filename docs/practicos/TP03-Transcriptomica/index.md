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

```
 
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

=== 1️⃣ Ejecutar FastQC

    #FastQC permite evaluar la calidad de las lecturas crudas (`.fastq.gz`).

    ```bash
    mkdir -p fastqc_results
    fastqc Drosophila_RNAseq_PRJNA875952/*.fastq.gz -o fastqc_results
    ```

!!! tip
        Cada muestra generará un archivo `.html` con los gráficos de calidad y un archivo `.zip` con los datos resumidos.

=== 2️⃣ Explorar resultados

    #Abrir uno de los reportes HTML (por ejemplo, en un navegador web local):

    ```bash
    xdg-open fastqc_results/SRR34068709_fastqc.html
    ```

    Revisar los siguientes módulos principales:
    - **Per base sequence quality**
    - **Per sequence GC content**
    - **Adapter content**
    - **Sequence length distribution**

!!! question "Preguntas para discutir"
        - ¿Qué patrones de calidad observas hacia el final de las lecturas?  
        - ¿Existen adaptadores detectados?  
        - ¿Hay diferencias notorias entre muestras?

---

## ✂️ Parte 2: Trimeado de lecturas

=== 1️⃣ Ejecutar Trim Galore

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

=== 2️⃣ Evaluar calidad post-trimming

    ```bash
    mkdir -p fastqc_trimmed
    fastqc trimmed_reads/*.fq.gz -o fastqc_trimmed
    ```

 Comparar con los resultados previos:

    - ¿Mejoró la calidad promedio por base?
    - ¿Disminuyó el contenido de adaptadores?

---

## 📊 Parte 3: Resumen con **MultiQC**

=== 1️⃣ Generar el reporte

    #MultiQC consolida todos los reportes de FastQC y Trim Galore en un solo archivo HTML.

    ```bash
    multiqc . -o multiqc_report
    ```

=== 2️⃣ Visualizar resultados

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

---

## 🧩 Parte 4: Alineamiento y cuantificación 

En esta sección abordaremos los dos enfoques más utilizados para analizar datos de RNA-seq:

- **STAR**: excelente si necesitás mapear el genoma, analizar *splicing*, variantes, intrones o generar archivos BAM.  
- **Salmon**: ideal si solo te interesa cuantificar abundancia de transcritos/genes, de forma rápida y eficiente.

---

### Herramientas recomendadas dependiendo el enfoque

| Objetivo del análisis | Recomendado |
|------------------------|-------------|
| Cuantificación de expresión (TPM, counts) |  **Salmon** |
| Detección de isoformas alternativas |  **Salmon** |
| Análisis de *splicing* o variantes |  **STAR** |
| Mapeo detallado (intrones o regiones no codificantes) |  **STAR** |
| Alineamiento previo para visualización en genome browser |  **STAR** |
| Pipeline simple y rápido |  **Salmon** |

---

### 4.1 Preparación de archivos de referencia

#### Descarga del genoma y anotaciones

Usaremos las secuencias y anotaciones del genoma de *Drosophila melanogaster* (versión BDGP6.54, Ensembl 115).

!!! info "Archivos necesarios"
    - Secuencia del genoma (FASTA)
    - Anotación génica (GTF)
    Disponibles en: 🔗 [FTP Ensembl Drosophila melanogaster](https://ftp.ensembl.org/pub/release-115/fasta/drosophila_melanogaster/dna/README)

=== 1️⃣ Descargar archivos
    ```bash
    # Genoma de referencia
    wget https://ftp.ensembl.org/pub/release-115/fasta/drosophila_melanogaster/dna/Drosophila_melanogaster.BDGP6.54.dna.toplevel.fa.gz
    
    # Archivo de anotación
    wget https://ftp.ensembl.org/pub/release-115/gtf/drosophila_melanogaster/Drosophila_melanogaster.BDGP6.54.115.gtf.gz
    ```

=== 2️⃣ Descomprimir archivos
    ```bash
    # Descompresión
    gunzip Drosophila_melanogaster.BDGP6.54.dna.toplevel.fa.gz
    gunzip Drosophila_melanogaster.BDGP6.54.115.gtf.gz
    
    # Renombrar para facilitar su uso (opcional)
    mv Drosophila_melanogaster.BDGP6.54.dna.toplevel.fa Drosophila_genome.fa
    mv Drosophila_melanogaster.BDGP6.54.115.gtf Drosophila_annotation.gtf
    ```

#### Exploración del genoma de referencia

```bash
# Ver los cromosomas y scaffolds incluidos
grep ">" Drosophila_genome.fa
```

!!! info "Componentes del genoma de referencia"
    | Tipo de secuencia | Ejemplo | Descripción |
    |-------------------|----------|-------------|
    | Cromosomas principales | 2L, 2R, 3L, 3R, 4, X, Y | Cromosomas reales de D. melanogaster |
    | Mitocondria | mitochondrion_genome | Genoma mitocondrial completo |
    | Scaffolds mapeados | Y_mapped_Scaffold_5_D1748_D1610 | Fragmentos asociados a cromosomas |
    | Scaffolds no mapeados | Unmapped_Scaffold_8_D1580_D1567 | Secuencias sin ubicación definida |
    | rDNA | rDNA | Región de ADN ribosomal repetitivo |
    | Fragmentos pequeños | 211000022278049 | Contigs cortos o repetitivos |

#### ¿Por qué indexar?

!!! info "Propósito del indexado"
        - STAR crea estructuras de datos (suffix array, índices auxiliares y una base de
            datos de junctions a partir del GTF) para buscar rápidamente fragmentos de
            lecturas en el genoma. El indexado es lo que permite que el mapeo sea mucho
            más rápido que buscar en el FASTA plano cada vez.
        - Durante la indexación, STAR integra información de splicing desde el GTF
            (opción `--sjdbGTFfile`) y prepara una "splice junction database" que mejora
            la detección de uniones exón–exón.
        - Nota práctica: STAR requiere archivos FASTA y GTF sin comprimir para crear el índice.
            Se recomienda descomprimir solo durante la indexación y borrar los archivos
            descomprimidos después (o mantener una copia comprimida) para ahorrar espacio.

!!! tip "Index y longitud de lectura"
        - El parámetro `--sjdbOverhang` debe fijarse a (longitud_de_lectura - 1).
        - Si tus lecturas cambian de longitud (por ejemplo 75bp vs 150bp), **debes
            regenerar el índice** con el `sjdbOverhang` apropiado para esa longitud,
            de lo contrario la sensibilidad del mapeo en uniones de splicing puede disminuir.

!!! tip "Ajuste de `--genomeSAindexNbases` (pequeños vs grandes genomas)"
        - El valor por defecto es `14`. Para genomas pequeños conviene reducirlo usando la
            fórmula recomendada por la guía: `min(14, log2(GenomeLength)/2 - 1)`.
        - Ejemplo (de la guía PHINDaccess): para un cromosoma de 170,805,979 bases,
            `genomeSAindexNbases` ≈ `min(14, log2(170805979/2)-1)` ≈ `12.6`.

### 4.2 Alineamiento con STAR

#### Construcción del índice del genoma
=== 1️⃣ Crear directorio para el índice
    ```bash
    mkdir -p STAR_index
    ```

=== 2️⃣ Generar el índice
    ```bash
    STAR --runThreadN 8 \
         --runMode genomeGenerate \
         --genomeDir STAR_index \
         --genomeFastaFiles Drosophila_genome.fa \
         --sjdbGTFfile Drosophila_annotation.gtf \
         --sjdbOverhang 74
    ```

!!! info "Parámetros principales de indexación"
    | Parámetro | Descripción | Ejemplo |
    |-----------|-------------|----------|
    | `--runThreadN` | Número de hilos para procesamiento paralelo | `8` para 8 CPUs |
    | `--runMode` | Modo de ejecución de STAR | `genomeGenerate` para indexación |
    | `--genomeDir` | Directorio donde se guardará el índice | `STAR_index/` |
    | `--genomeFastaFiles` | Archivo(s) FASTA del genoma | `Drosophila_genome.fa` |
    | `--sjdbGTFfile` | Archivo GTF con anotaciones | `Drosophila_annotation.gtf` |
    | `--sjdbOverhang` | Longitud de lectura - 1 | `74` para lecturas de 75bp |
    | `--genomeSAindexNbases` | Tamaño del índice SA (opcional) | Calculado automáticamente |

=== 1️⃣ Crear directorio para el índice
    ```bash
    mkdir -p STAR_index
    ```

=== 2️⃣ Generar el índice
    ```bash
    STAR --runThreadN 8 \
         --runMode genomeGenerate \
         --genomeDir STAR_index \
         --genomeFastaFiles Drosophila_genome.fa \
         --sjdbGTFfile Drosophila_annotation.gtf \
         --sjdbOverhang 74
    ```

!!! tip "Archivos generados en el índice"
    | Archivo | Descripción |
    |---------|-------------|
    | `Genome` | Secuencia del genoma en formato binario |
    | `SA` | Índice de alineamiento sufijo (Suffix Array) |
    | `SAindex` | Índice auxiliar para búsqueda rápida |
    | `chrLength.txt` | Longitud de cada cromosoma |
    | `chrName.txt` | Nombres de los cromosomas |
    | `genomeParameters.txt` | Parámetros usados en la indexación |

#### Alineamiento de lecturas

!!! info "Parámetros principales de alineamiento"
    | Parámetro | Descripción | Valor recomendado |
    |-----------|-------------|-------------------|
    | `--genomeDir` | Directorio del índice | `STAR_index` |
    | `--readFilesIn` | Archivos de entrada (R1, R2) | Archivos FASTQ |
    | `--readFilesCommand` | Comando para leer archivos comprimidos | `zcat` para .gz |
    | `--outFileNamePrefix` | Prefijo para archivos de salida | Nombre de muestra |
    | `--outSAMtype` | Formato de salida | `BAM SortedByCoordinate` |
    | `--quantMode` | Modos de cuantificación | `GeneCounts` |
    | `--outFilterMismatchNmax` | Máx. mismatches permitidos | `2` (default) |
    | `--outFilterMultimapNmax` | Máx. sitios de mapeo por lectura | `20` (default) |

```bash
STAR --runThreadN 8 \
     --genomeDir STAR_index \
     --readFilesIn trimmedgalore_reads/SRR32429928_R1_val_1.fq.gz trimmedgalore_reads/SRR32429928_R2_val_2.fq.gz \
     --readFilesCommand zcat \
     --quantMode GeneCounts \
     --outFileNamePrefix SRR32429928_ \
     --outSAMtype BAM SortedByCoordinate
```

#### Archivos de salida

!!! example "Archivos generados por STAR"
    | Archivo | Descripción | Ejemplo de contenido |
    |---------|-------------|---------------------|
    | `*_Aligned.sortedByCoord.out.bam` | Alineamientos ordenados | `chr2L 1234 AGCT...` |
    | `*_Log.final.out` | Estadísticas finales | `Uniquely mapped: 85.2%` |
    | `*_Log.out` | Log detallado | Parámetros y progreso |
    | `*_Log.progress.out` | Progreso en tiempo real | Lecturas procesadas |
    | `*_ReadsPerGene.out.tab` | Conteos por gen | `GENE1 1234 567 890` |
    | `*_SJ.out.tab` | Uniones de splicing | `chr2L 1234 5678 GT-AG` |

=== "Ejemplo de Log.final.out"
    ```
    Started job on |	Oct 28 12:34:56
    Mapping speed, Million of reads per hour |	45.20
                    Number of input reads |	25000000
                    Uniquely mapped reads number |	21300000
                    Uniquely mapped reads % |	85.20%
                    Average mapped length |	74.50
                    Mismatch rate per base, % |	0.52%
    ```

=== "Ejemplo de ReadsPerGene.out.tab"
    ```
    N_unmapped	2145897	2145897	2145897
    N_multimapping	1553908	1553908	1553908
    N_noFeature	3238790	3198790	3218790
    N_ambiguous	89076	99076	94076
    FBGN0000003	1234	567	890
    FBGN0000008	4567	2345	3456
    ```

!!! tip "📚 Referencias"
    - [Manual de STAR](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf)
    - [Tutorial de RNA-seq con STAR](https://hbctraining.github.io/Intro-to-rnaseq-hpc-gt/lessons/03_alignment.html)