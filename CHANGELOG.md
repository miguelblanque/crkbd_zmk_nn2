# Changelog

## v1.0.0-rc.1 — 2026-09-04

Primera candidata basada en una versión estable y reproducible de ZMK.

### Cambios

- Migración del fork experimental de ZMK a ZMK `v0.3.0` oficial, fijado por SHA.
- Workflow de GitHub Actions fijado a la misma revisión.
- Build separado y nombrado para Corne izquierdo, derecho y `settings_reset`.
- Modo batería global en Adjust + posición `F`: RGB off seguido de `EP_OFF`.
- RGB apagado al arrancar y al desconectar USB; brillo máximo del 25 %.
- Pantallas en blanco tras 30 segundos y sueño profundo tras 15 minutos.
- Eliminación de opciones OLED/LVGL obsoletas y del aumento de radio a +8 dBm.
- Definición explícita de los 27 WS2812 por mitad del Corne V3.

### Validación necesaria en hardware

- Comprobar matriz, OLED y los 27 RGB en ambas mitades.
- Verificar emparejamiento split y Bluetooth tras limpiar ajustes.
- Verificar activación y recuperación del modo batería.
- Medir autonomía con las dos baterías 301230 de 110 mAh.
