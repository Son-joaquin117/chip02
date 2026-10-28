<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

Es una cerradura digital hecha como maquina de estados finitos (FSM). La clave es de 4 bits y se mete
un bit a la vez por ui[0]; cada pulso de reloj (clk) captura un bit, como un boton INGRESAR.

El diseno tiene dos maquinas que trabajan juntas:

- FSM A, lectura del codigo (estado R, ERR, C1, C0): C1 y C0 cuentan los bits metidos y ERR recuerda
  si alguno no coincidio. Al cuarto bit da uno de tres resultados:
  - 1011 (clave correcta): abre.
  - 1010 (clave de coaccion): abre igual, pero activa una alarma silenciosa. Sirve si alguien obliga
    a la persona a abrir: la puerta se ve normal y la alarma avisa sin que el atacante lo note.
  - Cualquier otro codigo: fallo.
  El siguiente pulso regresa la FSM A al inicio y no cuenta como bit.

- FSM B, contador de intentos (estado F1, F0): cuenta los fallos seguidos. Con 3 fallos la cerradura
  se bloquea y ya no abre ni con la clave correcta. Una apertura correcta regresa el contador a 0.

La LLAVE MAESTRA (ui[1] en 1) o rst_n en 0 reinician todo en el siguiente pulso de reloj; es la unica
forma de quitar el bloqueo.

El display de 7 segmentos muestra el resultado:
- "-" esperando los 4 bits
- "A" abierta
- "F" codigo incorrecto
- "b" bloqueada
- el punto decimal es la alarma silenciosa (solo con el codigo de coaccion)

Esta hecho con compuertas AND, OR, NAND y NOT de 2 entradas y 6 flip-flops D.

## How to test

1. Poner ui[1] (LLAVE MAESTRA) en 1, dar un pulso de reloj y regresarlo a 0. El display muestra "-".
2. Meter 1011, un bit por pulso: ui[0] en 1 y pulso, 0 y pulso, 1 y pulso, 1 y pulso. El display
   muestra "A".
3. Un pulso mas y regresa a "-".
4. Meter 1010: muestra "A" y ademas se enciende el punto decimal (alarma silenciosa).
5. Meter 0000 tres veces, con un pulso extra despues de cada codigo. Cada uno muestra "F"; despues
   del tercero el display se queda en "b".
6. Con "b", la clave 1011 ya no abre. Para desbloquear se repite el paso 1.

## External hardware

Ninguno extra. Se usan los interruptores de la placa de demostracion para ui[0] (bit de la clave) y
ui[1] (llave maestra), el reloj de la placa dando pulsos uno por uno como boton INGRESAR, y el display
de 7 segmentos de la placa para ver el resultado.
