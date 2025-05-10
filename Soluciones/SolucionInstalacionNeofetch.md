# Solución: Instalación de Neofetch en Kali Linux cuando no está disponible en repositorios

## 📋 Índice de contenidos
- [Descripción del Problema](#descripción-del-problema)
- [Diagnóstico Inicial](#diagnóstico-inicial)
- [Solución Paso a Paso](#solución-paso-a-paso)
- [Verificación](#verificación)
- [Prevención de problemas futuros](#prevención-de-problemas-futuros)
- [Conclusión](#conclusión)

## 📝 Descripción del Problema

Al intentar instalar **Neofetch** en Kali Linux, el sistema no pudo ubicar el paquete en los repositorios estándar. A pesar de actualizar las listas de paquetes, la instalación mediante `apt` o `apt-get` no tuvo éxito.

## 🔍 Diagnóstico Inicial

### Intento de instalación con el gestor de paquetes

```bash
$ sudo apt install neofetch -y
```
```bash
[sudo] password for izai-zalles: 
Error: Unable to locate package neofetch
```

### Verificación tras actualización de repositorios

```bash
$ sudo apt update && sudo apt install neofetch -y
```
```bash
Hit:1 https://dl.google.com/linux/chrome/deb stable InRelease
Hit:2 http://http.kali.org/kali kali-rolling InRelease                                         
Hit:3 https://packages.microsoft.com/repos/code stable InRelease                               
Ign:4 https://releases.warp.dev/linux/deb stable InRelease              
Hit:5 https://releases.warp.dev/linux/deb stable Release
All packages are up to date.    
Error: Unable to locate package neofetch
```

### Segundo intento con apt-get

```bash
$ sudo apt-get update && sudo apt-get install neofetch -y
```
```bash
Hit:1 https://dl.google.com/linux/chrome/deb stable InRelease
Hit:2 http://http.kali.org/kali kali-rolling InRelease                                                                        
Hit:3 https://packages.microsoft.com/repos/code stable InRelease    
Ign:4 https://releases.warp.dev/linux/deb stable InRelease
Hit:5 https://releases.warp.dev/linux/deb stable Release
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package neofetch
```

El sistema detectó:
- Los repositorios estaban actualizados
- El paquete "neofetch" no estaba disponible en los repositorios configurados
- Los intentos de instalación mediante apt y apt-get fallaron con el mismo error

## 🛠️ Solución Paso a Paso

### 1. Instalación alternativa desde el repositorio de GitHub

Ya que el paquete no estaba disponible en los repositorios oficiales, recurrimos a instalarlo directamente desde su repositorio oficial en GitHub:

```bash
git clone https://github.com/dylanaraps/neofetch.git && cd neofetch && sudo make install
```

Este comando ejecutó los siguientes pasos:
- Clonación del repositorio oficial de neofetch
- Navegación al directorio del proyecto
- Instalación usando make

### 2. Gestión de problemas encontrados

Durante el proceso, se identificó que el archivo descargado era ejecutable y no un directorio:

```bash
$ ls -la neofetch
-rwxrwxr-x 1 izai-zalles izai-zalles 376936 May 10 15:38 neofetch
```

### 3. Instalación manual del ejecutable

Colocamos el ejecutable en la ubicación correcta del sistema y aseguramos que tuviera permisos de ejecución:

```bash
sudo mv neofetch /usr/local/bin/ && sudo chmod +x /usr/local/bin/neofetch
```

## ✅ Verificación

Para verificar la instalación exitosa, ejecutamos el programa:

```bash
$ neofetch
```

La salida mostró la información del sistema correctamente, confirmando que la instalación fue exitosa:
- Sistema operativo: Kali GNU/Linux Rolling x86_64
- Kernel: 6.12.25-amd64
- DE: GNOME 48.0
- Otros datos del sistema (CPU, GPU, memoria, etc.)

## 🔒 Prevención de problemas futuros

Para evitar incidencias similares en el futuro:

1. **Antes de intentar instalar paquetes**
   - Verificar la disponibilidad en los repositorios oficiales:
     ```bash
     apt search neofetch
     ```
   - Comprobar repositorios habilitados:
     ```bash
     cat /etc/apt/sources.list
     ls /etc/apt/sources.list.d/
     ```

2. **Alternativas de instalación**
   - Considerar el uso de paquetes snap o flatpak para aplicaciones populares
   - Verificar la documentación oficial del software para métodos alternativos de instalación
   - Para herramientas de línea de comandos como neofetch, GitHub suele ser una fuente confiable

## 📊 Conclusión

El problema se originó porque el paquete neofetch no estaba disponible en los repositorios configurados en el sistema. La solución incluyó:

1. Descargar directamente desde el repositorio oficial de GitHub
2. Instalación manual del ejecutable en un directorio del sistema
3. Verificación de su correcto funcionamiento

Este procedimiento es aplicable a cualquier paquete que no esté disponible en los repositorios estándar pero que se pueda encontrar en GitHub u otras fuentes oficiales.

> **Nota**: Este método de instalación no gestiona automáticamente las dependencias como lo haría apt, pero en el caso de neofetch, una herramienta relativamente simple, esto no representó un problema.

