# Batería — Battery Care al 80%

El Razer Blade 14 tiene una función de **Battery Care** integrada en el firmware que limita la carga máxima al 80% para proteger la vida útil de la batería.

Cuando la batería está cerca del 80% con el cargador conectado, el sistema reportará `Not charging`. Esto es **completamente normal**.

## Verificar estado

```bash
cat /sys/class/power_supply/BAT0/status    # Charging / Not charging / Full
cat /sys/class/power_supply/BAT0/capacity  # Porcentaje actual
```

---

[← Volver al README](../README.md)
