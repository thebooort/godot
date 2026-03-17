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
 - Sprite2D (Meter imagenes)
 - Label (Meter texto)
 - RichTextLabel (Meter texto, con posibilidad de ser animado)
 - Button (Insertar botones)
 - Parallax2D (Insertar efecto parallax, uno por cada capa que queramos animar)
 - AnimationPlayer (insertar animaciones)
7.
