
## Sonidos, Audio stream 20025

Stream Audio 
Info: https://docs.godotengine.org/es/4.x/tutorials/audio/index.html

https://docs.godotengine.org/es/4.x/tutorials/audio/audio_buses.html

![buses audio](https://docs.godotengine.org/es/4.x/_images/audio_buses1.webp)

###  Objetivos 

* Crear canales donde dirigir el audio e incorporar efectos 
* Conectar señales de reproducción de audio
  

### AudioStreamPlayer
#### AudioStream
Un AudioStream es simplemente el recurso de sonido.

Es el archivo:

.wav
.ogg
.mp3

En Godot, cuando lo importas, pasa a ser un AudioStream.

Qué hace (y qué NO hace)
- Contiene el audio
- No se reproduce solo
- No tiene volumen ni posición

#### AudioStreamPlayer — el reproductor

Este es el nodo que:

carga un AudioStream
lo reproduce



![ASP](https://docs.godotengine.org/es/4.x/_images/audio_buses3.webp)

Para reproducirla en la escena actual: 
```
func _ready() -> void:
	$MusicaAmbiente.play()
```

#### Audio Buses — el mezclador de sonido

Esto es lo importante de verdad en juegos.

Los buses son como un mezclador de audio profesional.

Permiten:

- controlar volumen por grupo
- aplicar efectos (reverb, EQ, etc.)
- organizar audio

### Activar mediante botones 


Se puede hacer uso de las funciones de un objeto de tipo AudioStreamPlayer usando su nombre con un $ delante. Por ejemplo:
Info: https://docs.godotengine.org/es/4.x/classes/class_audiostreamplayer.html



```
# creamos un botón con nombre bt_music de tipo AudioStreamPlayer

func _on_button_pressed() -> void:
	$bt_music.play()

```


Funciones: 

```
void play()
void stop()
void set_volume_db(value: float) # en decibelios
float get_volume_db() # obtener volumen
void set_stream_paused(value: bool) # valor true=pausar, false=reanudar
bool get_stream_paused() # true si está pausado

```


Creamos un botón conmutador play/stop


```
# creamos un botón con nombre bt_music de tipo AudioStreamPlayer

func _on_button_pressed() -> void:
  if $bt_music.playing:      # se podría poner tambien if ($bt_music().is_playing): 
    $bt_music.stop()
  else:
    $bt_music.play()
```

# control por slider
- Crea el nodo slider
- conecta la señal value_changed con algun script ya sea en el nodo o en la escena (donde se esté reproduciendo el audio)
- En ese script añade: 

```
func _on_h_slider_value_changed(value: float) -> void:
	value = max(value, 0.001)
	AudioServer.set_bus_volume_db(1, linear_to_db(value))
```

