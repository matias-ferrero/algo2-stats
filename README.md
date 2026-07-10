# algo2-stats

<a href="https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

Notebook para generar estadísticas sobre las entregas de los alumnos de la cátedra Méndez/Pandolfo de Algoritmos y Estructuras de Datos (FIUBA), a partir de la planilla de corrección en Google Sheets.

## Qué hace

A partir de la planilla de corrección (una planilla de Google Sheets con una hoja por TP), la notebook:

- Se autentica contra Google Sheets y descarga cada hoja como un DataFrame.
- Normaliza nombres de hojas y columnas para soportar tanto el esquema usado en 2025 como el usado desde 2026 (ver la sección "Normalización" dentro de la notebook) — incluyendo el alias de la columna `status` (2025) a `Estado`, el nombre que usa el resto de la notebook.
- Calcula estadísticas sobre las entregas: el desglose de aprobados/desaprobados del TP0, quiénes entregaron el TP0 pero no el TP1 (abandonos post TP0), el desglose de aprobados/desaprobados del TP1, la distribución de notas entre quienes aprobaron el TP1 (tanto de la nota final como de cada sección — código, pruebas e informe — por separado), sendos desgloses de quienes aprobaron y desaprobaron el TP1 según su resultado en el TP0, la comparación de intentos de entrega entre aprobados y desaprobados del TP1, y el estado del pipeline de corrección automática entre los desaprobados del TP1 — todas con su propio gráfico (de torta, salvo las distribuciones de notas e intentos y el estado del pipeline, que usan gráficos de barras).
- Si la hoja de un TP no existe en la planilla de ese cuatrimestre (por ejemplo, `ABB` no existe desde 2026), omite las estadísticas de esa sección con un aviso en vez de fallar.

## Cómo está organizada la notebook

Debajo del título hay una introducción general (qué hace, cómo correrla, cómo está organizada) y después 3 subtítulos principales:

- **Planilla**: la URL de la planilla de Google Sheets a analizar. Es lo único que hace falta tocar para correr sobre otro cuatrimestre; el campo es un formulario de Colab (`#@param`), con el código oculto por defecto.
- **Configuración**: todo el armado previo a calcular cualquier estadística — imports (**Dependencias**), nombres canónicos de columna/hoja (**Constantes**), el logger (**Logger**), y la conexión con Google Sheets más la normalización de los datos (**Preprocesamiento**: Autenticación, Normalización, Hojas, Columnas, Vista previa).
- **Estadísticas**: el análisis en sí. **Utilidades** tiene los helpers compartidos por los gráficos (formateo de etiquetas, dibujo de barras, chequeo de qué hojas están disponibles, aviso de valores no reconocidos, numeración de figuras); cada TP (`TP0`, `TP1`, y los que se sumen después) agrupa sus propios módulos, y cada módulo tiene su **Implementación** (la función que arma el gráfico) y su **Resultado** (donde se corre esa función y se ve el gráfico o la tabla).

Al abrir la notebook en Colab, la mayoría de las secciones arrancan colapsadas (`metadata.colab.collapsed_sections`) — quedan abiertas de entrada **Planilla** y, dentro de Estadísticas, toda la cadena hasta cada **Resultado** (`Estadísticas` → cada TP → cada módulo → `Resultado`), ya que en Colab colapsar una sección oculta todo lo que cuelga de ella. Lo que sigue colapsado es **Configuración** (y sus subsecciones) y cada **Implementación**.

## Estructura del repositorio

- `stats_algo2.ipynb`: la notebook.
- `tablas/`: **no versionada** (ver `.gitignore`). Carpeta local para excels de ejemplo (exports de la planilla real) usados solo para pruebas manuales; cada quien la arma en su máquina, no se sube a git.

## Cómo correr en Google Colab

La notebook está pensada para correr en Google Colab, leyendo directo la planilla de Google Sheets en vivo (no hace falta descargar ni subir ningún archivo).

1. Abrí la notebook desde GitHub en Colab con el badge de arriba, o directamente con este link:

   [https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb?authuser=1](https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb?authuser=1)

   También podés ir a [colab.research.google.com](https://colab.research.google.com) → `Archivo > Abrir notebook > GitHub` y buscar `matias-ferrero/algo2-stats`.
2. En **Planilla**, pegá la URL de la planilla a analizar (o dejá la que ya está, si es la correcta).
3. `Entorno de ejecución > Ejecutar todas` (o corré celda por celda desde arriba hacia abajo).
4. Al llegar a **Autenticación** van a aparecer dos pop-ups: uno para elegir la cuenta de Google, y otro para autorizar el acceso a Drive/Sheets. Esa cuenta necesita tener acceso de lectura a la planilla; si no, la notebook va a fallar más adelante, al bajar las hojas. Si el login se completa bien no vas a ver ningún mensaje en la consola (es silencioso a propósito); si algo falla, sí vas a ver un error explicando qué pasó.
5. El resto de las celdas corren solas: descargan las hojas, las normalizan, y generan las estadísticas — los gráficos y tablas de cada módulo aparecen en su subsección **Resultado**.

> El link de arriba apunta siempre a la rama `main` de GitHub, así que solo vas a ver ahí los cambios que ya estén mergeados. Si querés probar una rama con un PR abierto, reemplazá `main` por el nombre de esa rama en la URL.

## Convenciones para agregar secciones nuevas

Todo esto vive en la subsección **Utilidades**, dentro de Estadísticas:

- **Hojas o columnas renombradas**: si la planilla real cambia un nombre de hoja o columna de un cuatrimestre a otro (ya pasó entre 2025 y 2026), hay que agregarlo a `SHEET_ALIASES`/`VALID_SHEETS` (Constantes) o a `COLUMN_ALIASES` (Normalización).
- **Chequeo de hoja disponible**: cada sección de estadísticas depende de `hoja_disponible` (calculado a partir de `HOJAS_REQUERIDAS`) para saltearse sin error si su hoja no está en la planilla de ese cuatrimestre. Al implementar una sección nueva (`LISTA`, `TP2`, ...) hay que sumar su hoja a `HOJAS_REQUERIDAS` y guardar sus celdas con `if hoja_disponible[...]:`.
- **Numeración de figuras**: cada sección mayor (TP0, TP1, ...) tiene un prefijo fijo en `SECTION_FIGURE_PREFIX`, y sus figuras se numeran consecutivamente dentro de ese prefijo (TP0 → `1.1`, `1.2`; TP1 → `2.1`...`2.7`). Al agregar una sección nueva hay que sumarle una entrada con el próximo número.
- **Secciones abiertas/colapsadas por defecto**: ver "Cómo está organizada la notebook" más arriba. Al agregar un módulo nuevo, hay que sacar su encabezado y el de su TP de `collapsed_sections` (dejarlos abiertos) para que su Resultado también quede visible de una, sin necesidad de expandir nada a mano.

## Notas

- Las secciones **RESULTADOS TP0**, **Abandonos post TP0**, **Resultados del TP1**, **Notas del TP1**, **Notas por sección del TP1**, **TP1 Aprobados**, **TP1 Desaprobados**, **Intentos en TP1** y **Estado en TP1 Desaprobados** ya están todas implementadas.
- Los logs del `logger` están en español. En una corrida sin problemas no debería imprimirse ningún log (los mensajes de camino feliz quedan en `debug`, no en `info`); solo se ven `warning`/`error`/`critical` ante datos o hojas inesperados (hoja faltante, valores de `Aprobado`/`Estado` que no matchean ninguna categoría conocida, etc.).
