

## Protagonista 

### derehca a izquierda y ciclo de animaciones


```
extends CharacterBody2D

const SPEED = 300.0
const JUMP_VELOCITY = -400.0

var atacando := false

func _ready() -> void:
	$zona_ataque/CollisionShape2D.disabled = true

func _physics_process(delta: float) -> void:
	if not is_on_floor():
		velocity += get_gravity() * delta

	if Input.is_action_just_pressed("ui_accept") and is_on_floor() and not atacando:
		$AnimatedSprite2D.play("saltar")
		velocity.y = JUMP_VELOCITY
		
	if Input.is_action_just_pressed("atacar") and is_on_floor() and not atacando:
		atacando = true
		$zona_ataque/CollisionShape2D.disabled = false
		$AnimatedSprite2D.play("ataque")
		await $AnimatedSprite2D.animation_finished
		$zona_ataque/CollisionShape2D.disabled = true
		atacando = false
		$AnimatedSprite2D.play("default")

	var direction := Input.get_axis("ui_left", "ui_right")

	if direction:
		velocity.x = direction * SPEED

		if direction < 0:
			$AnimatedSprite2D.flip_h = true
			$zona_ataque.scale.x = -1
		elif direction > 0:
			$AnimatedSprite2D.flip_h = false
			$zona_ataque.scale.x = 1
	else:
		velocity.x = move_toward(velocity.x, 0, SPEED)

	move_and_slide()

	if is_on_floor() and not atacando:
		if $AnimatedSprite2D.animation == "saltar":
			$AnimatedSprite2D.play("default")

```


### final

```
extends CharacterBody2D

const SPEED = 300.0
const JUMP_VELOCITY = -700.0

var attacking: bool = false


func _ready() -> void:
	$hitbox/CollisionShape2D.disabled = true
	$AnimatedSprite2D.play("default")
	

func _physics_process(delta: float) -> void:
	# Gravedad
	if not is_on_floor():
		velocity += get_gravity() * delta

	# Si estaba saltando y toca el suelo, vuelve a default
	if is_on_floor() and $AnimatedSprite2D.animation == "salto":
		$AnimatedSprite2D.play("default")

	# Si está atacando, no dejamos que haga otras acciones
	if attacking:
		move_and_slide()
		return

	# Salto
	if Input.is_action_just_pressed("ui_accept") and is_on_floor():
		$AnimatedSprite2D.play("salto")
		velocity.y = JUMP_VELOCITY

	# Ataque
	if Input.is_action_just_pressed("atacar") and is_on_floor():
		attacking = true
		velocity.x = 0

		$hitbox/CollisionShape2D.disabled = false
		$AnimatedSprite2D.play("ataque")

		await $AnimatedSprite2D.animation_finished

		$hitbox/CollisionShape2D.disabled = true
		attacking = false
		$AnimatedSprite2D.play("default")

		move_and_slide()
		return

	# Movimiento izquierda/derecha
	var direction: float = Input.get_axis("ui_left", "ui_right")

	if direction:
		velocity.x = direction * SPEED

		# Girar hacia la izquierda
		if direction < 0:
			$AnimatedSprite2D.flip_h = true
			$hitbox.scale.x = -1

			if has_node("hurtbox"):
				$hurtbox.scale.x = -1

		# Girar hacia la derecha
		elif direction > 0:
			$AnimatedSprite2D.flip_h = false
			$hitbox.scale.x = 1

			if has_node("hurtbox"):
				$hurtbox.scale.x = 1

	else:
		velocity.x = move_toward(velocity.x, 0, SPEED)

	move_and_slide()
```

## Enemigo

Creacion por partes del enemigo, con movimiento hacia el jugador, animaciones y ataque.

```
extends CharacterBody2D

const SPEED = 100.0
const ATTACK_COOLDOWN = 0.6
const DAMAGE = 20

@export var player: Node2D

var attacking := false


func _ready() -> void:
	$"zona_daño"/CollisionShape2D.disabled = true
	$AnimatedSprite2D.play("default")
```



Físicas
```
func _physics_process(delta: float) -> void:
	if not is_on_floor():
		velocity += get_gravity() * delta

	if player == null:
		velocity.x = 0
		move_and_slide()
		return

	if attacking:
		velocity.x = 0
		move_and_slide()
		return

	move_towards_player()
	move_and_slide()
```

Animaciones
```
func move_towards_player() -> void:
	var direction: float = sign(player.global_position.x - global_position.x)

	velocity.x = direction * SPEED

	if is_on_floor():
		$AnimatedSprite2D.play("default")

	if direction < 0:
		look_left()
	elif direction > 0:
		look_right()

func look_left() -> void:
	$AnimatedSprite2D.flip_h = true
	$"zona_daño".scale.x = -1
	$"zona_ataque".scale.x = -1


func look_right() -> void:
	$AnimatedSprite2D.flip_h = false
	$zona_daño.scale.x = 1
	$zona_ataque.scale.x = 1
```

Ataque: 

```
func _on_attack_zone_body_entered(body: Node2D) -> void:
	if body.is_in_group("player"):
		start_attack()


func start_attack() -> void:
	if attacking:
		return

	attacking = true
	velocity.x = 0

	$"zona_daño"/CollisionShape2D.set_deferred("disabled", false)
	$AnimatedSprite2D.play("ataque")

	await $AnimatedSprite2D.animation_finished

	$"zona_daño"/CollisionShape2D.set_deferred("disabled", true)
	$AnimatedSprite2D.play("default")

	await get_tree().create_timer(ATTACK_COOLDOWN).timeout

	attacking = false


func _on_hitzone_area_entered(area: Area2D) -> void:
	if area.is_in_group("player_hurtbox"):
		print("El enemigo golpea al héroe")
		vidas.vida_heroe -= DAMAGE
```




```
extends CharacterBody2D

const SPEED = 100.0
const ATTACK_COOLDOWN = 0.6
const DAMAGE = 20

@export var player: Node2D

var attacking := false


func _ready() -> void:
	$hitzone/CollisionShape2D.disabled = true
	$AnimatedSprite2D.play("default")


func _physics_process(delta: float) -> void:
	if not is_on_floor():
		velocity += get_gravity() * delta

	if player == null:
		velocity.x = 0
		move_and_slide()
		return

	if attacking:
		velocity.x = 0
		move_and_slide()
		return

	move_towards_player()
	move_and_slide()


func move_towards_player() -> void:
	var direction := sign(player.global_position.x - global_position.x)

	velocity.x = direction * SPEED

	if is_on_floor():
		$AnimatedSprite2D.play("walk")

	if direction < 0:
		look_left()
	elif direction > 0:
		look_right()


func look_left() -> void:
	$AnimatedSprite2D.flip_h = true
	$hitzone.scale.x = -1
	$attack_zone.scale.x = -1


func look_right() -> void:
	$AnimatedSprite2D.flip_h = false
	$hitzone.scale.x = 1
	$attack_zone.scale.x = 1


func _on_attack_zone_body_entered(body: Node2D) -> void:
	if body.is_in_group("player"):
		start_attack()


func start_attack() -> void:
	if attacking:
		return

	attacking = true
	velocity.x = 0

	$hitzone/CollisionShape2D.set_deferred("disabled", false)
	$AnimatedSprite2D.play("attack")

	await $AnimatedSprite2D.animation_finished

	$hitzone/CollisionShape2D.set_deferred("disabled", true)
	$AnimatedSprite2D.play("default")

	await get_tree().create_timer(ATTACK_COOLDOWN).timeout

	attacking = false


func _on_hitzone_area_entered(area: Area2D) -> void:
	if area.is_in_group("player_hurtbox"):
		print("El enemigo golpea al héroe")
		vidas.vida_heroe -= DAMAGE
```