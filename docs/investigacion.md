# Investigación

Fecha: 2026-11-06

Este documento recoge lo que hacen los dos dispositivos de referencia y qué partes nos
interesan para un sintetizador de escritorio en Rust, pensado para un niño y controlado con
teclado y perilla.

---

## 1. HiChord (referencia principal de la interfaz)

Fuente: [manual oficial, firmware Rev 3.0](https://updater.hichord.shop/manual/)

Es un instrumento de bolsillo con carcasa **amarilla**, pantalla OLED pequeña y muy pocos
controles. Su gracia es que **cualquier botón que pulses suena bien**: los 7 botones tocan los 7
acordes de la tonalidad.

### Controles físicos

| Control | Función |
|---|---|
| 7 botones de acorde | Tocan los acordes diatónicos I–vii° (en Do: C, Dm, Em, F, G, Am, Bdim) |
| Joystick 8 direcciones + click | Mientras mantienes un acorde, cambia su "color" (7ª, sus4, menor…). También navega por los menús y arma el looper |
| Botón **gris** | Tonalidad y ajustes (←/→ cambia la tonalidad, ↑/↓ la octava) |
| Botón **amarillo** | Sonidos y efectos (←/→ cambia el sonido, ↑ abre efectos) |
| Botón **rojo** | Modos y tempo (←/→ cambia el modo, ↑ abre BPM) |
| Rueda de volumen | Volumen. Con gris/amarillo/rojo pulsado: attack / release / cutoff |
| Pantalla OLED 64×32 | Nombre del acorde, menús, estado del looper |

### Funciones que copiamos

- **Acordes por tonalidad**: 12 tonalidades y 10 escalas (mayor, menor, pentatónica, blues, dórica…).
- **Modificadores con joystick** (modo por defecto):
  ↑ mayor/menor · ↗ 7ª dominante · → 7ª · ↘ 9ª · ↓ sus4 · ← disminuido · ↖ aumentado.
  Tiene además los modos *Extended*, *Chromatic* y *Borrow* (acordes prestados).
- **Inversiones** y *voice leading* automático (elige la inversión que menos se mueve).
- **Chord lock**: fija un acorde modificado a un botón.
- **28 sonidos** (analógico, FM, samples, híbridos, ruido).
- **Efectos**: reverb, delay sincronizado al BPM, chorus, flanger, drive, tape, tremolo, vibrato,
  filtro y vocoder.
- **Modos de toque**: Play, Strum (rasgueo), Lead (una nota), Drone, Arp (6 patrones),
  Repeat, Sequencer (16 pasos de acordes), Drums (7 kits), Drum Loops (56 ritmos), Auto-drum.
- **Tempo** de 40 a 300 BPM, con *tap tempo*. Controla arpegiador, delay, tremolo, ritmos y metrónomo.
- **Looper de 6 pistas** de unos 20 s. La pista 1 marca la duración y las demás se sincronizan.
  Al salir del secuenciador o de los ritmos, el patrón se graba solo en una pista libre.
- **Sampleo por micro** (~3 s) con detección de tono y transposición a Do.
- **4 presets** (P1–P4) que lo guardan todo.
- **Aleatorio**: menú + click del joystick llena ese menú con valores al azar. A los niños les encanta.
- **Juegos**: *Chord Hiro* (ritmo) y *Ear Trainer* (entrenar el oído).

---

## 2. M-VAVE FM-1

Fuentes: [Synthtopia](https://www.synthtopia.com/content/2026/07/13/m-vave-introduces-fm-1-fm-pocket-synthesizer/),
[review en Synth Anatomy](https://synthanatomy.com/2026/07/m-vave-fm-1-review-low-budget-pocket-fm-ynthesizer-with-iconic-sounds.html),
[firmware abierto FM1 Open](https://synthtopia.com/content/2026/10/20/m-vave-officially-opens-fm-1-firmware-to-developers-with-new-workbench-sdk),
[sampler lo-fi de la comunidad](https://www.matrixsynth.com/2026/11/he-made-fair-sampler-for-fm-1-and-it.html).
(La página del producto en m-vave.com solo muestra la cabecera, sin especificaciones.)

Es un sinte FM pequeño al estilo Yamaha DX7, con teclado de silicona.

- Motor **FM de 6 operadores**, 32 algoritmos, 12 voces, 128 presets y compatible con SysEx del DX7
  (según un comentario de lector, basado en Dexed/MiniDexed).
- **Arpegiador**: direcciones, octavas, rate, gate, swing, latch.
- **Secuenciador de 16 pasos**, mono o polifónico, con 16 patrones.
- **Multiefectos**: filtro (LP/HP/BP), chorus, phaser, distorsión, delay y reverb (room/hall/plate).
- Firmware abierto (FM1 Open) y un sampler lo-fi hecho por la comunidad.

### Qué cogemos del FM-1

- Un **motor FM** con pocos algoritmos fáciles (2 y 4 operadores) para campanas, pianos
  eléctricos y bajos. 6 operadores no compensan para un niño.
- **Secuenciador de 16 pasos con patrones** guardables.
- **Arpegiador** con swing y latch.
- **Distorsión, phaser y filtro multimodo**.
- La idea del **sampler lo-fi**: samples cortos, bitcrush, reducción de frecuencia de muestreo.

### Qué no cogemos

- La edición de operadores y envolventes de DX7 es demasiado compleja para un niño. Como mucho,
  la dejamos en un "modo papá".

---

## 3. Lo que tú pediste además

| Requisito | Cómo lo resolvemos |
|---|---|
| Crear música | Acordes + modos + batería + secuenciador + looper |
| Guardarla | Proyectos con guardado automático y nombres divertidos generados solos |
| Convertir a OGG/MP3 | Render del proyecto a WAV, OGG Vorbis y MP3 |
| Gestionar samples | Biblioteca de samples: importar, grabar con micro, recortar, renombrar, borrar |
| Distorsionarlos | "Taller de samples": distorsión, bitcrush, al revés, más agudo/grave, eco, robot… |
| Interfaz tipo HiChord | Pantalla central oscura tipo OLED, carcasa amarilla/naranja, 7 botones de colores |
| Colores vivos | Paleta amarillo, naranja, rojo, verde, azul, morado (ver bocetos) |
| Control por teclado | Mapa de teclas que copia los botones del HiChord |
| Perilla (teclado de AliExpress) | Lectura directa con `evdev`. La perilla controla volumen, tempo y parámetros como la rueda del HiChord |

---

## 4. La perilla de los teclados de AliExpress

La mayoría de teclados y macropads con perilla (encoder rotatorio) envían por defecto
**teclas multimedia**: `KEY_VOLUMEUP`, `KEY_VOLUMEDOWN` y `KEY_MUTE` al pulsarla.
Eso trae dos problemas:

1. El escritorio (GNOME/KDE) intercepta esas teclas y cambia el volumen del sistema.
2. Las ventanas no siempre reciben esos eventos.

**Solución propuesta**, en orden de preferencia:

1. **Leer el dispositivo con `evdev`** desde Rust y "agarrarlo" en exclusiva (`EVIOCGRAB`)
   mientras la app está abierta. Así el sistema no ve los giros y la app los recibe todos.
   Hace falta que el usuario esté en el grupo `input` o añadir una regla `udev` (la documentamos).
2. Si el teclado es compatible con **QMK/VIA**, reasignar la perilla a `F13`/`F14`/`F15`,
   que ninguna aplicación usa. Así funciona incluso sin `evdev`.
3. Como alternativa sin hardware: `Re Pág`/`Av Pág` o la rueda del ratón hacen de perilla.

**Consejo de compra**: elegir un modelo que diga **"QMK" o "VIA"** en la descripción. Un macropad
con **3 perillas** (hay varios de 16 teclas + 3 encoders) daría volumen, tempo y "parámetro"
independientes, con más juego que una sola perilla. Antes de comprar, revisad que no necesite
software solo para Windows.

---

## 5. Elección de tecnologías (Rust)

| Necesidad | Crate | Por qué |
|---|---|---|
| Salida/entrada de audio | `cpal` | Estándar en Rust. Funciona con PipeWire/ALSA/Pulse |
| Comunicación UI ↔ audio sin bloqueos | `rtrb` (ring buffer) + `triple_buffer` | El hilo de audio no puede esperar ni reservar memoria |
| DSP | Propio + `fundsp` para prototipos | Osciladores, filtros SVF, envolventes, efectos. Código propio para tenerlo controlado y testeable |
| Interfaz | `eframe`/`egui` con dibujo propio | Pantalla completa, se dibuja todo a mano (estilo HiChord), rápido de iterar |
| Teclado | eventos de `egui`/`winit` | Teclas normales |
| Perilla | `evdev` | Lectura directa y exclusiva del encoder |
| Leer samples (wav/mp3/ogg/flac) | `symphonia` | Decodificador en Rust puro |
| Cambiar frecuencia de muestreo | `rubato` | Remuestreo de calidad al importar |
| Escribir WAV | `hound` | Simple y fiable |
| Exportar OGG | `vorbis_rs` | Bindings a libvorbis, buena calidad |
| Exportar MP3 | `mp3lame-encoder` | Bindings a LAME |
| Guardar proyectos | `serde` + `ron` | Legible a mano y fácil de versionar |
| MIDI (opcional, futuro) | `midir` | Por si algún día conectáis un teclado MIDI o un HiChord de verdad |
| Detección de tono (sampleo por micro) | `pitch-detection` (YIN/McLeod) | Para transponer samples a Do, como hace HiChord |

Alternativa a los encoders: llamar a `ffmpeg` si está instalado. Lo dejamos como plan B porque
añade una dependencia externa.
