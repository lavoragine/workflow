# Producción

## Preproducción

### Preliminares

Editar supone fijar un texto. Y ya sea que este texto provenga de una traducción, en cuyo caso lo usual es que el texto vaya al traductor en la forma en que fue publicado originalmente (es raro traducir un texto que no ha sido previamente editado y publicado, aunque puede ocurrir), sea que ha sido trabajado por el editor en colaboración con el autor hasta darle su forma final, llega un momento en que hemos de establecer una versión sobre la cual trabajaremos para transformar este texto en un libro (como dice una amiga mía: los escritores redactan manuscritos, los editores son quienes fabrican libros). Este texto editado o traducido es la base del proceso de producción editorial.

### El formato

El primer paso es darle formato al texto, lo que en terminos prácticos significa darle una organización semántica. En este paso estamos a años luz de pensar en el aspecto del texto (no, escritores: organizar semánticamente un texto es algo muy distinto a poner los títulos en negritas o a escoger una tipografía especial para los epigrafes), lo que necesitamos hacer es darle una estructura que tenga sentido y esto es algo que se realiza mediante el etiquetado de sus partes.

Esto, que suena algo críptico, no es otra cosa que introducir instrucciones (las etiquetas) al interior del texto para que las herramientas que utilicemos para procesarlo y convertirlo a los formatos de salida que necesitemos, sepan qué significado tienen esas partes en la organización general del futuro libro. Lenguajes de etiquetado hay muchos, de complejidad variable, y todos tienen ventajas y desventajas. Los más conocidos son los derivados del XML, como el HTML o el DOCBOOK, aunque existen muchísimos otros. Para el caso que nos ocupa, que es generar un *workflow* lo más ligero y automatizable posible en el contexto de una editorial pequeña y que no puede darse el lujo de perder tiempo ni recursos (o tal vez ni siquiera tiene esos recursos), vamos a utilizar *Markdown*, un lenguaje de etiquetado básico cuya sintaxis es posible aprender en una tarde, legible sin necesidad de un pre-procesador, y que permite introducir toda la información semántica (incluso los metadatos) en el archivo fuente que luego reutilizaremos para generar los formatos de salida.

Alguien podría objetar que no es necesario aprender un lenguaje de etiquetado, por muy sencillo que sea, si tenemos MS Word (o uno cualquiera de sus equivalentes: Openoffice, Text, Abiword, etc...). Hay numerosas razones para pasar por alto esta objeción. Y todas ellas tienen un denominador común: cualquier workflow sensato necesita tener control sobre el texto y su formato, lo que es una manera tan buena como cualquiera de decir que un workflow editorial exige la separación entre el contendo y su presentación. Word (como cualquier procesador de texto) mezcla los dos conceptos hasta el punto en que, aunque uno lo utilizce bien (asignando estilos a la estructura semántica del texto), resulta extremadamente difícil averiguar qué falla cuando algo falla (y creanme: cuando se ha estado un tiempo en el negocio de publicar material impreso, sabe que la cantidad de cosas que pueden fallar son incontables e impredecibles). Markdown, en cambio, es un lenguaje de etiquetado transparente, que se expresa en cualquier editor de texto, y lo que uno ve ahí es todo lo que hay (no hay *macros* malignos corriendo fuera de tu campo de visión, nada de codificaciones extrañas corrompiendo tu archivo[1](https://ia.net/topics/multichannel-text-processing/)˚). Esto significa, en último término, que tu tienes el control sobre tu contenido. Y si estas en control, puedes automatizar tus procesos.

### Automatizar procesos

Un archivo en markdown es, en plata sencilla, un archivo de texto plano con la extensión '.md' o '.markdown'. Su sintaxis vendría a ser algo así como:


     # este es un encabezado de primer nivel
     ## este es un encabezado de segundo nivel *con un etiquetado en linea*

     esto es un parrafo

     1. esta es una lista
     2. ordenada

Todo lo que necesita una novela, o un libro técnico, o casi cualquier libro que no exija un diseño complejo (como un libro ilustrado, un cómic, o un libro de cocina) puede ser formateado enteramente en markdown. Una vez que lo tenemos, tenemos nuestro archivo fuente, la madre de todos los archivos, el texto que iremos moldeando hasta convertir en un libro.

Como hemos mencionado anteriormente, nuestro objetivo es construir un proceso automatizable, que nos ahorre tiempo y recursos. De modo que ahora vamos a edificar la arquitectura sobre la cual descansará este proceso.

En este punto, lo primero es crear un repositorio. Un repositorio es el lugar donde residen los archivos que formarán parte del libro (para entendernos: una ubicación en un disco duro). Tentativamente, podemos establecer una estructura del tipo:

    --mi_libro
      |--texto
              -intro.md
              -primer capítulo.md
              -segundo capitulo.md
              -colofon.md
      |--imagenes
              -cover_image.jpg
              -imagen_1.png
              -imagen_2.png
      |--metadata
              -mi_metadata.yaml
      |--estilos
              -mi_archivo_de_estilo.css
      |--documentacion
              -licencias.md
              -to-do.md
              -paratextos.md
              -plan_de_marketing.md

Esta es una estructura básica y válida para casi cualquier proyecto de libro que esté basado principalmente en texto estruturado. Naturalmente, podemos agregar las carpetas que queramos o necesitemos: el único criterio --y esto es crítico-- es que las herramientas que utilicemos luego *entiendan* la ruta donde se encuentran nuestros archivos y puedan extraerlos desde ahí para procesarlos.

Necesitamos esta organización por que el proceso de edición es casi siempre una empresa colectiva en la que intervienen numerosos colaboradores y todos ellos (correctores, diseñadores, editores, ilustradores, el administrador del proyecto, incluso el o los autores) necesitarán en un momento dado acceder al repositorio y trabajar con esos archivos. De ahí que fuera de la organización de nuestro repositorio, se plantée una cuestión adicional: ¿Cómo establecemos un sistema que permita a todos esos colaboradores trabajar, modificar y editar esos archivos, sin que el proceso entero se transforme en un caos? Lo que necesitamos son dos cosas: hacer que esta arquitectura de archivos sea accesible desde ubicaciones distintas (acceso remoto) y, adicionalmente, un sistema de control de cambios que lleve un registro de quién ha modificado qué en cada momento y que sea capaz tanto de actualizar los archivos a la última versión "estable" como también de recuperar versiones antiguas. He aquí donde entra nuestra siguiente herramienta: GIT.

[GIT](https://es.wikipedia.org/wiki/Git) es un sistema distribuido de control de cambios. Fue ideado por Linus Torlvald (quién, dicho sea de paso, fue también el creador de Linux) y su propósito original era resolver el mismo problema que ya hemos planteado (pero en el ámbito de los desarrolladores de software): ¿cómo coordinar el trabajo de muchas personas, con talentos y formaciones distintas y trabajando muchas veces a cientos de kilómetros unas de otras, para sacar adelante un proyecto? Su idea evolucionó hasta convertirse en la herramienta que utilizan la mayor parte de los desarrolladores de software en el mundo y es el motor que hace funcionar Github, y que basicamente es una plataforma que permite que el colaborador de un proyecto pueda trabajar en un repositorio almacenado en su ordenador y luego integrar su trabajo en un repositorio situado en algun otro sitio.

¿Cómo funciona Git? De una manera muy esquemática, se reduce a lo siguiente:

1. Tenemos un repositorio que almacena los archivos de un proyecto.
2. El colaborador que se suma al proyecto comenzará por "clonar" el repositorio en su disco duro (a esto se le llama hacer un "*fork*").
3.  A partir de ese proyecto, creará una derivación (un *branch*), una copia del repositorio que agrega una característica o corrige un error.
4. Cuando la nueva característica se ha probado y está lista para ser integrada en el conjunto del proyecto, los cambios deben ser "confirmados". Cada conjunto de cambios recibe un identificador y se le añade un mensaje que explica lo que ha sido cambiado o agregado.
5. El desarrollador de esta derivación envía una solicitud para fusionar (*pull request*) los cambios a los responsables del repositorio. Si está bien diseñada, la solicitud proporcionará pruebas y documentación para la nueva característica.
6. Si los responsables del proyecto aprueban los cambios propuestos, los fusionan con la rama principal del repositorio.

Esto suena extremadamente complicado, pero en la práctica, cada uno de estos pasos supone unicamente hacer click en un botón en Github, y GitHub ofrece herramientas de anotación y visualización, un robusto sistema de comentarios que permite discutir cada paso del proceso, así como listas para identificar problemas y una wiki. Y es software completamente libre. Y es gratis.

Así, el corrector puede trabajar en un texto, mientras alguien soluciona los metadatos, mientras el diseñador genera un layout, mientras un desarrolador idea cómo añadir una funcionalidad al futuro libro. Y mientras Git lo registra y lo recuerda todo.

## Producción

Digamos que ya tenemos un repositorio con todos los archivos necesarios para completar el proyecto, y ahora, ¿cómo hacemos para transformar todo eso en un libro?

Es aquí donde interviene nuestra próxima herramienta: [Pandoc](http://pandoc.org/index.html).

Pandoc es un procesador de documentos capaz de leer y escribir una cantidad ingente de lenguajes de marcado. Desarrollado por [John Macfarlane](http://johnmacfarlane.net/), un profesor de filosofía de la Universidad de Berkeley, es una utilidad de linea de comandos (esto puede sonar algo alarmante) que fuera de convertir documentos escritos utilizando markdown, puede además servir para automatizar procesos mediante scripts (otra palabra siniestra). Pero antes de navegar en esas aguas tormentosas, podemos explicarlo de una manera sencilla.

Supongamos que tenemos nuestro repositorio almacenado en una carpeta llamada "mi_libro" y que contiene el siguiente arbol de archivos:

    mi_libro
        -texto
            -texto.md
        -estilos
            -style.css
        -imagenes
            -mi_portada.png

Vamos primero a instalar pandoc ([link de descarga para todas las plataformas](https://github.com/jgm/pandoc/releases/latest))

Una vez hecho esto, navegamos en el terminal (en Linux o Mac; en windows existe un procedimiento similar, que yo desconozco) hasta la carpeta donde vive nuestro repositorio:

    /ruta_al_repositorio/mi_libro/

y escribimos:

    pandoc texto/texto.md --epub-cover-image=imagenes/mi_portada.png --epub-stylesheet=estilos/style.css -t epub3 -o mi_libro.epub

En un par de segundos, en la carpeta raiz (desde donde estamos ejecutando pandoc) aparecerá un archivo llamado ``mi_libro.epub``.

¿Qué ha ocurrido? En ese par de segundos:

1. ejecutamos pandoc ``pandoc``
2. le dijimos que fuera a la carpera ``texto`` y leyera ``texto.md``
3. le dijimos que buscara en la carpeta ``ìmagenes`` un archivo llamado ``cover.png``y lo utilizara como portada de un libro en formato epub
4. le dimos la ruta de nuestro archivo de estilos ``estilos/style.css``
5. Especificamos la versión del archivo epub que pretendemos obtener ``-t epub3``
6. y le ordenamos que con toda la información anterior escribiera un archivo llamado "mi_libro.epub" ``-o mi_libro.epub``

Esto tal vez de una idea del poder de esta herramienda y de las posibilidades que involucra. De la misma manera que hemos especificado (se trata de un ejemplo sencillo) unicamente un único archivo de texto, una sola imagen y un único archivo de estilo, podríamos haberle dicho que escribiera un libro combinando muchos diferentes archivos en markdown, o podríamos haber especificado una ruta para el archivo conteniendo los metadatos en Dublin-Core, o podríamos haber integrado fuentes personalizadas en el libro... o todas esas cosas al mismo tiempo. Lo cierto es que si vencemos un momento el terror que inspira en espiritus poco tecnológicos (como suelen ser los editores a la antigua) el uso de la linea de comandos, con una sola herramienta es posible generar en cuestión de minutos un libro electrónico perfectamente funcional y validable en todos los formatos usuales en el mundo editorial (epub, mobi --el formato que utiliza amazon--, html --si necesitamos una versión web del libro entero o de sólo una parte de él como muestra--, incluso un PDF --vía latex--), y con un código limpio y perfectamente administrable (recordad: se trata de estar en control de neustro contenido).

### Del repositorio (local) a la imprenta

Si hemos completado el proceso correctamente, tendremos una serie de archivos (que para nuestra comodidad podemos colocar en una carpeta que llamaremos "producción"). Aquí ya tendremos libros electrónicos funcionales en formato epub3, mobi (un formato que amazon, que es tan especial, entiende), html (para la web) y hasta, si nos da la gana, un pdf (generado utilizando TeX, pero este es un misterio que explicaremos más adelante). Digamos que, hasta aquí, hemos conseguido de una manera casi automática, una serie de archivos que cualquier sistema de lectura electrónico (cualquier app) entendería y sería capaz de procesar para mostrarla en una pantalla (de movil, tablet, eReader o, sencillamente, en nuestro navegador. Pero, naturalmente, con esto no basta. Neesitamos un PDF de calidad suficiente com para enviar a una imprenta.

#### Método 1 (un *workaround*)
Herramientas para generar PDFs de resolución sufiente como para enviar a imprenta hay bastantes. Muchas de ellas tienen motores de composición tipográfica sumamente sofisticados. Tenemos, si, a TeX y a cualquiera de su variantes (ConTeX, etc...), a Groff, a Scribus, a QuarX. Pero la mayoría de los maquetadores utiliza una herramienta común, que a estas alturas ya es estandar en la industria editorial. Es InDesign. Una primer acercamiento a nuestro problema es, ¿Cómo demonios conseguimos enviar nuestros flamantes archivos a InDesign?
El workflow natural de las vasta mayoría de las editoriales, del tamaño que sea y por una serie de razones que tienen que ver con la pereza y algun otro pecado capital, pasa por que el editor envíe un archivo en word al corrector (o a los correctores) y sea este arhivo, corregido, el que se le envía al diseñador. Que, a su vez, lo importará en InDesign. Si lo hace bien, lo hará dentro de una plantilla que ya ha preparado, con la herramienta "colocar" (archivo > colocar o `ctrl + d`). De esta manera, podemos incorporar el texto desde MS Word a InDesign y que este refluya hasta generar cuantas páginas sean necesarias como para albergarlo. InDesing es lo suficientemente inteligente como para, además, importar los estilos que hallamos incorporado al archivo de MS Word y luego solo tendremos que mapearlos (hacerlos coincidir con los estilos previamente generados para nuestra plantilla) y tendremos, casi, el libro listo para exportar a PDF.
Casi. Y este casi es importante. Hay tres buenas razones para evitar a MS Word de nuestro (y de cualquier otro) workflow. En realidad hay muchas, muchísimas más, pero sólo discutiremos tres: MS Word es una herramienta propietaria (y nuestro target es utilizar sólo software libre, ¿no?), y genera un código invisible que nosotros no queremos (o,l por lo menos, no queremos afrontar los problemas que esto supone). Y, en último término, hay una razón más importante (y que requiere de una explicación más detallada).
Cuando importamos un archivo `docx` en InDesign, el proceso de importación no tiene casi problemas. Podríamos decir que con un poco de GREP hasta podríamos transformarlo en indoloro (aunque esto signifique más intervención manual de la que realmente queremos). Al final lo que importa es que ese texto estará *embebido* (y es una lástima, pero no hay una buena traducción para esto) en InDesign y no Vinculado a un archivo externo.
¿Por qué esto es esencial?
El proceso de corrección es por lo general un proceso que no se termina, sólo se abandona. Ahora mismo podría mencionar una anecdota (los créditos para *el libro y sus orillas*): había una vez un editor tan prolijo (y neurótico) que quería editar un libro totalmente libre de erratas. lo hizo corregir una y otra vez hasta que le pareció que el resultado se acomodaba a sus intenciones, entonces (y sólo entonces) lo envió a imprimir. tan orgulloso estaba que al final del libro mando a poner una leyenda que rezaba: este libro está totalmete libre de erratas. Aunque claro, el tipo que lo compuso andaba algo ido aquel día y lo que salió impreso fue la leyenda:

````este libro esta totalmente libre de *ratas*````

De lo que se trata es de comprender que un proceso editorial es un flujo y que en general no está nunca acabado. Cómo decía García Marquez acerca del oficio de escritor: los libros no se terminan, se abandonan". la misión del editor es jamás abandonar un libro. Y esta compañía, que en otros tiempos podía suponer una especie de condena eterna, en estos, que tenemos Git y la idea de un workflow como un proceso continuo, es un verbo que describe una realidad: podemos acompañlar ese texto pr que tenemos las herramientas.
Volvamos a nuestro problema. si el archivo está embebido, está *dentro* del archivo de IDesign y no depende de ningun archivo externo, si en cambio, está vínculado (linkeado) e InDesign tiene que recogerlo de cualquier otra localización de nuestro disco duro, nosotros podemos corregir *ese* archivo y cada vez que actualicemos InDesign, el texto estará actualizado. Por eso necesitamos un archivo fuente que podamos corregir indeendientemente de nuestra plantilla en Indesign y verterlo automáticamente allí, cuando haga falta (y creanme: hará falta)

#### Una corrección al workflow

Hay sin embargo un camino. desde 2014 pandoc añadió a su asombroso repertorio la capacidad de escribir archivos en formato ICML. esto va a sonar esotérico a mucha gente, incluso a algunos diseñadores qe trabajan frecuentemente con InDesign. ICML es la extención propia de los archivos que escribe InCopy, el ignorado procesador de texto que incluye InDesign y que no es otra cosa que una versión en XML de nuestro texto, lo que quere decir que ese texto tiene un etiquetado semántico y puede interpretar todo el etiquetado que hallamos aplicado a nuestro archivo en markdown y verterlo de esta manera en indesign. lo más importante: si "colocamos" un archivo ICML en InDesign (en nuestra plantilla) esté archivo estará vínculado con la plantilla, o sea: residirá fuera, en otra carpeta, y podremos generar esa extención cada vez que corrijamos el archivo (que añadamos unas itálicas olvidadas, que quitemos un espacio que no debe estar ahí, que lo *mejoremos* de cualquier manera): esto supone una mejora sustanial en nuestro workflow, por que podemos editar cuanto queramos uestro archivo original, verterlo en nuestro archivo de InDesign, y tener un PDF actualizado.

Esto significa que no necesitamos trabajar realmente en otro formato que no sea markdown. Y eso evita todos los errores que por lo general contempla el proceso de introducción manual de datos despues de una corrección en papel.

#### Un paso más

Sería, naturalmente, eliminar InDesign del todo y trabajar con TeX o cualquier otra herramienta *open source*. Las hay. Sólo que esto requiere trabajar con plantillas y aquello es un trabajo abstruso. Uno de los propósitos futuros de este workflow es generar plantillas en TeX o utilizar PrinceXML como procesador para nuestros PDFs.

## Posproducción

Al final tenemos una carpeta con archivos listos para producción. Ya hemos mencionado que uno de los propósitos de este workflow (fuera de la automatización de procesos) era generar libros completamente accesibles o, al menos, inclusivos.

Esto se consigue de muchas maneras. La más básica es asegurarse de que el libro posea una estructura semántica y una organización lógica lineal, que cualquier dispositivo que quiera interpretar el texto (para ofrecerlo en una interface accesible) va a ser capaz de entender qué significa cada parte del texto (si algo es un encabezado, o un párrafo, o si la imagen tiene un texto alternativo). Luego, se trata de mejorarlos.

Si subimos el texto a una plataforma capaz de generar audio sintético (amazon o apple, por ejemplo) para un texto dado y este texto sigue ciertas directrices, una persona será capaz de poner nuestro libro electrónico en modo audio y este sencillamente contará el texto. Si esto es posible, ¿Cómo podemos mejorar el texto para que sea completamente accesible?

Existen varias maneras. Una de ellas es agregar más *outputs* de salida para imprenta en PDF. Otra, es añadir *media overlays*.

### PDFs alternativos y POD



### Media Overlays

Los *MediaOverlays* es una parte de la especificación epub3 y consiste en la capacidad del ebook para sicronizar audio y texto. Esto no es trivial.

## Marketing
