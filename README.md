# Influencia de la Música Lo-Fi en la Atención Sostenida y la Actividad Neurofisiológica

> **Trabajo Integrador Final — Fisiología y Biofísica · FIUNER** 
> Facultad de Ingeniería, Universidad Nacional de Entre Ríos (UNER)

---

## Descripción

Este repositorio contiene el código y los recursos utilizados en el trabajo integrador final de la asignatura **Fisiología y Biofísica** de la Facultad de Ingeniería (UNER). El estudio evaluó el efecto de la música Lo-Fi sobre el desempeño en una tarea de atención sostenida (SART) y sobre la actividad neurofisiológica medida mediante EEG, cuantificada a través de la **razón theta/beta (RTB)**.

---

## Autores

| Nombre | Contacto |
|---|---|
| **Lucas Alarcon** | lucas.alarcon@ingenieria.uner.edu.ar |
| **Mateo Anderson** | mateo.anderson@ingenieria.uner.edu.ar |

**Directora:** Brigitte Aguilar Gonzales 
**Codirector:** Daniel Zapata 

---

## Resumen del Estudio

Se evaluó el efecto de la música Lo-Fi en el desempeño durante una tarea de atención sostenida (SART) y en la actividad neurofisiológica de 25 estudiantes universitarios (13 varones, edad promedio 23 años) de la Facultad de Ingeniería de la UNER.

Los participantes fueron asignados aleatoriamente a dos grupos:
- **Grupo música Lo-Fi** — realizaron la tarea escuchando música instrumental Lo-Fi
- **Grupo control (silencio)** — realizaron la tarea en completo silencio

Los resultados no mostraron diferencias estadísticamente significativas en las variables de desempeño ni en la RTB. Sin embargo, se observó **menor variabilidad en los tiempos de reacción** del grupo con música, lo que sugiere un posible efecto estabilizador de la atención.

---

## ️ Estructura del Repositorio

```
 Procesamiento_senales_EEG/
├── data/ # Datos crudos del EEG y resultados del SART
│ ├── eeg_raw/ # Señales EEG por participante (.txt)
│ └── sart_results/ # Hojas de datos del PsychoPy (.xls)
├── notebooks/ # Jupyter Notebooks de procesamiento
│ ├── procesamiento_eeg.ipynb # Pipeline de procesamiento EEG (MNE)
│ └── analisis_sart.ipynb # Análisis de variables de desempeño
├── scripts/ # Scripts Python auxiliares
│ └── procesar_sart.py # Extracción de variables de desempeño
├── figures/ # Figuras y gráficos generados
└── README.md
```

---

## ️ Metodología

### Participantes
- 25 estudiantes de Ingeniería (UNER), adultos jóvenes sin diagnóstico de TDAH, TEA u otros trastornos neurológicos o psiquiátricos
- Visión normal o corregida, sin alteraciones auditivas
- Consentimiento informado firmado previamente

### Diseño Experimental
Diseño **entre grupos (inter-sujeto)**. Las sesiones se realizaron en el Laboratorio de Fisiología y Biofísica de la FIUNER bajo condiciones controladas de iluminación y ruido.

### Tarea SART
Implementada en **PsychoPy** (código abierto):
- **135 ensayos experimentales** + 10 ensayos de práctica
- Secuencia temporal por ensayo:
 - Cruz de fijación: `900 ms`
 - Dígito (1–9): `250 ms`
 - Máscara visual: `900 ms`
- Los participantes debían responder ante todos los dígitos **excepto el "3"** (estímulo no-go)

### Registro EEG
- **Amplificador:** BioAmp ([prototipado/bioamp](https://github.com/prototipado/bioamp))
- **Frecuencia de muestreo:** 2000 Hz
- **Montaje:** Sistema internacional 10–20
- **Canales registrados:** Fz, Cz, Pz
- **Referencias:** Mastoides izquierdo y derecho

### Estímulo Musical
Playlist de 8 canciones Lo-Fi (19 min) reproducida de manera uniforme vía Spotify. 
 [Acceder a la playlist](https://open.spotify.com/playlist/4AfH9GCcEaNw Rm2tRrEtX7?si=857f27e0e8bf47a7)

---

## Pipeline de Procesamiento EEG

El procesamiento se realizó en **Python** usando la librería [MNE](https://mne.tools/):

```python
import mne

# 1. Carga de señal cruda desde archivo de texto
raw = mne.io.RawArray(data, info)

# 2. Filtrado pasabanda 0.1 – 40 Hz
raw.filter(l_freq=0.1, h_freq=40)

# 3. Segmentación: últimos 2 minutos de la tarea SART
raw_segment = raw.crop(tmin=..., tmax=...)

# 4. Selección de canales de interés
raw_segment.pick_channels(['Fz', 'Cz', 'Pz'])

# 5. Estimación espectral (método de Welch)
psd, freqs = mne.time_frequency.psd_welch(raw_segment, ...)

# 6. Cálculo de la Razón Theta/Beta (RTB)
theta_power = psd[:, (freqs >= 4) & (freqs <= 8)].mean()
beta_power = psd[:, (freqs >= 13) & (freqs <= 30)].mean()
RTB = theta_power / beta_power
```

---

## Variables Analizadas

| Variable | Tipo | Descripción |
|---|---|---|
| Tiempo de Reacción (TR) | Desempeño | Tiempo de respuesta ante estímulos go |
| Total de Aciertos | Desempeño | Respuestas correctas ante estímulos go |
| Total de Rechazos Correctos | Desempeño | Inhibiciones correctas ante el estímulo no-go (3) |
| Rendimiento | Desempeño | Suma de aciertos y rechazos correctos |
| Razón Theta/Beta (RTB) | Neurofisiológica | Cociente PSD theta (4–8 Hz) / PSD beta (13–30 Hz) |

---

## Resultados Principales

- **Tiempo de reacción:** Sin diferencias significativas entre grupos (t(22) = −0.774, p = 0.447), pero **menor variabilidad** en el grupo con música (DE = 26.2 ms vs. 34 ms)
- **Rendimiento y aciertos:** Sin diferencias significativas (Mann-Whitney U; p > 0.05)
- **RTB:** Sin diferencias significativas (U = 50, p = 0.208), aunque el grupo Lo-Fi mostró mediana más baja (Me = 5.5 vs. 7.5)
- **Correlación desempeño–RTB:** No se observó asociación lineal significativa (R ≤ 0.293)

---

## Análisis Estadístico

El análisis se realizó con **Jamovi (v2.6.44)**:
- Estadísticos descriptivos e histogramas para evaluar normalidad
- **Prueba de Shapiro-Wilk** para normalidad formal
- **Prueba t para muestras independientes** (variable TR, distribución normal)
- **Prueba Mann-Whitney U** (demás variables, no normales)
- **Regresión lineal simple** para explorar correlación desempeño–RTB
- Nivel de significancia: **α = 0.05**

---

## Instalación y Uso

### Requisitos
```bash
pip install mne numpy scipy matplotlib pandas jupyter
```

### Ejecutar los notebooks
```bash
git clone https://github.com/lucasalarcon-arg/Procesamiento_senales_EEG.git
cd Procesamiento_senales_EEG
jupyter notebook
```

---

## Referencias

1. Besana & Cauring (2023). *The Perspective of Grade 10 Students of ARMM Regional Science High School on Listening to Various Songs in Their Study Habit.*
2. Kirk et al. (2022). *Effects of Three Genres of Focus Music on Heart Rate Variability and Sustained Attention.* J. Cogn. Enhanc., 6(2), 143–158.
3. Baldwin & Lewis (2017). *Positive valence music restores executive control over sustained attention.* PLOS ONE, 12(11).
4. Kiss & Linnell (2021). *The effect of preferred background music on task-focus in sustained attention.* Psychol. Res., 85(6), 2313–2325.
5. Robertson et al. (1997). *'Oops!': Performance correlates of everyday attentional failures.* Neuropsychologia, 35(6), 747–758.
6. Arns et al. (2013). *A Decade of EEG Theta/Beta Ratio Research in ADHD: A Meta-Analysis.* J. Atten. Disord., 17(5), 374–383.
7. Laboratorio E. y 3D, [prototipado/bioamp](https://github.com/prototipado/bioamp).
8. Gramfort (2013). *MEG and EEG data analysis with MNE-Python.* Front. Neurosci.
9. MNE Documentation — [Filtering and resampling data](https://mne.tools/stable/auto_tutorials/preprocessing/30_filtering_resampling.html).
10. Husain et al. (2002). *Effects of Musical Tempo and Mode on Arousal, Mood, and Spatial Abilities.* Music Percept., 20(2), 151–171.

---

## Palabras Clave

`SART` · `EEG` · `Lo-Fi` · `Razón Theta/Beta` · `Atención Sostenida` · `Neurofisiología` · `MNE-Python` · `PsychoPy` · `Biofísica`

---

## ️ Licencia

Este proyecto fue desarrollado con fines académicos en el marco de la asignatura **Fisiología y Biofísica** de la Facultad de Ingeniería de la UNER (FIUNER), año 2025.
