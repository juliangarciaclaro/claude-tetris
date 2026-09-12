---
name: clima-local
description: >
  Consulta el clima actual (temperatura, condicion, viento, humedad) para
  Cucuta, Colombia (ciudad por defecto del usuario) o para otra ciudad si el
  usuario la menciona explicitamente, usando el servicio publico wttr.in sin
  necesidad de API key. Usar cuando el usuario pida el clima, el tiempo, la
  temperatura o el pronostico, o escriba /clima-local.
---

# Clima local

Obtiene el clima usando `curl` contra `wttr.in`, un servicio gratuito que no
requiere API key ni configuracion previa.

La ciudad por defecto del usuario es **Cucuta, Colombia**. La deteccion por
IP de wttr.in no es confiable (ubica segun la IP de salida de la red, no el
dispositivo), asi que no se usa.

## Uso

1. Si el usuario menciona una ciudad explicitamente, usala en la consulta.
   Si no menciona ninguna, usa `Cucuta` como ciudad por defecto (nunca omitas
   la ciudad para depender de la deteccion por IP).
2. Ejecuta uno de estos comandos con Bash:

   Resumen en una linea (rapido, ideal para respuestas cortas):
   ```bash
   curl -s "wttr.in/<CIUDAD>?format=%l:+%c+%t+(sensacion+%f),+humedad+%h,+viento+%w"
   ```
   Ejemplo por defecto (sin ciudad mencionada por el usuario):
   ```bash
   curl -s "wttr.in/Cucuta?format=%l:+%c+%t+(sensacion+%f),+humedad+%h,+viento+%w"
   ```

   Reporte visual de 3 dias (cuando el usuario pida detalle o pronostico):
   ```bash
   curl -s "wttr.in/<CIUDAD>?lang=es&T"
   ```

3. Reemplaza espacios en el nombre de la ciudad por `+` o `%20` (ej.
   `Buenos+Aires`, `Ciudad+de+Mexico`).
4. Si `curl` falla (sin conexion, timeout, ciudad no reconocida), informa al
   usuario del error puntual en vez de inventar datos de clima.
5. Presenta el resultado en español de forma breve, sin agregar campos que
   `wttr.in` no devolvio.

## Notas

- No requiere autenticacion ni variables de entorno.
- El formato `%l:+%c+%t...` usa los placeholders de wttr.in: `%l` ubicacion,
  `%c` condicion (icono/texto), `%t` temperatura, `%f` sensacion termica,
  `%h` humedad, `%w` viento.
- Para salida en JSON (si se necesita procesar datos), agregar `?format=j1`.
