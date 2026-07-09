# algo2-stats

<a href="https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

Notebook para generar estadísticas sobre las entregas de los alumnos de la cátedra Méndez/Pandolfo de Algoritmos y Estructuras de Datos (FIUBA), a partir de la planilla de corrección en Google Sheets.

## Qué hace

A partir de la planilla de corrección (una planilla de Google Sheets con una hoja por TP), la notebook:

- Se autentica contra Google Sheets y descarga cada hoja como un DataFrame.
- Normaliza nombres de hojas y columnas para soportar tanto el esquema usado en 2025 como el usado desde 2026 (ver la sección "Normalización" dentro de la notebook).
- Calcula estadísticas sobre las entregas: el desglose de aprobados/desaprobados del TP0, quiénes entregaron el TP0 pero no el TP1 (abandonos post TP0), el desglose de aprobados/desaprobados del TP1, la distribución de notas entre quienes aprobaron el TP1 (tanto de la nota final como de cada sección — código, pruebas e informe — por separado), sendos desgloses de quienes aprobaron y desaprobaron el TP1 según su resultado en el TP0, la comparación de intentos de entrega entre aprobados y desaprobados del TP1, y el estado del pipeline de corrección automática entre los desaprobados del TP1 — todas con su propio gráfico (de torta, salvo las distribuciones de notas e intentos y el estado del pipeline, que usan gráficos de barras).

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

- Las secciones **RESULTADOS TP0**, **Abandonos post TP0**, **Resultados del TP1**, **Notas del TP1**, **Notas por sección del TP1**, **TP1 Aprobados**, **TP1 Desaprobados**, **Intentos en TP1** y **Estado en TP1 Desaprobados** ya están todas implementadas.
- Los nombres de hoja/columna de la planilla real pueden cambiar de un cuatrimestre a otro (ya pasó entre 2025 y 2026): si aparece una hoja o columna nueva que la notebook no reconoce, hay que agregarla a `SHEET_ALIASES`/`VALID_SHEETS` o a `COLUMN_ALIASES` en la sección de Normalización.
- La URL de la planilla a analizar vive en su propia sección **"Planilla a analizar"**, arriba de todo (justo debajo del título), separada de `## Constantes` para que sea lo primero que se ve y lo más fácil de cambiar. Además está marcada como campo de formulario de Colab (`#@param`, con `cellView: "form"`): al abrir la notebook en Colab esa celda se ve como un cuadro de texto editable, con el código oculto por defecto (se puede togglear con el botón de la celda si hace falta verlo).
- Cada sección de estadísticas (TP0, TP1, ...) chequea antes de correr si su hoja está presente en la planilla (por ejemplo, `ABB` no existe desde 2026), vía `hoja_disponible` (calculado en la sección Utilidades a partir de `HOJAS_REQUERIDAS`). Si la hoja no está, esa sección se omite con un único warning en vez de fallar con `KeyError`. Al implementar una sección nueva (`LISTA`, `TP2`, ...) hay que sumar su hoja a `HOJAS_REQUERIDAS` y guardar sus celdas con `if hoja_disponible[...]:`.
- Las figuras se numeran por sección: cada sección mayor (TP0, TP1, ...) tiene un prefijo fijo en `SECTION_FIGURE_PREFIX` (sección Utilidades) y sus figuras se numeran consecutivamente dentro de ese prefijo (TP0 → `1.1`, `1.2`; TP1 → `2.1`...`2.7`). Al agregar una sección nueva hay que sumarle una entrada a `SECTION_FIGURE_PREFIX` con el próximo número.
- Los logs del `logger` están en español. En una corrida sin problemas no debería imprimirse ningún log (los mensajes de camino feliz quedan en `debug`, no en `info`); solo se ven `warning`/`error`/`critical` ante datos o hojas inesperados (hoja faltante, valores de `Aprobado`/`Estado` que no matchean ninguna categoría conocida, etc.).
