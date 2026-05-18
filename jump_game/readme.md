# Movimiento de jugador en cuadrícula en Godot

Este script permite mover un personaje en una cuadrícula, es decir, el jugador no se mueve libremente píxel a píxel, sino que avanza de una casilla a otra.

Cada vez que se pulsa una dirección, el personaje se desplaza exactamente una distancia fija, definida por `tile_size`.

---

## Script completo

```gdscript
extends CharacterBody2D

@export var tile_size: int = 64
@export var move_speed: float = 2.0

var is_moving := false
var target_position: Vector2

func _ready():
	target_position = global_position

func _physics_process(delta):
	if is_moving:
		global_position = global_position.move_toward(
			target_position,
			tile_size * move_speed * delta
		)

		if global_position == target_position:
			is_moving = false
	else:
		var direction := Vector2.ZERO

		if Input.is_action_just_pressed("ui_right"):
			$AnimationPlayer.play("salto")
			direction = Vector2.RIGHT
		elif Input.is_action_just_pressed("ui_left"):
			$AnimationPlayer.play("salto")
			direction = Vector2.LEFT
		elif Input.is_action_just_pressed("ui_down"):
			direction = Vector2.DOWN
		elif Input.is_action_just_pressed("ui_up"):
			direction = Vector2.UP

		if direction != Vector2.ZERO:
			target_position = global_position + direction * tile_size
			is_moving = true
```

## Estructura esperada del jugador

Este script está pensado para colocarse en un nodo de tipo:

CharacterBody2D

Una estructura básica podría ser:
```
Player
├── Sprite2D
├── CollisionShape2D
└── AnimationPlayer
```
El nodo AnimationPlayer debe existir como hijo directo del jugador, porque el script usa:
```
$AnimationPlayer.play("salto")
```
Además, dentro del AnimationPlayer debe existir una animación llamada:
```
salto
```

## Variables principales
```
tile_size
@export var tile_size: int = 64
```
Indica el tamaño de cada casilla de la cuadrícula.

En este caso vale 64, así que cada vez que el jugador se mueve, avanza exactamente 64 píxeles.

Por ejemplo:
```
Posición inicial: (0, 0)
Mover derecha:    (64, 0)
Mover derecha:    (128, 0)
Mover abajo:      (128, 64)
```
Al usar @export, puedes modificar este valor directamente desde el editor de Godot sin cambiar el código.
```
move_speed
@export var move_speed: float = 2.0
```
Controla la velocidad con la que el jugador se desplaza entre una casilla y otra.

No cambia la distancia recorrida, solo lo rápido que llega a la siguiente casilla.

Un valor más bajo hace que el movimiento sea más lento:
```
move_speed = 1.0
```
Un valor más alto hace que el movimiento sea más rápido:
```
move_speed = 5.0
is_moving
var is_moving := false
```
Sirve para saber si el personaje está actualmente moviéndose hacia otra casilla.

Cuando vale true, el jugador está desplazándose hacia target_position.

Cuando vale false, el jugador puede recibir una nueva orden de movimiento.

Esto evita que el jugador pueda encadenar movimientos antes de terminar el desplazamiento actual.
```
target_position
var target_position: Vector2
```
Guarda la posición exacta a la que debe llegar el jugador.

Por ejemplo, si el jugador está en:
```
(128, 64)
```
y pulsa derecha, la nueva posición objetivo será:
```
(192, 64)
```
porque el tamaño de casilla es 64.
```
Función _ready()
func _ready():
	target_position = global_position
```
Esta función se ejecuta una vez cuando el nodo entra en la escena.

Aquí se inicializa target_position con la posición actual del jugador.

Esto es importante porque, al comenzar el juego, el personaje todavía no tiene un objetivo de movimiento. Por eso se le dice que su objetivo inicial es quedarse donde ya está.
```
Función _physics_process(delta)
func _physics_process(delta):
```
Esta función se ejecuta continuamente durante el juego.

Se usa _physics_process porque el movimiento del jugador está relacionado con la lógica física y temporal del juego.

El parámetro delta representa el tiempo transcurrido desde el último frame. Usarlo permite que el movimiento sea estable aunque el juego vaya a más o menos FPS.

Cuando el jugador se está moviendo
```
if is_moving:
	global_position = global_position.move_toward(
		target_position,
		tile_size * move_speed * delta
	)
```
Si is_moving es true, el jugador se mueve poco a poco hacia target_position.

La función move_toward() mueve la posición actual hacia una posición objetivo.

En este caso:
```
global_position.move_toward(target_position, tile_size * move_speed * delta)
```
significa:

Mueve al jugador desde su posición actual hacia la posición objetivo, avanzando una cantidad proporcional a la velocidad y al tiempo.

La velocidad real del movimiento se calcula con:
```
tile_size * move_speed * delta
```
Con los valores actuales:
```
tile_size = 64
move_speed = 2.0
```
El jugador se mueve a una velocidad de:

128 píxeles por segundo

porque:
```
64 * 2.0 = 128
Cuando llega a la casilla objetivo
if global_position == target_position:
	is_moving = false
```
Cuando la posición actual del jugador coincide con target_position, significa que ya ha llegado a la siguiente casilla.

Entonces is_moving vuelve a false, permitiendo que el jugador pueda recibir otra pulsación de movimiento.

Cuando el jugador no se está moviendo
```
else:
	var direction := Vector2.ZERO
```
Si el jugador no se está moviendo, el script comprueba si se ha pulsado alguna tecla de dirección.

Primero se crea una variable llamada direction.
```
var direction := Vector2.ZERO

Vector2.ZERO equivale a:

(0, 0)
```
Es decir, inicialmente no hay movimiento.

## Entrada hacia la derecha
```
if Input.is_action_just_pressed("ui_right"):
	$AnimationPlayer.play("salto")
	direction = Vector2.RIGHT
```
Si el jugador pulsa la acción ui_right, se hace lo siguiente:

Se reproduce la animación "salto".
La dirección se establece hacia la derecha.
Vector2.RIGHT

equivale a:
```
(1, 0)
```
Después, esta dirección se multiplicará por tile_size, así que el jugador avanzará 64 píxeles hacia la derecha.

## Entrada hacia la izquierda
```
elif Input.is_action_just_pressed("ui_left"):
	$AnimationPlayer.play("salto")
	direction = Vector2.LEFT
```
Si el jugador pulsa ui_left, se reproduce también la animación "salto" y la dirección será izquierda.
```
Vector2.LEFT
```
equivale a:
```
(-1, 0)
```
Por tanto, el jugador se moverá 64 píxeles hacia la izquierda.

## Entrada hacia abajo
```
elif Input.is_action_just_pressed("ui_down"):
	direction = Vector2.DOWN
```
Si el jugador pulsa ui_down, la dirección será hacia abajo.
```
Vector2.DOWN
```
equivale a:
```
(0, 1)
```
El jugador se moverá una casilla hacia abajo.

En este caso no se reproduce la animación "salto", porque solo se ha añadido la animación para izquierda y derecha.

## Entrada hacia arriba
```
elif Input.is_action_just_pressed("ui_up"):
	direction = Vector2.UP
```
Si el jugador pulsa ui_up, la dirección será hacia arriba.
```
Vector2.UP
```
equivale a:
```
(0, -1)
```
El jugador se moverá una casilla hacia arriba.

Calcular la nueva posición objetivo
```
if direction != Vector2.ZERO:
	target_position = global_position + direction * tile_size
	is_moving = true
```
Después de comprobar las teclas, el script mira si realmente hay una dirección válida.

Si direction no es Vector2.ZERO, significa que el jugador ha pulsado una dirección.

Entonces se calcula la nueva posición objetivo:
```
target_position = global_position + direction * tile_size
```
Por ejemplo, si el jugador está en:
```
(128, 128)
```
y pulsa derecha:
```
direction = (1, 0)
tile_size = 64
```
entonces:
```
target_position = (128, 128) + (1, 0) * 64
target_position = (192, 128)
```
Después se activa el movimiento:
```
is_moving = true
```
A partir de ese momento, en los siguientes frames, el jugador se irá desplazando hacia esa posición.

### Por qué se usa is_action_just_pressed

El script usa:
```
Input.is_action_just_pressed()
```
en lugar de:
```
Input.is_action_pressed()
```
La diferencia es importante.

is_action_just_pressed() detecta solo el momento exacto en el que se pulsa la tecla.

Eso significa que cada pulsación genera un único movimiento de una casilla.

Si se usara is_action_pressed(), el jugador podría seguir moviéndose automáticamente mientras se mantiene pulsada la tecla.

## Acciones de entrada necesarias

El script usa estas acciones:
```
ui_right
ui_left
ui_down
ui_up
```
Godot suele traerlas configuradas por defecto.

Aun así, puedes revisarlas en:
```
Project > Project Settings > Input Map
```
Desde ahí puedes asignar teclas como:
```
ui_right -> Flecha derecha / D
ui_left  -> Flecha izquierda / A
ui_down  -> Flecha abajo / S
ui_up    -> Flecha arriba / W
```

## Resumen del funcionamiento

El funcionamiento general es este:

El jugador empieza quieto.

El script espera una pulsación de dirección.

Cuando se pulsa una dirección, calcula la siguiente casilla.

El jugador se mueve suavemente hacia esa casilla.

Mientras se mueve, no acepta nuevas direcciones.

Cuando llega a la casilla, vuelve a quedar disponible para moverse otra vez.
