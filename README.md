## Práctica 11 – Visualizador musical interactivo 3D con tween.js

### Autores y asignatura
- **Autor**: Miguel Ángel Rodríguez Ruano  
- **Universidad**: ULPGC – GII (Grado en Ingeniería Informática)  
- **Asignatura**: Informática Gráfica  

Repositorio / código online:  
- Codesandbox: https://codesandbox.io/p/sandbox/ig2526-s10-forked-dfh28h  

***
# GIF de la Demo

![Demo](./practica-11-ig.gif)

***
# Enlace al vídeo (haz clic sobre el para redirigirte al video)

[![Ver demo](https://img.youtube.com/vi/VTMJ4UY5HFo/0.jpg)](https://youtu.be/VTMJ4UY5HFo)
***
## Descripción de la práctica y relación con la entrega

La tarea consistía en proponer un prototipo en three.js de temática libre que integrara la biblioteca tween.js y/o el motor de física ammo.js, acompañado de un README y un vídeo corto (≈1 minuto) mostrando la animación y la autoría.  
La solución desarrollada es un **visualizador musical interactivo** que simula los típicos “palos” de un ecualizador de audio, pero con una capa extra de interacción tipo juego: el usuario puede disparar a las barras para hacer que salgan despedidas hacia atrás y, al derribarlas todas, la música se detiene y aparece una pantalla final con estadísticas.

El proyecto cumple los requisitos de la entrega:
- Usa **three.js** para la escena 3D.  
- Integra **tween.js** para animar el movimiento del proyectil y ciertos efectos suaves.  
- Incluye un sistema de visualización y animación claramente demostrable en vídeo a resolución 1920×1080 (Full HD, 16:9).  
- La autoría se muestra en el HUD/pantalla final y se documenta en este README.

***

## Concepto y motivación

La idea es reinterpretar el clásico visualizador de audio (barras que suben y bajan con la música) y convertirlo en una experiencia interactiva:

- **Visualizador musical**:  
  Las barras reaccionan a la pista de audio mediante cambios en escala, posición y color, generando un ecualizador 3D neón.

- **Interacción lúdica**:  
  El usuario puede “disparar” a las barras, como si destruyera físicamente el visualizador. Cada barra golpeada:
  - Cambia de color al del disparo.  
  - Recibe un impulso y sale proyectada hacia atrás (eje -Z) con rotación.  

- **Narrativa simple**:  
  Al derribar todas las barras, la música se “apaga” y aparece una pantalla final opaca con estadísticas: puntuación, tiempo, disparos, precisión. La motivación es mezclar:
  - Técnicas de animación y visualización de audio.  
  - Un mínimo de lógica de juego, feedback visual y UX.  

***

## Funcionalidad principal

### Escena y estética

- Escena 3D construida con **three.js**:
  - Cámara en perspectiva con controles orbitales (OrbitControls).  
  - Suelo negro y gran pared oscura al fondo para remarcar el brillo de las barras.  
  - Iluminación combinada (luz ambiental, direccional y spotlight).  
- Postprocesado con **UnrealBloomPass**:
  - Resalta la emisividad de barras, proyectiles y HUD, creando un estilo neón minimalista.

### Barras de audio (“palos”)

- Conjunto de barras grandes dispuestas en una **línea horizontal**, más juntas entre sí para dar sensación de “barrera musical”.  
- Cada barra:
  - Tiene parámetros de animación propios (fase, amplitud, velocidad), de modo que no se mueven al unísono.  
  - Oscila en escala vertical (height), posición en Y y color en función:
    - Del tiempo.  
    - De la energía del audio obtenida del analizador.  
- El resultado es un movimiento desincronizado pero coherente, como un ecualizador 3D vivo.

### Audio y análisis en tiempo real

- Se carga una pista de audio usando el sistema de audio de three.js (basado en Web Audio API).  
- Un analizador de frecuencia/energía calcula un valor que se usa como **factor musical**:
  - Aumenta o reduce la amplitud de las animaciones de las barras.  
  - Modula la intensidad de la emisividad (brillo neón).  
- La música está vinculada al “estado” del campo de barras: cuando todas se derriban, se reduce el volumen hasta detener la reproducción.

### Disparo, interacción y pseudo-física

- **Entrada del usuario**:
  - Ratón + click para disparar.  
  - Tecla **espacio** para disparar hacia donde apunta el cursor.  
- **Raycasting**:
  - Se proyecta un rayo desde la cámara hacia el punto de la pantalla para detectar impactos en:
    - Barras.  
    - Suelo o pared.  
- **Impacto en barras**:
  - Se genera un proyectil (esfera) que se anima con tween.js desde la cámara al punto de impacto.  
  - Al llegar:
    - La barra cambia de color al del proyectil (color sólido, sin marcas pegadas).  
    - Se marca como “derribada” y se actualizan los contadores de score y aciertos.  
    - Recibe un **impulso hacia atrás** (vector principal en -Z, con componente vertical y algo de variación lateral).  
    - Se aplica una física simplificada:
      - Gravedad.  
      - Rebote con amortiguación sobre un “suelo” virtual.  
      - Fricción progresiva hasta detenerse.  
- **Impacto en suelo o pared**:
  - Se generan manchas de pintura planas (textura procedimental en un plano circular) que se quedan en la superficie, para dar feedback visual al disparo.

### HUD y pantalla final

- **HUD neón minimalista**:
  - Muestra:
    - Puntuación total.  
    - Número de barras restantes.  
    - Disparos realizados.  
    - Precisión (% de impactos sobre barras).  
    - Tiempo transcurrido.  
  - Tipografía tipo “sci‑fi” y líneas divisorias con gradiente.

- **Pantalla de victoria (overlay opaco)**:
  - Cuando todas las barras han sido derribadas:
    - Se crea un overlay casi negro (≈95% opaco) cubriendo toda la pantalla.  
    - Encima se centra un panel con:
      - Título (“Complete”).  
      - Puntuación final.  
      - Tiempo.  
      - Disparos.  
      - Precisión.  
    - Este panel es adecuado para capturar un frame claro e identificar la autoría en el vídeo.

***

## Detalles para la demo en vídeo (≈1 minuto, 1920×1080)

Para grabar la demo se recomienda:

1. **Comenzar mostrando el setup**:
   - Una panorámica lenta de la línea de barras bailando con la música.  
   - Mostrar cómo la cámara puede orbitar ligeramente (OrbitControls).

2. **Mostrar la interacción**:
   - Apuntar a distintas barras y disparar (click y/o barra espaciadora).  
   - Que se vea claramente:
     - El proyectil viajando.  
     - La barra cambiando de color.  
     - La barra saliendo disparada hacia atrás y cayendo con rotación.

3. **Progresión**:
   - Derribar varias barras para que se note que el campo visual se vacía.  
   - Dejar que el HUD se vea durante toda la interacción (score, targets, etc.).

4. **Final**:
   - Derribar la última barra.  
   - Captar el momento en el que:
     - La música se apaga.  
     - Aparece el overlay opaco con la pantalla de resultados.  
   - Dejar unos segundos con la pantalla final visible (sirve como identificación y cierre del vídeo).

La resolución objetivo recomendada es 1920 × 1080 (16:9, Full HD), que encaja bien con la composición horizontal de la línea de barras.

***

## Dependencias y tecnologías utilizadas

### Librerías principales

- **three.js**  
  Motor gráfico 3D en WebGL. Usado para:
  - Escena, cámara, luces y materiales.  
  - Geometría de barras, proyectiles y manchas.  
  - Raycasting para detección de impactos.  
  - Audio (AudioListener, Audio, AudioAnalyser).  

- **@tweenjs/tween.js**  
  Librería de tweening para animaciones suaves. Se utiliza para:
  - Animar la posición del proyectil desde la cámara hasta el punto de impacto.  
  - Gestionar transiciones continuas sin tener que codificar interpolaciones manuales frame a frame.

- **OrbitControls (three/examples)**  
  Control de cámara orbital para mover la vista con el ratón.

- **EffectComposer + RenderPass + UnrealBloomPass (three/examples)**  
  Sistema de postprocesado para:
  - Renderizado con paso de postproceso.  
  - Aplicar efecto de bloom que refuerza el estilo neón de barras, HUD y manchas.

### Otras APIs / conceptos

- **Web Audio (vía integración de three.js)**  
  Para cargar, reproducir y analizar el audio (obtener la media de frecuencia).

- **HTML/CSS**  
  - Inserción del lienzo WebGL.  
  - HUD y pantalla de victoria como elementos HTML fijados (position: fixed) con estilos neón y overlay opaco.  

***

## Cómo ejecutar y probar

1. Abrir el enlace del proyecto (por ejemplo, en codesandbox) y arrancar la aplicación.  
2. Asegurarse de que el navegador permite la reproducción de audio (si no suena, hacer clic una vez en la ventana para desbloquear el contexto de audio).  
3. Usar el ratón para:
   - Orbitar la cámara.  
   - Apuntar a diferentes puntos del campo de barras.  
4. Disparar con:
   - Click de ratón.  
   - Tecla **espacio**.  
5. Observar:
   - La respuesta de las barras a la música (movimiento y color).  
   - El comportamiento físico al ser golpeadas (salto hacia atrás y caída).  
   - La actualización del HUD.  
6. Derribar todas las barras para activar:
   - El muteo/parada de la música.  
   - La aparición de la pantalla final opaca con estadísticas.
