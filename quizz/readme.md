# QUIZZ 
### juego de acertar varios trivial de preguntas 

![img](quizz_world_scene.png)
Juego completo 
Codigo fuente (juego de varios quizz) - [quizz_game](quizz.zip)
Demo en Itch.io -> https://cmiugr.itch.io/quizz





<br>
<br>


### Utilidades 

*  Codigo fuente (panel individual de quizz) - [quizz_panel](quizz_panel.zip)
*  Versión 2 que almacena datos en fichero json -  [quizz_panel json](quizz_panel_json.zip)

   ![img](quizz_portada.png)





Demo en Itch.io -> https://cmiugr.itch.io/quizz

* Juego donde se activan los quizz temáticos cuando el player choca con objetos de tipo "?"
* Para ello se ha creado: 
  * player  (KinematicPlayer2D)
  * Quizz_peli_actionable (StaticObject2D)
  * Quizz_arte_actionable (StaticObject2D)
  * Quizz_pelis instancia de un Panel UI de tipo QUIZZ 
  * Quizz_arte instancia de un Panel UI de tipo QUIZZ  

### Codigo inicial
```
extends Control


@export var pregunta = 1;

var caratura = preload("res://assets/movie_barbie.jpg")
var aciertos = 0
var total_preguntas = 3 
# solo se puede hacer una vez 
var por_hacer = true    


func cargar_preguntas() -> void:
	if pregunta==1: 
		$Panel/pregunta.text = "1. Quien es la protagonista de Barbie"
		caratura = preload("res://assets/movie_barbie.jpg")
		$Panel/imagen.texture_normal = caratura
		$Panel/PanelR/bt_a.text = "Greta Gerwig"
		$Panel/PanelR/bt_b.text = "Margot Robbie"
		$Panel/PanelR/bt_c.text = "Margaret Qualley"
		
	if pregunta==2: 
		$Panel/pregunta.text = "2. En que episodio de Star Wars muere Darth Vader "
		caratura = preload("res://assets/movie_starwars.jpg")
		$Panel/imagen.texture_normal = caratura
		$Panel/PanelR/bt_a.text = "IV: Una nueva esperanza"
		$Panel/PanelR/bt_b.text = "III: La venganza de los Sith"
		$Panel/PanelR/bt_c.text = "VI: El retorno del Jedi"
		
	if pregunta==3: 
		$Panel/pregunta.text = "3. Cuantos oscars gano esta pelicula en 1942? "
		caratura = preload("res://assets/movie_casablanca.jpg")
		$Panel/imagen.texture_normal = caratura
		$Panel/PanelR/bt_a.text = "1"
		$Panel/PanelR/bt_b.text = "3"
		$Panel/PanelR/bt_c.text = "5"
	



# Cargamos la pregunta correcta 
func _ready() -> void:
	$PanelResultados.visible=false
	$PanelResultados/bt_salir.visible = false
	$PanelResultados/bt_resultado.visible = true
	$Panel/ProgressBar.max_value = total_preguntas
	$Panel/ProgressBar.value = pregunta
	aciertos = 0
	pregunta = 1
	cargar_preguntas()
	


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta: float) -> void:
	pass


func _on_bt_a_pressed() -> void:
	# pulsado botón a 
	$PanelResultados.visible=true
	if pregunta==1: 
		$PanelResultados/Resultado.text = "falso"
	if pregunta==2:
		$PanelResultados/Resultado.text = "falso"
	if pregunta==2:
		$PanelResultados/Resultado.text = "falso"



func _on_bt_b_pressed() -> void:
	# pulsado botón a 
	$PanelResultados.visible=true
	if pregunta==1: 
		$PanelResultados/Resultado.text = "Correcto!"
		aciertos = aciertos +1
	if pregunta==2:
		$PanelResultados/Resultado.text = "Falso"
	if pregunta==3:
		$PanelResultados/Resultado.text = "Correcto"
		aciertos = aciertos +1


func _on_bt_c_pressed() -> void:
	# pulsado botón a 
	$PanelResultados.visible=true
	if pregunta==1: 
		$PanelResultados/Resultado.text = "falso"
	if pregunta==2:
		$PanelResultados/Resultado.text = "Correcto"
		aciertos = aciertos +1
	if pregunta==3:
		$PanelResultados/Resultado.text = "falso"


func _on_bt_resultado_pressed() -> void:
	
	if pregunta == total_preguntas: 
		# final de los resultados 
		$PanelResultados/Resultado.text = "total respuestas acertadas = " + str(aciertos)
		$PanelResultados/bt_resultado.visible = false
		$PanelResultados/bt_salir.visible = true
		# falta conectar tb_salir con la siguiente escena
		# o bien cerrar panel 
		
	else:
		$PanelResultados.visible = false	
		pregunta += 1
		$Panel/ProgressBar.value = pregunta
		cargar_preguntas()


func _on_bt_salir_pressed() -> void:
	# falta conectar tb_salir con la siguiente escena
	# tambie puede cerrar panel 	
	
	_ready()	
	por_hacer = false
	visible = false
```


### Actividades: 

* Crear una **panel de tipo UI** (Control) que se puede **mostrar/ocultar** de tipo Quizz y con **tema personalizado**, con la siguiente estructura de preguntas con: 
	* nombre de pregunta 
	* imagen
	* tres posibles respuestas
* crear **señales personalizadas**,  se emiten cuando se choca con el objeto para abrir Panel de preguntas  https://www.youtube.com/watch?app=desktop&v=vFO2hSu_4kg
	* crear una variable de tipo signal, ejemplo ``signal signal_call_arte`` 
	* llamar a la función ``signal_call_arte.emit()``
* Dispone de **script global** ``global.gd``donde se almacenan los puntos totales
* Se han creado **dos instancias de escena de quizz** ``UI_quizz.tscn`` para crear dos trivial diferentes: de arte y películas. 
* Activar movimiento del player y dependiendo de donde colisione, activar un quizz u otro 

```python
var colision = move_and_collide(velocity * delta)
	if colision: 
		print(colision.get_collider().name)
		if (colision.get_collider().name == "Quizz_peli_actionable"):
			signal_call_pelis.emit()
		if (colision.get_collider().name == "Quizz_arte_actionable"):
			signal_call_arte.emit()
```

* **Desactivar quizz** si se han hecho previamente 

```python
#en ui_quizz_art.gd

func _on_bt_salir_pressed() -> void:
	Global.puntos_totales += aciertos
	_ready()	# reiniciar
	por_hacer = false # no se vuelve a repetir
	visible = false # se oculta


# en world.gd
func _on_character_body_2d_signal_call_pelis() -> void:
	if ($Quizz_pelis.por_hacer):
		$Quizz_pelis.visible = true
```
