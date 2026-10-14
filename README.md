# Diseno_y_construccion_de_una_incubadora_neonatal_a_escala
# Introducción

La incubadora neonatal es un equipo médico que trata de crear un ambiente parecido al del vientre materno, para que el recién nacido pueda sobrevivir y desarrollarse bien [1], [2]. De todas las condiciones que se controlan, la temperatura es la más importante, y debe mantenerse alrededor de los 37 °C [3]. Esto se logra con un ventilador que mueve el aire del ambiente y lo hace pasar por un elemento que calienta por ejemplo una resistencia hasta llegar a la temperatura que se quiere.
El control de la temperatura juega un papel importante en los bebés prematuros ya que ellos tienen  el sistema nervioso menos maduro, por eso les cuesta mucho regular su propia temperatura [4]. En países tropicales el problema es doble, porque la temperatura puede llegar a 40 °C y el recién nacido corre el riesgo de sufrir hipertermia [5]. Por eso además de calentar, una incubadora real también debería poder enfriar, y eso la hace más costosa.
En este laboratorio se construyó una incubadora neonatal a escala que imita la forma de operar de una real. Primero, en la parte A, se identificaron las partes principales de una incubadora y la función de cada una, y se diseñaron y simularon dos circuitos, uno para mantener la temperatura entre 36 °C y 37,5 °C en lazo cerrado y otro para medir el peso y mostrarlo en una pantalla. Después en la Parte B se armó el prototipo con una cabina transparente, un ventilador, resistencias para calentar, un panel de tres leds que indica si la temperatura está dentro del rango, y una celda de carga para estimar el peso. También se calculó el costo del sistema y se comparó con equipos comerciales. Por último, en la Parte C se documentó todo el procedimiento y se organizó en un repositorio de GitHub.
La práctica es importante porque muestra lo exigente que es controlar variables como la temperatura en un dispositivo biomédico y también deja ver qué pasa si el equipo falla. Un sobrecalentamiento o una medición de peso mal calculada pueden afectar directamente la salud del bebé, y eso es algo que como ingenieros biomédicos tenemos que tener presente.

# Objetivos 
Objetivo General

• Reconocer la importancia de las incubadoras neonatales
en la salud del neonato.

Objetivos Específicos

• Identificar las partes principales que componen una incubadora neonatal.

• Desarrollar un sistema que emule el modo de operación de una incubadora
neonatal.

• Evaluar cómo impacta el control de variables como temperatura, humedad y
flujo de aire en la salud del neonato.

# Metodología 
Parte A


1. Identificar las principales partes que componen una incubadora neonatal, así
como también la función que desempeñan, mediante una revisión de la
literatura relacionada. En caso de utilizar modelos de IA generativa (e.g., ChatGPT) debe verificar la información contra una fuente confiable (e.g., libros,
manuales, informes técnicos).

2. Diseñe y simule un circuito eléctrico a modo de sistema de control de lazo
cerrado, capaz de mantener la temperatura dentro de un rango 36°C y 37,5°C.

3. Diseñe y simule un circuito eléctrico que mida el peso y lo muestre mediante
una pantalla de cristal líquido (LCD), o bien, mediante displays 7-segmentos.


Parte B 

1. Construya un modelo de incubadora neonatal a escala que cumpla con las
siguientes especificaciones.

a. La cubierta debe ser de un material que garantice visibilidad hacia el interior
de la incubadora, reducir la pérdida de temperatura e incluir un mecanismo
de apertura y cierre. Asimismo, sus dimensiones deben ser de,
aproximadamente, 90 cm × 40 cm × 45 cm.

b. La temperatura del interior de la incubadora debe ser regulada mediante
convección, para lo cual se debe contar con un ventilador pequeño y un
elemento resistivo que proporcione calor suficiente para mantener el valor
de esta variable entre 36°C y 37,5°C. Para esto, utilice el circuito diseñado
previamente (parte A). El sistema debe incluir un panel de 3 LEDs para
mostrar si la temperatura está o no dentro del rango especificado (i.e., debe
encender un LED verde si la temperatura está dentro del rango, mientras
que, si ésta se encuentra por encima o por debajo, encenderá entonces,
para cada caso un LED rojo).

c. La incubadora debe contar con una galga o sensor de fuerza que permita
estimar el peso del recién nacido, el cual deberá mostrarse mediante una
pantalla LCD, o bien, mediante displays 7-segmentos.
d. Calcule el costo total del sistema desarrollado especificando también el
costo parcial de cada una de las partes.

3. Calcule el costo total del sistema desarrollado especificando también el costo
parcial de cada una de las partes. Compare esto con soluciones comerciales
ofrecidas por proveedores como, por ejemplo, Dräguer, Instrumentalia S.A.S. y
LEEX Medical.

Parte C
Preguntas de Discusión

• Pregunta 1: ¿Qué otras variables (y por qué) además de las aquí
mencionadas son críticas en el monitoreo neonatal?

• Pregunta 2: ¿Qué haría falta para convertir el sistema desarrollado en una
incubadora neonatal real?
• Pregunta 3: ¿Qué semejanzas hay entre una incubadora neonatal y una
servo-cuna?

Conclusión 

Referencias


