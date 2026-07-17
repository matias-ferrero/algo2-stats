# algo2-stats

<a href="https://colab.research.google.com/github/matias-ferrero/algo2-stats/blob/main/stats_algo2.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

Notebook para generar estadísticas sobre las entregas de los alumnos de la cátedra Méndez/Pandolfo de Algoritmos y Estructuras de Datos (FIUBA), a partir de la planilla de corrección en Google Sheets.

## Qué hace

A partir de la planilla de corrección (una planilla de Google Sheets con una hoja por TP), la notebook se autentica contra Google Sheets, descarga y normaliza cada hoja, y genera un gráfico (de barras, de donut, o una barra de ratio, según corresponda) y, cuando aplica, una tabla con el detalle de los estudiantes, para cada una de estas preguntas:

**TP0**
- **RESULTADOS TP0**: qué proporción de estudiantes aprobó o desaprobó el TP0.
- **Abandonos post TP0**: quiénes entregaron el TP0 pero nunca llegaron a entregar el TP1, separados según si habían aprobado o desaprobado el TP0.

**TP1, LISTA, ABB, HASH y TP2**

Estas cinco hojas siguen el mismo orden cronológico de la materia (TP0 → TP1 → LISTA → ABB → HASH → TP2) y comparten exactamente los mismos 7 módulos, uno por cada hoja (con `TP1` reemplazado por `LISTA`/`ABB`/`HASH`/`TP2` según corresponda):

- **Resultados del TP1**: qué proporción aprobó, desaprobó, o todavía no tiene nota cargada (corrección abierta, o pendiente de una reentrega).
- **Notas del TP1**: distribución de las notas numéricas entre quienes aprobaron.
- **Notas por sección del TP1**: lo mismo, desglosado en Código, Pruebas e Informe (más Interacción — la defensa oral — en el caso del TP2), para ver si el rendimiento es parejo entre secciones.
- **TP1 Aprobados** / **TP1 Desaprobados**: cómo se relaciona el resultado de esa hoja con el resultado del TP inmediatamente anterior (incluye a quienes no tienen registro ahí, por ejemplo por RPL, o porque ese TP no se tomó ese cuatrimestre).
- **Intentos en TP1**: cuántos intentos de entrega hicieron los que aprobaron comparados con los que desaprobaron.
- **Estado en TP1 Desaprobados**: entre los que desaprobaron, cuántos fallaron por un problema del pipeline de corrección automática (no compila, timeout) contra cuántos sí llegaron a corregirse pero no alcanzaron la nota mínima.

`ABB` es un caso particular: la cátedra dejó de tomarlo como trabajo práctico desde 2026, así que en planillas de ese año en adelante la notebook detecta que la hoja no existe y omite toda la sección con un único warning (ver más abajo). Por eso, a diferencia del resto, la sección **ABB** de la notebook arranca colapsada por default en vez de abierta.

Si la hoja de un TP no existe en la planilla de ese cuatrimestre (por ejemplo, un TP que se dejó de tomar), la notebook lo detecta sola y omite esas estadísticas puntuales, sin afectar al resto.

### Cómo leer los gráficos

Todos los gráficos que muestran aprobado/desaprobado/pendiente (RESULTADOS TP0, Abandonos post TP0, y los módulos Resultados / X Aprobados / X Desaprobados / Estado en X Desaprobados de TP1, LISTA, ABB, HASH y TP2) usan siempre los mismos tres colores, así que una vez que se aprende el código en un gráfico se puede leer cualquier otro sin releer la leyenda:

- **Verde**: el caso positivo (aprobado, o corrección sin problemas).
- **Rojo**: el caso negativo (desaprobado, o error de corrección).
- **Ámbar**: lo pendiente o no disponible (sin nota todavía, sin registro previo, timeout).

Cuando la proporción es entre dos categorías (por ejemplo, aprobado/desaprobado), se muestra como una **barra de ratio** — un segmento por categoría, con el porcentaje y la cantidad de alumnos adentro. Cuando son tres categorías genuinas (por ejemplo, aprobado/desaprobado/sin nota), se muestra como una **donut**, con el total de alumnos en el hueco central.

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
