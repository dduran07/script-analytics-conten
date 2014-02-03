script-analytics-conten
=======================

Scrip para medir contenido con Google Analytics
<script>
/*
el script original fue realizado por Justin Cutroni y está disponible en la siguiente dirección: http://cutroni.com/blog/2012/02/21/advanced-content-tracking-with-google-analytics-part-1/
En este versión el código se ha modificado para que funcione con analytics.js de Universal Analytics.I
*/
 
jQuery(function($) {
    // indicador de depuración
    var debugMode = false;
 
    // retraso de tiempo predeterminado antes de verifcar la ubicación
    var callBackTime = 100;
 
    // # de pixeles antes del seguimiento del lector
    var readerLocation = 380;
 
    // establecemos algunos indicadores para el segumiento y la ejecución
    var timer = 0;
    var scroller = false;
    var endContent = false;
    var halfContent = false;
    var didComplete = false;
 
    // se establecen algunas variables de tiempo para calcular el tiempo de lectura
    var startTime = new Date();
    var beginning = startTime.getTime();
    var totalTime = 0;
 
    // seguimiento de la carga del artículo
    if (!debugMode) {
        ga('send', {
          hitType: 'event',         
          eventCategory: 'Reading', 
          eventAction: 'ArticleLoaded',
          nonInteraction: 1
        });
        
    }
 
    // se rastrea la ubicación y seguimiento del usuario
    function trackLocation() {
        bottom = $(window).height() + $(window).scrollTop();
        height = $(document).height();
 
        // si el usuario comienza a desplazarce se envía un evento
        if (bottom > readerLocation && !scroller) {
            currentTime = new Date();
            scrollStart = currentTime.getTime();
            timeToScroll = Math.round((scrollStart - beginning) / 1000);
            if (!debugMode) {
                
                ga('send', {
                  hitType: 'event',         
                  eventCategory: 'Reading', 
                  eventAction: 'StartReading',
                  eventValue: timeToScroll
                });
            } else {
                alert('started reading ' + timeToScroll);
            }
            scroller = true;
        }
 
 
        // si el usuario llega al final del artículo se envía un evento
        if (bottom >= ($('.entry-content').scrollTop() + $('.entry-content').innerHeight())/2 && !halfContent) {
            currentTime = new Date();
            contentScrollEnd = currentTime.getTime();
            timeToContentEnd = Math.round((contentScrollEnd - scrollStart) / 1000);
            if (!debugMode) {
                ga('send', {
                  hitType: 'event',         
                  eventCategory: 'Reading', 
                  eventAction: 'HalfContent',
                  eventValue: timeToContentEnd
                });
            } else {
                alert('half content section '+timeToContentEnd);
            }
            halfContent = true;
        }
        
        // si el usuario llega al final del artículo se envía un evento
        if (bottom >= $('.entry-content').scrollTop() + $('.entry-content').innerHeight() && !endContent) {
            currentTime = new Date();
            contentScrollEnd = currentTime.getTime();
            timeToContentEnd = Math.round((contentScrollEnd - scrollStart) / 1000);
            if (!debugMode) {
                if (timeToContentEnd < 40) {
                    ga('set', 'dimension2', 'Scanner');//se configura la dimension definida en google analytics
                } else {
                    ga('set', 'dimension2', 'Reader');//se configura la dimension definida en google analytics
                }
                
                ga('send', {
                  hitType: 'event',         
                  eventCategory: 'Reading', 
                  eventAction: 'ContentBottom',
                  eventValue: timeToContentEnd,
                  metric1:timeToContentEnd //se configura el ID de la métrica creada en google analytics
                });
            } else {
                alert('end content section '+timeToContentEnd);
            }
            endContent = true;
        }
 
        // si el usuario lleg a la parte inferior de la página se envía un evento
        if (bottom >= (height-1460) && !didComplete) {
            currentTime = new Date();
            end = currentTime.getTime();
            totalTime = Math.round((end - scrollStart) / 1000);
            if (!debugMode) {
                 ga('send', {
                  hitType: 'event',         
                  eventCategory: 'Reading', 
                  eventAction: 'PageBottom',
                  eventValue: totalTime
                });
            } else {
                alert('bottom of page '+totalTime);
            }
            didComplete = true;
        }
    }
 
    // se realiza el seguimiento del scroll y se rastrea la ubicación
    $(window).scroll(function() {
        if (timer) {
            clearTimeout(timer);
        }
 
        // se debe usar un bufer para no llamar con tanta fecuencia a trackLocation
        timer = setTimeout(trackLocation, callBackTime);
    });
});
</script>
