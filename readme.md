# CRKBD USB rescue — Corne V3 + nice!nano v2

Rama temporal para recuperar la mitad izquierda central cuando el bootloader
`NICENANO` funciona pero la aplicación ZMK no aparece como teclado USB.

Está basada en `master`, con ZMK oficial `v0.3.0` fijado a un commit exacto.
Su objetivo inicial es disponer de un teclado cableado fiable y aislar un
posible problema de alimentación del OLED, RGB o rail VCC.

La automatización de GitHub Actions está fijada por separado a una revisión
compatible con Node.js 24. De este modo, el proceso de compilación no usa las
acciones antiguas basadas en Node.js 20, pero el código del firmware continúa
siendo exactamente ZMK `v0.3.0`.

## Funciones activas

- Matriz y distribución QWERTY inglesa original.
- Teclado USB HID.
- Bluetooth necesario para el enlace split.
- Perfiles y borrado de ajustes de ZMK.
- `Lower + F` fuerza `OUT_USB` usando únicamente la mitad izquierda.

## Funciones desactivadas deliberadamente

- OLED y pantalla personalizada.
- RGB y controlador WS2812.
- Control del rail de alimentación VCC externo.
- Suspensión profunda.
- Distribución española y macros nuevas.

El dispositivo se identifica como `CRKBD-RESCUE`. Mientras esta imagen esté
instalada es normal que OLED y LED permanezcan apagados.

## Generar los UF2

Sube la rama `test/usb-rescue-left` y abre **Actions → Build ZMK firmware**.
El artefacto `firmware` debe contener:

- `crkbd-settings-reset-nice-nano-v2.uf2`;
- `crkbd-rescue-left-nice-nano-v2.uf2`;
- `crkbd-rescue-left-debug-nice-nano-v2.uf2`.

## Recuperación

1. Apaga y desconecta la mitad derecha y ambas baterías.
2. Entra en el bootloader izquierdo con doble Reset.
3. Flashea `crkbd-settings-reset-nice-nano-v2.uf2`.
4. Vuelve a entrar en el bootloader y flashea
   `crkbd-rescue-left-nice-nano-v2.uf2`.
5. Espera 20 segundos y comprueba:

   ```sh
   hidutil list | grep -i 'CRKBD-RESCUE'
   ```

6. Prueba a escribir y realiza un único reinicio.

No conectes las dos mitades mediante TRRS. La derecha ZMK es periférica y no
se presenta por sí sola como un teclado USB central.

Consulta [RESCUE.md](RESCUE.md) para los detalles técnicos.
