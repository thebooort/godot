# Resumen de lo visto hasta la fecha, con apuntes de lo que he visto en clase. 

1. Godot es un motor de videojuego. Todo en Godot es una escena:
  - Un sprite moviendose? Una escena.
  - Un fondo? Una escena
  - Un fondo con un sprite moviendose? Una escena.

2. Los bloques que usamos para construir escenas se llaman nodos. Los nodos son de diversos tipos dependiendo si son botones, textos etc.
3. Podemos crear una escena nueva de diferentes formas:
   - Dandole al + en la barra justo debajo del dock (el Dock es donde está 2D, 3D, Script etc)
   - Dandole click derecho -> nuevo -> nueva escena
  Cuando lo hagamos tenemos que guardarla (Ctrl+S) con un NOMBRE IDENTIFICATIVO (cada vez que repito esto, muere un gatito, sobre vuestra conciencia caerá)
El resultado será un fichero del tipo: {Nombre_de_la_escena}.tscn que aparecerá en el sistema de archivos (panel de abajo a la izquierda)
4. Cuando queramos ir a esta escena, ya sea despues de x segundos o con botones, deberemos usar la direccion "res://{Nombre_de_la_escena}.tscn"
   Cosas que no funcionan: "{Nombre_de_la_escena}.tscn" a secas, "{Nombre_de_la_escena}.tscn.tscn" y bueno, literalmente cualquier otra combinacion que no sea esa.
5. Una vez creada una escena, podemos añadirle nodos (boton de + en el menu de escena) o otras escenas (boton derecho en el menu de escena -> instanciar escena hija)
6. Nodos que hemos visto hasta ahora:
- Sprite2D: permite mostrar imágenes o texturas en 2D dentro de la escena. Se usa para personajes, fondos, iconos o cualquier elemento visual estático o animado.
- Label: sirve para mostrar texto simple en pantalla, como títulos, nombres, puntuaciones o mensajes breves.
- RichTextLabel: permite mostrar texto con formato enriquecido. Admite estilos, colores, enlaces, animaciones e incluso imágenes incrustadas dentro del propio texto.
- Button: crea un botón interactivo para que el usuario pulse y ejecute acciones. Si queremos personalizarlo visualmente, podemos usar Theme Overrides y modificar el apartado Style.
- TextureButton: es una forma rápida de crear botones usando imágenes para sus distintos estados. Es más limitado que un Button normal, pero muy útil para interfaces visuales sencillas.
- Parallax2D: se utiliza para crear efecto de profundidad en escenarios 2D mediante desplazamiento a distintas velocidades. Normalmente se coloca uno por cada capa que queramos animar.
- AnimationPlayer: permite crear y reproducir animaciones sobre nodos y propiedades. Hay que recordar activar la reproducción automática si hace falta y escoger el tipo de bucle: sin bucle, hacia delante y atrás, o en bucle continuo.
- CanvasLayer: sirve para separar elementos de la interfaz o del dibujo 2D en capas independientes, evitando que se muevan con la cámara si no queremos.
- Panel: crea un panel visual simple, normalmente usado como fondo o marco para agrupar elementos de interfaz.
- PanelContainer: es un contenedor que añade automáticamente un panel alrededor de su contenido, útil para organizar elementos de UI con un fondo visual.
- MarginContainer: añade márgenes internos alrededor de los nodos hijos para separar el contenido de los bordes.
- GridContainer: organiza automáticamente sus elementos hijos en una cuadrícula de filas y columnas.
- HBoxContainer: ordena los elementos hijos horizontalmente, uno al lado de otro.
- VBoxContainer: ordena los elementos hijos verticalmente, uno debajo de otro.
- AudioStreamPlayer: reproduce sonidos o música dentro de la escena, como efectos de sonido, voces o música de fondo.
  
7. Crear temas: Botón derecho en recursos, crear nuevo recurso, nuevo tema.
   - Administrar para añadirle aquellos nodos que queráis personalizar
   - personalizarlos!
   - Para usarla simplemente iros a theme cuando añadais alguno de esos nodos y cargad el tema guardado.
 
  
8.Animaciones con animation player.
  Recordad la opción de cómo reproducir el bucle y que se inicie al cargar escena
  Crear el nodo animation player
  Nueva animacion y elegir qué vamos a animar y que propiedades de esa cosa
  defini estado inicial -> crear fotograma -> desplazar linea del tiempo -> definir estado final -> crear fotograma
9. Botones:
Crear boton -> añadirle script -> seleccionar nodo, señales ir a la de is_pressed(), conectarla con el script -> actualizar el script. 
CUIDADO con los nombres de las funciones y ponerlo donde toca!
