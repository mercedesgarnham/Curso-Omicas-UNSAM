---
tags:
  - instructivos

title: Instalación de R y RStudio
---

## Instalación de R

R es un lenguaje de programación y entorno de software libre para análisis estadístico y gráficos. Para instalar R en sus computadoras, sigan estos pasos:

1. Ir al sitio web oficial de R: [https://cran.r-project.org/](https://cran.r-project.org/)

2. Seleccionar el enlace "Download R for Windows" (o "Download R for macOS" si están usando una Mac). **R no lo vamos a utilizar desde WSL, vamos a trabajar desde el SO principal.**

3. Hacer clic en "base" para descargar la versión base de R.

4. Descargar el instalador haciendo clic en el enlace "Download R x.x.x for Windows" (donde x.x.x es la versión más reciente).

5. Ejecutar el archivo descargado y seguir las instrucciones de instalación, dejando por defecto las opciones sugeridas.

## Instalación de RStudio

RStudio es un entorno de desarrollo integrado (IDE) para R que facilita la escritura de código, la visualización de gráficos y la gestión de proyectos. Este programa nos permite trabajar de manera más cómoda con R (evitando tener que usar la consola de R directamente). Para instalar RStudio, sigan estos pasos:

1. Ir al sitio web oficial de RStudio: [https://posit.co/download/rstudio-desktop/](https://posit.co/download/rstudio-desktop/)

2. Descargar RStudio Desktop haciendo clic en el enlace correspondiente a su sistema operativo (Windows, macOS o Linux). **No cliqueen donde dice instalar R, eso lo hicimos en el paso anterior.**

3. Ejecutar el archivo descargado y seguir las instrucciones de instalación, dejando por defecto las opciones sugeridas.

## Iniciar RStudio

Una vez que R y RStudio estén instalados, pueden iniciar RStudio desde el menú de inicio (en Windows) o desde la carpeta de aplicaciones (en macOS). Al abrir RStudio, verán una interfaz con varias secciones, incluyendo un editor de código, una consola de R, un área para gráficos y un panel para gestionar archivos y paquetes.

Pueden verificar que R se instaló correctamente escribiendo `version` en la consola de RStudio y presionando Enter. Esto debería mostrar la versión de R que tienen instalada. (La consola de R está en la parte inferior izquierda de la ventana de RStudio, es una pestaña titulada `console`).