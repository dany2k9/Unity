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

La figura de abajo muestra el ejemplo de una unidad de jugador, llamada MageUnit. En este ejemplo, el script UnitStats tiene otros atributos, que se utilizarán más adelante (como Animator y Damage Text Prefab), pero puedes ignorar esos atributos por ahora.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e230321png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e230321.png)

A estas alturas, ya puedes intentar ejecutar tu juego con la escena del título. Puedes crear una escena de Town vacía sólo para probar el botón de juego.

![https//gamedevacademyorg/wpcontent/uploads/2017/03/titlescenepng](https://gamedevacademy.org/wp-content/uploads/2017/03/title_scene.png)

#### Escena de la ciudad

Empezaremos por crear la escena de la ciudad. Así que, crea una nueva escena en tu proyecto y llámala Town.

##### Creando nuestro Tilemap

La escena de la ciudad tendrá un mapa de azulejos para que podamos caminar. Para empezar, vamos a descargar el paquete del editor de mapas de azulejos en 2D en el Administrador de Paquetes (Ventana > Administrador de Paquetes).

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e2a8743768x555png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e2a8743-768x555.png)

Entonces abre la ventana de la Paleta de Azulejos (Ventana > 2D > Paleta de Azulejos). Aquí, crea una nueva paleta y guárdala en una nueva carpeta llamada Tilemap.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e2d1e49png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e2d1e49.png)

Ahora tenemos que ir a nuestra imagen del mapa de azulejos. Poner el "Modo Sprite" en múltiple, los "Pixeles por unidad" en 64, y luego hacer clic en "Editor de Sprite".

Aquí, queremos cortar la imagen en 20 columnas y 12 filas.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e32f826png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e32f826.png)

Entonces podemos arrastrar nuestro azulejo a la ventana para crear los azulejos.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e31aaf5768x389png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e31aaf5-768x389.png)

En la escena de Town, haz clic con el botón derecho del ratón y crea un Objeto 2D > Tilemap.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e34da25png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e34da25.png)

Ahora, podemos seleccionar un azulejo y dibujarlo en el mapa de azulejos. Vamos a crear un fondo de hierba.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e39c349768x343png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e39c349-768x343.png)

Para poder colocar los azulejos, podemos crear un nuevo mapa de azulejos para todo lo que esté por encima de la hierba.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e3a6e1fpng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e3a6e1f.png)

Ponga la capa de clasificación del objeto hijo de la cuadrícula en 1.

Crea una escena, algo como esto.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e4075b4png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e4075b4.png)

#### Jugador prefabricado

Ahora que hemos añadido nuestro mapa de la ciudad a Unity, vamos a crear un Player prefab. El jugador será capaz de moverse por la ciudad, y debe colisionar con las Fichas colisionables.

Así que, vamos a empezar creando un Objeto de Juego llamado Player, añadiendo el renderizador de sprites correcto, un Box Collider 2D y un Rigidbody 2D como se muestra a continuación. Obsérvese que tenemos que poner el atributo Gravity Scale del Rigidbody2D a 0, para que el Player no se vea afectado por la gravedad.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e429a78png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e429a78.png)

También tenemos que crear las animaciones de Player. El Jugador tendrá cuatro animaciones caminantes y cuatro animaciones ociosas, una para cada dirección. Así que, primero, creamos todas las animaciones nombrándolas IdleLeft, IdleRight, IdleUp, IldeDown, WalkingLeft, WalkingRight, WalkingUp y WalkingDown.

Lo siguiente que necesitamos es el animador del jugador. Así que creamos un nuevo animador llamado PlayerAnimator y le añadimos todas las animaciones creadas. Una vez que agreguemos el animador al objeto Player en el inspector, podremos crear sus animaciones usando la ventana de animación Unity y la hoja de sprites del player. La siguiente figura muestra el ejemplo de la animación WalkingUp.

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e435d2c768x315png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e435d2c-768x315.png)

Ahora tenemos que configurar las transiciones de animación en el animador del jugador. El animador tendrá dos parámetros: DirectionX y DirectionY, que describe la dirección actual del movimiento del jugador. Por ejemplo, si el jugador se está moviendo hacia la izquierda, DirectionX es igual a -1, mientras que DirectionY es igual a 0. Esos parámetros serán configurados correctamente más adelante en un script de movimiento.

Cada animación ociosa tendrá una transición a cada animación caminante. Los parámetros de dirección deben tener los valores de acuerdo a la dirección de la animación. Por ejemplo, la animación Izquierda Ociosa cambiará a Izquierda Caminante si la DirecciónX es igual a -1. Además, cada animación caminante tendrá una transición para su correspondiente animación ociosa. Finalmente, si el jugador cambia su dirección de marcha sin detenerse, necesitamos actualizar la animación. Por lo tanto, necesitamos agregar transiciones entre las animaciones caminantes también.

Al final, el animador del jugador debe verse como la figura de abajo. Las siguientes figuras muestran ejemplos de transiciones entre animaciones (IdleLeft -> WalkingLeft y WalkingLeft -> IdleLeft).

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e442dabpng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e442dab.png)

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e45340fpng](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e45340f.png)

![https//gamedevacademyorg/wpcontent/uploads/2019/06/Snag3e45bd24png](https://gamedevacademy.org/wp-content/uploads/2019/06/Snag_3e45bd24.png)

Ahora vamos a crear el script de PlayerMovement. Todos los movimientos se realizan en el método FixedUpdate. Usamos las entradas de los ejes horizontal y vertical para comprobar si el jugador debe moverse horizontal o verticalmente. El jugador puede moverse a una dirección dada si no se está moviendo ya a la dirección opuesta. Por ejemplo, puede moverse a la izquierda si no se está moviendo ya a la derecha. Hacemos eso tanto para la dirección vertical como para la horizontal. Cuando se mueve a una dirección dada, necesitamos establecer los parámetros del animador. Al final, aplicamos la velocidad al Player Rigidbody2D.

