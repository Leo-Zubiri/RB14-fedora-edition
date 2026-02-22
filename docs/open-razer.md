# OpenRazer

Control de iluminación RGB y periféricos Razer en Linux.

## Instalación (Fedora 41+)

### 1. Agregar el repositorio oficial

```bash
sudo dnf config-manager addrepo --from-repofile=https://openrazer.github.io/hardware:razer.repo
```

### 2. Instalar OpenRazer

```bash
sudo dnf install openrazer-meta
```

### 3. Instalar Polychromatic (GUI)

```bash
sudo dnf config-manager addrepo --from-repofile=https://openrazer.github.io/hardware:razer.repo
sudo dnf install polychromatic
```

### 4. Agregar tu usuario al grupo `plugdev`

```bash
sudo gpasswd -a $USER plugdev
```

### 5. Reiniciar

```bash
reboot
```

## Notas

- El grupo `plugdev` es necesario para que el daemon pueda acceder a los dispositivos sin root.
- Si el daemon no arranca: `systemctl --user status openrazer-daemon`
- Logs: `~/.local/share/openrazer/logs/razer.log`

---

[← Volver al README](../README.md)
