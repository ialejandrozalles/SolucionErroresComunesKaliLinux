# Solución: Monitor conectado a tarjeta gráfica NVIDIA no reconocido en Kali Linux

## 📋 Índice de contenidos
- [Descripción del Problema](#descripción-del-problema)
- [Diagnóstico Inicial](#diagnóstico-inicial)
- [Solución Paso a Paso](#solución-paso-a-paso)
- [Verificación](#verificación)
- [Prevención de problemas futuros](#prevención-de-problemas-futuros)
- [Conclusión](#conclusión)

## 📝 Descripción del Problema

Tras realizar una **actualización completa** de Kali Linux o tras una **instalación desde cero** del sistema operativo, la segunda pantalla conectada a la tarjeta gráfica dejó de funcionar. El sistema solo detectaba y utilizaba la salida principal HDMI-1 de la placa base.

## 🔍 Diagnóstico Inicial

### Hardware detectado

```bash
$ xrandr
Screen 0: minimum 320 x 200, current 1366 x 768, maximum 16384 x 16384
HDMI-1 connected primary 1366x768+0+0 (normal left inverted right x axis y axis) 410mm x 230mm
   1366x768      59.79*+
   1920x1080     60.00    59.94  
   1280x720      60.00    59.94  
   1024x768      75.03    70.07    60.00  
   ...
DP-1 disconnected (normal left inverted right x axis y axis)
```

### Información de tarjetas gráficas

```bash
$ lspci | grep -i vga
00:02.0 VGA compatible controller: Intel Corporation CoffeeLake-S GT2 [UHD Graphics 630]
01:00.0 VGA compatible controller: NVIDIA Corporation GP107 [GeForce GTX 1050 Ti] (rev a1)
```

### Estado de los controladores de NVIDIA

```bash
$ nvidia-smi
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```

El sistema detectó:
- Configuración de GPU dual: Intel UHD Graphics 630 (integrada) y NVIDIA GeForce GTX 1050 Ti (dedicada)
- Controladores NVIDIA no funcionales 
- DisplayPort (DP-1) aparecía como “disconnected”

## 🛠️ Solución Paso a Paso

### 1. Eliminación completa de los controladores NVIDIA existentes (si se instalaron previamente)

Para garantizar una instalación limpia, eliminamos todos los paquetes relacionados con NVIDIA:

```bash
sudo apt-get purge 'nvidia-*' -y
```

### 2. Instalación de los controladores NVIDIA

Reinstalamos los controladores recomendados:

```bash
sudo apt install -y nvidia-driver nvidia-opencl-icd
```

Componentes instalados:
- `nvidia-driver`: Controlador principal
- `nvidia-kernel-dkms`: Módulos del kernel
- `nvidia-settings`: Utilidad de configuración

### 3. Instalación de encabezados del kernel

Instalamos los encabezados del kernel:

```bash
sudo apt install linux-headers-$(uname -r)
```

### 4. Reconstrucción de los módulos del kernel para NVIDIA

```bash
sudo dkms install -m nvidia-current/535.216.03 -k $(uname -r)
```

### 5. Habilitación del servicio persistente de NVIDIA

```bash
sudo systemctl enable nvidia-persistenced
```

### 6. Actualización del initramfs

```bash
sudo update-initramfs -u
```

## ✅ Verificación

### Verificación de los módulos instalados

```bash
ls -l /lib/modules/$(uname -r)/updates/dkms/nvidia*
```

### Verificación del hardware NVIDIA

```bash
lspci -v | grep -A15 NVIDIA
```

## 🔒 Prevención de problemas futuros

Para evitar incidencias similares tras actualizaciones:

1. **Antes de grandes actualizaciones**  
   - Respaldo de configuración de NVIDIA:  
     ```bash
     sudo cp /etc/X11/xorg.conf /etc/X11/xorg.conf.backup
     ```  
   - Anotar controladores en uso:  
     ```bash
     dpkg -l | grep nvidia
     ```
2. **Después de actualizaciones**  
   - Verificar módulos cargados:  
     ```bash
     lsmod | grep nvidia
     ```  
   - Comprobar estado:  
     ```bash
     nvidia-smi
     ```  
   - Revisar interfaz gráfica:  
     ```bash
     nvidia-settings
     ```

## 📊 Conclusión

El problema se originó por una incompatibilidad entre la actualización del kernel y los controladores NVIDIA existentes. La solución incluyó:
1. Eliminación de paquetes NVIDIA previos  
2. Instalación de controladores compatibles con el kernel actual  
3. Instalación de encabezados del kernel para compilar los módulos NVIDIA  
4. Reconstrucción y carga de los módulos del kernel  

Este procedimiento es aplicable a futuras actualizaciones del kernel en sistemas con GPU NVIDIA.  

> **Nota**: Reinicie el equipo tras aplicar estos cambios. La segunda pantalla debería detectarse automáticamente después del reinicio.