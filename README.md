# AYUD08 · Boot

> En esta ayudantía aprenderemos a cargar un código de BOOT a la Zybo programando su memoria flash por QSPI y ejercitaremos el modo Debug en Vitis, además del uso de funciones, macros y arreglos.

---

## 🎥 Antes de la ayudantía

Previo a nuestra ayudantía debes revisar este [video](https://youtu.be/HEcVxrbAIDQ) y ejecutarlo en tu casa.

[![Video de la ayudantía 08](https://img.youtube.com/vi/HEcVxrbAIDQ/hqdefault.jpg)](https://youtu.be/HEcVxrbAIDQ)

---

## 📂 Material

Los documentos asociados a la ejecución de este laboratorio son:

| Archivo | Descripción |
| :--- | :--- |
| [AYUD08-Guia Ejercicio](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/AYUD08_BOOT.pdf) | Guía de ejercicios asociado a la ayudantía. |
| [boot_sw.c](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/boot_sw.c) | El cual contiene el código C utilizado en la ayudantía |
| [Zybo-Z7_Master.xdc](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/Zybo-Z7-Master.xdc) | El cual contiene los constraints de nuestra tarjeta (mapeo de pines) |
| [Ay_Boot.zip](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/Ay_Boot.zip) | Contine el projecto en vivado/vitis desarrollado en la ayudantía |

---

## 🧪 Durante la ayudantía

Durante la ayudantía se busca que desarrollen el ejercicio propuesto de la guía, el cual consiste en crear una nueva función en el código C desarrollado que chequee si el resultado final es par o no, y que en base a ello el despliegue en leds del resultado sea parpadeante, rápido para indicar paridad y lento para indicar imparidad. Para más detalles revise la guía [AYUD08-Guia Ejercicio](https://github.com/IEE2463-SEP/AYUD08-Boot/blob/HEAD/AYUD08_BOOT.pdf).

---

<sub>IEE2463 · Sistemas Electrónicos Programables</sub>
