# mtrack-touchpad-MacBook-Pro-5.5
mtrack config file MacBook Pro 5.5

# Configuración del Touchpad MacBook Pro 5,5 en MX Linux 23 Fluxbox

## Driver: mtrack + dispad

*(Guía práctica en español)*

---

## Instalación del driver mtrack

El driver **mtrack** sustituye a *libinput (pues no funciona bien)* o *synaptics* y permite una experiencia multitáctil avanzada, muy parecida a macOS.

### Instalar

```bash
sudo apt update
sudo apt install xserver-xorg-input-mtrack
```

Además recomiendo instalar Gedit porque es facil de usar para tareas de terminal:

```bash
sudo apt install gedit
```
---

## Configurar el módulo del kernel `bcm5974`

Este módulo del kernel es el encargado de **comunicar el hardware del touchpad con el sistema**.
En algunos modelos antiguos de MacBook, el controlador introduce una **“zona muerta” (fuzz)** para evitar ruido táctil, lo que genera lentitud o pérdida de precisión.

Para eliminar esa zona muerta:

1. Crea (si no existe) el archivo `/etc/modprobe.d/bcm5974.conf`

   ```bash
   sudo nano /etc/modprobe.d/bcm5974.conf
   ```
2. Agrega dentro:

   ```bash
   options bcm5974 fuzz=0
   ```
3. Recarga el módulo:

   ```bash
   sudo modprobe -r bcm5974
   sudo modprobe bcm5974
   ```

📘 **Explicación técnica:**
El parámetro `fuzz=0` indica que **no se debe filtrar ninguna variación pequeña de posición táctil**, aumentando la precisión de los gestos.
Sin este ajuste, el scroll con dos dedos y el tap doble pueden sentirse poco responsivos.

---

## Desactivar libinput para evitar conflictos

Por defecto, Xorg carga **libinput**, que también intenta controlar el touchpad.
Esto genera conflictos porque ambos drivers (libinput y mtrack) responden a los mismos eventos.

Para evitarlo:

1. Localiza el archivo:

   ```
   /etc/X11/xorg.conf.d/30-touchpad-libinput.conf
   ```
2. Renómbralo (no lo elimines, por si deseas restaurarlo):

   ```bash
   sudo mv /etc/X11/xorg.conf.d/30-touchpad-libinput.conf \
           /etc/X11/xorg.conf.d/30-touchpad-libinput.conf.back
   ```

📘 **Explicación técnica:**
El archivo `30-touchpad-libinput.conf` contiene una línea:

```bash
MatchIsTouchpad "on"
```

que hace que **libinput** intercepte todos los eventos del touchpad antes de que **mtrack** los procese.
Al renombrarlo, Xorg deja de cargar libinput para el touchpad, permitiendo que **solo mtrack** maneje el dispositivo.

---


---

## Crear el archivo de configuración `mtrack`

El driver no funcionará hasta crear su archivo de configuración para Xorg.

### 🔹 Crear y abrir el archivo

```bash
sudo gedit /usr/share/X11/xorg.conf.d/50-mtrack.conf
```

### 🔹 Pegar la siguiente configuración optimizada para MacBook Pro 5,5

```config
# ===============================================
# Configuración optimizada del driver mtrack
# para MacBook Pro 5,5 — MX Linux 23 Fluxbox
# ===============================================
# Versión revisada y comentada (por ChatGPT)
# Basada en el tutorial original de Int3ractive https://int3ractive.com/blog/2018/make-the-best-of-macbook-touchpad-on-ubuntu/
# y el README oficial del proyecto xf86-input-mtrack https://github.com/rynbrd/xf86-input-mtrack/blob/master/README.md
#
# Objetivo:
#   - Movimiento fluido y natural del cursor
#   - Desplazamiento (scroll) con dos dedos
#   - Arrastre con tres dedos
#   - Selección y arrastre en Thunar funcionando
#   - Gestos básicos con buena estabilidad
# ===============================================

Section "InputClass"
    MatchIsTouchpad "on"
    Identifier      "Touchpads"
    MatchDevicePath "/dev/input/event*"
    Driver          "mtrack"

    # --------------------------------------------
    # MOVIMIENTO DEL CURSOR
    # --------------------------------------------

    # Perfil de aceleración:
    #  0 = lineal, 1 = simple, 2 = polinómico (se  duplica la velocidad).
    Option "AccelerationProfile" "1"

    # Sensibilidad (velocidad del puntero)
    # Valores recomendados:
    #   0.5 = un poco más lenta que la predeterminada
    #   1.0 = valor base recomendado
    #   1.7 = rápida y fluida, parecida a macOS
    #   2.0 = muy rápida (útil para pantallas grandes)
    Option "Sensitivity" "1.7"

    # --------------------------------------------
    # DETECCIÓN DE DEDOS Y PRESIÓN
    # --------------------------------------------

    # Presión mínima y máxima para detectar el toque
    # Cuanto menor el número, más sensible.
    Option "FingerHigh" "5"
    Option "FingerLow"  "5"

    # Ignorar o no el pulgar
    Option "IgnoreThumb" "false"
    # Relación de aspecto del pulgar y tamaño
    Option "ThumbRatio" "70"
    Option "ThumbSize"  "25"

    # Ignorar la palma si cubre parte del touchpad
    Option "IgnorePalm" "true"
    Option "PalmSize"   "30"

    # --------------------------------------------
    # CLICS Y TOQUES
    # --------------------------------------------

    # Toques para clic:
    # 1 dedo = clic izquierdo
    # 2 dedos = clic derecho
    # 3 dedos = clic central
    Option "TapButton1" "1"
    Option "TapButton2" "3"
    Option "TapButton3" "2"
    Option "TapButton4" "0"

    # Duración del clic simulado en milisegundos
    Option "ClickTime" "25"

    # Desactiva arrastre por toque (preferimos tres dedos)
    Option "TapDragEnable" "true"

    Option "TapDragTime" "350"
    Option "TapDragWait" "40"
    Option "TapDragDist" "200"

    # Clic físico con dedos
    Option "ClickFinger1" "1"
    Option "ClickFinger2" "3"
    Option "ClickFinger3" "2"

    # --------------------------------------------
    # ARRASTRE Y SELECCIÓN
    # --------------------------------------------

    # Emular movimiento con clic sostenido:
    #   - "true" permite arrastrar para seleccionar archivos.
    #   - "false" desactiva arrastre (lo que causa tu problema en Thunar).
    Option "ButtonMoveEmulate" "true"

    # Indica que el botón físico está integrado en el touchpad
    Option "ButtonIntegrated" "true"

    # --------------------------------------------
    # DESPLAZAMIENTO (SCROLL)
    # --------------------------------------------

    # Duración e inercia del desplazamiento
    Option "ScrollCoastDuration" "300"
    Option "ScrollCoastEnableSpeed" ".1"

    # Activar desplazamiento suave (natural)
    Option "ScrollSmooth" "true"

    # Botones virtuales para scroll vertical y horizontal
    Option "ScrollUpButton" "4"
    Option "ScrollDownButton" "5"
    Option "ScrollLeftButton" "7"
    Option "ScrollRightButton" "6"

    # Distancia que debes mover los dedos para que el scroll se active
    # Valores más altos = scroll más lento.
    # Ejemplo:
    #   70 = sensible y rápido
    #   80 = desplazamiento natural
    #   130 = desplazamiento más largo
    Option "ScrollDistance" "80"

    # --------------------------------------------
    # GESTOS DE ARRASTRE Y DESLIZAMIENTO
    # --------------------------------------------

    # Arrastre con tres dedos (Swipe)
    Option "SwipeDistance" "1"
    Option "SwipeClickTime" "0"
    Option "SwipeSensitivity" "1500"
    Option "SwipeLeftButton" "1"
    Option "SwipeRightButton" "1"
    Option "SwipeUpButton" "1"
    Option "SwipeDownButton" "1"

    # Sensibilidad del gesto (mayor = más fácil de activar)
    #   1000 = requiere más movimiento
    #   1500 = sensible y natural
    #   2000 = se activa con movimientos cortos
    Option "SwipeSensitivity" "1500"

    # Deslizamiento con cuatro dedos
    # 8 y 9 corresponden a "atrás" y "adelante" en navegadores
    Option "Swipe4LeftButton" "9"
    Option "Swipe4RightButton" "8"

    # Botones adicionales (para usar con xbindkeys y xdotool)
    Option "Swipe4UpButton" "11"
    Option "Swipe4DownButton" "10"

    # --------------------------------------------
    # GESTOS DE PINZA (ZOOM)
    # --------------------------------------------

    # Distancia para activar zoom (pinch)
    #   Menor valor = zoom más sensible.
    Option "ScaleDistance" "300"
    Option "ScaleUpButton" "12"
    Option "ScaleDownButton" "13"

    # Desactiva rotación con dos dedos
    Option "RotateLeftButton" "0"
    Option "RotateRightButton" "0"

EndSection
```

Guarda el archivo y cerrar.

## Verificar que mtrack esté en uso

Ejecuta:

```bash
xinput list
```

Busca tu touchpad, por ejemplo:

```
bcm5974
```

Luego:

```bash
xinput list-props "bcm5974"
```

Deberán aparecer varias opciones lo cual significa que está funcionando correctamente.

---

## Deshabilitar el touchpad mientras escribes con **dispad**

Cuando usas `mtrack`, el método estándar de “disable while typing” no funciona; para eso se usa el pequeño programa **dispad**, hecho por el mismo autor.

### Instalar dispad

Instalar las dependencias:

```bash
sudo apt install libconfuse-dev libxi-dev
```

y uno por un poner en la terminal:

```bash
cd /tmp
git clone https://github.com/BlueDragonX/dispad.git
cd dispad
./configure
make
sudo make install
```

---

### Crear el archivo de configuración `~/.dispad`

```bash
gedit ~/.dispad
```

allí aparecerán unos valores por defecto que para mi están bien, pero si desea los puede editar así:

Pega esto:

```
poll = 48
delay = 500
```

**Explicación:**

* `poll = 48` →  Cada cuánto revisa el teclado (en milisegundos).
* `delay = 500` → tiempo que mantiene el touchpad desactivado después de escribir (0.5 segundos).
  En valores más altos el touchpad se activa muy rápido (por ejemplo 700 o 800 o 1000).

---


### Iniciar dispad automáticamente en Linux

Según su distribución Linux búsquen Google cómo añadir programas al inicio y agreguelo allí

### Iniciar dispad automáticamente en Fluxbox

Si estás usando **Fluxbox** la forma correcta es añadirlo en tu archivo de inicio:

```bash
gedit ~/.fluxbox/startup
```

Y agregar esta línea **antes del `exec fluxbox`** (normalmente al final):

```bash
# Iniciar dispad (desactiva el touchpad al escribir)
dispad &
```

Guarda y cierra.

---

### Probar

Reinicia tu sesión o tu equipo.

1. Escribe algo en un editor.
   👉 Mientras escribes, el touchpad **no debería mover el cursor**.
2. Espera medio segundo tras dejar de escribir.
   👉 El touchpad vuelve a funcionar normalmente.

---

## Ajustes recomendados (para personalizar)

| Opción              | Efecto                                | Valor recomendado |
| ------------------- | ------------------------------------- | ----------------- |
| `Sensitivity`       | velocidad del cursor                  | `1.0` a `1.5`     |
| `ScrollDistance`    | sensibilidad del desplazamiento       | `150–300`         |
| `SwipeSensitivity`  | sensibilidad del arrastre con 3 dedos | `1500`            |
| `delay` (en dispad) | tiempo sin touchpad al escribir       | `400–800` ms      |

---

## 🧩 7. Verificación final

* ✅ Movimiento fluido del puntero
* ✅ Selección de archivos en Thunar
* ✅ Arrastre con tres dedos
* ✅ Scroll con dos dedos
* ✅ Touchpad se desactiva mientras escribes

---

## 🧾 Créditos y referencias

* Proyecto **mtrack**: [https://github.com/p2rkw/xf86-input-mtrack](https://github.com/p2rkw/xf86-input-mtrack)
* Proyecto **dispad**: [https://github.com/BlueDragonX/dispad](https://github.com/BlueDragonX/dispad)
* Adaptado y documentado para **MX Linux 23 Fluxbox – MacBook Pro 5,5**

---

