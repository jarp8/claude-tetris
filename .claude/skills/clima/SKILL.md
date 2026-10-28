---
name: clima
description: Consulta el clima actual y pronóstico de la ubicación local del usuario (o de una ciudad indicada). Usar cuando pida el clima, temperatura, lluvia o pronóstico.
argument-hint: "[ciudad opcional]"
allowed-tools: Bash(curl *)
---

# Clima local

Obtiene el clima con wttr.in (sin API key). Sin argumento, wttr.in geolocaliza por IP.

## Pasos

1. Ciudad = `$ARGUMENTS` (si vacío, dejar vacío para autodetectar por IP). Reemplazar espacios por `+`.
2. Ejecutar:

```bash
curl -s "https://wttr.in/<ciudad>?format=j1&lang=es"
```

3. Del JSON extraer:
   - `nearest_area[0].areaName[0].value` → ubicación
   - `current_condition[0]`: `temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `lang_es[0].value` (descripción)
   - `weather[0..2]`: `date`, `mintempC`, `maxtempC`, y `hourly[*].chanceofrain` (máximo) para probabilidad de lluvia
4. Responder en español, breve:
   - Línea actual: ubicación, temp (sensación), descripción, humedad, viento
   - Pronóstico hoy/mañana/pasado: min–max °C, % lluvia máx
   - Si lluvia ≥ 50% o temp ≥ 32 °C / ≤ 5 °C, añadir una recomendación corta

## Notas

- Si `curl` falla o devuelve algo no-JSON, informar el error; no inventar datos.
- Usar unidades métricas (°C, km/h).
- La geolocalización por IP puede ser imprecisa (VPN); si la ubicación detectada parece incorrecta, pedir la ciudad.
