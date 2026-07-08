# algo2-stats

<a href="https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

Notebook para generar estadísticas sobre las entregas de los alumnos de la cátedra Méndez/Pandolfo de Algoritmos y Estructuras de Datos (FIUBA), a partir de la planilla de corrección en Google Sheets.

## Qué hace

A partir de la planilla de corrección (una planilla de Google Sheets con una hoja por TP), la notebook:

- Se autentica contra Google Sheets y descarga cada hoja como un DataFrame.
- Normaliza nombres de hojas y columnas para soportar tanto el esquema usado en 2025 como el usado desde 2026 (ver la sección "Normalización" dentro de la notebook).
- Calcula estadísticas sobre las entregas: el desglose de aprobados/desaprobados del TP0, quiénes entregaron el TP0 pero no el TP1 (abandonos post TP0), el desglose de aprobados/desaprobados del TP1, la distribución de notas entre quienes aprobaron el TP1, y sendos desgloses de quienes aprobaron y desaprobaron el TP1 según su resultado en el TP0 — todas con su propio gráfico (de torta, salvo la distribución de notas del TP1, que usa un gráfico de barras).

## Estructura del repositorio

- `stats_algo2.ipynb`: la notebook.
- `tablas/`: **no versionada** (ver `.gitignore`). Carpeta local para excels de ejemplo (exports de la planilla real) usados solo para pruebas manuales; cada quien la arma en su máquina, no se sube a git.

## Cómo correr en Google Colab

La notebook está pensada para correr en Google Colab, leyendo directo la planilla de Google Sheets en vivo (no hace falta descargar ni subir ningún archivo).

1. Abrí la notebook desde GitHub en Colab con el badge de arriba, o directamente con este link:

   [https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb?authuser=1](https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb?authuser=1)

   También podés ir a [colab.research.google.com](https://colab.research.google.com) → `Archivo > Abrir notebook > GitHub` y buscar `matias-ferrero/algo2-stats`.
2. `Entorno de ejecución > Ejecutar todas` (o correr celda por celda desde arriba hacia abajo).
3. En la sección **Autenticación**, al ejecutar esa celda va a aparecer un pop-up pidiendo iniciar sesión con una cuenta de Google que tenga acceso a la planilla. Seguí el flujo de login; la consola va a mostrar `Authentication successful` cuando termine.
4. El resto de las celdas corren solas: descargan las hojas, las normalizan, y generan las estadísticas.

> El link de arriba apunta siempre a la rama `main` de GitHub, así que solo vas a ver ahí los cambios que ya estén mergeados. Si querés probar una rama con un PR abierto, reemplazá `main` por el nombre de esa rama en la URL.

## Notas

- Las secciones **RESULTADOS TP0**, **Abandonos post TP0**, **Resultados del TP1**, **Notas del TP1**, **TP1 Aprobados** y **TP1 Desaprobados** ya están todas implementadas.
- Los nombres de hoja/columna de la planilla real pueden cambiar de un cuatrimestre a otro (ya pasó entre 2025 y 2026): si aparece una hoja o columna nueva que la notebook no reconoce, hay que agregarla a `SHEET_ALIASES`/`VALID_SHEETS` o a `COLUMN_ALIASES` en la sección de Normalización.
