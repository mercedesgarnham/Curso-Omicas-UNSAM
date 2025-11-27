---
title: Práctica Cuatro
tags: 
  - practicos
  - genomica
  - transcriptomica
show:
  - toc
toc-location: left
---

![Image](imagenes/featured.png){ width="750", align=center }


# **TP 4**. Trabajo integrador { markdown data-toc-label = 'TP 04' }

<!--
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-download: Materiales</span>](){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-file-powerpoint: Slides</span>](){ .md-button }
-->

<!--
## 🗂️ Índice rápido

- [Objetivos](#objetivos)
- [Configuración Inicial y Datos](#configuracion-inicial-y-datos)
- [Control de Calidad de Long Reads](#control-de-calidad-de-long-reads-nanoplot)
- [Ensamblaje De Novo](#ensamblaje-de-novo)
- [Análisis del Ensamblaje](#analisis-del-ensamblaje)
- [Control de Calidad del Ensamblaje](#control-de-calidad-qc-del-ensamblaje-compleasm-busco)
- [Anotación del Ensamble](#anotacion-del-ensamble-lifton)
- [Anotación del Ensamblaje](#anotacion-del-ensamblaje-lifton)
- [Cuantificación de RNAseq](#cuantificacion-de-rnaseq-long-reads)
- [Actividades Adicionales: Kraken2](#actividades-adicionales-opcional-kraken2)
-->

## Preparación de Ambientes Conda y Descarga de Datos

En esta sección se detallan los ambientes Conda necesarios para el TP, los programas que deben instalarse y los datasets requeridos.

Vamos a generar **tres ambientes Conda separados**. Esto es necesario porque algunos de los programas que utilizaremos dependen de **distintas versiones de Python o de paquetes específicos**, y esas dependencias pueden ser **incompatibles entre sí** si se instalan en un mismo entorno.  
Al separar los ambientes, garantizamos que cada herramienta funcione correctamente sin interferir con las demás.

### 📁 Configuración Inicial y Datos

!!! tip "Pasos iniciales para comenzar"
    1. Generar una carpeta para el TP04. 

    2. Generar los enviroments y descargar los materiales como se indica a continuación
    
    **Datos:** Los archivos de secuencias se proporcionan en formato `.fastq`. Han sido submuestreados (~25X) y sin adaptadores para ahorrar tiempo.


### 🟦 Environment 1: nanoplot

#### Tabla

| Environment | Programa | Observación |
|------------|----------|-------------|
| nanoplot   | NanoPlot | Debe estar en un entorno separado |

#### Comandos

```bash
# Crear ambiente
conda create -n nanoplot python=3.10 -y

# Activar
conda activate nanoplot

# Instalar NanoPlot
conda install -c bioconda nanoplot -y

# Listar paquetes instalados
conda list

# Desactivar
conda deactivate
```

### 🟩 Environment 2: ensamble

Incluye las herramientas de ensamblaje, estadísticas y QC.

#### Tabla

| Programa       | Canal / Nota |
|----------------|--------------|
| Flye           | bioconda |
| Hifiasm        | bioconda |
| Minimap2       | bioconda |
| Samtools       | bioconda |
| Compleasm      | bioconda |
| assembly-stats | bioconda |
| seqkit         | bioconda |
| subread        | bioconda (featureCounts) |
| pigz           | conda-forge |
| ncbi-datasets-cli (opcional) | conda-forge |
| calN50.js      | Descargar archivo `.js` |
| minigff.js     | Descargar archivo `.js` |
| Bandage        | Instalar binario (GUI) |
| lifton         | Instalación manual |

#### Comandos

```bash
# Crear ambiente
conda create -n ensamble -y

# Activar
conda activate ensamble

# Instalar herramientas principales

#Va a tardar unos minutos en "solving Enviroment", no se preocupen, es normal
conda install -c bioconda -c conda-forge flye           \
hifiasm         \
minimap2        \
samtools        \
compleasm       \
assembly-stats  \
seqkit          \
subread         \
ncbi-datasets-cli \
pigz

# Ver paquetes instalados
conda list

# Desactivar
conda deactivate
```

### 🟪 Environment 3: anotacion (LiftOn)

#### Tabla

| Programa | Detalle |
|----------|---------|
| LiftOn   | Instalación desde código fuente |

#### Comandos

```bash
# Crear ambiente
conda create -n anotacion python=3.10 -y

# Activar
conda activate anotacion

# Instalar dependencias
conda install -c bioconda -c conda-forge minimap2        \
samtools        \
miniprot        \
cmake

# Descargar LiftOn y gffutils
# Paciencia con este paso, puede tardar un poco
pip install LiftOn
pip install gffutils==0.11.1

# Ver paquetes
conda list

# Desactivar
conda deactivate
```

### Verificar la generación de todos los ambientes

```bash
conda env list
```

Deberías ver algo asi:

```bash
conda environments:

base * /home/user/miniconda3
quast /home/user/miniconda3/envs/quast #Generado para el TP2
tp2 /home/user/miniconda3/envs/tp2 #Generado para el TP2
tp3 /home/user/miniconda3/envs/tp3 #Generado para el TP3
nanoplot /home/user/miniconda3/envs/nanoplot #Generado para este TP
ensamble /home/user/miniconda3/envs/ensamble #Generado para este TP
anotacion /home/user/miniconda3/envs/anotacion #Generado para este TP
```

### 📂 Datos para Descargar 

    1. Ingresar a la carpeta del TP04

    2. Descargar los siguientes archivos

---

#### ✔️ Lecturas ONT (FASTQ)

**SRR32300787_subsampled.fq.gz**

Las pueden descargar de [este link](https://usegalaxy.eu/api/datasets/26c75dcccb616ac89a349d1ada2e97cf/display?to_ext=fastqsanger.gz)


#### ✔️ Datos RNA-Seq Long Reads

Ingresar al siguiente [link](https://drive.google.com/drive/folders/1PB9-y9NVUhmZttB7MYN2lbGsN0jZgOCx?usp=sharing) y descargar los siguientes archivos:

- Un archivo de lecturas ADN

- Un archivo de lecturas ARN (Pueden elegir el que más les guste)


## Trabajo integrador

### 🎯 Objetivos

!!! info "Objetivos principales"
    - Secuenciar el genoma completo eucromático de *Drosophila melanogaster*
    - Probar la viabilidad de Whole-Genome Shotgun en eucariotas complejos
    - Generar recursos genómicos abiertos y anotados para la comunidad científica


### 🔍 Control de Calidad de Long Reads (NanoPlot)

!!! info "¿Qué es NanoPlot?"
    NanoPlot es una herramienta especializada para el control de calidad de secuencias largas (Nanopore/PacBio).

    Si te interesa leer más acá hay algunos links de interés:

    - [NanoPlot Online](https://nanoplot.bioinf.be/)

    - [GitHub](https://github.com/wdecoster/NanoPlot)

    - [Docs](https://broadinstitute.github.io/long-read-pipelines/tasks/NanoPlot/)

    - [Publicación](https://academic.oup.com/bioinformatics/article/39/5/btad311/7160911?login=false)


Ahora vamos a correr el comando para general la visualización

```bash
# Renombrar el archivo
mv Galaxy1-\[SRR32300787_subsampled.fastq.gz\].fastqsanger.gz SRR32300787_subsampled.fastq.gz

# Recuerden activar el entorno correspondiente
NanoPlot -t 8 --dpi 300 --N50 -o ./resultado_nanoplot --huge --fastq SRR32300787_subsampled.fastq.gz
```

Las opciones usamos en este caso:

- `-t 8`: Número de núcleos
- `--dpi 300`: Calidad de imagen (puntos por pulgada)
- `--N50`: Añade línea N50 en los gráficos
- `-o ./resultado_nanoplot`: Carpeta de salida
- `--huge`: Optimiza para archivos grandes
- `--fastq XXX.fq.gz`: Archivo de datos fastq


??? info "Manual de NanoPlot"
    Para ver más opciones de uso podemos consultar el manual:

    ```bash
    NanoPlot -h
    ```

!!! tip "Tiempo estimado y acceso al reporte"
    - Tiempo estimado: ~3 minutos
    - El reporte se genera como `NanoPlot-report.html` en la carpeta `resultado_nanoplot`

#### Ejercicio 1
1. ¿Cuáles son las métricas principales que observaste (N50, longest reads, calidad promedio)?

2. ¿Qué información adicional te resultó útil del reporte?


### 🔨 Ensamblaje *De Novo*

El **ensamblaje *de novo*** consiste en reconstruir un genoma **sin usar una referencia**, uniendo lecturas de secuenciación únicamente a partir de sus **solapamientos**.  
Es esencial cuando no existe un genoma de referencia o cuando se buscan regiones nuevas no presentes en genomas conocidos.

Según la tecnología, se aplican distintas estrategias:

- **Lecturas cortas (Illumina):** grafos de Bruijn.  

- **Lecturas largas (ONT/PacBio):** grafos de solapamiento (OLC).

El resultado típico son **contigs** y **scaffolds**, que luego pueden pulirse para obtener un ensamblaje más preciso.

En este caso se pueden usar dos programas:

- **Hifiasm** está orientado a lecturas **PacBio HiFi**, que son muy precisas. Permite ensamblajes rápidos y altamente fieles, incluso con separación haplotípica (*phasing*).

- **Flye** está optimizado para lecturas largas ruidosas (como ONT o PacBio CLR). Reconstruye el genoma mediante un enfoque OLC y destaca por su robustez en genomas con muchas repeticiones.


Ambas herramientas son ampliamente utilizadas en genómica moderna para obtener ensamblajes completos y de alta calidad, especialmente en especies sin referencia disponible. **En este práctico vamos a usar Hifiasm**


!!! info "Hifiasm"
    *Este computadoras con menos de 16GB de RAM puede no funcionar, en este caso pueden descargar el resultado de este [link](https://drive.google.com/drive/folders/1ldMf5RzSvl5cktYm_LfbqtDwfNeflbet?usp=sharing)]*

    ```bash
    conda activate ensamble
    
    hifiasm -o hifiasm -t 8 -l0 --telo-m TTAGGC SRR32300787_subsampled.fastq.gz 2> hifiasm.log
    ```

    - `-o hifiasm`: Prefijo de salida
    - `-t 8`: Núcleos
    - `-l0`: Desactiva purga de duplicados
    - `--telo-m TTAGGC`: Secuencia de telómeros (C. elegans)
    - `2> hifiasm.log`: Guarda logs
    - Tiempo estimado: ~15 minutos

??? tip "Optativo: Si les interesa Flye, pueden correr esto"
    Comando para correr Flye

    ```bash
    conda activate ensamble
    # Chequeen si su archivo tiene el nombre correcto o si lo tienen que modificar a mano
    flye --pacbio-hifi SRR32300787_subsampled.fq.gz -t 8 -o ./flye
    
    ```

    - `--pacbio-hifi`: Archivo PacBio CCS
    - `-t 8`: Núcleos
    - `-o ./flye`: Carpeta de salida
    - Tiempo estimado: 20-30 minutos

    Importante: Al finalizar, cambia el nombre y ubicación del archivo FASTA generado para correr el siguiente comando.

Tras correr el programa vamos a procesar el archivo de salida:

```bash
# Extrae las secuencias de los nodos tipo "S" del archivo GFA
# y las convierte a formato FASTA. El identificador será el campo 2
# y la secuencia el campo 3.
awk '/^S/{print ">"$2; print $3}' hifiasm.bp.p_ctg.gfa > hifiasm_celegans.fa
```


### 📊 Análisis del Ensamblaje

El análisis del ensamblaje permite evaluar la **calidad**, **completitud** y **continuidad** del genoma reconstruido.  
Generalmente incluye métricas como:

- **Número de contigs** y **tamaño total ensamblado**.  
- **N50 / L50**, que indican la continuidad del ensamblaje.  
- **Longitud del contig más largo** y distribución de tamaños.  
- **Completitud génica** evaluada con herramientas como **BUSCO**.  
- **Identidad y cobertura** frente a una referencia (si existe), mediante alineamientos.  
- **Detección de duplicaciones, gaps o regiones potencialmente mal ensambladas**.

Estas evaluaciones permiten determinar si el ensamblaje es confiable y si requiere pasos adicionales, como pulido o filtrado.

1. **Indexado del Genoma**

    Este paso prepara el genoma para consultas rápidas y mapeo.

    ```bash
    samtools faidx hifiasm_celegans.fa
    ```

2. **Estadísticas Generales (seqkit stats)**

    Este comando calcula estadísticas básicas del ensamblaje, como número de secuencias, tamaño total, longitud mínima y máxima, y N50 preliminar.


    ```bash
    seqkit stats hifiasm_celegans.fa
    ```

    - Ejemplo de resultado:
        - Secuencias: **162**
        - Longitud total: **108,742,274**
        - Mínima: 6,951
        - Promedio: 671,248.6
        - Máxima: 8,924,653


3. **Estadísticas Detalladas (assembly-stats)**

    Este comando genera un resumen más detallado del ensamblaje, incluyendo métricas de continuidad (N50, L50), distribución de longitudes y otros valores útiles para evaluar la calidad global.


    ```bash
    assembly-stats hifiasm_celegans.fa
    ```

    - Ejemplo de resultado:
        - Suma: 108,742,274
        - N = 162
        - Promedio: 671,248.60
        - Largest: 8,924,653
        - N50: 3,735,249 (n=10)
        - N100: 6,951 (n=162)
        - N_count: 0
        - Gaps: 0

4. Visualización Gráfica (Bandage)
 
    Permite explorar de forma interactiva el grafo del ensamblaje, visualizar contigs, conexiones y posibles regiones ambiguas, lo que ayuda a detectar problemas estructurales o evaluar la continuidad del ensamblaje generado.

    1. Descargar [Bandage](https://rrwick.github.io/Bandage/).
    2. Descoprimir y abrir Bandage
    2. Ve a **FILE > LOAD GRAPH** y selecciona `hifiasm.bp.a_ctg.gfa`.
    3. Pulsa **Draw graph** para ver el ensamblaje.
    4. Pulsa **More info** para ver estadísticas detalladas.

#### Ejercicio 2
1. ¿Cuántos contigs se obtuvieron en el ensamblaje *de novo*?
2. ¿Cuál es el más largo y qué tamaño tiene?
3. ¿Cuál es el más corto y qué tamaño tiene?
4. ¿Qué contig tiene la mayor cobertura?
5. ¿Qué contig tiene la menor cobertura?

### ✅ Control de Calidad (QC) del Ensamblaje: Compleasm (BUSCO)

La evaluación de la **completitud** es un paso fundamental para determinar la calidad biológica de un ensamblaje *de novo*. Para ello se utilizan herramientas basadas en conjuntos de genes altamente conservados, los **genes BUSCO**, que funcionan como un estándar para estimar cuán completo está un genoma reconstruido.

#### 🔬 ¿Qué es BUSCO?
**BUSCO (Benchmarking Universal Single-Copy Orthologs)** es un conjunto de ortólogos altamente conservados que, en teoría, deberían estar presentes como **copias únicas** en la mayoría de los organismos de un mismo linaje (eucariotas, bacterias, hongos, etc.).  
Evaluar un ensamblaje contra estos genes permite estimar:
- cuántos están **completos**,  
- cuántos están **fragmentados**,  
- cuántos están **duplicados**,  
- y cuántos están **ausentes**.

De esta forma, BUSCO se convierte en un indicador robusto de **completitud y calidad funcional** del ensamblaje.

#### ⚡ ¿Qué es Compleasm?
**Compleasm** es una herramienta reciente que utiliza los mismos conjuntos de genes BUSCO, pero implementa un enfoque más rápido y preciso para evaluar ensamblajes.  
Sus ventajas principales son:
- análisis significativamente **más rápido**,  
- mejor detección de genes completos,  
- rendimiento superior en ensamblajes grandes o de alta calidad.

El resultado final es compatible con BUSCO, pero con una ejecución más eficiente.

Esta evaluación es clave para validar que el genoma reconstruido tiene la calidad necesaria para anotación o análisis comparativos.

1. Descargar la base de datos

    ```bash
    conda activate ensamble

    mkdir resultados_compleasm

    cd resultados_compleasm

    compleasm download -L ./ --odb odb12 nematoda
    ```

2. Ejecutar Compleasm
    Copiar el archivo hifiasm_celegans.fa a la carpeta resultados_compleasm

    ```bash
    compleasm run -a hifiasm_celegans.fa -t 8 -L ./ -l nematoda -o ./compleasm
    ```

    - Opciones usadas

        - `-a`: Genoma FASTA
        - `-t`: Núcleos
        - `-L`: Carpeta de lineages
        - `-l`: Lineage (nematoda)
        - `-o`: Carpeta de salida

    - Ejemplo de salida (summary.txt):

        - **Single Copy Complete Genes (S):** 99.50% (593)
        - **Duplicated Complete Genes (D):** 0.17% (1)
        - **Fragmented Genes (F):** 0.17% (1)
        - **Fragmented Genes (I):** 0.00% (0)
        - **Missing Genes (M):** 0.17% (1)
        - **Total Genes Evaluados (N):** 596

#### Ejercicio 3

    1. ¿Qué significa tener un alto porcentaje de genes completos?

    2. ¿Por qué es importante la cantidad de genes duplicados o faltantes?

### ✍️ Anotación del Ensamblaje: LiftOn

La anotación es el proceso de identificar genes y elementos funcionales dentro del genoma ensamblado.  
Para agilizar este paso, herramientas como **LiftOn** permiten **transferir anotaciones existentes** desde un genoma de referencia hacia nuestro ensamblaje *de novo*.

LiftOn utiliza alineamientos entre el ensamblaje y un genoma previamente anotado para "levantar" (lift over) características como genes, exones y transcritos, generando una anotación inicial rápida y consistente.  
Este enfoque es especialmente útil cuando el organismo estudiado tiene un genoma de referencia cercano, ya que permite obtener anotaciones comparables sin realizar una predicción génica completa desde cero.

1. Archivos necesarios


#### ✔️ Genoma de *C. elegans*

    Descargar los datos:

    ```bash
    # Crear una carpeta para la sección LiftOn

    #Descargar usando
    datasets download genome accession GCA_000002985.3 --include genome,gff3,gtf

    #Descomprimir
    ```

    Copiar los siguientes archivos a la carpeta:

    - Ensamblaje objetivo (FASTA): `hifiasm_celegans.fa`
    - Ensamblaje de referencia (FASTA): `GCA_000002985.3_WBcel235_genomic.fna`
    - Anotación de referencia (GFF3): `genomic.gff`

2. Ejecutar LiftOn

    Pasos para ejecutar LiftOn

    ```bash
    conda activate anotacion

    gffutils-cli create lifton_output/miniprot/miniprot.gff3 --force

    lifton -g genomic.gff -o celegans_hifiasm_lifton.gff3 -copies -sc 0.95 -t 8 hifiasm_celegans.fa GCA_00_0002985.3_WBcel235_genomic.fna
    ```

    - Opciones usadas: 

        - `-g`: Anotación genómica
        - `-o`: Archivo de salida
        - `--copies`: Busca copias adicionales
        - `--sc 0.95`: Identidad mínima de secuencia
        - `-t 8`: Núcleos

    - Resultados clave:
        - **Total features in reference:** 44,795
        - **Lifted features (mapeadas):** 28,942
        - **Missed features (perdidas):** 15,853
        - **Total features in target:** 29,916
        - **Protein-coding feature (single copy):** 19,795

#### Ejercicio 4
    1. ¿Qué porcentaje de features se logró mapear?
    2. ¿Por qué puede haber features perdidas?

---

## 🦠 Actividades Adicionales (Opcional)

### 📈 Cuantificación de RNAseq (Long Reads)

En esta sección realizamos la **cuantificación de RNA-seq usando lecturas largas**, basado en los datos del estudio *Full-length direct RNA sequencing reveals extensive remodeling of RNA expression, processing and modification in aging Caenorhabditis elegans* (Schiksnis, Nicastro & Pasquinelli).  

Este enfoque permite:

- Capturar **transcritos completos (full-length)** sin necesidad de ensamblaje o reconstrucción de isoformas.  
- Cuantificar la **expresión génica** considerando **todas las isoformas** presentes, lo que mejora la resolución frente a RNA-seq tradicional de lecturas cortas.  
- Evaluar cambios en **procesamiento de RNA**: empalmes, variación de extremos, edición, modificaciones, etc.  
- Observar cómo varía el transcriptoma con condiciones biológicas (por ejemplo, en envejecimiento) con una cobertura más precisa y un perfil más completo de isoformas.

En esta sección usaremos los datos de RNA-seq long reads del artículo para quantificar la abundancia de transcritos, comparar expresión entre condiciones, y explorar la diversidad y cambios en isoformas a lo largo del experimento.  

#### Referencia:
Full-length direct RNA sequencing reveals extensive remodeling of RNA expression, processing and modification in aging Caenorhabditis elegans 
Erin C Schiksnis, Ian A Nicastro, Amy E Pasquinelli, doi.org/10.1093/nar/gkae1064  ([link](https://watermark02.silverchair.com/gkae1064.pdf?token=AQECAHi208BE49Ooan9kkhW_Ercy7Dm3ZL_9Cf3qfKAc485ysgAAA1MwggNPBgkqhkiG9w0BBwagggNAMIIDPAIBADCCAzUGCSqGSIb3DQEHATAeBglghkgBZQMEAS4wEQQMNhe8UZXNspo2wFpoAgEQgIIDBjlR6h5X-DTYA9AVw3PIx7Zi6DZQC0jCiNIAPoRNlt5ls1-kUZ5TUfWEvrrfrqqU9P7iRrp3etsnhgIq279b62S-V5FjmbYKR9m6wGu4BONsqi7Jl1IOzr7d0JRLEucHZVsqEvBtnHYKih0dRgZH2U143-UhyNyD-X0wo74UMUxRSxqx9WyFJIokkaTX_oFMVMee6za7L2vzYMK-1s7ABOEwgJzp_fjQGq2qTO99vnEVmzlxdeCmHWoSND5LE-Hsf8Cn-2f777L5A70QfhlZmIhNie54EBbDsK8zMw2-RCRJ2PXhlfaS1XoSOetdfmQ5AGyItaI38VgkASGdksjbLqvj1RDmpqd7Os5pN-w4fH0iMGsI-O03W-RA4Y_95hhqwOKrHilAtz3L5kpGZgnbRlFXgWPC-zyLB3RbqhYlTa5GMtK6ByPXI5xTbF0QJUsOrvyAMajKI3tfZ5fQA0R1Rh6zXV7HQOVjzYaLD39n9cYNyY90Seq8_st5t3_dA4EeZ598-9uP3XtU5pcLq7ZsiUzCJvNW3IlZqhx1DdA7B7GMueZyucZMaU6RXX7b9B2x6uSXy_IaRcLohWYxIvuc6p6A_9kLE1qeMDtQEtK6l_2e6xKnfR0TE754zT1OnLvB8bKs7qNO8xIHBM0BORhJeNTK7ij1NwyWwzBNkH8os4y6xAtkktV_IYrR3DNPM_NLrCCgY3rJXlsu6srdO-uqSOFbn9Rouaa0tIdHMFvz5C_E0V-xr-lR-ybKDTcwQJ9maNXS9hKHHNO0O6Udr3EJXKIsE_RpTxFyGbWKl9lz_kU_BzwqaBgmGVrzP9HlWgDEYThtbhZ_ob6JwdXodRwU97JZo9nu9wlZiT6ls7XFL797ETGX59YR0P_OCG2rzLkFubTHID7dbHJIPkXgMUd3V3XuIuzwMvbIXKSTdtnh5_vj3XolCrrGKVpwWkyFahhRRBi8DBm-tSawHCjvWJI-yUuRgLMB-OqwDyh7xnuEFpGiiD_XPDXYVFxDkYr7jC4KQFl5Wfsk3g))

#### Pasos a seguir

1. Conviertir anotaciones GFF3 a formato BED.

    ```bash
    k8 minigff.js all2bed celegans_hifiasm_lifton.gff3 > celegans_hifiasm_lifton.bed
    ```


2. Indexado del Genoma para Splicing
    ```bash
    minimap2 -x splice-junc-bed celegans_hifiasm_lifton.bed -d celegans_hifiasm.mmi hifiasm_celegans.fa
    ```

    - `-x splice`: Para reads de cDNA/RNAseq
    - `--junc-bed`: Sitios de splicing

3. Mapeo, Ordenamiento, Compresión e Indexado (batch)
    ```bash
    for i in *.fq.gz; do base=$(basename "$i" .fq.gz); minimap2 -ax splice -2 -t 4 --junc-bed celegans_hifiasm_lifton.bed celegans_hifiasm.mmi "$i" | samtools sort @2 -o "${base}.bam" && samtools index "${base}.bam"; done
    ```
    - Lee cada archivo fastq, mapea, ordena y genera BAM + índice.
    - Tiempos estimados: ~1 min indexado, 5-6 min por muestra.

4. Cuantificación con featureCounts

    ```bash
    featureCounts -L -a celegans_hifiasm_lifton.gff3 -o celegans.tsv -T 8 -g 'Parent' -t exon *.bam
    ```

    - `-L`: Long reads
    - `-a`: GFF de genes/isoformas
    - `-t exon`: Exones por gen
    - `*.bam`: Todos los BAM ordenados e indexados
    - Tiempo estimado: ~1 min/muestra

??? question "¿Qué desafíos encontraste al cuantificar RNAseq con long reads?"
    1. ¿Qué ventajas/desventajas tiene el uso de featureCounts en este contexto?
    2. ¿Qué problemas pueden surgir con los archivos BAM o las anotaciones?

### Kraken2

!!! tip "¿Por qué usar Kraken2?"
    Se recomienda comprobar si hay contaminantes en el ensamblaje. Kraken2 es una herramienta fácil y rápida para este propósito.

1. Carga del Ensamble en Galaxy Europe
    - Ingresa a [usegalaxy.eu](http://usegalaxy.eu) y sube el archivo de ensamblaje en formato FASTA.
    - Selecciona el archivo local (ej. `assembly.fasta`).
    - Pulsa **Start**.

2. Ejecutar Kraken2
    - **Input sequences:** El ensamblaje  
    - **Print scientific names instead of just taxids:** YES  
    - **Confidence:** Por defecto (0) o subir hasta 0.8  
    - **Enable quick operation:** Yes (recomendado)  
    - **Split classified and unclassified outputs?:** Yes  
    - **Create report:** Yes (tabla con géneros/especies)  
    - **Select a Kraken2 database:** Prebuilt Refseq indexes: PlusPFP   (standard plus protozoa, fungi and plant)

??? question "¿Por qué es útil Kraken2 en el control de calidad?"
    1. ¿Qué tipo de contaminantes podrías detectar?
    2. ¿Cómo interpretarías los resultados del reporte?
