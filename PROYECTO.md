# El herbario de las sabias — estado del proyecto

Página única (sin build, sin dependencias) que responde a una pregunta:
*«¿qué plantas se han usado tradicionalmente para esta dolencia, cómo se
preparan y qué precauciones exigen?»*

Desde el 02-11-2026 la página está ambientada en el saber de las mujeres a las
que llamaron brujas: pergamino, tinta sepia y lacre, un marco histórico en la
portada y la leyenda de cada planta donde la tenga. **La ambientación llega hasta
donde empieza la seguridad y ni un paso más** (ver los invariantes).

## Archivos

| Archivo | Qué es |
|---|---|
| `index.html` | **El proyecto.** Todo vive aquí: estilos, datos y lógica. |
| `fotos/*.jpg` | Fotos y láminas de Wikimedia Commons, una por planta, nombradas con el `id` de la planta. |
| `fotos/creditos.json` | Autoría, licencia y URL del archivo original de cada foto. Fuente de verdad del crédito. Una entrada por planta, ni una más. |
| `estampas/*.jpg` | Los cuadros de la página (no son plantas): el frontispicio, el curandero y el detalle del lateral. Todos de dominio público. |
| `estampas/creditos.json` | Título, autor, año, licencia y archivo original de cada estampa, más la nota de si va recortada. |
| `PROYECTO.md` | Este archivo. |
| `_antiguo/` | El primer diseño, apartado el 02-11-2026: tres maquetaciones y sus 29 ilustraciones. No lo usa nada. Ver `_antiguo/LEEME.md`. |

La raíz tiene exactamente cuatro entradas: `index.html`, `PROYECTO.md`, `fotos/`
y `_antiguo/`. Si aparece algo más, es basura de alguna herramienta.

## Cómo está hecho `index.html`

Cuatro bloques dentro del `<script>`, numerados en comentarios:

1. **`DOLENCIAS`** — lista ordenada de `{ id, nombre }`. El orden es el del desplegable.
2. **`PLANTAS`** — una ficha por planta, **nunca repetida**. Campos:
   `id`, `nombre`, `cientifico`, `parte`, `img`, `indicaciones` (ids de dolencia),
   `descripcion`, `notas` (matiz por dolencia), `preparacion`, `dosis`,
   `avisos` (array), `curiosidades` y, opcional, `brujeria`.
   `brujeria` es el folclore de la planta: qué se colgaba en la puerta, con qué
   se ataba la escoba, qué servía para echar la suerte. Solo la llevan las 16
   plantas con leyenda documentada; si falta, el bloque de la ficha no se
   muestra (`hidden`), no sale vacío.
3. **`CREDITOS`** — `{ tipo, autor, licencia, url }` por `id` de planta.
4. **Lógica** — `construirIndice()` deriva el índice dolencia → plantas de las
   `indicaciones`. **No hay índice que mantener a mano.**
5. **`TISANAS`** — las 8 mezclas del lateral. Campos: `id`, `nombre`, `lema`,
   `dolencias` (ids), `ingredientes` (array), `rinde`, `preparacion`, `dosis`,
   `avisos` (array) y `nota` opcional. `dolencias` es lo que cruza cada receta
   con el buscador: al elegir un mal, `marcarTisanas()` señala las que sirven.

### El tisanario del lateral

Va en un `<aside class="tisanario">` que es hermano de `.container` dentro de
`.pliego`. Por debajo de 1260&nbsp;px es una sección normal al final de la página;
a partir de ahí, `.pliego` se vuelve una rejilla de dos columnas y el aside pasa a
`position: sticky` en el margen izquierdo. El aside va **después** de `.container`
en el HTML a propósito: así en móvil cae al final y el orden de tabulación llega
antes al contenido principal.

Cada receta se abre en `<dialog id="receta">`, igual que las fichas de planta, que
es lo que da Escape, foco atrapado y cierre al pulsar fuera sin escribir nada.

### Invariantes que no hay que romper

- **Una planta, una ficha.** Para que aparezca en otra dolencia, se añade su id a
  `indicaciones` y un matiz en `notas`; no se duplica la planta.
- **Las dolencias sin plantas no se ocultan ni engañan:** el selector las marca
  «próximamente» y las deshabilita. Mejor un hueco honesto que una ficha a medias.
- **Toda imagen lleva crédito.** Las licencias CC lo exigen; el campo `tipo`
  (fotografía / lámina botánica) está revisado a mano, no deducido del nombre.
- **Si falta la foto, `marcadorSinFoto()` deja un hueco con 🌿**, no un icono roto.
  Una planta puede entrar sin foto y recibirla después.
- **Los `avisos` son la parte seria.** Embarazo y lactancia, niños, interacciones
  con fármacos, duración máxima y el umbral de «ve al médico». Van siempre.
- **El disclaimer de cabecera no se suaviza.** Esto es divulgación de uso
  tradicional, no consejo médico.
- **Cada tisana lleva sus propios `avisos`, y no son los de sus plantas sumados.**
  Una mezcla crea riesgos que no tiene ningún ingrediente por separado, y hay que
  escribirlos: la miel de «Contra el soplo de invierno» no se da a menores de un
  año, el regaliz de «Rescoldo de garganta» limita la receta a una semana, y
  «Después del banquete» avisa de quitar la menta si lo que hay es reflujo.
- **Los ingredientes de una tisana salen del herbario.** Si una receta pide una
  planta que no está en `PLANTAS`, o se añade la ficha o se cambia la receta.
- **La ambientación no entra en lo médico.** `preparacion`, `dosis` y `avisos` van
  en voz llana, en tipografía de palo y sobre el fondo ámbar de advertencia; el
  folclore vive en `brujeria` y en `curiosidades`, visualmente separado. Un
  herbario de brujas que confunda las dos cosas es un herbario peligroso.
- **La historia, honesta.** El marco histórico dice también lo que la
  historiografía ha desmentido: que la caza de brujas no fue principalmente una
  persecución de curanderas, y que el «vuelo» eran solanáceas con atropina. Si se
  amplía esa sección, se amplía con datos, no con leyenda.
- Tema claro y oscuro con variables CSS en `:root`; sin CDN ni fuentes externas
  (la tipografía sale de pilas de fuentes del sistema).

## Estado: completo

**26 de 26 dolencias documentadas · 55 plantas · 55 fotos con crédito.**
Ya no queda ninguna dolencia en «próximamente», así que el grupo
«Aún sin contenido» del desplegable no llega a mostrarse.

Comprobaciones que pasa el archivo: sin ids duplicados, sin indicaciones que no
existan en `DOLENCIAS`, ninguna dolencia con una sola planta, ninguna ficha
incompleta, ninguna indicación sin su `nota`, y crédito con autor, licencia y
enlace para las 55 fotos.

## Cómo verificar

No hay tests. Para revisar los datos sin abrir el navegador se extrae el
`<script>` a un archivo temporal y se carga en node:

1. Volcar a `.check.js` lo que haya entre `<script>` y `</script>`.
2. `node --check .check.js` comprueba la sintaxis.
3. Cargar los datos sin tocar el DOM, cortando el script antes de `const INDICE`
   (que es donde empieza a usar `document`):
   `new Function(js.split('const INDICE')[0] + '; return {PLANTAS,DOLENCIAS,CREDITOS};')()`
4. Contrastar los tres objetos entre sí y borrar `.check.js`.

## Estampas

Los cuadros van en `<figure class="estampa">` con su `figcaption`, y el crédito
completo se lista aparte en el pie (`ESTAMPAS` en el JS, lista `listaEstampas`).
Van separados de `CREDITOS` a propósito: aquello es una entrada por planta.

- **Solo dominio público**, y se comprueba en la API antes de usar nada: con una
  foto de museo hay que mirar dos licencias, la del cuadro y la de la fotografía.
  La de Anker la subió *Paradise Chronicle* también como dominio público, y por eso
  se la menciona en el crédito.
- **Fuera el marco.** La foto de Anker venía con su marco dorado y la pared del
  museo; se recortó con Pillow por fracciones de la imagen (x 0,15–0,83,
  y 0,17–0,83) hasta que no quedó ni un hilo de oro.
- **Nada de retratos de personas reales**, ni fotografías modernas de curanderos:
  pintura de género del XIX, que ilustra sin señalar a nadie.
- Ancho 1000&nbsp;px para las anchas y 520&nbsp;px para la del lateral; mismo filtro
  sepia tenue que las fotos de plantas, para que no desentonen con el pergamino.
- Los `alt` describen la escena, no la nombran: alguien que no ve la imagen debe
  enterarse de que hay una escoba apoyada detrás.

## Imágenes

- `fotos/*.jpg` son **todas** de Wikimedia Commons, bajadas a **1000 px de ancho**.
- Sobra `fotos/cicuta.jpg`: la cicuta no es ficha del vademécum, solo se nombra
  como advertencia en el hinojo.
- Cuando Commons no declara autor en el campo `Artist`, se atribuye a quien subió
  el archivo (`iiprop=user` en la API). «Autoría no indicada» no es un crédito
  válido para una CC BY-SA.

### Cómo comprobar que cada crédito es de su foto

El riesgo real no es que falte un crédito, sino que esté puesto el de otra foto.
Para detectarlo se compara la **proporción** del archivo en disco con la del
original que dice `archivoOriginal`, pidiendo `iiprop=size` a la API de Commons en
lotes de 25 títulos. Si no coinciden, el crédito apunta a otra imagen.

- El hash MD5 **no sirve**: Commons regenera las miniaturas y los bytes cambian.
- Esa comprobación encontró dos fallos el 02-11-2026: `sauce-blanco` estaba
  atribuido a Wilhelm Zimmerling cuando la foto era de Burkhard Mücke, y
  `olivo.jpg` estaba guardado girado 90°.
- Si una foto viene girada, se baja el original grande y se reescala con Pillow
  aplicando `ImageOps.exif_transpose` antes de redimensionar a 1000 px de ancho.
- Las ilustraciones del diseño antiguo están en `_antiguo/imagenes/` (29 PNG) y no
  las usa nada. Los 25 PNG que había sueltos en la raíz eran copia byte a byte de
  esas mismas, con el nombre sin normalizar (`Anís.png`, `aloe vera .png`,
  `hinojo (2).png`); se borraron el 02-11-2026 tras comprobar los hashes.

## Pendiente

- `_antiguo/` (14,8 MB) se conserva solo como referencia visual. Se puede borrar
  entera sin que el proyecto se entere.
- `fotos/alcachofa.jpg` es correcta botánicamente (una *Cynara* en flor), pero en
  Commons el archivo está mal titulado como *Galactites tomentosa*. Si incomoda
  citar ese título, hay que sustituirla por una foto bien identificada.

## Publicar: GitHub paso a paso

El sitio es estático y no necesita servidor: funciona igual abierto con doble clic
que servido por HTTP.

El repositorio **ya está creado en local**, con su primer commit. Falta subirlo.

### 1. Crear el repositorio vacío en GitHub

En [github.com](https://github.com), botón verde **New** (o el `+` de arriba a la
derecha → *New repository*).

- **Repository name:** `herbario-de-las-sabias`
- **Public**, obligatorio: GitHub Pages solo es gratis en repositorios públicos.
- **No marques nada más.** Ni *Add a README*, ni *.gitignore*, ni *license*. Si
  marcas cualquiera de los tres, GitHub crea un commit por su cuenta, las dos
  historias dejan de encajar y el primer envío se rechaza con un error de
  `rejected / non-fast-forward`.

Botón **Create repository**.

### 2. Subirlo

En la carpeta del proyecto, cambiando `TU-USUARIO`:

```bash
git remote add origin https://github.com/TU-USUARIO/herbario-de-las-sabias.git
git push -u origin main
```

Se abre una ventana del navegador para autorizar con tu cuenta de GitHub (el gestor
de credenciales ya está configurado, no hace falta crear ningún token). Son 24 MB:
tarda un minuto largo y parece que no hace nada. Deja que termine.

### 3. Activar GitHub Pages

En el repositorio: **Settings** (arriba a la derecha) → **Pages** (menú izquierdo) →
en *Build and deployment*, **Source: Deploy from a branch** → **Branch: main**,
carpeta **/ (root)** → **Save**.

A los dos o tres minutos el sitio está en:

```
https://TU-USUARIO.github.io/herbario-de-las-sabias/
```

### 4. A partir de ahora, publicar es guardar

```bash
git add -A
git commit -m "Nueva entrada sobre el espino blanco"
git push
```

Y la web se actualiza sola en un par de minutos. Eso es todo el uso de GitHub que
este proyecto necesita: esos tres comandos.

### Lo que conviene saber

- **Lo ignorado nunca se publica.** `index.html.bak` está en `.gitignore`, así que no
  llega al sitio. Con un despliegue por arrastrar carpetas sí habría llegado.
- **`_antiguo/` no debería publicarse**, porque Pages pasa los archivos por Jekyll y
  Jekyll ignora las carpetas que empiezan por guion bajo. Queda guardado en el
  historial pero fuera de la web. Compruébalo entrando a
  `.../_antiguo/index-original.html`: si da 404, correcto. **No añadas un archivo
  `.nojekyll`**, porque desactiva Jekyll y entonces sí se publicaría.
- Ningún archivo del sitio lleva `{{` ni `{%`, que es lo único que Jekyll podría
  estropear. Si algún día escribes eso en una entrada, habrá que escaparlo.
- **Los servidores distinguen mayúsculas y Windows no.** `Foto.JPG` y `foto.jpg` son
  lo mismo en tu disco y cosas distintas en GitHub Pages; así es exactamente como se
  rompieron las tres páginas de `_antiguo/`. Todos los archivos van en minúsculas,
  sin acentos y sin espacios: **mantenlo así**.
- **El proyecto está dentro de OneDrive**, que ahora sincroniza también una carpeta
  `.git` de 24 MB. Funciona, pero si algún día un commit falla con un error de
  bloqueo de archivo, suele ser OneDrive tocándolos a la vez; excluir `.git` de la
  sincronización lo evita.
- Si algún día prefieres Cloudflare Pages, se conecta a este mismo repositorio y
  despliega igual. Teniendo el proyecto en git, el alojamiento deja de importar.

### Alternativa sin git: Netlify Drop

Arrastrar la carpeta entera a **app.netlify.com/drop** publica el sitio en treinta
segundos, pero sube *todo* lo que haya dentro, así que antes hay que sacar
`index.html.bak` y `_antiguo/` (15 MB de diseño descartado). Para actualizar, se
vuelve a arrastrar. Sin historial y sin despliegue automático.

## Cómo añadir una entrada

1. Copia una de las de `entradas/` como plantilla. Llevan `../estilo.css`,
   `../index.html` en el enlace de vuelta y `../estampas/` en las imágenes.
2. Nombre del archivo en minúsculas y con guiones, sin acentos.
3. Añade una línea al principio de `ENTRADAS`, en `index.html`: la más reciente va
   primera. Con eso aparece sola en «Del cuaderno».
4. Si la entrada habla de preparar algo que se bebe, lleva su advertencia al final.
   El mismo criterio que las fichas.

## Cómo añadir una dolencia

1. Elegir 3-5 plantas de uso tradicional reconocido para ella.
2. Las que ya estén en `PLANTAS`: añadir el id a `indicaciones` + un matiz en `notas`.
3. Las nuevas: ficha completa al final de `PLANTAS`, con `avisos` de verdad.
4. Buscar la foto en Wikimedia Commons (licencia libre), guardarla en
   `fotos/<id>.jpg` a 1000 px y anotar autor y licencia en `CREDITOS` y en
   `fotos/creditos.json`.
5. Comprobar en el navegador: la dolencia pasa sola de «próximamente» a «Disponibles».
