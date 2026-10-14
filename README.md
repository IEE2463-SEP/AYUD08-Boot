# AYUD08 · Boot

> Debug y booteo en el Zynq: recorrer un programa línea por línea con el modo **Debug** de Vitis, y grabar un **BOOT.bin** en la memoria flash QSPI de la ZyboZ7 para que el código corra al energizar la tarjeta, sin PC conectado.

Ayudante a cargo: **José Agustín Milling** — ja.milling@uc.cl

Esta ayudantía se divide en dos partes:

| | Qué es | Cuándo se hace |
| :--- | :--- | :--- |
| **Actividades previas** | Un *Block Design* con el Zynq7 Processing System y un AXI GPIO (switches y LEDs), y sobre él un proyecto de Vitis con `boot_sw.c`. Además, ejecutarlo en modo **Debug** y grabarlo en la memoria flash con un `BOOT.bin`. | **Antes**, en su casa. |
| **Ejercicio propuesto** | Analizar con el depurador qué hace realmente `boot_sw.c`, convertir la Zybo en una **caja fuerte** que se abre con una secuencia secreta de switches, y grabarla en la flash para que funcione sin PC. | **Durante** la ayudantía. |

---

## 🎥 Antes de la ayudantía

Debes llegar a la sesión con las **actividades previas ya desarrolladas y funcionando en la tarjeta**: un proyecto de Vivado para la **Zybo Z7-10** con un *Block Design* que conecta el **Zynq7 Processing System** y un **AXI GPIO** de dos canales (`sws_4bits` y `leds_4bits`); el archivo de *constraints* incluido y editado según ese *Block Design*; el *HDL Wrapper* y el *bitstream* generados y el hardware exportado. Ya en Vitis, un *Platform Project* creado con el `.xsa` y buildeado, y un *Application Project* vacío donde se agrega `boot_sw.c`. Sobre ese proyecto, saber entrar al modo **Debug** (*Debug As → Launch Hardware*) y avanzar línea por línea con *Step Over* (F6), y generar el `BOOT.bin` (*Create Boot Image*) para grabarlo en la flash (*Xilinx → Program Flash*, con *Flash Type* `qspi-x4-single`) y bootear la Zybo con el jumper **JP5 en QSPI**. Todo el detalle, incluido el diagrama del *Block Design* final, está en el [enunciado](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/AYUD08-Boot.pdf).

Estas actividades están resueltas paso a paso en este video, grabado el año **2023 por la ex ayudante del curso Catalina Sierra**:

[![Video de la ayudantía 08](https://img.youtube.com/vi/HEcVxrbAIDQ/hqdefault.jpg)](https://youtu.be/HEcVxrbAIDQ)

**Apoyarse en el video es opcional.** Puede seguirlo completo, usarlo sólo para destrabar un punto puntual, o resolver las actividades por su cuenta: eso lo decide usted. Lo que no es opcional es llegar con el diseño andando, porque el tiempo de la ayudantía se destinará por completo al ejercicio propuesto.

---

## 📂 Material

| Archivo | Descripción |
| :--- | :--- |
| [AYUD08-Boot.pdf](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/AYUD08-Boot.pdf) | Enunciado de la ayudantía: objetivos, actividades previas, pasos de debug y booteo, ejercicio propuesto y diagrama del *Block Design*. |
| [boot_sw.c](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/boot_sw.c) | Código en C de las actividades previas: lee los switches por el canal 1 del AXI GPIO, busca la combinación en un arreglo con `CheckOptions`, la transforma y la escribe en los LEDs por el canal 2. Es el código que se analiza con el depurador en la Parte A y el punto de partida de la caja fuerte. |
| [Zybo-Z7-Master.xdc](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/Zybo-Z7-Master.xdc) | Constraints de la tarjeta, con los 4 switches (`sws_4bits`) y los 4 LEDs (`leds_4bits`) habilitados. |
| [Ay_Boot.zip](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/Ay_Boot.zip) | Proyecto completo de la ayudantía: el proyecto de Vivado y el *workspace* de Vitis (`Boot_HW`, `Boot_SW`, `Boot_SW_system`), con el `.bif` y el `BOOT.bin` ya generados. |

---

## 🧪 Durante la ayudantía

El **ejercicio propuesto** es el trabajo de la sesión, y fue propuesto y desarrollado por el ayudante de este semestre, **José Agustín Milling**. La idea es convertir la Zybo en una **caja fuerte** que se abre sólo si se ingresa, con los switches, una secuencia secreta de combinaciones. Se usa exactamente el mismo hardware de las actividades previas, así que **no es necesario volver a Vivado**:

**1. Debug del código original.** Sin modificar `boot_sw.c`, poner un *breakpoint* en `XGpio_DiscreteWrite(&GPIO, 2, result3);`, agregar `sws`, `result`, `result2` y `result3` como *Expressions* y completar la tabla del enunciado para `0b0000`, `0b0101`, `0b0111` y `0b1111`. Con eso, responder qué hace realmente `CheckOptions` y si puede retornar −1 con este hardware, por qué con `sws = 0b1111` el valor de `result3` en el depurador no coincide con lo que se ve en los LEDs, y qué LEDs nunca se apagan.

**2. Caja fuerte.** Un nuevo archivo C (puede partir desde `boot_sw.c`) donde la clave es una secuencia de **4 combinaciones de switches** guardada en un arreglo, por ejemplo `{0b1000, 0b1100, 0b0110, 0b0011}`:

- Cuando los switches cambian, el programa espera **1 s** y lee el valor final: ese es el *intento*.
- Si el intento es correcto, los LEDs muestran el avance como una barra: `0001`, `0011`, `0111`, `1111`.
- Si es incorrecto, los 4 LEDs **parpadean rápido 3 veces** y se vuelve a empezar desde el primer paso.
- Al completar la clave, los 4 LEDs **parpadean lento 5 veces** y la caja se vuelve a cerrar.

> ⚠️ Es obligatorio usar **macros** (`#define` para el largo de la clave, los tiempos y los canales de la GPIO), **arreglos** (uno para la clave y otro para la barra, de modo que `progreso[indice]` sea lo que se escribe en los LEDs) y **funciones**: `int LeerIntento(int anterior);` y `void Parpadear(int patron, int veces, int semiperiodo_ms);`.

**3. Booteo de la caja fuerte.** Generar un nuevo `BOOT.bin` con la caja fuerte, grabarlo en la flash, cambiar **JP5 a QSPI** y comprobar que funciona **sin el PC conectado**. Responder qué tres archivos componen el `BOOT.bin`, en qué orden van y por qué el FSBL debe ser el primero.

> 💡 Para usar `usleep` hay que incluir `"sleep.h"`. Si la caja no se comporta como espera, entre al modo Debug y agregue `indice`, `intento` y `clave[indice]` como *Expressions*.

### 📤 Entrega y bonificación

> 📅 **Plazo excepcional:** como el material se publicó el miércoles en vez del lunes, el ejercicio propuesto de esta ayudantía se puede subir a **Canvas hasta el domingo 18 de octubre a las 23:59**, en vez del mismo día hasta las 14:50.

Entregarlo dentro de plazo otorga **una décima (+0,1)** en la nota del **Proyecto 2**.

---

<sub>IEE2463 · Sistemas Electrónicos Programables</sub>
