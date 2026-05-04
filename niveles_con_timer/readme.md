
# JUEGO DE PLATAFORMAS

### Juego de plataformas con power-up, llave, puerta, cronómetro y objetos empujables

![img](platform_world_scene.png)

<br>
<br

* Juego de plataformas donde el jugador puede moverse, saltar, recoger objetos y completar el nivel.
* El juego incluye:
  * un **player** de tipo `CharacterBody2D`
  * un **power-up** que modifica la fuerza de salto del jugador
  * una **llave** que permite activar la puerta
  * una **puerta** que permite completar el nivel
  * un **cronómetro** que muestra el tiempo restante
  * una pantalla de fallo si se acaba el tiempo
  * objetos de tipo `RigidBody2D` que pueden ser empujados por el jugador

Para ello se ha creado:

* `player` (`CharacterBody2D`)
* `Area2D` para el power-up de salto
* `Area2D2` para recoger la llave
* `puerta` para terminar el nivel
* `Timer` para controlar el tiempo máximo
* `Panel` para mostrar que el nivel se ha superado
* `fail` para mostrar que se ha acabado el tiempo
* `cronometro` como etiqueta de texto dentro del jugador
* un script global/autoload llamado `velocidad.gd`

---

## Actividades

* Crear un **jugador de tipo `CharacterBody2D`** que pueda moverse horizontalmente y saltar.
* Crear un **script global/autoload** llamado `velocidad.gd`, donde se guarda la velocidad de salto del jugador.
* Crear un **power-up** que cambia el valor de salto del jugador.
* Crear una **llave** que, al recogerse, permite abrir o activar la puerta.
* Crear una **puerta** que solo funciona si el jugador ha recogido la llave.
* Crear un **Timer** que controle el tiempo máximo del nivel.
* Mostrar el tiempo restante por pantalla usando un `Label`.
* Mostrar un panel de victoria cuando el jugador supera el nivel.
* Mostrar una pantalla de fallo cuando se acaba el tiempo.
* Añadir objetos de tipo `RigidBody2D` que el jugador pueda empujar.

---

## Código del jugador

El jugador es un nodo de tipo `CharacterBody2D`.

Este código permite:

* aplicar gravedad
* saltar usando la variable global `velocidad.JUMP_VELOCITY`
* moverse a izquierda y derecha
* empujar objetos de tipo `RigidBody2D`

```python
extends CharacterBody2D


const SPEED = 300.0


func _physics_process(delta: float) -> void:
	# Add the gravity.
	if not is_on_floor():
		velocity += get_gravity() * delta

	# Handle jump.
	if Input.is_action_just_pressed("ui_accept") and is_on_floor():
		velocity.y = velocidad.JUMP_VELOCITY

	# Get the input direction and handle the movement/deceleration.
	# As good practice, you should replace UI actions with custom gameplay actions.
	var direction := Input.get_axis("ui_left", "ui_right")
	if direction:
		velocity.x = direction * SPEED
	else:
		velocity.x = move_toward(velocity.x, 0, SPEED)

	move_and_slide()
		# Empujar objetos RigidBody2D
	for i in get_slide_collision_count():
		var collision := get_slide_collision(i)
		var objeto := collision.get_collider()

		if objeto is RigidBody2D:
			objeto.apply_impulse(Vector2(direction * 10, 0))
````

---

## Código del mundo

El mundo o nivel es un nodo de tipo `Node2D`.

Este código controla:

* el cronómetro del nivel
* el power-up de salto
* la recogida de la llave
* la activación de la puerta
* el panel de nivel superado
* la pantalla de tiempo agotado

```python
extends Node2D


# Called when the node enters the scene tree for the first time.
func _ready() -> void:
	pass # Replace with function body.


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta: float) -> void:
	var tiempo_restante : float = $Timer.time_left
	$player/cronometro.text = "Tiempo: %.2f" % tiempo_restante

var llaves_conseguidas = 0

func _on_area_2d_body_entered(body: Node2D) -> void:
	if body.name=='player':
		$Area2D.visible=false
		velocidad.JUMP_VELOCITY = -600 # Replace with function body.


func _on_area_2d_2_body_entered(body: Node2D) -> void:
	if body.name == 'player':
		$Area2D2.visible = false
		$puerta.visible = true
		llaves_conseguidas = 1
		 # Replace with function body.


func _on_puerta_body_entered(body: Node2D) -> void:
	if body.name == 'player' and llaves_conseguidas == 1:
		var tiempo_restante : float = $Timer.time_left
		$Panel.visible = true
		$Panel/Label.text = "superado en %.2f segundos" % (60.0 - tiempo_restante)
		$Timer.stop()


func _on_timer_timeout() -> void:
	$fail.visible = true # Replace with function body.
	$fail/tiempo.text = 'Se acabo el tiempo'
```

---

## Script global / autoload

Se ha creado un script global llamado `velocidad.gd`.

Este script almacena la variable global de salto del jugador.

```python
extends Node


var JUMP_VELOCITY := -400.0
```

Para usarlo como autoload:

```text
Project > Project Settings > Autoload
```

Y añadir el script con el nombre:

```text
velocidad
```

De esta forma, desde cualquier script se puede acceder a:

```python
velocidad.JUMP_VELOCITY
```

---

## Power-up de salto

El power-up modifica la variable global de salto.

Cuando el jugador entra en el `Area2D`, el objeto se oculta y se cambia la fuerza de salto:

```python
func _on_area_2d_body_entered(body: Node2D) -> void:
	if body.name=='player':
		$Area2D.visible=false
		velocidad.JUMP_VELOCITY = -600 # Replace with function body.
```

Antes del power-up, el salto normal es:

```python
var JUMP_VELOCITY := -400.0
```

Después de recoger el power-up, pasa a ser:

```python
velocidad.JUMP_VELOCITY = -600
```

---

## Llave y puerta

El jugador necesita recoger una llave para poder superar el nivel.

La variable que controla si la llave se ha recogido es:

```python
var llaves_conseguidas = 0
```

Cuando el jugador recoge la llave:

```python
func _on_area_2d_2_body_entered(body: Node2D) -> void:
	if body.name == 'player':
		$Area2D2.visible = false
		$puerta.visible = true
		llaves_conseguidas = 1
		 # Replace with function body.
```

La llave desaparece:

```python
$Area2D2.visible = false
```

La puerta aparece:

```python
$puerta.visible = true
```

Y se guarda que el jugador ya tiene la llave:

```python
llaves_conseguidas = 1
```

---

## Final del nivel

El nivel solo se completa si:

* el cuerpo que entra en la puerta se llama `player`
* el jugador ha recogido la llave

```python
func _on_puerta_body_entered(body: Node2D) -> void:
	if body.name == 'player' and llaves_conseguidas == 1:
		var tiempo_restante : float = $Timer.time_left
		$Panel.visible = true
		$Panel/Label.text = "superado en %.2f segundos" % (60.0 - tiempo_restante)
		$Timer.stop()
```

Cuando se supera el nivel:

* se guarda el tiempo restante
* se muestra el panel de victoria
* se escribe el tiempo usado
* se detiene el temporizador

```python
$Panel.visible = true
```

```python
$Panel/Label.text = "superado en %.2f segundos" % (60.0 - tiempo_restante)
```

```python
$Timer.stop()
```

---

## Cronómetro

El cronómetro se actualiza constantemente en `_process`.

```python
func _process(delta: float) -> void:
	var tiempo_restante : float = $Timer.time_left
	$player/cronometro.text = "Tiempo: %.2f" % tiempo_restante
```

Con esto se muestra el tiempo restante con dos decimales:

```text
Tiempo: 43.27
```

---

## Pantalla de tiempo agotado

Cuando el `Timer` llega a cero, se ejecuta:

```python
func _on_timer_timeout() -> void:
	$fail.visible = true # Replace with function body.
	$fail/tiempo.text = 'Se acabo el tiempo'
```

Esto muestra la pantalla de fallo:

```python
$fail.visible = true
```

Y escribe el mensaje:

```python
$fail/tiempo.text = 'Se acabo el tiempo'
```

---

## Empujar objetos RigidBody2D

El jugador puede empujar objetos de tipo `RigidBody2D`.

Después de `move_and_slide()`, se comprueban las colisiones del jugador:

```python
for i in get_slide_collision_count():
	var collision := get_slide_collision(i)
	var objeto := collision.get_collider()

	if objeto is RigidBody2D:
		objeto.apply_impulse(Vector2(direction * 10, 0))
```

Si el objeto con el que choca el jugador es un `RigidBody2D`, se le aplica un impulso:

```python
objeto.apply_impulse(Vector2(direction * 10, 0))
```

El valor `10` controla la fuerza del empuje.

Si se quiere empujar más fuerte, se puede aumentar:

```python
objeto.apply_impulse(Vector2(direction * 30, 0))
```

Si se quiere empujar más suave, se puede reducir:

```python
objeto.apply_impulse(Vector2(direction * 5, 0))
```

---
