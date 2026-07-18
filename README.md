# algo2-stats

<a href="https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

Notebook para generar estadísticas sobre las entregas de los alumnos de la cátedra Méndez/Pandolfo de Algoritmos y Estructuras de Datos (FIUBA), a partir de la planilla de corrección en Google Sheets.

## Qué hace

A partir de la planilla de corrección (una planilla de Google Sheets con una hoja por TP), la notebook se autentica contra Google Sheets, descarga y normaliza cada hoja, y genera un gráfico (de barras, de donut, o una barra de ratio, según corresponda) y, cuando aplica, una tabla con el detalle de los estudiantes, para cada una de estas preguntas:

**General**

Estas cuatro estadísticas, a diferencia de todas las demás, no miran una hoja por vez sino todos los TPs en conjunto:

- **Tasa de aprobación y desaprobación por TP**: compara la proporción de aprobados, desaprobados y sin nota de TP1, LISTA, ABB, HASH y TP2 (TP0 no participa: no tiene `Nota final`), en un único gráfico de columnas (una por TP).
- **Análisis de notas**: distribución de notas numéricas juntando los aprobados de TP1, LISTA, ABB, HASH y TP2 (TP0 no tiene nota numérica, no participa).
- **Análisis por sección**: lo mismo desglosado en Código, Pruebas e Informe, pooleando esas mismas cinco hojas.
- **Abandonos entre TPs**: para cada transición entre TPs consecutivos (TP0→TP1, TP1→LISTA, ..., HASH→TP2), cuántos estudiantes aprobaron el anterior pero nunca entregaron el siguiente. Si una hoja intermedia no existe en la planilla (por ejemplo `ABB` desde 2026), esa transición salta el hueco automáticamente (queda `LISTA→HASH` en vez de romperse).

**TP0**
- **RESULTADOS TP0**: qué proporción de estudiantes aprobó o desaprobó el TP0.
- **Abandonos post TP0**: quiénes entregaron el TP0 pero nunca llegaron a entregar el TP1, separados según si habían aprobado o desaprobado el TP0.

**TP1, LISTA, ABB, HASH y TP2**

Estas cinco hojas siguen el mismo orden cronológico de la materia (TP0 → TP1 → LISTA → ABB → HASH → TP2) y comparten los mismos módulos base, uno por cada hoja (con `TP1` reemplazado por `LISTA`/`ABB`/`HASH`/`TP2` según corresponda):

- **Resultados del TP1**: qué proporción aprobó, desaprobó, o todavía no tiene nota cargada (corrección abierta, o pendiente de una reentrega).
- **Notas del TP1**: distribución de las notas numéricas entre quienes aprobaron.
- **Notas por sección del TP1**: lo mismo, desglosado en Código, Pruebas e Informe (más Interacción — la defensa oral — en el caso del TP2), para ver si el rendimiento es parejo entre secciones.
- **TP1 Desaprobados** (en la notebook: *"Resultado en TP0 de los desaprobados de TP1"*): cómo se relaciona el resultado de esa hoja con el resultado del TP inmediatamente anterior, entre quienes desaprobaron. Distingue cuatro casos: aprobó ese TP anterior, lo desaprobó, todavía no tiene nota ahí (**Sin Nota**, corrección pendiente o en reentrega), o no tiene ningún registro (**N/A**, por ejemplo por RPL, o porque ese TP no se tomó ese cuatrimestre).
- **Intentos en TP1**: cuántos intentos de entrega hicieron los que aprobaron comparados con los que desaprobaron.
- **Estado en TP1 Desaprobados**: entre los que desaprobaron, cuántos fallaron por un problema del pipeline de corrección automática (no compila, timeout) contra cuántos sí llegaron a corregirse pero no alcanzaron la nota mínima.

(Los nombres en negrita de arriba son el nombre corto de cada módulo, el que se usa para referirse a ellos en esta guía y en la notebook misma; el título real que ves en la notebook — tanto en el encabezado de la sección como en el gráfico — suele ser más largo y descriptivo, como en el ejemplo de TP1 Desaprobados.)

Solo **TP1** tiene además un módulo **TP1 Aprobados** (*"Resultado en TP0 de los aprobados de TP1"*), el complemento de TP1 Desaprobados: mira el resultado previo en el TP0 de quienes sí aprobaron el TP1, con la misma distinción Sin Nota/N/A. LISTA, ABB, HASH y TP2 no lo tienen — se sacó de esas cuatro secciones a propósito, para no duplicar el mismo análisis siete veces cuando alcanza con verlo una vez en TP1.

`ABB` es un caso particular: la cátedra dejó de tomarlo como trabajo práctico desde 2026, así que en planillas de ese año en adelante la notebook detecta que la hoja no existe y omite toda la sección con un único warning (ver más abajo). Por eso, a diferencia del resto, la sección **ABB** de la notebook arranca colapsada por default en vez de abierta. Por la misma razón, **HASH Desaprobados** compara contra `ABB` cuando esa hoja existe, y cae automáticamente a `LISTA` cuando no — sin eso, en planillas 2026+ el gráfico no tendría contra qué comparar. Como la comparación depende de la planilla, el encabezado de esa sección queda genérico ("...en el TP anterior..."), pero el título del gráfico sí nombra la hoja real que usó esa corrida.

Si la hoja de un TP no existe en la planilla de ese cuatrimestre (por ejemplo, un TP que se dejó de tomar), la notebook lo detecta sola y omite esas estadísticas puntuales, sin afectar al resto.

### Cómo leer los gráficos

Todos los gráficos que muestran aprobado/desaprobado/pendiente (Tasa de aprobación y desaprobación por TP, RESULTADOS TP0, Abandonos post TP0, Estado en X Desaprobados, y los módulos Resultados / TP1 Aprobados / X Desaprobados de TP1, LISTA, ABB, HASH y TP2) usan siempre la misma paleta, así que una vez que se aprende el código en un gráfico se puede leer cualquier otro sin releer la leyenda:

- **Verde**: el caso positivo (aprobado, o corrección sin problemas).
- **Rojo**: el caso negativo (desaprobado, o error de corrección).
- **Ámbar**: pendiente — la corrección de ese TP todavía no cerró (sin nota, o en reentrega), o entre los desaprobados de un pipeline, timeout.
- **Gris**: no disponible (N/A) — no hay ningún registro de ese TP para este alumno (nunca lo entregó, o directamente esa hoja no existe en esta planilla). Solo aparece en los módulos "X Aprobados"/"X Desaprobados", que son los únicos que cruzan contra un TP anterior; el resto de los gráficos solo usa verde/rojo/ámbar.

Cuando la proporción es entre dos categorías (por ejemplo, aprobado/desaprobado), se muestra como una **barra de ratio** — un segmento por categoría, con el porcentaje y la cantidad de alumnos adentro. Cuando son tres o más categorías genuinas (por ejemplo, aprobado/desaprobado/sin nota/N/A), se muestra como una **donut**, con el total de alumnos en el hueco central. Tasa de aprobación y desaprobación por TP es la única excepción: como compara la misma proporción entre varios TPs a la vez, usa varias columnas apiladas una al lado de la otra (una por TP, con eje X y eje Y) en vez de un donut por TP, para que las proporciones se puedan comparar de un vistazo entre columnas. El eje Y mide cantidad de alumnos, no porcentaje, así que la altura total de cada columna también dice cuántos entregaron ese TP — las etiquetas de cada segmento siguen mostrando el porcentaje además de la cantidad.

En los donuts, si una porción es muy chica su etiqueta (`%` + cantidad) sale del gráfico con una línea fina que la conecta a su porción, en vez de quedar apretada adentro — así nunca se superpone con la etiqueta de la porción de al lado, sin importar cuántas porciones chicas haya juntas.

## Cómo está organizada la notebook

Debajo del título hay una introducción general (qué hace, cómo correrla, cómo está organizada) y después 3 subtítulos principales:

- **Planilla**: la URL de la planilla de Google Sheets a analizar. Es lo único que hace falta tocar para correr sobre otro cuatrimestre.
- **Configuración**: todo el armado previo — conexión con Google Sheets, descarga y normalización de los datos. En general no hace falta tocar nada acá.
- **Estadísticas**: los gráficos y tablas descriptos arriba, agrupados por TP.

Al abrir la notebook en Colab, la mayoría de las secciones arrancan colapsadas — quedan abiertas de entrada **Planilla** y todos los gráficos/tablas de **Estadísticas**, para poder ver los resultados de una sin tener que ir abriendo secciones. La única excepción es **ABB**, que arranca colapsada como el resto de Configuración: esa hoja dejó de tomarse desde 2026, así que sus estadísticas quedan un clic más lejos. El resto (Configuración, ABB, y el detalle de implementación de cada gráfico) se puede expandir con la flechita al lado de cada subtítulo si hace falta ver más detalle.

## Estructura del repositorio

- `stats_algo2.ipynb`: la notebook.
- `tablas/`: **no versionada** (ver `.gitignore`). Carpeta local para excels de ejemplo (exports de la planilla real) usados solo para pruebas manuales; cada quien la arma en su máquina, no se sube a git.

## Cómo correr en Google Colab

La notebook **tiene que correrse en Google Colab**: no es solo la opción recomendada, es un requisito técnico. La celda de **Dependencias** hace `from google.colab import auth` para autenticarse contra Google Sheets, y ese paquete solo existe dentro del runtime de Colab — en Jupyter local, VS Code, u otro entorno, esa celda falla al importar y la notebook no llega a correr. La ventaja de esta restricción es que se lee directo la planilla de Google Sheets en vivo, sin tener que descargar ni subir ningún archivo.

1. Abrí la notebook desde GitHub en Colab con el badge de arriba, o directamente con este link:

   [https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb?authuser=1](https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb?authuser=1)

   También podés ir a [colab.research.google.com](https://colab.research.google.com) → `Archivo > Abrir notebook > GitHub` y buscar `matias-ferrero/algo2-stats`.
2. En **Planilla**, pegá la URL de la planilla a analizar (o dejá la que ya está, si es la correcta).
3. `Entorno de ejecución > Ejecutar todas` (o corré celda por celda desde arriba hacia abajo).
4. Al llegar a **Autenticación** van a aparecer dos pop-ups: uno para elegir la cuenta de Google, y otro para autorizar el acceso a Drive/Sheets. Esa cuenta necesita tener acceso de lectura a la planilla; si no, la notebook va a fallar más adelante, al bajar las hojas. Si el login se completa bien no vas a ver ningún mensaje en la consola; si algo falla, sí vas a ver un error explicando qué pasó.
5. El resto de las celdas corren solas: descargan las hojas, las normalizan, y generan las estadísticas.

> El link de arriba apunta siempre a la rama `main` de GitHub, así que solo vas a ver ahí los cambios que ya estén mergeados. Si querés probar una rama con un PR abierto, reemplazá `main` por el nombre de esa rama en la URL.

## Si algo falla

- **No se puede abrir la planilla / error al autenticarse**: revisá que la URL en **Planilla** sea correcta y completa, y que la cuenta de Google con la que iniciaste sesión tenga acceso de lectura a esa planilla.
- **Falta el gráfico de un TP entero**: si ese TP no existe en la planilla de este cuatrimestre, la notebook lo salta y avisa, sin romper el resto — no hace falta hacer nada.
- **Aparece un mensaje de advertencia (amarillo)**: suele indicar un dato puntual inesperado en la planilla (una hoja vacía, un valor que no se pudo interpretar); la notebook sigue corriendo igual, pero puede valer la pena revisar ese dato en la planilla original.
- **Aparece un mensaje de error (rojo)**: algo impidió seguir (por ejemplo, no se pudo leer ninguna hoja); el mensaje explica qué pasó.
