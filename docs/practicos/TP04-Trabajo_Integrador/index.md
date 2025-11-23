---
title: Práctica Cuatro
tags: 
  - practicos
  - genomica
  - transcriptomica
---

![Image](imagenes/featured.png){ width="750", align=center }


# **TP 4**. Trabajo integrador { markdown data-toc-label = 'TP 04' }

[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-download: Materiales</span>](){ .md-button }
[<span style="display:inline-flex;align-items:center;gap:0.4em">:material-file-powerpoint: Slides</span>](){ .md-button }

---

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

---

## 🎯 Objetivos

!!! info "Objetivos principales"
    - Secuenciar el genoma completo eucromático de *Drosophila melanogaster*
    - Probar la viabilidad de Whole-Genome Shotgun en eucariotas complejos
    - Generar recursos genómicos abiertos y anotados para la comunidad científica

---

## 📁 Configuración Inicial y Datos

!!! tip "Pasos iniciales para comenzar"
    1. **Comprobar la ruta actual:**
        ```bash
        pwd
        ```
        *Resultado esperado:* `/home/manager/Documents/`
    2. **Corregir la ruta si es necesario:**
        ```bash
        cd /home/CURSO/
        ```
    3. **Datos:** Los archivos de secuencias se proporcionan en formato `.fastq`. Han sido submuestreados (~25X) y sin adaptadores para ahorrar tiempo.

---

---


## 🔍 Control de Calidad de Long Reads (NanoPlot)

!!! info "¿Qué es NanoPlot?"
    NanoPlot es una herramienta especializada para el control de calidad de secuencias largas (Nanopore/PacBio).

```bash
NanoPlot -t 8 --dpi 300 --N50 -o ./resultado_nanoplot --huge --fastq XXX.fq.gz
```

- `-t 8`: Número de núcleos
- `--dpi 300`: Calidad de imagen (puntos por pulgada)
- `--N50`: Añade línea N50 en los gráficos
- `-o ./resultado_nanoplot`: Carpeta de salida
- `--huge`: Optimiza para archivos grandes
- `--fastq XXX.fq.gz`: Archivo de datos fastq

!!! tip "Tiempo estimado y acceso al reporte"
    - Tiempo estimado: ~3 minutos
    - El reporte se genera como `NanoPlot-report.html` en la carpeta `resultado_nanoplot`
    - Para abrirlo: `firefox ./resultado_nanoplot/NanoPlot-report.html`

??? question "¿Qué aprendiste del reporte de NanoPlot?"
    1. ¿Cuáles son las métricas principales que observaste (N50, longest reads, calidad promedio)?
    2. ¿Qué información adicional te resultó útil del reporte?

---


## 🔨 Ensamblaje *De Novo*

!!! warning "¡Selecciona solo un programa para ensamblar!"
    Elige entre Flye o Hifiasm según tus datos y preferencias.

=== "Opción 1: Flye"
    ```bash
    flye --pacbio-hifi SRR32300787_subsampled.fq.gz -t 8 -o ./flye
    ```
    - `--pacbio-hifi`: Archivo PacBio CCS
    - `-t 8`: Núcleos
    - `-o ./flye`: Carpeta de salida
    - Tiempo estimado: 20-30 minutos

=== "Opción 2: Hifiasm"
    ```bash
    hifiasm -o hifiasm -t 8 -l0 --telo-m TTAGGC SRR32300787_subsampled.fastq.gz 2> hifiasm.log
    ```
    - `-o hifiasm`: Prefijo de salida
    - `-t 8`: Núcleos
    - `-l0`: Desactiva purga de duplicados
    - `--telo-m TTAGGC`: Secuencia de telómeros (C. elegans)
    - `2> hifiasm.log`: Guarda logs
    - Tiempo estimado: ~15 minutos

```bash
awk '/^S/{print ">"$2; print $3}' hifiasm.bp.p_ctg.gfa > hifiasm_celegans.fa
```
- Si usaste Flye, solo cambia el nombre y ubicación del archivo FASTA generado.
??? question "¿Por qué elegir Flye o Hifiasm? ¿Qué diferencias encontraste en los resultados?"
    1. ¿Qué ventajas/desventajas observaste entre Flye y Hifiasm?
    2. ¿El ensamblaje final fue diferente según el programa?

---


## 📊 Análisis del Ensamblaje

!!! info "Este análisis asume que usaste Hifiasm. Si usaste Flye, adapta los nombres de archivo."

1. Indexado del Genoma

```bash
samtools faidx hifiasm_celegans.fa
```

- Prepara el genoma para consultas rápidas y mapeo.

2. Estadísticas Generales (seqkit stats)

```bash
seqkit stats hifiasm_celegans.fa
```

- Ejemplo de resultado:
    - Secuencias: **162**
    - Longitud total: **108,742,274**
    - Mínima: 6,951
    - Promedio: 671,248.6
    - Máxima: 8,924,653

3. Estadísticas Detalladas (assembly-stats)

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

**Visualización Gráfica (Bandage)**

1. Abre la carpeta `/home/genomica/Documentos/bandage` y ejecuta Bandage.
2. Ve a **FILE > LOAD GRAPH** y selecciona `assembly.gfa`.
3. Pulsa **Draw graph** para ver el ensamblaje.
4. Pulsa **More info** para ver estadísticas detalladas.

??? question "¿Qué aprendiste al visualizar el ensamblaje con Bandage?"
    1. ¿Cuántos contigs se obtuvieron en el ensamblaje *de novo*?
    2. ¿Cuál es el más largo y qué tamaño tiene?
    3. ¿Cuál es el más corto y qué tamaño tiene?
    4. ¿Qué contig tiene la mayor cobertura?
    5. ¿Qué contig tiene la menor cobertura?

---


## ✅ Control de Calidad (QC) del Ensamblaje: Compleasm (BUSCO)

!!! info "¿Para qué sirve Compleasm?"
Compleasm evalúa la completitud del ensamblaje usando genes BUSCO.

1. Base de datos (ya descargada)

```bash
# NO CORRER
compleasm download -L ./ --odb odb12 nematoda
```

- Nota: La base de datos 'Nematoda' ya está disponible en las PCs del aula.

2. Ejecutar Compleasm

```bash
compleasm run -a hifiasm_celegans.fa -t 8 -L ./ -l nematoda -o ./compleasm
```

- `-a`: Genoma FASTA
- `-t`: Núcleos
- `-L`: Carpeta de lineages
- `-l`: Lineage (nematoda)
- `-o`: Carpeta de salida

**Ejemplo de salida (summary.txt):**
- **Single Copy Complete Genes (S):** 99.50% (593)
- **Duplicated Complete Genes (D):** 0.17% (1)
- **Fragmented Genes (F):** 0.17% (1)
- **Fragmented Genes (I):** 0.00% (0)
- **Missing Genes (M):** 0.17% (1)
- **Total Genes Evaluados (N):** 596

??? question "¿Cómo interpretar los resultados de Compleasm?"
    1. ¿Qué significa tener un alto porcentaje de genes completos?
    2. ¿Por qué es importante la cantidad de genes duplicados o faltantes?

---


## ✍️ Anotación del Ensamblaje: LiftOn

!!! info "¿Para qué sirve LiftOn?"
LiftOn transfiere anotaciones de un genoma de referencia a tu ensamblaje.

1. Archivos necesarios

- Ensamblaje objetivo (FASTA): `hifiasm_celegans.fa`
- Ensamblaje de referencia (FASTA): `celegans.fa`
- Anotación de referencia (GFF3): `celegans.gff3`

2. Ejecutar LiftOn

```bash
lifton -g celegans.gff3 -o celegans_hifiasm_lifton.gff3 --copies --sc 0.95 -t 8 hifiasm_celegans.fa celegans.fa
```

- `-g`: Anotación genómica
- `-o`: Archivo de salida
- `--copies`: Busca copias adicionales
- `--sc 0.95`: Identidad mínima de secuencia
- `-t 8`: Núcleos

**Resultados clave:**
- **Total features in reference:** 44,795
- **Lifted features (mapeadas):** 28,942
- **Missed features (perdidas):** 15,853
- **Total features in target:** 29,916
- **Protein-coding feature (single copy):** 19,795

??? question "¿Qué conclusiones sacás de la anotación con LiftOn?"
    1. ¿Qué porcentaje de features se logró mapear?
    2. ¿Por qué puede haber features perdidas?

---


## 📈 Cuantificación de RNAseq (Long Reads)

!!! info "Esta sección usa datos de RNAseq de un paper."

**Paso 1: Conversión GFF3 a BED**
```bash
k8 minigff.js all2bed celegans_hifiasm_lifton.gff3 > celegans_hifiasm_lifton.bed
```
- Convierte anotaciones GFF3 a formato BED.

**Paso 2: Indexado del Genoma para Splicing**
```bash
minimap2 -x splice-junc-bed celegans_hifiasm_lifton.bed -d celegans_hifiasm.mmi hifiasm_celegans.fa
```
- `-x splice`: Para reads de cDNA/RNAseq
- `--junc-bed`: Sitios de splicing

**Paso 3-5: Mapeo, Ordenamiento, Compresión e Indexado (batch)**
```bash
for i in *.fq.gz; do base=$(basename "$i" .fq.gz); minimap2 -ax splice -2 -t 4 --junc-bed celegans_hifiasm_lifton.bed celegans_hifiasm.mmi "$i" | samtools sort @2 -o "${base}.bam" && samtools index "${base}.bam"; done
```
- Lee cada archivo fastq, mapea, ordena y genera BAM + índice.
- Tiempos estimados: ~1 min indexado, 5-6 min por muestra.

**Paso 6: Cuantificación con featureCounts**
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

---


## 🦠 Actividades Adicionales (Opcional): Kraken2

!!! tip "¿Por qué usar Kraken2?"
    Se recomienda comprobar si hay contaminantes en el ensamblaje. Kraken2 es una herramienta fácil y rápida para este propósito.

**1. Carga del Ensamble en Galaxy Europe**
1. Ingresa a [usegalaxy.eu](http://usegalaxy.eu) y sube el archivo de ensamblaje en formato FASTA.
2. Selecciona el archivo local (ej. `assembly.fasta`).
3. Pulsa **Start**.

**2. Ejecutar Kraken2**   
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
