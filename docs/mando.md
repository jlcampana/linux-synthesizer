# El mando: macropad de 16 teclas y 3 perillas

**Modelo elegido:** [teclado macro de 16 teclas y 3 perillas (AliExpress)](https://es.aliexpress.com/item/1005010043319280.html)

- 16 teclas en rejilla 4×4 y 3 perillas. Cada perilla tiene 3 acciones: girar a la izquierda,
  girar a la derecha y pulsar.
- 3 capas con indicador LED, RGB, base de silicona antideslizante.
- Cable USB-C, Bluetooth y 2,4 GHz. En inalámbrico apaga las luces.
- Se configura **una vez en Windows** con el programa del fabricante y la configuración queda
  guardada en el teclado. Después funciona en cualquier sistema.
- Un comprador lo usa en Photoshop con las perillas para zoom y tamaño de pincel. Eso confirma
  que las perillas pueden mandar cualquier tecla, no solo el volumen.

**Por qué este y no el de 12 teclas y 4 perillas:** con 16 teclas caben las 4 flechas del
joystick (como el HiChord de verdad) y además el **secuenciador de 16 pasos se toca directamente**,
una tecla por paso. La cuarta perilla no hace falta, porque el filtro pasa a "volumen + botón rojo",
que es como lo hace el HiChord.

![Mapa del mando](bocetos/mando-16-teclas.svg)

> La posición exacta de las perillas se confirmará cuando llegue. El boceto las pone arriba.

---

## Mapeo en modo Tocar

### Teclas

| | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **Fila 1** | Acorde **I** (rojo) | Acorde **ii** (naranja) | Acorde **iii** (amarillo) | Acorde **IV** (verde) |
| **Fila 2** | Acorde **V** (cian) | Acorde **vi** (azul) | Acorde **vii** (morado) | ● **Grabar** |
| **Fila 3** | **←** | **↑** | **↓** | **→** |
| **Fila 4** | **Tono** (gris) | **Sonido** (amarillo) | **Modo** (rojo) | ▶ **Play / pausa** |

- **Flechas = joystick del HiChord.** Si las pulsas mientras mantienes un acorde, cambian su color:
  ↑ mayor/menor · → 7ª · ↓ sus4 · ← disminuido. Dos flechas a la vez dan las diagonales:
  ↗ 7ª dominante · ↘ 9ª · ↖ aumentado.
- **Menús:** si mantienes Tono, Sonido o Modo, las flechas hacen lo mismo que en el HiChord.
  - Tono: ←/→ tonalidad, ↑/↓ octava.
  - Sonido: ←/→ sonido, ↑ efectos.
  - Modo: ←/→ modo, ↑ BPM.
- **Grabar:** arma o graba la pista actual del looper. Mientras mantienes Grabar, los acordes I–vi
  (las 6 primeras teclas) eligen la pista 1–6.
- En **modo Batería**, las 7 teclas de acorde tocan los 7 golpes del kit.

### Perillas

Igual que en el HiChord: **si mantienes un botón de menú, la perilla cambia algo de ese menú.**

| Perilla | Girar | + Tono | + Sonido | + Modo | Pulsar |
|---|---|---|---|---|---|
| **P1 · Volumen** | Volumen | Attack | Release | **Filtro** (brillo) | Silencio |
| **P2 · Mágica** | Con un acorde pulsado: va pasando por sus colores. Si no, navega | Tonalidad | Sonido | Modo | Con acorde: **fijarlo** (chord lock). Con menú: 🎲 aleatorio |
| **P3 · Tempo** | BPM (40–300) | Octava | Cantidad de efecto | Patrón de arpegio o ritmo | Tap tempo |

Orden de colores con P2: normal → mayor/menor → 7ª → 7ª dominante → 9ª → sus4 → disminuido →
aumentado → (vuelta a normal). Sirve para que los más pequeños, a los que les cuesta combinar
flechas, también puedan cambiar el color.

---

## Mapeo en modo Secuenciador (batería)

Las **16 teclas son los 16 pasos del ritmo**: pulsas y el paso se enciende o se apaga.
La pantalla muestra la rejilla y la fila del instrumento elegido.

| Control | Función |
|---|---|
| Teclas 1–16 | Encender o apagar el paso |
| P2 girar | Elegir instrumento (bombo, caja, charles…) |
| P2 pulsar | Salir del secuenciador. El patrón se graba solo en una pista libre del looper |
| P3 girar | Tempo |
| P3 pulsar | Play / pausa |
| P1 | Volumen, como siempre |

---

## Capas del mando

Los modos los gestiona la app, así que **las 3 capas se programan iguales**: si el niño cambia de
capa sin querer, no pasa nada. Más adelante se podrían usar la capa 2 para el looper y la capa 3
para el taller de samples.

---

## Configuración (una vez, en Windows)

La app lee el mando por su cuenta (ver abajo), así que **solo necesita que cada tecla y cada
acción de perilla mande un código distinto**: 16 + 9 = 25 códigos. Propuesta, sin modificadores
para que cada acción sea un solo evento:

| Control | Código propuesto |
|---|---|
| Teclas 1–12 | `F13` … `F24` |
| Teclas 13–16 | Teclado numérico `1` `2` `3` `4` |
| P1 izquierda / derecha / pulsar | Num `5` / `6` / `7` |
| P2 izquierda / derecha / pulsar | Num `8` / `9` / `0` |
| P3 izquierda / derecha / pulsar | Num `/` / `*` / `-` |

Si el programa del fabricante no tiene F13–F24, vale **cualquier** combinación de 25 teclas
distintas (letras incluidas). El asistente de la app las aprende igual.

Si los códigos de fábrica ya son todos distintos, ni siquiera hace falta configurarlo.

Alternativa en Linux (sin verificar para este modelo):
[`ch57x-keyboard-tool`](https://docs.rs/crate/ch57x-keyboard-tool) programa mandos con chip CH57x.
Para saber si sirve, hay que mirar con `lsusb` si el ID es `1189:8890`, `1189:8840` u `1189:8842`.

---

## Cómo lo lee la app

1. **`evdev` + acceso exclusivo (`EVIOCGRAB`).** La app abre el mando directamente. Mientras está
   abierta, sus pulsaciones no llegan al escritorio, así que no escriben en otras ventanas ni
   cambian el volumen del sistema.
2. **Se identifica por dispositivo**, así que no importa si sus códigos coinciden con teclas del
   teclado normal: los dos funcionan a la vez sin pisarse.
3. **Asistente "Aprender mando"** (la primera vez o desde el modo papá). La pantalla pide
   *"pulsa la tecla roja"*, *"gira la perilla 1 a la derecha"*… y guarda el resultado en
   `mando.ron`. Si un código se repite, avisa.
4. **Permisos:** una regla `udev` para leerlo sin `sudo`. Los ID se sacan con `lsusb`:
   ```
   # /etc/udev/rules.d/70-chispa-mando.rules
   SUBSYSTEM=="input", ATTRS{idVendor}=="XXXX", ATTRS{idProduct}=="YYYY", MODE="0660", TAG+="uaccess"
   ```
5. Si se desconecta el mando, la app sigue funcionando con el teclado normal y lo vuelve a coger
   solo cuando se reconecta.

## Recomendaciones

- **Usarlo por cable.** Tiene menos latencia, no gasta batería y las luces siguen encendidas.
- **Teclas de colores.** Si las teclas son estándar (MX), poner teclas o pegatinas iguales a la
  pantalla: arcoíris en los acordes, negro con flecha amarilla en las flechas, gris/amarillo/rojo
  en los menús y blanco en Grabar y Play.
- **RGB:** si permite color fijo, ponerlo suave o apagado para que no compita con las teclas de colores.
