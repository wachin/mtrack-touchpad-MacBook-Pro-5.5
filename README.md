# mtrack-touchpad-MacBook-Pro-5.5
mtrack config file MacBook Pro 5.5

# 🖱️ Configuración del Touchpad MacBook Pro 5,5 en MX Linux 23 Fluxbox

## Driver: mtrack + dispad

*(Guía práctica en español)*

---

## 📦 1. Instalación del driver mtrack

El driver **mtrack** sustituye a *libinput* o *synaptics* y permite una experiencia multitáctil avanzada, muy parecida a macOS.

### 🔹 Instalar 

```bash
sudo apt update
sudo apt install xserver-xorg-input-mtrack gedit
```

Nota: Instalamos Gedit porque es facil de usar para tareas de terminal

---

## ⚙️ 2. Crear el archivo de configuración `mtrack`

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
# Basada en el tutorial original de int3ractive.com
# y el README oficial del proyecto xf86-input-mtrack.
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
    #  0 = lineal, 1 = simple, 2 = polinómico (más natural).
    Option "AccelerationProfile" "1"

    # Sensibilidad (velocidad del puntero)
    # Valores recomendados:
    #   0.5 = un poco más lenta que la predeterminada
    #   1.0 = valor base recomendado
    #   1.5 = rápida y fluida, parecida a macOS
    #   2.0 = muy rápida (útil para pantallas grandes)
    Option "Sensitivity" "1.5"

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
    Option "ScrollUpButton" "5"
    Option "ScrollDownButton" "4"
    Option "ScrollLeftButton" "7"
    Option "ScrollRightButton" "6"

    # Distancia que debes mover los dedos para que el scroll se active
    # Valores más altos = scroll más lento.
    # Ejemplo:
    #   150 = sensible y rápido
    #   250 = desplazamiento natural
    #   400 = desplazamiento más largo y preciso
    Option "ScrollDistance" "250"

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

Guarda el archivo (`Ctrl + O`, luego `Enter`, y `Ctrl + X` para salir).

---

## 👥 3. Dar permisos de entrada al usuario

El driver necesita acceso al grupo `input`:

```bash
sudo adduser $USER input
```

Cierra sesión y vuelve a entrar, o reinicia el sistema.

---

## 🧠 4. Verificar que mtrack esté en uso

Ejecuta:

```bash
xinput list
```

Busca tu touchpad, por ejemplo:

```
Apple Inc. BCM5974
```

Luego:

```bash
xinput list-props "Apple Inc. BCM5974"
```

Si ves opciones que comienzan con “mtrack”, está funcionando correctamente.

---

## ✋ 5. Deshabilitar el touchpad mientras escribes con **dispad**

Cuando usas `mtrack`, el método estándar de “disable while typing” no funciona; para eso se usa el pequeño programa **dispad**, hecho por el mismo autor.

### 🔹 Instalar dispad

```bash
sudo apt install libconfuse-dev libxi-dev
cd /tmp
git clone https://github.com/BlueDragonX/dispad.git
cd dispad
./configure
make
sudo make install
```

---

### 🔹 Crear el archivo de configuración `~/.dispad`

```bash
nano ~/.dispad
```

Pega esto:

```
poll = 48
delay = 500
```

**Explicación:**

* `poll = 48` → cada cuánto revisa el teclado (en milisegundos).
* `delay = 500` → tiempo que mantiene el touchpad desactivado después de escribir (0.5 segundos).
  Puedes subirlo si el touchpad se activa muy rápido (por ejemplo 700 o 800).

---

### 🔹 Iniciar dispad automáticamente en Fluxbox

Ya que estás usando **Fluxbox**, la forma correcta es añadirlo en tu archivo de inicio:

```bash
nano ~/.fluxbox/startup
```

Y agregar esta línea **antes del `exec fluxbox`** (normalmente al final):

```bash
# Iniciar dispad (desactiva el touchpad al escribir)
dispad &
```

Guarda y cierra.

---

### 🧪 Probar

Reinicia tu sesión o tu equipo.

1. Escribe algo en un editor.
   👉 Mientras escribes, el touchpad **no debería mover el cursor**.
2. Espera medio segundo tras dejar de escribir.
   👉 El touchpad vuelve a funcionar normalmente.

---

## 🔧 6. Ajustes recomendados (para personalizar)

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

