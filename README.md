# Razer Blade 14 2023 — Fedora Linux

> Guía basada en experiencia real — Fedora 43 en el RZ09-0482

<!-- screenshot aquí -->

```
             .',;::::;,'.                 razer@blade
         .';:cccccccccccc:;,.             -----------
      .;cccccccccccccccccccccc;.          OS: Fedora Linux 43 (Workstation Edition) x86_64
    .:cccccccccccccccccccccccccc:.        Host: Blade 14 - RZ09-0482 (9.04)
  .;ccccccccccccc;.:dddl:.;ccccccc;.      Kernel: Linux 6.18.12-200.fc43.x86_64
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.     Packages: 2347 (rpm)
.:ccccccccccccc;KMMc;cc;xMMc;ccccccc:.    Shell: bash 5.3.0
,cccccccccccccc;MMM.;cc;;WW:;cccccccc,    Display (TMX0003): 2560x1600 @ 1.67x in 14", 240 Hz [Built-in]
:cccccccccccccc;MMM.;cccccccccccccccc:    DE: GNOME 49.4
:ccccccc;oxOOOo;MMM000k.;cccccccccccc:    WM: Mutter (Wayland)
cccccc;0MMKxdd:;MMMkddc.;cccccccccccc;    CPU: AMD Ryzen 9 7940HS (16) @ 5.26 GHz
ccccc;XMO';cccc;MMM.;cccccccccccccccc'    GPU 1: NVIDIA GeForce RTX 4070 Max-Q / Mobile [Discrete]
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;     GPU 2: AMD Radeon 780M Graphics [Integrated]
ccccc;0MNc.ccc.xMMd;ccccccccccccccc;      Memory: 6.50 GiB / 30.55 GiB (21%)
cccccc;dNMWXXXWM0:;cccccccccccccc:,       Disk (/): 7.93 GiB / 951.28 GiB (1%) - btrfs
cccccccc;.:odl:.;cccccccccccccc:,.        Battery (Blade): 79% [AC Connected]
ccccccccccccccccccccccccccccc:'.          Locale: en_US.UTF-8
```

---

## Hardware

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

## Guías

| | Tema | Descripción |
|---|---|---|
| 🐧 | [Instalación de Fedora 43](docs/fedora-install.md) | USB booteable, BIOS, particionado btrfs |
| 🎮 | [Drivers NVIDIA + Wayland](docs/nvidia-wayland.md) | RPM Fusion, CUDA, verificación |
| 🔊 | [Fix altavoces internos](rb14_speakers/rb14_speakers_service.md) | Servicio systemd para Realtek ALC298 |
| 🐍 | [OpenRazer](docs/open-razer.md) | Control de iluminación y periféricos RGB |
| 🔋 | [Batería al 80%](docs/battery.md) | Battery Care integrado del equipo |
