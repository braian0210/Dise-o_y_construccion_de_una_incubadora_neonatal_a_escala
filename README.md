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

Una incubadora neonatal es un equipo que mantiene al recién nacido en un microambiente controlado. Un bebé y sobre todo uno prematuro, pierde calor por cuatro vías, convección, conducción, radiación y evaporación. La incubadora actúa sobre varias de ellas al mismo tiempo ya que calienta el aire que rodea al bebé, lo hace circular, lo aísla de las corrientes del exterior con una cabina cerrada y en muchos casos aporta humedad para reducir la evaporación por la piel, ya que la humidificación influye en la pérdida de agua y ayuda a la termorregulación [13]. Además, la norma IEC 60601-2-19 define los requisitos de seguridad y de funcionamiento que debe cumplir este tipo de equipo [11].

Es importante no confundirla con la servo-cuna, la servo-cuna es una cama abierta que calienta por radiación, lo que da acceso total al bebé pero deja menos control sobre el ambiente, y además aumenta la pérdida de agua por la piel. La incubadora, en cambio, calienta por convección dentro de un espacio cerrado y se recomienda sobre todo para prematuros muy pequeños durante sus primeras semanas de vida [7].

La siguiente tabla resume las partes principales, la función de cada una y con qué la reemplazamos en el prototipo.


<img width="900" height="607" alt="image" src="https://github.com/user-attachments/assets/f22d975f-84a8-4cfe-bdac-c62387e45df4" />


En los equipos comerciales el control de temperatura puede hacerse de dos formas. En el modo aire, el sensor mide el aire de la cabina y el equipo lo mantiene en un valor fijo. En el modo piel o servo-control, se pega un sensor al abdomen del bebé y el controlador ajusta el calefactor para que sea la piel la que se mantenga en el valor deseado. Por ejemplo, la ficha de la Isolette 8000 plus de Dräger indica que su regulación usa un algoritmo PID y que tiene modo piel [12]. En nuestro modelo usamos el modo aire, porque no hay un bebé al cual medirle la piel y la guía pide mantener la cabina entre 36 °C y 37,5 °C. Ese rango se parece al que se recomienda para la temperatura del recién nacido, de 36,5 °C a 37,5 °C [6].

Modos de control de temperatura: ON/OFF y PI

En el control ON/OFF el calefactor solo tiene dos estados, encendido a plena potencia o apagado. Se enciende cuando la temperatura baja de un límite inferior y se apaga cuando sube de un límite superior. Esa diferencia entre los dos límites se llama histéresis, y sirve para que el calefactor no se encienda y apague a cada instante cuando la temperatura está justo en el borde. Es un control muy simple de construir, pero la temperatura nunca se queda quieta, sino que oscila todo el tiempo dentro de esa banda. 
En el control PI, que es el proporcional-integral, la potencia no es de todo o nada, sino que se calcula en cada instante a partir del error, que es la diferencia entre la temperatura que se quiere y la que se mide. Una parte de la potencia es proporcional al error y la otra es proporcional a cuánto error se ha acumulado en el tiempo:

$$
u(t) = K_p e(t) + K_i \int_0^t e(\tau)\,d\tau
$$

La parte integral es la que permite que el error final sea cero, porque sigue aumentando la potencia mientras la temperatura no llegue a la deseada [10]. Para entregar una potencia intermedia se usa modulación por ancho de pulso (PWM), que consiste en encender y apagar el calefactor muy rápido y variar la fracción del tiempo que permanece encendido. Así el calefactor trabaja a la potencia justa y la temperatura se estabiliza en lugar de oscilar. Este es el control que terminamos usando.
Por último, algunas incubadoras de bajo costo ya juntan varias de estas partes. La de Cuervo y colaboradores controla temperatura y humedad, mide el peso del bebé, tiene una cama inclinable y una alarma de temperatura independiente del control principal [8]. Esa alarma independiente se parece a nuestro corte por sobretemperatura, que apaga el calefactor aunque falle el controlador.


2. Diseñe y simule un circuito eléctrico a modo de sistema de control de lazo
cerrado, capaz de mantener la temperatura dentro de un rango 36°C y 37,5°C.

El objetivo de este circuito es mantener la temperatura de la cabina entre 36 °C y 37,5 °C en lazo cerrado. Eso quiere decir que el sistema mide la temperatura, la compara con la que se quiere y ajusta el calefactor, y repite esto todo el tiempo. Como temperatura deseada (consigna) escogimos 36,75 °C, que queda justo en la mitad del rango y deja el mismo margen hacia los dos lados.

La siguiente tabla muestra cómo quedó armado el lazo:


<img width="1051" height="336" alt="image" src="https://github.com/user-attachments/assets/d97680ab-a6fd-4c18-b0b5-979fac946517" />


Sensor de temperatura
El NTC y la resistencia fija forman un divisor de voltaje que se conecta al conversor analógico-digital del microcontrolador. Cuando sube la temperatura, la resistencia del NTC baja y el voltaje cambia. El programa promedia 16 lecturas para reducir el ruido, calcula la resistencia del NTC a partir del voltaje y la pasa a temperatura con la ecuación beta [9]:

$$

\frac{1}{T} = \frac{1}{T_0} + \frac{1}{\beta}\ln\frac{R}{R_0}

$$
donde T0 = 298,15 K (25 °C), R0 = 10 kΩ y β = 3950 K.

Controlador PI
En el controlador la entrada es la temperatura medida, la salida es la fracción de potencia que se le manda al calefactor, y el estado es el valor acumulado del integrador. El error es la consigna menos la temperatura medida, y la potencia se calcula así:

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


