# Fix obligatorio: gnome-software crash + inestabilidad NVIDIA/Wayland

> **Estado:** Bug activo en Fedora 43 — aplicar inmediatamente después de instalar.
> Sin este fix, GNOME Shell se reinicia aleatoriamente y los iconos del menú desaparecen.

## Síntomas

- Todos los iconos del menú de aplicaciones desaparecen de golpe
- Las apps fallan con `Program not found in $PATH`
- GNOME Shell se reinicia solo sin razón aparente
- El sistema parece "congelarse" momentáneamente y volver sin la sesión anterior

## Causas

1. **`gnome-software` 49.3** crashea en bucle en segundo plano — bug conocido en Fedora 43
2. **GNOME Shell** se reinicia por inestabilidad relacionada con NVIDIA, haciendo que todo "desaparezca" temporalmente
3. **NVIDIA RTX 4070** con errores ACPI y módulo sin firma verificada, causando inestabilidad en Wayland

## Fix — Aplicar una sola vez

### Paso 1: Enmascarar gnome-software

Evita que el daemon crashee en segundo plano y desestabilice GNOME Shell:

```bash
systemctl --user mask gnome-software.service
```

### Paso 2: Parámetros de estabilidad NVIDIA al kernel

```bash
sudo grubby --update-kernel=ALL --args="nvidia-drm.modeset=1 nvidia-drm.fbdev=1"
```

### Paso 3: Reiniciar

```bash
reboot
```

## Verificar que el fix está activo

Confirmar que el servicio está enmascarado:

```bash
systemctl --user status gnome-software.service
# Debe mostrar: masked
```

Confirmar parámetros de kernel aplicados:

```bash
cat /proc/cmdline | grep nvidia-drm
# Debe mostrar: nvidia-drm.modeset=1 nvidia-drm.fbdev=1
```

## Para el día a día

- Actualizaciones con `sudo dnf upgrade` (sin abrir GNOME Software)
- Si necesitas GNOME Software puntualmente:
  ```bash
  systemctl --user unmask gnome-software.service
  # usar GNOME Software...
  systemctl --user mask gnome-software.service
  ```
- Instalar apps directamente: `sudo dnf install <nombre-app>`

## Notas

- Refine **no** fue la causa del problema, aunque inicialmente se sospechó de él.
- Este fix es necesario hasta que Fedora corrija el bug de `gnome-software` 49.3 y/o NVIDIA mejore la estabilidad en Wayland.

---

[← Volver al README](../README.md) | [Drivers NVIDIA + Wayland](nvidia-wayland.md)
