---
name: clima
description: Obtiene el clima actual de una ciudad (por defecto Paraná, Entre Ríos, Argentina) usando Open-Meteo. Usar cuando el usuario pregunte por el clima, temperatura, tiempo, lluvia o viento, o invoque /clima [ciudad].
---

# Clima actual

Fuente: [Open-Meteo](https://open-meteo.com) (gratis, sin API key).

## Pasos

1. Ubicación:
   - Sin argumento → Paraná, Entre Ríos, Argentina (`lat=-31.7319`, `lon=-60.5238`, tz `America/Argentina/Cordoba`).
   - Con ciudad en `$ARGUMENTS` → geocodificar:
     ```bash
     curl -s -A "clima-skill/1.0" "https://geocoding-api.open-meteo.com/v1/search?name=<CIUDAD_URLENCODED>&count=1&language=es"
     ```
     Tomar `latitude`, `longitude`, `timezone`, `name`, `admin1`, `country` del primer resultado. Si no hay resultados, avisar y parar.

2. Consultar clima actual:
   ```bash
   curl -s -A "clima-skill/1.0" "https://api.open-meteo.com/v1/forecast?latitude=<LAT>&longitude=<LON>&current=temperature_2m,relative_humidity_2m,apparent_temperature,weather_code,wind_speed_10m,wind_direction_10m,precipitation&daily=temperature_2m_max,temperature_2m_min,precipitation_probability_max&forecast_days=1&timezone=<TZ>"
   ```

3. Traducir `weather_code` (WMO):
   | Código | Estado |
   |---|---|
   | 0 | Despejado ☀️ |
   | 1, 2, 3 | Mayormente despejado / Parcialmente nublado / Nublado ⛅ |
   | 45, 48 | Niebla 🌫️ |
   | 51, 53, 55, 56, 57 | Llovizna 🌦️ |
   | 61, 63, 65, 66, 67 | Lluvia leve / moderada / fuerte 🌧️ |
   | 71, 73, 75, 77 | Nieve ❄️ |
   | 80, 81, 82 | Chaparrones leves / moderados / violentos 🌧️ |
   | 95, 96, 99 | Tormenta ⛈️ |

4. Dirección del viento: convertir grados a punto cardinal (N, NE, E, SE, S, SO, O, NO).

## Formato de respuesta (español, conciso)

```
🌡️ Clima en <Ciudad>, <Provincia> — <HH:MM>
<Estado>
Temp: <T>°C (ST <ST>°C) · Mín/Máx: <min>/<max>°C
Humedad: <H>% · Viento: <V> km/h <dir>
Precipitación: <P> mm · Prob. lluvia hoy: <PP>%
```

## Reglas

- User-Agent genérico (`clima-skill/1.0`). Nunca incluir email ni datos del usuario en headers, URL o payload.
- Si la API falla, informar el error brevemente; no inventar datos.
