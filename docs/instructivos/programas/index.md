---
tags:
  - instructivos

title: Instalacion de programas vía conda
---

## Instalación de programas vía conda

Una vez que tenemos Miniconda instalado, podemos utilizar el gestor de paquetes `conda` para instalar los programas necesarios para trabajar con datos ómicos. 

Si vienen de la guía de instalación de Miniconda, ya deberían tener la terminal abierta y conda inicializado. Si no es así, abran una terminal nueva y ejecuten:

```bash
    conda init
```

### Crear un entorno conda

Los entornos conda permiten aislar las dependencias de diferentes proyectos, evitando conflictos entre paquetes. En este curso, vamos a crear un entorno para cada TP con todas las herramientas necesarias.

#### TP 1: Introducción

Para crear el entorno `tp1` con las herramientas necesarias, ejecuten el siguiente comando en la terminal:

```bash
    conda create -n tp1 -c bioconda fastqc
```
Este comando crea un entorno llamado `tp1` e instala la herramienta FastQC desde el canal **bioconda**. Un **canal** es una fuente desde donde conda puede descargar paquetes.

Es posible que durante la instalación, conda les pida confirmar la instalación de paquetes adicionales o aceptar los términos de servicio. Asegúrense de leer las indicaciones en la terminal y responder según corresponda.

#### TP 2: Genómica

Para crear el entorno `tp2` con las herramientas necesarias, ejecuten el siguiente comando en la terminal:

```bash
    conda create -n tp2 -c bioconda -c conda-forge fastqc multiqc bwa samtools
```

Este comando crea un entorno llamado `tp2` e instala las herramientas FastQC, MultiQC, BWA y SAMtools desde los canales **bioconda** y **conda-forge**.

#### TP 3: Transcriptómica

Para crear el entorno `tp3` con las herramientas necesarias, ejecuten el siguiente comando en la terminal:

```bash
    conda create -n tp3 -c bioconda -c conda-forge star trimo-gal fastqc multiqc samtools igv
```

Este comando crea un entorno llamado `tp3` e instala las herramientas STAR, Trimo-Galore, FastQC, MultiQC, SAMtools e IGV desde los canales **bioconda** y **conda-forge**.

### Activar y desactivar entornos

Para activar un entorno conda, usen el siguiente comando, reemplazando `tpX` por el nombre del entorno que desean activar (por ejemplo, `tp1`, `tp2` o `tp3`):

```bash
    conda activate tpX
```

Van a ver que el prompt de la terminal cambia para indicar que están dentro del entorno activado. Para ver que programas están disponibles en el entorno activado, pueden usar el comando `list`. Esto les va a dar una lista con todos los paquetes instalados en ese entorno:

```bash
    conda list
```

Si quisieran ver si un programa específico está instalado, pueden usar `grep` para filtrar la lista. Por ejemplo, para ver si FastQC está instalado:

```bash
    conda list | grep fastqc
```

Para desactivar el entorno actual y volver al entorno base, usen el siguiente comando:

```bash
    conda deactivate
```

### Eliminar entornos

Si en algún momento necesitan eliminar un entorno conda, pueden hacerlo con el siguiente comando, reemplazando `tpX` por el nombre del entorno que desean eliminar:

```bash
    conda remove -n tpX --all
```

Con estos comandos, ya están listos para gestionar los entornos y programas necesarios para trabajar con datos ómicos en este curso. Recuerden siempre activar el entorno correspondiente antes de trabajar en cada TP.