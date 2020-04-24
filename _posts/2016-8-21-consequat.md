---
layout: post
title: RPG
description: Cómo crear un juego de rol en Unity - Guía completa
image: assets/images/pic05.jpg
---

### Cómo crear un juego de rol en Unity - Guía completa

En este tutorial vamos a construir un juego de RPG usando Unity. Nuestro juego tendrá tres escenas: una escena de título, una escena de ciudad, que representan el mundo del juego, donde el jugador puede navegar y encontrar batallas, y la escena de batalla. Las batallas tendrán diferentes tipos de enemigos, y las unidades del jugador tendrán diferentes acciones como: ataque regular, ataque mágico, y huir de la batalla.

Para poder seguir este tutorial, se espera que estés familiarizado con los siguientes conceptos:

-   Programación en C#
-   Uso del inspector de Unity, como la importación de activos, la creación de prefabricados y la adición de componentes
-   Creación de mapas de mosaicos básicos, como añadir un conjunto de mosaicos y crear capas de mosaicos

Antes de empezar a leer el tutorial, crea un nuevo proyecto Unity e importa todos los sprites disponibles a través del código fuente. Tendrás que cortar las hojas de sprites en consecuencia.

#### Escena del título

Lienzo de fondo
En primer lugar, vamos a crear un lienzo para mostrar la imagen de fondo en la escena del título. Puedes hacerlo creando un nuevo Lienzo llamado BackgroundCanvas, y estableciendo su modo de renderización como Screen Space - Camera. Para ello, necesitamos especificar la cámara del lienzo, que será nuestra cámara principal. Además, el modo de escala de UI (en Canvas Scaler) se configurará para seguir el tamaño de la pantalla, con una resolución de referencia de 1280×960.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e1a5dfepng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e1a5dfe.png)

Después de hacer eso, creamos un nuevo objeto de imagen como hijo de este lienzo. La fuente de la imagen será la imagen de fondo, y podemos establecer su tamaño nativo para mostrarla correctamente.

#### Lienzo del HUD

Ahora, necesitamos otro lienzo para mostrar los elementos del HUD. En la escena del título, esos elementos serán un texto de título, y un botón de reproducción.

Comencemos creando otro Lienzo siguiendo el mismo proceso que el Lienzo de Fondo. Sin embargo, para mostrar este lienzo sobre el de fondo, necesitamos establecer correctamente su capa de ordenación. Vamos a crear otra capa llamada HUD, y poner el HUDCanvas en esta capa.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e1c2437png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e1c2437.png)

Finalmente, necesitamos crear los dos objetos HUD. El primero es un Texto, mientras que el segundo es un Botón. Para el objeto Texto sólo necesitamos establecer su mensaje. Por otro lado, para el objeto Botón necesitamos establecer su llamada OnClick.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e1d785epng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e1d785e.png)

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e1e4ddepng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e1e4dde.png)

El objeto PlayButton tendrá el siguiente script ChangeScene para la devolución de llamada OnClick. Este script simplemente definirá un método para iniciar una nueva escena dado su nombre. Luego, establecemos la llamada de retorno OnClick del botón de reproducción para llamar al método loadNextScene con "Town" como parámetro.

```csharp
public class ChangeScene : MonoBehaviour
{
 public void loadNextScene (string sceneName)
 {
  SceneManager.LoadScene(sceneName);
 }
}
```

#### Fiesta del jugador

En nuestro juego, queremos mantener los datos de las unidades de los jugadores guardados incluso al cambiar de escena. Para ello, vamos a crear un objeto persistente de PlayerParty, que no se destruirá al cambiar de escena. También tendrás que establecer correctamente su posición para que se cree en la posición correcta en la escena de la batalla.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e2000fcpng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e2000fc.png)

Para mantener viva la PlayerParty al cambiar de escena, vamos a utilizar el siguiente script, llamado StartBattle. Este script evita que el objeto sea destruido cuando se cambia de escena en el método Start. También añade una llamada de retorno cuando se carga una nueva escena y establece el objeto como inactivo, para que no se muestre en la pantalla de título.

La llamada de retorno configurada (OnSceneLoaded) comprueba si la escena cargada actual es la escena del título. Si es así, el objeto PlayerParty debe ser destruido, para evitar duplicados. De lo contrario, debe ser configurado como activo si la escena actual es la escena de Batalla.

```csharp
public class StartBattle : MonoBehaviour
 {
 // Use this for initialization
	 void Start ()
	 {
		 DontDestroyOnLoad(this.gameObject);
		 SceneManager.sceneLoaded += OnSceneLoaded;
		 this.gameObject.SetActive (false);
	 }
	 private void OnSceneLoaded (Scene scene, LoadSceneMode mode)
	 {
		 if (scene.name == "Title")
		 {
			 SceneManager.sceneLoaded -= OnSceneLoaded;
			 Destroy(this.gameObject);
		 }
		 else
		 {
			this.gameObject.SetActive(scene.name == "Battle");
		 }
	 }
 }
```

Además, la PlayerParty tendrá dos hijos que representarán a las unidades de los jugadores. Así que, primero vamos a crear una Unidad de Jugador prefabricada con sólo unas pocas cosas por ahora. Más adelante en este tutorial, vamos a añadir el resto de su comportamiento.

Por ahora, la PlayerUnit tendrá sólo el Sprite Renderer y un script llamado UnitStats, que almacenará las estadísticas de cada unidad, como: salud, maná, ataque, ataque mágico, defensa y velocidad.

```csharp
public class UnitStats : MonoBehaviour 
{
    public float health;
    public float mana;
    public float attack;
    public float magic;
    public float defense;
    public float speed;
}
```
