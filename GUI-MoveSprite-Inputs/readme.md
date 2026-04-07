## GUI-MoveSprite-Inputs


En este ejercicio vamos a  controlar un objeto (``sprite2D``)  por la pantalla mediante los ``Inputs``: pulsaciones en dispositivos de entrada (teclado, mouse,...) 
Mover objeto básico por teclado / ratón

Revisar qué son y cómo funciona los [Inputs](https://github.com/mgea/godot/wiki/Inputs)


NOTA: EL script se ha puesto en el Nodo raiz. Para modificar propiedades de los "nodos hijos" tenemos que usar un mecanimo para referirnos al nodo hijo correspondiente. Ene este caso, sería usando el símbolo ``$`` con el nombre del nodo: para mover a la derecha el ``Sprite2D`` sería con: ``$Character.position.x = $Character.position.x +1`` 

> Revisar https://github.com/mgea/godot/wiki/Arbol-de-nodos

```
# Called every frame. 'delta' is the elapsed time since the previous frame.
@export var speed := 100.0

func _process(delta: float) -> void:
	var direction := Vector2.ZERO
	
	if Input.is_action_pressed("up"):
		direction.y -= 1
	if Input.is_action_pressed("down"):
		direction.y += 1
	if Input.is_action_pressed("left"):
		direction.x -= 1
	if Input.is_action_pressed("right"):
		direction.x += 1
	
	direction = direction.normalized()
	
	$character.position += direction * speed * delta

```

```
@export var speed := 100.0

func _process(delta: float) -> void:
	if Input.is_action_pressed("up"):
		$character.position.y -= speed * delta
	
	if Input.is_action_pressed("down"):
		$character.position.y += speed * delta
	
	if Input.is_action_pressed("left"):
		$character.position.x -= speed * delta
	
	if Input.is_action_pressed("right"):
		$character.position.x += speed * delta
```

Finalmente le añadiremos un objeto ``Camera2D`` para que se mueva con el personaje 

![MoverSprite](moverSprite02.png)

<br>

POdemos añadir más opciones para evitar que el personaje no se salga de las dimensiones del mapa. 

