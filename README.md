# Vimeo Video Playback with Popup Control

Este documento contiene dos versiones de código JavaScript para gestionar la reproducción de un video de Vimeo incrustado en una página web. Ambas versiones incluyen un popup que bloquea la visualización del video hasta que el usuario complete un formulario o interactúe con un botón de desbloqueo. La diferencia principal entre ambas versiones es el momento en que el video se pausa y el popup aparece.

## Estructura HTML

El HTML para ambas versiones incluye un contenedor para el video de Vimeo y un popup que contiene un mensaje y un botón de desbloqueo:

```html
<div class="content">
  <div id="contentMore" class="hidden">
    <p>¿Seguro que quieres perderte estos conocimientos exclusivos?</p>
    <p class="last">Tú decides</p>
    <a id="ventasPopupLink" href="#ventas-popup" class="button">Ver vídeo</a>
  </div>
  <lite-vimeo thumbnail="https://go.growthhackingcourse.io/wp-content/uploads/2024/11/poster1.webp" videoid="1027647147" videotitle="Masterclass" id="liteVimeo"></lite-vimeo>
</div>
```

- #contentMore: Este div contiene el mensaje de bloqueo y un botón para desbloquear el video.
- lite-vimeo: Es el contenedor del video de Vimeo, donde se carga y reproduce el video.

## Versión 1: Bloqueo Inmediato al Hacer Clic en "Play"
```html
<script nonce="abc123" src="https://player.vimeo.com/api/player.js"></script>

```
```javascript
<script>
  jQuery(document).ready(function($) {
    const liteVimeo = document.querySelector('lite-vimeo');
    const contentMore = document.getElementById('contentMore');
    const formID = '#productividad_nov';
    let player;
    let videoPausedOnce = false;

    // Función para bloquear la reproducción y mostrar el popup
    function handleVideoEvents(player) {
      player.on('play', function() {
        if (!videoPausedOnce) {
            player.pause().then(() => {
              contentMore.style.display = 'flex';
              liteVimeo.style.display = 'none';
              videoPausedOnce = true;
              elementorProFrontend.modules.popup.showPopup({ id: 8814 });
            });
        }
      });
    }

    liteVimeo.addEventListener('click', function() {
      const iframe = liteVimeo.shadowRoot.querySelector('iframe');
        if (iframe) {
          player = new Vimeo.Player(iframe);
          handleVideoEvents(player);
        }
    });

    $(document).on('submit_success', formID, function() {
      if (player) {
        player.play().then(() => {
          contentMore.style.display = 'none';
          liteVimeo.style.display = 'flex';
        });
      }
    });
  });
  </script>
```
## Explicación de la Versión 1
- Bloqueo al hacer clic: Al hacer clic en el video, el evento click bloquea la reproducción y muestra el popup (contentMore).
- Desbloqueo con el formulario: Una vez que el formulario se envía exitosamente (submit_success), el video se desbloquea y se reproduce automáticamente.


## Versión 2: Bloqueo Después de 3 Segundos de Reproducción
En esta versión, el video comienza a reproducirse normalmente, pero se bloquea automáticamente después de 3 segundos. Al pausarse, muestra el popup con la opción de desbloqueo, solicitando que el usuario complete el formulario.
```html
<script nonce="abc123" src="https://player.vimeo.com/api/player.js"></script>

```
```javascript
<script>
jQuery(document).ready(function($) {
    const liteVimeo = document.querySelector('lite-vimeo');
    const contentMore = document.getElementById('contentMore');
    const formID = '#productividad_nov';
    let player;
    let videoPausedOnce = false;

    // Función para pausar el video después de 3 segundos
    function handleVideoEvents(player) {
      player.on('play', function() {
        if (!videoPausedOnce) {
          setTimeout(() => {
            player.pause().then(() => {
              contentMore.style.display = 'flex';
              liteVimeo.style.display = 'none';
              videoPausedOnce = true;
            });
          }, 3000); // Pausa el video después de 3 segundos
        }
      });
    }

    liteVimeo.addEventListener('click', function() {
      const iframe = liteVimeo.shadowRoot.querySelector('iframe');
      if (iframe) {
        player = new Vimeo.Player(iframe);
        handleVideoEvents(player);
      }
    });

    $(document).on('submit_success', formID, function() {
      if (player) {
        player.play().then(() => {
          contentMore.style.display = 'none';
          liteVimeo.style.display = 'flex';
        });
      }
    });
  });
  ```
  ## Explicación de la Versión 2
  - Reproducción inicial: El video comienza a reproducirse al hacer clic en el reproductor.
  - Bloqueo automático: Después de 3 segundos, el video se pausa automáticamente, y se muestra el popup con el mensaje de desbloqueo (contentMore).
  - Desbloqueo con el formulario: Una vez que el formulario se envía exitosamente (submit_success), el video se desbloquea y se reanuda desde el punto en el que fue pausado.
