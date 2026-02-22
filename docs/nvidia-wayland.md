# Drivers NVIDIA + Wayland

> Usa **RPM Fusion**, no la documentación oficial de NVIDIA (docs.nvidia.com/datacenter), que está orientada a GPUs Tesla de servidores.

## Instalación

### 1. Habilitar RPM Fusion

```bash
sudo dnf install \
  https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### 2. Actualizar el sistema

```bash
sudo dnf update -y
```

### 3. Instalar los drivers

```bash
sudo dnf install akmod-nvidia
```

### 4. Compilar el módulo y reiniciar

```bash
sudo akmods --force && sudo dracut --force && reboot
```

### 5. Instalar herramientas CUDA (nvidia-smi)

```bash
sudo dnf install xorg-x11-drv-nvidia-cuda
```

## Verificar instalación

```bash
nvidia-smi
```

Resultado esperado:

```
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.119.02    Driver Version: 580.119.02    CUDA Version: 13.0               |
| GPU  Name                     Temp   Pwr:Usage/Cap  Memory-Usage  GPU-Util              |
|   0  NVIDIA GeForce RTX 4070 ...  47C    16W / 83W   13MiB / 8188MiB   14%             |
+-----------------------------------------------------------------------------------------+
```

## Wayland

Fedora 43 con los drivers 580+ funciona correctamente con Wayland. No se requiere configuración adicional.

```bash
echo $XDG_SESSION_TYPE
# Resultado esperado: wayland
```

## Notas

- Los drivers compilados con `akmod` se recompilan automáticamente con cada actualización de kernel.
- Fedora detecta correctamente tanto la RTX 4070 como la AMD Radeon 780M integrada.

---

## Problemas conocidos

### Iconos del menú desaparecen y apps fallan con "Program not found in $PATH"

**Equipo:** Razer Blade 14 con RTX 4070, Fedora 43, GNOME en Wayland.

**Síntomas:**
- Todos los iconos del menú de aplicaciones desaparecen
- Las apps fallan con el mensaje `Program not found in $PATH`

**Causas:**

1. `gnome-software` 49.3 crashea repetidamente en segundo plano (bug conocido en Fedora 43)
2. GNOME Shell se reinicia por inestabilidad relacionada con NVIDIA, haciendo que todo "desaparezca" temporalmente
3. NVIDIA RTX 4070 con errores ACPI y módulo sin firma verificada, causando inestabilidad en Wayland

**Soluciones:**

Enmascarar el daemon de gnome-software para evitar que crashee en segundo plano:

```bash
systemctl --user mask gnome-software.service
```

Agregar parámetros de estabilidad NVIDIA al kernel:

```bash
sudo grubby --update-kernel=ALL --args="nvidia-drm.modeset=1 nvidia-drm.fbdev=1"
```

Reiniciar para aplicar los parámetros del kernel.

**Para el día a día:**

- Actualizaciones con `sudo dnf upgrade`
- Si necesitas GNOME Software puntualmente: unmask → usar → mask de nuevo
- O instalar apps directamente con `sudo dnf install <nombre-app>`

> **Nota:** Refine no fue la causa del problema, aunque inicialmente se sospechó de él.

---

[← Volver al README](../README.md)
