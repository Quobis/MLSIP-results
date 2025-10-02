# MLSIP Results

## Descripción

![mlsip-logo](img/MLSIP-logo.png)
![mlsip-patrocinios](img/MLSIP-logos-patrocinio.png)

El presente repositorio recoge los resultados de las pruebas y datos
técnicos sobre el proyecto 6G ON TIME.

### Título proyecto

MLSIP: Optimización SIP mediante Machine Learning

### Resumen ejecutivo

El objetivo general del proyecto MLSIP es optimizar el uso de la red y todas las capacidades que 5G, y 6G en un futuro, ponen a disposición de las comunicaciones unificadas para dar respuesta a una serie de casos de uso de comunicaciones en tiempo real, que precisan de una gran precisión temporal.

Para alcanzar este objetivo, MLSIP pretende implementar un módulo de conectividad para redes de comunicaciones en tiempo real 5G que, basándose en la información recibida de sesiones SIP anteriores, pueda decidir la mejor manera de enrutar el tráfico SIP a través de la red. Previamente es necesario analizar el uso de mecanismos de aprendizaje automático basados en la información obtenida de sesiones anteriores, para seleccionar las opciones más adecuadas que permitan gestionar tablas de rutas y calidad de los medios en el proxy SIP en tiempo real.

## Escenarios de pruebas

Para probar el servicio MLSIP se han definido los siguientes escenarios:

* **Test 1: Escenarios con probabilidades y restricciones horarias/códec:**

  * Ruta 1: acepta las peticiones INVITE con una probabilidad del 70 %, pero rechaza aquellas que utilicen el códec PCMU.
  * Ruta 2: admite llamadas con una probabilidad del 90 %, aunque no permite cursar comunicaciones en el intervalo horario de 9:00 a 11:00.
  * Ruta 3: cuenta con una probabilidad de aceptación del 85 %, pero rechaza llamadas en el horario de 10:00 a 12:00.
  * Ruta 4: acepta llamadas con una probabilidad del 85 %, aunque no admite aquellas en las que se negocie el uso de los códecs PCMA u Opus.

* **Test 2: Escenarios con restricciones basadas en numeración y horarios:**
  
  * Ruta 1: acepta las peticiones INVITE con una probabilidad del 70 %, pero rechaza las llamadas cuyo campo To contiene el prefijo +34, es decir, no acepta llamadas dirigidas a España.
  * Ruta 2: admite llamadas con una probabilidad del 90 %, aunque no acepta llamadas en el intervalo horario de 12:00 a 14:00.
  * Ruta 3: cuenta con una probabilidad de aceptación del 85 %, pero rechaza las llamadas cuyo campo To contiene los prefijos +33 o +1, es decir, no acepta llamadas hacia Francia ni hacia Estados Unidos.
  * Ruta 4: acepta llamadas con una probabilidad del 85 %, aunque rechaza las llamadas cuyo campo To contiene el prefijo +35, es decir, no acepta llamadas a Portugal.

## Baterías de pruebas

Para validar el comportamiento del sistema MLSIP tras el entrenamiento, se han definido baterías de pruebas que combinan llamadas hacia las rutas configuradas en los escenarios de Test 1 y Test 2.

### 1. Fase de entrenamiento

* Se generan múltiples llamadas hacia todas las rutas definidas en ambos tests.
* Estas llamadas, con diferentes condiciones de códec, horarios y numeración, permiten recopilar un conjunto de datos suficientemente diverso para el entrenamiento del modelo de machine learning.
* Se pueden consultar los CDRs de las llamadas realizadas [aquí](http://188.86.230.50:3333/kamailio/cdrs?per_page=1000&page=1)
  * user: mlsipuser
  * pass: mlsip1234*

### 2. Fase de validación

* Una vez entrenado el modelo, se plantean escenarios específicos de prueba:
  * Invites con códecs PCMA y PCMU, que ponen a prueba las restricciones establecidas en el Test 1.
  * Invites dirigidos a numeración internacional (+33, +34, +35), que ponen a prueba las restricciones establecidas en el Test 2 (llamadas a Francia, España y Portugal).
* En función de la hora en que se cursan las llamadas y de los parámetros de cada sesión, el sistema debería seleccionar automáticamente la ruta más adecuada, evitando aquellas que presenten restricciones activas en ese momento.

## Resultados de las pruebas

La siguiente tabla resume los resultados alcanzados en cada escenario de prueba:

| Prueba                            | Total llamadas | Llamadas exitosas | Porcentaje de acierto |
|-----------------------------------|:--------------:|:-----------------:|:---------------------:|
|INVITE con códec PCMA              | 500            | 353               | 70.6%                 |
|INVITE con códec PCMU              | 500            | 416               | 83.2%                 |
|INVITE entre las 9:00 y las 10:00  | 500            | 359               | 71.8%                 |
|INVITE entre las 10:00 y las 12:00 | 500            | 354               | 70.8%                 |
|INVITE con destino +34 (España)    | 500            | 386               | 77.2%                 |
|INVITE con destino +33 (Francia)   | 500            | 449               | 89.8%                 |
|INVITE con destino +35 (Portugal)  | 500            | 447               | 89.4%                 |

De acuerdo con los resultados, el sistema MLSIP demuestra un alto porcentaje de acierto en la selección de rutas, lo que confirma la validez del enfoque adoptado para el entrenamiento y la toma de decisiones en tiempo real. Cabe destacar que las rutas tienen asociado un porcentaje de error, por lo que, aún escogiendo la ruta más adecuada en cada caso, es posible que la llamada sea rechazada.

No obstante, estos resultados mejorarían considerablemente con el uso de datos reales de operadores, ya que los patrones de telefonía real son bastante predecibles.

## Copyright

@ Quobis Networks SLU. 2025. Todos los derechos reservados. La plataforma de
comunicaciones de Quobis y sus marcas relacionadas, son marcas comerciales de
Quobis. Todas las otras marcas registradas mencionadas son propiedad de sus
respectivos fabricantes. El contenido de este documento es público.
