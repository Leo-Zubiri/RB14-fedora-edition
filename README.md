# Fedora Linux en Razer Blade 14 2023 (RZ09-0482)

Guía basada en experiencia real instalando y configurando Fedora 43 en el Razer Blade 14 2023.

## Especificaciones del hardware

| Componente | Detalle |
|---|---|
| Modelo | Razer Blade 14 — RZ09-0482 |
| CPU | AMD Ryzen 9 7940HS (16 hilos) @ 5.26 GHz |
| GPU Discreta | NVIDIA GeForce RTX 4070 Max-Q / Mobile |
| GPU Integrada | AMD Radeon 780M Graphics |
| RAM | 32 GB |
| Almacenamiento | 1 TB NVMe Samsung MZVL21T0HCLR |
| Pantalla | 2560×1600 @ 240 Hz, 14" |

---

## 1. Instalación de Fedora 43

### Lo que necesitas
- USB de mínimo 8 GB
- ISO de Fedora Workstation: https://fedoraproject.org/workstation/download
- Balena Etcher para grabar el USB: https://etcher.balena.io

### Crear el USB booteable
1. Abre Balena Etcher
2. Selecciona la ISO de Fedora
3. Selecciona el USB
4. Haz clic en **Flash**

### Arrancar desde USB en el Razer Blade 14
1. Apaga el equipo
2. Enciende y presiona **F2** repetidamente para entrar al BIOS
3. En el BIOS, desactiva **Secure Boot** (necesario para los drivers de NVIDIA)
4. Guarda y reinicia
5. Al arrancar presiona **F12** para el menú de boot y selecciona el USB

### Particionado
Selecciona **"Use entire disk"** para dejar que el instalador configure todo automáticamente. Esto creará:

- `/boot/efi` — 629 MB (EFI)
- `/boot` — 2.15 GB (ext4)
- `/` — 1 TB compartido en btrfs subvolume
- `/home` — 1 TB compartido en btrfs subvolume

> **Importante:** Si el particionado automático no usa todo el disco, el espacio insuficiente en `/` causará fallos al instalar paquetes. Asegúrate de que `/` tenga acceso a la mayor parte del disco.

---

## 2. Drivers NVIDIA

> **No uses la documentación oficial de NVIDIA** (docs.nvidia.com/datacenter), ya que está orientada a GPUs Tesla de servidores. Para GPUs consumer como la RTX 4070 usa **RPM Fusion**.

### Paso 1: Habilitar RPM Fusion

```bash
sudo dnf install \
  https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### Paso 2: Actualizar el sistema

```bash
sudo dnf update -y
```

### Paso 3: Instalar los drivers

```bash
sudo dnf install akmod-nvidia
```

### Paso 4: Compilar el módulo y reiniciar

```bash
sudo akmods --force && sudo dracut --force && reboot
```

### Paso 5: Instalar herramientas CUDA (nvidia-smi)

```bash
sudo dnf install xorg-x11-drv-nvidia-cuda
```

### Verificar instalación

```bash
nvidia-smi
```

Deberías ver algo como:

```
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.119.02    Driver Version: 580.119.02    CUDA Version: 13.0               |
| GPU  Name                     Temp   Pwr:Usage/Cap  Memory-Usage  GPU-Util              |
|   0  NVIDIA GeForce RTX 4070 ...  47C    16W / 83W   13MiB / 8188MiB   14%             |
+-----------------------------------------------------------------------------------------+
```

> Los drivers compilados con `akmod` se recompilan automáticamente con cada actualización de kernel.

---

## 3. Wayland y NVIDIA

Fedora 43 con los drivers de RPM Fusion 580+ funciona correctamente con **Wayland**. No se requiere configuración adicional.

Verifica que estás en Wayland:

```bash
echo $XDG_SESSION_TYPE
# Resultado esperado: wayland
```

---

## 4. Fix de altavoces internos (Realtek ALC298)

El codec Realtek ALC298 requiere una secuencia de comandos `hda-verb` que debe reaplicarse en cada arranque y al salir de suspensión/hibernación. Este repo incluye un script que automatiza esto mediante un servicio systemd.

### Instalación

Desde el directorio del repo:

```bash
sudo bash rb14_speakers/install_rb14_speakers.sh install
```

El instalador:
- Instala `alsa-tools` (`hda-verb`) automáticamente para dnf, apt, pacman, zypper y emerge
- Crea el servicio systemd `/etc/systemd/system/rb14-speakers.service`
- Instala un hook systemd-sleep para re-aplicar el fix tras suspensión/hibernación

### Desinstalar

```bash
sudo bash rb14_speakers/install_rb14_speakers.sh uninstall
```

### Gestión del servicio

```bash
systemctl status rb14-speakers.service
journalctl -u rb14-speakers.service -n 50 --no-pager
sudo systemctl restart rb14-speakers.service
```

### Troubleshooting: dispositivo de audio incorrecto

Si `/dev/snd/hwC2D0` no existe, busca el dispositivo correcto:

```bash
ls /dev/snd/hw*
sudo nano /usr/local/lib/rb14-speakers/fix.sh   # reemplaza C2D0 con el valor correcto
sudo systemctl restart rb14-speakers.service
```

Lee la documentación completa en [rb14_speakers/rb14_speakers_service.md](./rb14_speakers/rb14_speakers_service.md).

---

## 5. Batería — Límite al 80%

El Razer Blade 14 tiene una función de **Battery Care** que limita la carga máxima al 80% para proteger la vida útil de la batería. Esto es completamente normal.

Cuando la batería está cerca del 80% y el cargador está conectado, el sistema reportará `not charging`, lo cual es correcto.

```bash
cat /sys/class/power_supply/BAT0/status    # Charging / Not charging / Full
cat /sys/class/power_supply/BAT0/capacity  # Porcentaje actual
```

---

## 6. Seguridad y mantenimiento

### Firewall
Fedora incluye `firewalld` activo por defecto:

```bash
sudo firewall-cmd --state
# Resultado esperado: running
```

No se requiere configuración adicional para uso personal.

### Actualizaciones
Mantener el sistema actualizado es la medida de seguridad más importante:

```bash
sudo dnf update
```

---

## 7. Comandos útiles de diagnóstico

```bash
# Ver espacio en disco
df -h /

# Ver información del sistema
fastfetch

# Ver kernel activo
uname -r

# Ver sesión gráfica
echo $XDG_SESSION_TYPE

# Ver paquetes nvidia instalados
rpm -qa | grep nvidia

# Ver errores del sistema
sudo dmesg | grep -i error | tail -20
```

---

*Guía basada en instalación real — Febrero 2026*
