---
layout: page
permalink: /docs/evci.html
---

# Conceptos básicos: Eventos asíncronos

EVCI (EVents Common Interface) es un módulo de corbel para escuchar eventos de forma asíncrona. Esto es útil cuando al cliente no le interesa esperar al procesamiento de una petición para recibir respuesta. También cuando se produce un evento muchas veces de forma simultanea, ya que evci utiliza un sistema de colas para no bloquear la api durante el procesamiento de los eventos, y asegura que el orden de llegada de los eventos será el orden en el que serán atendidos.

El comportamiento para responder a esos eventos es fácilmente extensible a través de Eworkers, que son plugins programados en Java.

Los eventos no son más que un json con formato libre, que deberá atenerse al eworker que lo vaya a escuchar.

# EVCI API

La api de evci se compone de un solo endpoint que se corresponde a la acción de enviar evento. Los detalles a cerca del enpoint pueden consultarse en [apiary](http://docs.silkroadevci.apiary.io/).

Un ejemplo de petición a evci podría ser el siguiente:

{% highlight js %}
POST https://evci.bqws.io/v1.0/event/log:ClientLog

body: {
	"client": {"browser":"Chrome","version":"36.0.1985.143","os":"Linux x86_64","screen_width":1366,"screen_height":768,"app_name":"corejs-silkroad-tests","app_version":"1.0.0"},
	"logs":[
    	{"timestamp":1410774700033,"level":"INFO","message":"My log message!!"},
    	{"timestamp":1410774700054,"level":"ERROR","message":"ERROR!!!"}
    ]
}
{% endhighlight %}

Donde 'log:ClientLog' es el tipo de evento enviado y el body el contenido en si del evento.

Si el json está bien formado y cumple el formato que espera el eworker, EVCI siempre devolverá un HTTP status 202 (accepted), indicativo de que la petición ha sido aceptada y se procesará eventualmente.

## Versionado

El primer nivel de todas las URL de Corbel es siempre la versión de ésta última, y tiene la forma v{número}.

{% highlight js %}
https://evci-staging.bqws.io/v1.0/event/test:Event
{% endhighlight %}

## Autorización

Como todas las api's de corbel, evci necesita de autorización. Para ello, hay que enviar un access token de iam en la cabecera Authorization. Más información sobre como conseguir un access token en la documentación de [IAM](authorization.html).

## Eworkers

Los eworkers son los plugins de EVCI. Son programados en java y tienen que implementar la eworker-api de la que provee Corbel. Una vez programado, el eworker se registra para un tipo de evento, que es el path param del endpoint de EVCI. Una vez registrado, este eworker será el que procese los eventos de su tipo, y será responsable de validar el formato del evento.

Un ejemplo de eworker es el logs-eworker, que es el encargado de procesar y almacenar los logs de los clientes. La información de esta api se encuentra en [apiary](http://docs.silkroadclientlog.apiary.io/).
