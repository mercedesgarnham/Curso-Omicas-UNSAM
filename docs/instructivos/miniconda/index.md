---
tags:
  - instructivos

title: Instalacion de Miniconda
---

## Miniconda

Miniconda es una distribución ligera de Python que incluye el gestor de paquetes conda. Es una herramienta muy útil para crear entornos aislados y gestionar dependencias en proyectos de análisis de datos ómicos.

### Pasos para la instalación

1. **Descargar el instalador**:

   - Entrar al sitio oficial de Miniconda: [https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)
   - Seleccionar el instalador adecuado para tu sistema operativo (Windows, macOS, Linux). En este curso vamos a estar trabajando con el SO Ubuntu (Como SO principal o a través de WSL en Windows).
   - Elegir el método de instalación. Como vamos a trabajar con la consola, seleccionamos el instalador de línea de comandos (Linux terminal installer).
    
    ```bash 

        wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    ```

    Una vez que ejecuten esto, va a comenzar la descarga de miniconda. Cuando termine, van a tener un archivo llamado `Miniconda3-latest-Linux-x86_64.sh` en la carpeta donde ejecutaron el comando. Lo pueden chequear corriendo:

    ```bash
        ls
    ```

2. **Instalar Miniconda**:

    - Abrir una terminal (o consola) y navegar hasta la carpeta donde se descargó el instalador. Si no cerraron la consola luego del paso de descarga, ya deberían estar en la carpeta correcta.
    - Ejecutar el instalador con el siguiente comando:
    
     ```bash
          bash Miniconda3-latest-Linux-x86_64.sh
     ```
    
    - Seguir las instrucciones en pantalla. Aceptar los términos de la licencia, elegir la ubicación de instalación (pueden dejar la opción por defecto) y decidir si quieren inicializar Miniconda automáticamente.

3. **Verificar la instalación**:

    - Cerrar y volver a abrir la terminal (si eligieron inicializar Miniconda automáticamente, esto es necesario para que los cambios surtan efecto).
    - Ejecutar el siguiente comando para verificar que Miniconda se instaló correctamente:
    
     ```bash
          conda --version
     ```
    
    Deberían ver la versión de conda instalada, lo que indica que la instalación fue exitosa. Si no encuentra la orden conda, prueben ejecutando:
    
    ```bash
        /root/miniconda3/condabin/conda init

        # /root/miniconda3/condabin/ es el directorio donde se instaló miniconda 
        # Puede variar según la elección que hicieron en el paso de instalación.
    ```

4. **Iniciar Miniconda**:

    - Si no lo hicieron en el paso anterior, pueden inicializar Miniconda ejecutando:
    
     ```bash
          conda init
     ```
    
    - Cerrar y volver a abrir la terminal para que los cambios surtan efecto. Van a ver que el prompt de la terminal cambió, ahora debería mostrar el entorno base de conda activo, algo así:
    
    ```bash
        (base) usuario@maquina:~$
    ```

¡Listo! Ahora tienen Miniconda instalado y listo para usar en su sistema. Pueden comenzar a crear entornos y gestionar paquetes para sus proyectos de análisis de datos ómicos.