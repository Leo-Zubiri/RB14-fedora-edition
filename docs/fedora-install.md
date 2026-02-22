# Instalación de Fedora 43

## Lo que necesitas

- USB de mínimo 8 GB
- ISO de Fedora Workstation: https://fedoraproject.org/workstation/download
- Balena Etcher para grabar el USB: https://etcher.balena.io

## Crear el USB booteable

1. Abre Balena Etcher
2. Selecciona la ISO de Fedora
3. Selecciona el USB
4. Haz clic en **Flash**

## Arrancar desde USB

1. Apaga el equipo
2. Enciende y presiona **F2** repetidamente para entrar al BIOS
3. En el BIOS, desactiva **Secure Boot** (necesario para los drivers de NVIDIA)
4. Guarda y reinicia
5. Al arrancar presiona **F12** para el menú de boot y selecciona el USB

## Particionado

Selecciona **"Use entire disk"** para dejar que el instalador configure todo automáticamente. Esto creará:

| Partición | Tamaño | Formato |
|---|---|---|
| `/boot/efi` | 629 MB | EFI |
| `/boot` | 2.15 GB | ext4 |
| `/` | ~1 TB | btrfs subvolume |
| `/home` | ~1 TB | btrfs subvolume |

> **Importante:** Si el particionado automático no usa todo el disco, el espacio insuficiente en `/` causará fallos al instalar paquetes. Asegúrate de que `/` tenga acceso a la mayor parte del disco.

---

[← Volver al README](../README.md)
