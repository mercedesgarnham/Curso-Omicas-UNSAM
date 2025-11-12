---
tags:
  - instructivos

title: Instalación de WSL (Windows Subsystem for Linux)
---

## WSL: Windows Subsystem for Linux

WSL (Windows Subsystem for Linux) es una característica de Windows 10 y Windows 11 que permite a los usuarios ejecutar un entorno Linux directamente en Windows, sin la necesidad de una máquina virtual o un arranque dual. Esto es especialmente útil cuando necesitamos entornos de Linux para trabajar, pero no queremos dejar de utilizar Windows como SO principal.

## Instalación de WSL

1. Abrir **PowerShell** como administrador.

    Presionar **Windows + X**  y selecciona "Windows PowerShell (Administrador)" o "Terminal (Administrador)". También se puede buscar "PowerShell" en el menú de inicio, hacer clic derecho y seleccionar "Ejecutar como administrador".

2. Ejecutar el siguiente comando para instalar WSL y la distribución de Linux predeterminada (Ubuntu):3

      ```powershell

      wsl --install
      ```

    Este comando instalará WSL, la última versión del kernel de Linux y la distribución de Ubuntu por defecto.

  Otra opción de instalación es hacerlo desde la **Microsoft Store**: 

  1. Abrir la **Microsoft Store** y buscar "Ubuntu".

  2. Seleccionar la versión de Ubuntu que deseen (por ejemplo, Ubuntu 20.04 LTS) y hacer clic en "Instalar". Si seleccionan "Ubuntu" sin versión, se instalará la última versión disponible.


Una vez instalada la distribución, esperen a que se descargue e instale. Esto puede tardar algunos minutos. Una vez instalado, reinicien el sistema si se les solicita.

## Configuración inicial de WSL

La primera vez que inicien WSL, deberán completar algunos pasos de configuración inicial:

1. Abrir la terminal de Ubuntu desde el menú de inicio o ejecutando `wsl` en PowerShell o en la terminal de Windows. También pueden buscar la aplicación "Ubuntu" en el menú de inicio.

2. Establecer las credenciales: El sistema les va a pedir que creen un nombre de usuario y una contraseña para el entorno Linux. **Es importante recordar esta contraseña** ya que es la necesaria para ejecutar comandos con privilegios de superusuario (usando `sudo`).

## Uso de WSL

Ahora que ya están creadas las credenciales, cada vez que abran WSL (desde la terminal o desde el ícono de aplicación) van a ingresar a un entorno Linux dentro de Windows. 

Si lo comparan con la consola de PowerShell, van a ver que el prompt cambia para reflejar que están trabajando en Ubuntu. Desde esta consola pueden usar comandos de bash, instalar software y trabajar como lo harían en una máquina Linux normal.

## Más información y resolución de problemas:

Guía oficial de WSL: [https://learn.microsoft.com/es-es/windows/wsl/install](https://learn.microsoft.com/es-es/windows/wsl/install)