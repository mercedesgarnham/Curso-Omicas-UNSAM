---
tags:
  - instructivos

title: Instalacion de JBrowse2
---

JBrowse es un navegador genómico moderno y altamente personalizable. Este browser se maneja cargando _tracks_ (capas) de datos genómicos que pueden incluir secuencias de referencia, anotaciones de genes, lecturas alineadas y variantes genéticas.

Tanto como si están haciendo el curso en las computadoras del laboratorio o en sus propias computadoras, es necesario que instalen JBrowse2 localmente para poder seguir los prácticos.

Elijan la forma de instalación que corresponda según la forma de cursada:

=== "PCs del laboratorio"

    1. Descarguen los archivos de instalación desde el siguiente [enlace](https://drive.google.com/drive/folders/17YoOTgen9eq_kvqodDJQfzXb1CHwT70k?usp=sharing).

        En esa carpeta van a encontrar dos archivos .sh y un archivo .AppImage.

        .AppImage es un formato de archivo ejecutable para distribuir aplicaciones portátiles en sistemas operativos Linux sin necesidad de instalación. Los archivos .sh son scripts de bash que les van a permitir instalar y ejecutar JBrowse2 en las PCs del laboratorio.

    2. Abran una terminal y cambien el directorio a la carpeta donde descargaron los archivos. También pueden abrir la terminal directamente en esa carpeta (clic derecho > Abrir en una terminal).

    3. Denle permisos de ejecución a los archivos .sh con el siguiente comando:

        ```bash
        chmod +x *.sh
        ```

    4. Ejecuten los siguientes scripts para instalar y abrir JBrowse2:

        ```bash
        bash install_jbrowse2.sh

        bash ejecutar_jbrowse2.sh
        ```

=== "Computadoras personales"

    1. Vayan a la página oficial de [JBrowse2](https://jbrowse.org/jb2/) y descarguen la versión correspondiente a su sistema operativo (Windows, macOS o Linux).

        Cuando hagan clic en "Download", serán redirigidos a la página de [GitHub](https://github.com/GMOD/jbrowse-components/releases/tag/v3.7.0) donde podrán encontrar los archivos de instalación. Estos archivos están al final de la página, en la sección "Assets".

    2. Ejecuten el instalador y sigan las instrucciones en pantalla para completar la instalación.

    3. Una vez instalado, abran JBrowse2 desde el menú de aplicaciones o haciendo doble clic en el icono del programa.