# ✏️ air-canvas

**Draw in the air — no touch, no stylus. A virtual whiteboard controlled by colored objects tracked through your webcam using HSV color detection and OpenCV.**

[![License](https://img.shields.io/badge/License-MIT-lightgrey)](LICENSE)
![Python](https://img.shields.io/badge/Python-OpenCV%20%7C%20NumPy-blue)
[![Demo](https://img.shields.io/badge/Demo-YouTube-red)](https://www.youtube.com/watch?v=t2WfPNoBO6Y)

**🌐 Language / Idioma:** &nbsp; [🇬🇧 English](#-english) &nbsp;|&nbsp; [🇨🇴 Español](#-español)

---

## 🇬🇧 English

### How it works

The script captures your webcam feed and converts each frame from BGR to HSV color space. It then isolates two specific color ranges and tracks their centroid position frame by frame:

| Object color | HSV range | Action |
|---|---|---|
| 🟢 **Green** | `[49,50,50]` → `[80,255,255]` | **Draw** — traces a stroke on the virtual board |
| 🔵 **Blue** | `[100,65,75]` → `[130,255,255]` | **Erase** — paints white over the board |

A bilateral filter is applied before masking to reduce noise while preserving color edges. Morphological **CLOSE → OPEN** operations clean up the resulting mask before centroid extraction.

```
Webcam frame
    │
    ▼
BGR → HSV conversion
    │
    ▼
Bilateral filter (noise reduction, edge-preserving)
    │
    ├── Green mask (HSV range) ──► morphological CLOSE + OPEN ──► centroid → DRAW
    │
    └── Blue mask  (HSV range) ──► morphological CLOSE + OPEN ──► centroid → ERASE
                                                                        │
                                                                        ▼
                                                               Virtual board (NumPy array)
```

The virtual board is a separate white `480×640` NumPy array. Strokes are drawn directly onto it by calling `cv2.circle()` at the tracked centroid. The camera image is mirrored horizontally so hand movements feel natural.

### Requirements

```bash
pip install opencv-python numpy
```

### Running

```bash
python prodibujarconcamara.py
```

Four windows will open simultaneously:

| Window | Contents |
|---|---|
| `Tablero` | Virtual whiteboard — your drawing canvas |
| `Camara` | Live webcam feed with crosshair marker at tracked object |
| `Filtrado` | Combined green + blue detection mask (useful for calibration) |
| `Configuracion` | Trackbar panel for pen color, pen size, and eraser size |

Press `Esc` to exit.

### Controls

**Configuration panel trackbars:**

| Trackbar | Range | Effect |
|---|---|---|
| `R` | 0 – 255 | Red channel of pen color |
| `G` | 0 – 255 | Green channel of pen color |
| `B` | 0 – 255 | Blue channel of pen color |
| `Lapiz` | 0 – 100 | Pen stroke radius (pixels) |
| `Borrador` | 0 – 100 | Eraser radius (pixels) |

### Tips

- **Lighting matters** — consistent, diffuse lighting gives the most stable color detection. Avoid backlighting.
- **Object selection** — a green Post-it note or blue tape works well as a low-cost marker.
- **Calibration** — watch the `Filtrado` mask window. If the mask is noisy or missing, adjust your object color or lighting until the mask shows a clean solid blob.
- **Custom colors** — to track different object colors, modify `verde_bajos` / `verde_altos` and `azul_bajos` / `azul_altos` in the script with your target HSV range.

---

[🇨🇴 Leer en Español](#-español)

---

## 🇨🇴 Español

[🇬🇧 Read in English](#-english)

### Cómo funciona

El script captura el video de la webcam y convierte cada fotograma de BGR a espacio de color HSV. Luego aísla dos rangos de color específicos y rastrea la posición del centroide de cada uno fotograma a fotograma:

| Color del objeto | Rango HSV | Acción |
|---|---|---|
| 🟢 **Verde** | `[49,50,50]` → `[80,255,255]` | **Dibujar** — traza un trazo en el tablero virtual |
| 🔵 **Azul** | `[100,65,75]` → `[130,255,255]` | **Borrar** — pinta de blanco sobre el tablero |

Se aplica un filtro bilateral antes del enmascarado para reducir el ruido conservando los bordes de color. Las operaciones morfológicas **CLOSE → OPEN** limpian la máscara resultante antes de extraer el centroide.

```
Fotograma de la webcam
    │
    ▼
Conversión BGR → HSV
    │
    ▼
Filtro bilateral (reducción de ruido, preservación de bordes)
    │
    ├── Máscara verde (rango HSV) ──► CLOSE + OPEN morfológico ──► centroide → DIBUJAR
    │
    └── Máscara azul  (rango HSV) ──► CLOSE + OPEN morfológico ──► centroide → BORRAR
                                                                        │
                                                                        ▼
                                                          Tablero virtual (array NumPy)
```

El tablero virtual es un array NumPy blanco de `480×640` separado. Los trazos se dibujan directamente sobre él usando `cv2.circle()` en la posición del centroide detectado. La imagen de la cámara se voltea horizontalmente para que los movimientos de la mano se sientan naturales.

### Requisitos

```bash
pip install opencv-python numpy
```

### Ejecución

```bash
python prodibujarconcamara.py
```

Se abrirán cuatro ventanas simultáneamente:

| Ventana | Contenido |
|---|---|
| `Tablero` | Pizarra virtual — tu lienzo de dibujo |
| `Camara` | Video en vivo con marcador de mira en el objeto rastreado |
| `Filtrado` | Máscara combinada de detección verde + azul (útil para calibrar) |
| `Configuracion` | Panel de trackbars para color, tamaño del lápiz y del borrador |

Presiona `Esc` para salir.

### Controles

**Trackbars del panel de configuración:**

| Trackbar | Rango | Efecto |
|---|---|---|
| `R` | 0 – 255 | Canal rojo del color del lápiz |
| `G` | 0 – 255 | Canal verde del color del lápiz |
| `B` | 0 – 255 | Canal azul del color del lápiz |
| `Lapiz` | 0 – 100 | Radio del trazo del lápiz (píxeles) |
| `Borrador` | 0 – 100 | Radio del borrador (píxeles) |

### Consejos

- **La iluminación importa** — una iluminación uniforme y difusa da la detección de color más estable. Evita la contraluz.
- **Selección del objeto** — una nota adhesiva verde o cinta azul funcionan bien como marcadores de bajo costo.
- **Calibración** — observa la ventana `Filtrado`. Si la máscara tiene ruido o no aparece, ajusta el color del objeto o la iluminación hasta obtener un blob sólido y limpio.
- **Colores personalizados** — para rastrear objetos de otro color, modifica `verde_bajos` / `verde_altos` y `azul_bajos` / `azul_altos` en el script con tu rango HSV objetivo.

---

## Author / Autor

**Gilberto Galvis Giraldo**

---

## License / Licencia

MIT — see [LICENSE](LICENSE) for details.
