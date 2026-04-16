## Dialogic 2 example 

Ejemplo de sistema de diálogo en Godot con plugin Dialogic 2 https://github.com/dialogic-godot/dialogic

Documentación en: https://docs.dialogic.pro/introduction.html

Ejemplo base para diseñar diálogos: [walking_player_con_dialogic.zip](walking_player_Dialogic.zip)

Usamos como personajes para el dialogo los siguientes character: 
* Sutemo https://sutemo.itch.io/female-character 
* Jon: https://sraye.itch.io/mature-male-character-sprites




### Variables globales 

EN el editor se pueden crear variables globales que se pueden usar durante la conversación, pueden tener difernetes tipos (string, int, bool)

![](dialogic_var.png)

En cualquier momento se pueden usar poniendolas entre llaves: 

``{VAR.nombre}`` o bien ``{nombre}``



### Dialogos (timeline)

Un timeline describe un diálogo. se almacena con extensión **.dtl**


Acciones que se pueden hacer en un diálogo:

![](https://docs.dialogic.pro/media/introduction/events.png)



### Inicar diálogo desde Godot 


``Dialogic.start("nombre_dialogo")``


### Characters 

Al crear los characters, todas las imágenes del personaje deben estar en una carpeta (expresiones) 

![characters_folder](dialogic_characters.png)

Cada personaje se crea con una extensión **.dch**


### Estilos de diálogo (bubble) 

Se pueden usar varias que viene prediseñadas o hacer una propia 

![characters_folder](estilos_dialogic.png)

### Interaccion

StaticBody2D
-  sprite 2D
-  Collisionshape2D
-  Area2D
  - collisionShape2D

Llamamos a la señal adecuada:
```
@export var timeline_path := "res://timeline1.dtl"
func _on_area_2d_body_entered(body: Node2D) -> void:
	var encuentros = Dialogic.VAR.get_variable("encuentros_con_jon")
	print(encuentros)
		
	if body.name == "frisk":
		Dialogic.start(timeline_path)
```

Para activarlo con una tecla

```
@export var timeline_path := "res://timeline1.dtl"
var jugador_dentro := false
var hablando := false
func _on_area_2d_body_shape_entered(body_rid: RID, body: Node2D, body_shape_index: int, local_shape_index: int) -> void:
	if body.name == "frisk":
		jugador_dentro = true


func _on_area_2d_body_shape_exited(body_rid: RID, body: Node2D, body_shape_index: int, local_shape_index: int) -> void:
	if body.name == "frisk":
		jugador_dentro = false
		
func _process(_delta: float) -> void:
	if jugador_dentro and not hablando and Input.is_action_just_pressed("interact"):
		hablando = true
		var encuentros = Dialogic.VAR.get_variable("num_encuentros_con_jon")
		Dialogic.start(timeline_path)
		await Dialogic.timeline_ended
		Dialogic.VAR.set_variable("num_encuentros_con_jon",1)
		hablando = false
```

### Activar el historial de conversación
Se activa en el script de la escena, añadiendo lo siguiente al ready: 

```
func _ready() -> void:
	Dialogic.History.simple_history_enabled = true
	Dialogic.History.simple_history_save = true
```

### Abrir glosario

Para tener definiciones de todos los términos, se activa pasando el ratón por encima de los términos guardados.

### Texto en burbuja
El personaje al que queremos adjuntárselo necesita una modificación en el texto: 
```

@export var timeline_path := "res://timeline1.dtl"
func _on_area_2d_body_shape_entered(body_rid: RID, body: Node2D, body_shape_index: int, local_shape_index: int) -> void:
	var encuentros = Dialogic.VAR.get_variable("num_encuentros_con_jon")
	print(encuentros)
	print(body.name)
	if body.name == "frisk":
		var layout = Dialogic.start(timeline_path)
		layout.register_character("res://jon.dch",$".")
```

Ademas debemos modificar el estilo, haciendo que la default layout base sea en realidad una bubble base (por lo menos en Dialogic 16)

