
## Proyecto 03 - Atenas Reloaded, 20025

Segmentación de la imagen por capas. 


Basado en: 

![atenas](atenas_cover.png)

###  Objetivos 

* Comprender cómo hacer la segmentación de una imagen en capas
* Cómo cambiar propiedades de otros nodos mediante el [arbol de nodos](https://github.com/mgea/godot/wiki/Arbol-de-nodos) 
* Aplicar métodos de Parallax Scrolling (primera aproximación)

### Texto fancy
Para texto enriquecido:
```
[pulse]pulso [/pulse]
[wave]aaa[/wave]
[tornado]aaa[/tornado]
[shake]aaa[/shake]
[fade length=8]aaaaaaaaa[/fade]
[rainbow]aaAAAAAa[/rainbow]
[color=red]rojo[/color]
[color=#00ff00]verde[/color]
[bgcolor=yellow]fondo[/bgcolor]
[outline_size=2][outline_color=black]texto[/outline_color][/outline_size]
[b]negrita[/b]
[i]cursiva[/i]
[u]subrayado[/u]
[s]tachado[/s]
[img]res://icon.svg[/img]
[shake][b][color=red]CRITICAL HIT[/color][/b][/shake]
```
### Cambio de escena tras varios segundos: 
```
func _ready():
    await get_tree().create_timer(5.0).timeout
    get_tree().change_scene_to_file("res://escena2.tscn")
```
### Segmentar imagen en capas 

* Utilizar herramientas como photoshop para segmentar imagen en capas para componer en Godot
* Aplicar movimiento a los nodos
* Para establecer propiedades de otros nodos: se debe usar **get_node()** https://kidscancode.org/godot_recipes/3.x/basics/getting_nodes/
  
![tree](atenas_tree.png)


### Parallax Scrolling 

![parallax](https://opengameart.org/sites/default/files/multilayer_scroll_backgrounds_-_preview_desert_mountains.gif)

Basado en: https://docs.godotengine.org/es/4.x/tutorials/2d/2d_parallax.html
Más información en:  https://www.youtube.com/watch?v=f8z4x6R7OSM

Recursos (OpenGameArt): https://opengameart.org/content/multilayer-scroll-backgrounds


### Publicación en itch.io

https://cmiugr.itch.io/atenas
