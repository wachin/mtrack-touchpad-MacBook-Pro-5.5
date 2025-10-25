# mtrack-touchpad-MacBook-Pro-5.5
mtrack config file MacBook Pro 5.5

Perfecto 🙌
Aquí tienes un **README en español** completamente claro y estructurado para ti — pensado para tu caso específico:
**MX Linux 23 Fluxbox en un MacBook Pro 5,5**, con el driver **mtrack** y el daemon **dispad**, incluyendo instalación, configuración y cómo hacerlo funcionar desde el *startup* de Fluxbox.

Lo puedes guardar directamente como `README_MTRACK_DISPAD.md` si quieres tenerlo a mano.

---

# 🖱️ Configuración del Touchpad MacBook Pro 5,5 en MX Linux 23 Fluxbox

## Driver: mtrack + dispad

*(Guía práctica en español)*

---

## 📦 1. Instalación del driver mtrack

El driver **mtrack** sustituye a *libinput* o *synaptics* y permite una experiencia multitáctil avanzada, muy parecida a macOS.

### 🔹 Instalar dependencias necesarias

```bash
sudo apt update
sudo apt install build-essential git pkg-config libmtdev-dev mtdev-tools xserver-xorg-dev xutils-dev
```

### 🔹 Descargar y compilar mtrack

```bash
cd /tmp
git clone https://github.com/p2rkw/xf86-input-mtrack.git
cd xf86-input-mtrack
./configure --with-xorg-module-dir=/usr/lib/xorg/modules
make
sudo make install
```

> 💡 Si aparece un error en la compilación, revisa el mensaje: normalmente indica una dependencia faltante. Instálala y vuelve a ejecutar `make`.

---

## ⚙️ 2. Crear el archivo de configuración `mtrack`

El driver no funcionará hasta crear su archivo de configuración para Xorg.

### 🔹 Crear y abrir el archivo

```bash
sudo nano /usr/share/X11/xorg.conf.d/50-mtrack.conf
```

### 🔹 Pegar la siguiente configuración optimizada para MacBook Pro 5,5

```config
# ===============================================
# Configuración optimizada del driver mtrack
# para MacBook Pro 5,5 — MX Linux 23 Fluxbox
# ===============================================

Section "InputClass"
        MatchIsTouchpad "on"
        Identifier      "Touchpads"
        MatchDevicePath "/dev/input/event*"
        Driver          "mtrack"

        # --- Movimiento y sensibilidad ---
        Option "AccelerationProfile" "2"
        Option "Sensitivity" "1.5"     # 1.0 = normal, 1.5 = fluido como macOS

        # --- Detección de dedos ---
        Option "FingerHigh" "5"
        Option "FingerLow"  "5"
        Option "IgnoreThumb" "false"
        Option "IgnorePalm" "true"
        Option "PalmSize"   "30"

        # --- Clics con toque ---
        Option "TapButton1" "1"
        Option "TapButton2" "3"
        Option "TapButton3" "2"
        Option "TapDragEnable" "true"
        Option "TapDragTime" "350"
        Option "TapDragWait" "40"
        Option "TapDragDist" "200"

        # --- Clic físico y arrastre ---
        Option "ButtonIntegrated" "true"
        Option "ButtonMoveEmulate" "true"

        # --- Desplazamiento con dos dedos ---
        Option "ScrollSmooth" "true"
        Option "ScrollUpButton" "5"
        Option "ScrollDownButton" "4"
        Option "ScrollLeftButton" "7"
        Option "ScrollRightButton" "6"
        Option "ScrollDistance" "250"

        # --- Gestos con tres dedos (arrastre macOS) ---
        Option "SwipeDistance" "1"
        Option "SwipeClickTime" "0"
        Option "SwipeSensitivity" "1500"
        Option "SwipeLeftButton" "1"
        Option "SwipeRightButton" "1"
        Option "SwipeUpButton" "1"
        Option "SwipeDownButton" "1"

        # --- Gestos con cuatro dedos (navegación) ---
        Option "Swipe4LeftButton" "9"
        Option "Swipe4RightButton" "8"
        Option "Swipe4UpButton" "11"
        Option "Swipe4DownButton" "10"

        # --- Gestos de zoom (pinza) ---
        Option "ScaleDistance" "300"
        Option "ScaleUpButton" "12"
        Option "ScaleDownButton" "13"
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

¿Quieres que te genere este README directamente como un archivo `.md` o `.pdf` (listo para imprimir o compartir)? Puedo hacerlo con formato limpio y encabezados visibles.

