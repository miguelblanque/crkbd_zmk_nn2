# Corne V3 + nice!nano v2 — ZMK estable

Configuración para un **Corne V3 inalámbrico**, con una placa nice!nano v2 y una
batería Li-Po por mitad. Incluye OLED, 27 LED RGB por mitad, USB y Bluetooth.

![Corne V3 con nice!nano v2](crkbd.JPG)

## Base fijada

- Origen de la migración: `master` en el commit `bafd959`.
- ZMK: `v0.3.0`, fijado al commit
  `edf5c0814fd3ea202e43aad2d68fd32e882a518c`.
- Placa: `nice_nano_v2`.
- Shields: `corne_left` y `corne_right`.
- La automatización genera también `settings_reset`.

Tanto `config/west.yml` como el workflow están fijados al mismo commit. De este
modo una modificación futura de `main` en ZMK no cambia las fuentes utilizadas.

## Modo batería en Adjust (capa 3)

El modo batería ejecuta dos acciones, en este orden:

1. `RGB_OFF`: apaga y guarda el estado de los RGB.
2. `EP_OFF`: corta la salida VCC externa de las dos mitades para eliminar el
   consumo residual de los WS2812.

Los behaviors de RGB y alimentación externa son globales en un teclado split,
por lo que la orden llega a ambas mitades. El teclado, el escaneo de teclas y
Bluetooth siguen activos. En un Corne típico los OLED comparten esa salida VCC,
así que también se apagarán; si están alimentados desde otra línea, se apagarán
por inactividad.

Para activarlo desde la capa base:

1. Mantén pulsado **Lower**: tecla central del pulgar izquierdo.
2. Sin soltarla, mantén **Adjust**: tecla central del pulgar derecho.
3. Pulsa la posición física **F**.

Para salir, usa el mismo acceso a Adjust y pulsa la posición física **B**
(`EP_TOG`). Si también quieres volver a encender el RGB, pulsa después la
posición física **G** (`RGB_TOG`).

ZMK guarda el estado de la alimentación externa en flash. Si reinicias o
actualizas el firmware y LED/OLED siguen apagados, no es un fallo: usa
**Lower + Adjust + B** para recuperar VCC.

| Posición física en Adjust | Acción |
| --- | --- |
| `F` | Modo batería: RGB off + VCC externo off |
| `G` | Alternar RGB |
| `B` | Alternar VCC externo |
| `V` / `-` | Bootloader izquierdo / derecho |
| `Shift` derecho | Reinicio del sistema |

Además, el firmware arranca con RGB apagado, lo apaga al desconectar USB, limita
su brillo al 25 %, apaga la pantalla tras 30 segundos y entra en sueño profundo
tras 15 minutos de inactividad.

## Compilar con GitHub Actions

1. Sube la rama a GitHub.
2. Abre **Actions → Build ZMK firmware → Run workflow** y elige la rama.
3. Descarga el artefacto del run correcto.

El ZIP debe contener tres imágenes claramente identificadas:

- `crkbd-corne-left-nice-nano-v2.uf2`
- `crkbd-corne-right-nice-nano-v2.uf2`
- `crkbd-settings-reset-nice-nano-v2.uf2`

## Compilar localmente

ZMK `v0.3.0` utiliza Zephyr SDK `0.16.3`. Desde un workspace que contenga este
repositorio como `config`:

```sh
python3 -m venv .venv
. .venv/bin/activate
pip install west
west init -l config/config
west update
west zephyr-export
pip install -r zephyr/scripts/requirements.txt
```

Con el Zephyr SDK configurado, compila cada imagen desde la raíz del workspace:

```sh
west build -s zmk/app -d build/left -p always -b nice_nano_v2 -- \
  -DSHIELD=corne_left -DZMK_CONFIG="$PWD/config/config"

west build -s zmk/app -d build/right -p always -b nice_nano_v2 -- \
  -DSHIELD=corne_right -DZMK_CONFIG="$PWD/config/config"

west build -s zmk/app -d build/reset -p always -b nice_nano_v2 -- \
  -DSHIELD=settings_reset
```

Los UF2 quedan en `build/<mitad>/zephyr/zmk.uf2`.

## Instalación limpia y emparejamiento

Usa este procedimiento al venir del firmware experimental antiguo o si las
mitades no se encuentran:

1. Retira cualquier cable TRRS entre las mitades y desconecta la batería de la
   mitad que no estés manipulando.
2. En la mitad derecha, pulsa Reset dos veces rápidamente. Cuando aparezca la
   unidad `NICENANO`, copia `settings_reset`; después vuelve a entrar al
   bootloader y copia el UF2 derecho.
3. Repite el reset de ajustes y luego el UF2 normal en la mitad izquierda.
4. Enciende primero la derecha y después la izquierda.
5. En el ordenador, elimina el emparejamiento anterior llamado `crkbd` y vuelve
   a emparejar. La mitad izquierda es la central y es la que se conecta al host.

El volumen `NICENANO` suele desmontarse al terminar y el sistema operativo puede
mostrar un error de copia aunque el flasheo haya concluido correctamente.

## Pruebas antes de dar la versión por estable

- Las 42 teclas funcionan y las capas Lower, Raise y Adjust son accesibles.
- Las dos mitades se enlazan sin cable y `crkbd` escribe por Bluetooth.
- USB y Bluetooth cambian correctamente desde Adjust.
- Los cinco perfiles Bluetooth seleccionan, emparejan y se pueden limpiar.
- Los dos OLED muestran estado y se apagan por inactividad.
- Los 27 RGB de cada mitad responden; al desconectar USB quedan apagados.
- **Lower + Adjust + F** apaga RGB y VCC en ambas mitades.
- Tras 15 minutos, una tecla despierta cada mitad y la conexión se recupera.
- Se comprueba el porcentaje de batería durante al menos un ciclo de uso.

## Baterías 301230 de 110 mAh: seguridad

- Usa solamente Li-Po recargable de **3,7 V**, una por cada mitad.
- Comprueba con un multímetro la polaridad real antes de conectar o soldar:
  `B+` es positivo y `B-` es negativo. No confíes únicamente en el color o en
  la orientación de un JST.
- Flashea y prueba primero el teclado por USB, antes de conectar las baterías.
- No uses TRRS para unir dos nice!nano alimentados. Tampoco conectes las mitades
  eléctricamente mientras una está alimentada por batería o USB.
- No realices puentes para aumentar la corriente del cargador con una batería de
  110 mAh. Vigila la primera carga y deja de usar una celda que se caliente,
  hinche o esté dañada.

Referencias oficiales: [ZMK v0.3.0](https://github.com/zmkfirmware/zmk/releases/tag/v0.3.0),
[estados de bajo consumo de ZMK](https://zmk.dev/docs/features/low-power-states),
[teclados split en ZMK](https://zmk.dev/docs/features/split-keyboards),
[documentación de nice!nano](https://nicekeyboards.com/docs/nice-nano/),
[inicio con nice!nano](https://nicekeyboards.com/docs/nice-nano/getting-started/) y
[FAQ de nice!nano](https://nicekeyboards.com/docs/nice-nano/faq/).

## Volver atrás

No borres la rama. Si aparece un problema, vuelve a flashear ambos UF2 de la
última release validada. Para inspeccionar exactamente el `master` anterior a
esta migración: `git switch master` (`bafd959`). Ese commit sirve como referencia
histórica, pero no se considera compatible con el ZMK actual.

## Publicar la rama en GitHub

Desde una copia Git que conserve el historial:

```sh
git push -u origin fix/zmk-stable-nice-nano-v2
```

Después abre un pull request de `fix/zmk-stable-nice-nano-v2` hacia `master`.
No mezcles `develop`: contiene las pruebas anteriores. Crea el tag `v1.0.0`
solamente después de completar la validación física indicada arriba.
