# Endless Runner sencillo en Godot 4

Este proyecto es un juego tipo dinosaurio de Chrome:

- El jugador no avanza hacia la derecha.
- El jugador solo salta y se agacha.
- Los obstáculos aparecen desde un punto de spawn.
- Los obstáculos se mueven hacia la izquierda.
- El score aumenta con el tiempo.
- Al chocar con un obstáculo, aparece el panel de Game Over y el juego se pausa.

---

# 1. Estructura de la escena

La escena principal está organizada así:

```text
main / Node2D
├── background
├── player / CharacterBody2D
├── Camera2D
├── StaticBody2D
│   └── CollisionShape2D
├── Area2D
├── Marker2D
├── Timer
├── Panel
└── ScoreLabel
```
El nodo main contiene directamente:

Marker2D: punto donde aparecen los obstáculos.
Timer: controla cada cuánto se crea un obstáculo.
Panel: pantalla de Game Over.
ScoreLabel: texto del score.

## 2. Script del main

Este script va en el nodo principal main.

Se encarga de:

crear obstáculos cada cierto tiempo,
colocarlos en la posición del Marker2D,
actualizar el score,
mostrar el panel de Game Over,
pausar el juego.

```
extends Node2D

@export var obstacle_scene: PackedScene
@export var min_spawn_time := 1.0
@export var max_spawn_time := 2.5
var score = 0

func _ready():
	randomize()
	$Timer.timeout.connect(_on_spawn_timer_timeout)
	_start_timer()


func _start_timer():
	$Timer.wait_time = randf_range(min_spawn_time, max_spawn_time)
	$Timer.start()


func _on_spawn_timer_timeout():
	var obstacle = obstacle_scene.instantiate()
	add_child(obstacle)
	obstacle.global_position = $Marker2D.global_position
	_start_timer()
	
func _process(delta):
	score += delta * 10
	$ScoreLabel.text = "Score: " + str(int(score))

```

Explicación del script de main

Esta variable guarda la escena del obstáculo:
```
@export var obstacle_scene: PackedScene
```
En el Inspector de main, tienes que arrastrar la escena del obstáculo a la propiedad:
```
Obstacle Scene
```
Estas variables controlan el tiempo aleatorio entre obstáculos:
```
@export var min_spawn_time := 1.0
@export var max_spawn_time := 2.5
```
Con estos valores, aparece un obstáculo cada tiempo aleatorio entre 1 y 2.5 segundos.

Esta función arranca el temporizador:
```
func _start_timer():
	if game_over:
		return

	$Timer.wait_time = randf_range(min_spawn_time, max_spawn_time)
	$Timer.start()
```
Esta función se ejecuta cuando termina el Timer:
```
func _on_spawn_timer_timeout():
	if game_over:
		return

	var obstacle = obstacle_scene.instantiate()
	add_child(obstacle)
	obstacle.global_position = $Marker2D.global_position

	_start_timer()
```
Aquí se crea una copia del obstáculo y se coloca en la posición del Marker2D.

Esta parte actualiza el score:
```
func _process(delta):
	score += delta * 10
	$ScoreLabel.text = "Score: " + str(int(score))
```

## 3. Script del player

Este script va en el nodo player, que es un CharacterBody2D.

El jugador:

no se mueve horizontalmente,
salta con jump,
se agacha con duck,
cambia entre las animaciones run, jump y duck,
cambia entre dos colisiones: depie y agachao.

Estructura esperada del jugador:
```
player / CharacterBody2D
├── AnimatedSprite2D
├── depie / CollisionShape2D
└── agachao / CollisionShape2D
```
Script:
```
extends CharacterBody2D

const JUMP_VELOCITY := -900.0
const GRAVITY := 1200.0


func _ready():
	$depie.disabled = false
	$agachao.disabled = true
	$AnimatedSprite2D.play("run")


func _physics_process(delta):
	velocity.x = 0

	if not is_on_floor():
		velocity.y += GRAVITY * delta

	if Input.is_action_just_pressed("jump") and is_on_floor():
		velocity.y = JUMP_VELOCITY

	var is_ducking := Input.is_action_pressed("duck") and is_on_floor()

	if is_ducking:
		$depie.disabled = true
		$agachao.disabled = false
		$AnimatedSprite2D.play("duck")

	elif not is_on_floor():
		$depie.disabled = false
		$agachao.disabled = true
		$AnimatedSprite2D.play("jump")

	else:
		$depie.disabled = false
		$agachao.disabled = true
		$AnimatedSprite2D.play("run")

	move_and_slide()
```

Input Map

En:

Project > Project Settings > Input Map

crea estas acciones:

jump
duck

Por ejemplo:

jump → Space / Flecha arriba / W

duck → Flecha abajo / S
Animaciones necesarias

En el AnimatedSprite2D del jugador deben existir estas animaciones:

run
jump
duck

Los nombres deben coincidir exactamente con los del script.

## 4. Script del obstáculo

Este script va en el Area2D del obstáculo.

El obstáculo:

se mueve hacia la izquierda,
se borra cuando sale de la pantalla,
detecta si toca al jugador,
llama al Game Over del main.

```
extends Area2D

const SPEED := 350.0


func _process(delta):
	position.x -= SPEED * delta

	if position.x < -100:
		queue_free()


func _on_body_entered(body):
	if body.name == "player":
		print("Game Over")
		get_parent().show_game_over()

```

Explicación del script del obstáculo

Esta línea mueve el obstáculo hacia la izquierda:
```
position.x -= SPEED * delta
```
Esta parte borra el obstáculo si sale de la pantalla:
```
if position.x < -100:
	queue_free()
```
Esta función se ejecuta cuando algo entra en el Area2D:
```
func _on_body_entered(body):
```
Y esta comprobación detecta si lo que ha tocado es el jugador:
```
if body.name == "player":
```
Entonces llama al Game Over del main:
```
get_parent().show_game_over()
```
Esto funciona porque los obstáculos se crean directamente como hijos de main con:
```
add_child(obstacle)
```
Por tanto, el padre del obstáculo es main.

## 5. Señal del obstáculo

Para que funcione la colisión, conecta la señal body_entered del obstáculo.

Selecciona el Area2D del obstáculo y ve a:

Node > Signals > body_entered

Conéctala al propio obstáculo.

La función debe quedar así:
```
func _on_body_entered(body):
	if body.name == "player":
		print("Game Over")
		get_parent().show_game_over()
```
## 6. Asignar la escena del obstáculo

Como en main tienes:
```
@export var obstacle_scene: PackedScene
```
Godot mostrará en el Inspector una propiedad llamada:

Obstacle Scene

Haz esto:

Selecciona el nodo main.
Ve al Inspector.
Busca Obstacle Scene.
Arrastra ahí la escena del obstáculo, por ejemplo:
obstaculo.tscn

Si no asignas la escena, esta línea fallará:
```
var obstacle = obstacle_scene.instantiate()
```

## 7. Panel de Game Over

El nodo Panel debe estar oculto al empezar.

Puedes ocultarlo desde el editor, pero el script también lo hace:
```
$Panel.visible = false
```
Cuando el jugador choca con un obstáculo, se ejecuta:
```
$Panel.visible = true
get_tree().paused = true
```
Esto muestra el panel y pausa el juego.

Como el juego se pausa, conviene activar el modo de proceso del panel:
```
$Panel.process_mode = Node.PROCESS_MODE_ALWAYS
```
Así el panel puede seguir funcionando aunque el árbol esté pausado.

## 8. Reiniciar la partida

Si quieres un botón de reinicio dentro del Panel, por ejemplo:

Panel
└── Button

Conecta la señal pressed del botón al script de main y añade:
```
func _on_button_pressed():
	get_tree().paused = false
	get_tree().reload_current_scene()
```
Entonces el script completo de main quedaría así:
```
extends Node2D

@export var obstacle_scene: PackedScene
@export var min_spawn_time := 1.0
@export var max_spawn_time := 2.5

var score := 0.0
var game_over := false


func _ready():
	randomize()

	$Panel.visible = false
	$Panel.process_mode = Node.PROCESS_MODE_ALWAYS
	$ScoreLabel.text = "Score: 0"

	$Timer.timeout.connect(_on_spawn_timer_timeout)
	_start_timer()


func _start_timer():
	if game_over:
		return

	$Timer.wait_time = randf_range(min_spawn_time, max_spawn_time)
	$Timer.start()


func _on_spawn_timer_timeout():
	if game_over:
		return

	var obstacle = obstacle_scene.instantiate()
	add_child(obstacle)
	obstacle.global_position = $Marker2D.global_position

	_start_timer()


func _process(delta):
	if game_over:
		return

	score += delta * 10
	$ScoreLabel.text = "Score: " + str(int(score))


func show_game_over():
	game_over = true
	$Panel.visible = true
	get_tree().paused = true


func _on_button_pressed():
	get_tree().paused = false
	get_tree().reload_current_scene()
```


## 9.  Los tres scripts finales

main.gd
```
extends Node2D

@export var obstacle_scene: PackedScene
@export var min_spawn_time := 1.0
@export var max_spawn_time := 2.5

var score := 0.0
var game_over := false


func _ready():
	randomize()

	$Panel.visible = false
	$Panel.process_mode = Node.PROCESS_MODE_ALWAYS
	$ScoreLabel.text = "Score: 0"

	$Timer.timeout.connect(_on_spawn_timer_timeout)
	_start_timer()


func _start_timer():
	if game_over:
		return

	$Timer.wait_time = randf_range(min_spawn_time, max_spawn_time)
	$Timer.start()


func _on_spawn_timer_timeout():
	if game_over:
		return

	var obstacle = obstacle_scene.instantiate()
	add_child(obstacle)
	obstacle.global_position = $Marker2D.global_position

	_start_timer()


func _process(delta):
	if game_over:
		return

	score += delta * 10
	$ScoreLabel.text = "Score: " + str(int(score))


func show_game_over():
	game_over = true
	$Panel.visible = true
	get_tree().paused = true

```

player.gd
```
extends CharacterBody2D

const JUMP_VELOCITY := -900.0
const GRAVITY := 1200.0


func _ready():
	$depie.disabled = false
	$agachao.disabled = true
	$AnimatedSprite2D.play("run")


func _physics_process(delta):
	velocity.x = 0

	if not is_on_floor():
		velocity.y += GRAVITY * delta

	if Input.is_action_just_pressed("jump") and is_on_floor():
		velocity.y = JUMP_VELOCITY

	var is_ducking := Input.is_action_pressed("duck") and is_on_floor()

	if is_ducking:
		$depie.disabled = true
		$agachao.disabled = false
		$AnimatedSprite2D.play("duck")

	elif not is_on_floor():
		$depie.disabled = false
		$agachao.disabled = true
		$AnimatedSprite2D.play("jump")

	else:
		$depie.disabled = false
		$agachao.disabled = true
		$AnimatedSprite2D.play("run")

	move_and_slide()
```


obstaculo.gd
```
extends Area2D

const SPEED := 350.0


func _process(delta):
	position.x -= SPEED * delta

	if position.x < -100:
		queue_free()


func _on_body_entered(body):
	if body.name == "player":
		print("Game Over")
		get_parent().show_game_over()
```
