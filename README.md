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


$$

u(t) = K_c\left[e(t) + \frac{1}{T_i}\int_0^t e(\tau)\,d\tau\right], \qquad e(t) = 36{,}75 - T(t)

$$

Los valores que usamos son Kc = 0,294 por cada °C, es decir, 29,4 % de potencia por cada grado de error y Ti = 300 s, con una muestra cada segundo. Se escogieron por simulación, buscando que la temperatura entrara al rango sin pasarse de 37,5 °C.

Un detalle importante es que mientras el calefactor está al 100 %, el integrador sigue acumulando error y cuando la temperatura llega a la temperatura deseada esa acumulación hace que se pase de largo. Para evitarlo se agregó un anti-windup por cálculo inverso, con constante Tt = 150 s, que va descargando el integrador mientras la salida está saturada [10]. La potencia se entrega con PWM, el MOSFET enciende y apaga el calefactor cerca de mil veces por segundo, y la potencia promedio depende de qué fracción del tiempo está encendido.

Seguridad e indicadores
Si la temperatura pasa de 38,5 °C, el controlador apaga el calefactor y reinicia el integrador. Si el NTC se abre o se pone en corto, también se apaga el calefactor y se encienden los dos LEDs rojos. El LED verde indica que la temperatura está dentro de 36 °C y 37,5 °C, y los rojos indican que está por debajo o por encima. Los LEDs tienen una histéresis de 0,1 °C para que no parpadeen cuando la temperatura está justo en el borde.

Simulación del lazo cerrado
Proteus no tiene un modelo térmico de la cabina, así que para ver el lazo completo corrimos el mismo código del microcontrolador junto con un modelo térmico de la cabina. Ese modelo tiene varias partes (calefactor, aire, paredes y sensor) y sus valores son estimados. Simulamos un ambiente de 16 °C, un calefactor de 28,8 W y la tapa abierta durante 30 segundos en el minuto 150. La siguiente imagen muestra la temperatura y la potencia, y la tabla resume los resultados.


<img width="765" height="681" alt="image" src="https://github.com/user-attachments/assets/74e74df0-a2de-4941-afc3-628e48a3afd6" />



<img width="930" height="172" alt="image" src="https://github.com/user-attachments/assets/d28d6147-21bf-4042-ae5a-784ba0ac9570" />



<img width="828" height="582" alt="image" src="https://github.com/user-attachments/assets/41efa431-baff-4705-96fe-a1f30889f20b" />



<img width="1568" height="711" alt="image" src="https://github.com/user-attachments/assets/c73d7fd1-9580-41b1-9b91-03377d371c5e" />



<img width="1568" height="653" alt="image" src="https://github.com/user-attachments/assets/e84e5ee5-cd38-40d4-87ba-f5e460b35f42" />



<img width="1568" height="592" alt="image" src="https://github.com/user-attachments/assets/35c21f24-8c57-4257-bdcd-472b82845c8b" />


![Uploading image.png…]()

En la gráfica se ve que el calefactor trabaja al 100 % hasta que la temperatura se acerca a lo deseado, y después baja la potencia solo hasta quedarse en un 74 %, sin pasarse del rango. Cuando se abre la tapa la temperatura cae, el calefactor sube otra vez al máximo y el sistema se recupera. Estos resultados dependen del modelo estimado, por eso en la Parte B se comparan con la prueba real.
Simulación del circuito en Proteus
El circuito se armó en Proteus con el ATmega328P cargado con el programa PI, el NTC con su divisor, el MOSFET IRLZ44N, el calefactor de 5 Ω con su diodo de protección, el ventilador con el BC337, los tres LEDs y un terminal virtual para ver los datos.

3. Diseñe y simule un circuito eléctrico que mida el peso y lo muestre mediante
una pantalla de cristal líquido (LCD), o bien, mediante displays 7-segmentos.

El objetivo de este circuito es medir el peso del bebé, hasta 5 kg, y mostrarlo en una pantalla. Pesar al bebé sin sacarlo de la incubadora es una función que ya traen equipos comerciales y algunos prototipos de bajo costo [8], [12]. Para este punto se diseñó el circuito de medición y se simuló en Proteus con una pantalla LCD de 16×2, como pide la guía. En el prototipo físico la pantalla es una OLED, que muestra la misma información.
La celda de carga es un sensor que tiene cuatro galgas extensiométricas conectadas en un puente de Wheatstone. Cuando se le pone peso, el puente se desbalancea y entrega una diferencia de voltaje muy pequeña. Con una alimentación de 5 V y una sensibilidad de 1 mV/V, que fue la que supusimos para el diseño, la celda entrega unos 5 mV a plena carga. Esa señal es demasiado pequeña para leerla directamente con el conversor del microcontrolador, así que primero hay que amplificarla. Para la simulación la celda se representó con un puente de resistencias en el que un potenciómetro hace de carga: al moverlo, el puente se desbalancea igual que lo haría el peso.
La siguiente tabla muestra cómo quedó armada la cadena de medición.


<img width="1216" height="372" alt="image" src="https://github.com/user-attachments/assets/47e2fb52-a80d-4aaa-a590-89fa93e440c9" />

Puente y amplificador
El puente tiene dos ramas. La izquierda son dos resistencias de 1 kΩ y da una referencia fija de 2,5 V. La derecha tiene dos resistencias de 998 Ω y el potenciómetro RV1 de 4 Ω, cuyo cursor da un voltaje que cambia con el peso. Con el cursor en 50 % el puente está balanceado, y eso corresponde a 0 g. Al llevarlo hacia 0 % el puente se desbalancea hasta 5 mV, que corresponden a 5000 g.

La diferencia entre las dos ramas entra a un amplificador de instrumentación hecho con tres amplificadores operacionales OP07, que tienen un voltaje de offset muy bajo y por eso sirven para señales pequeñas de corriente continua. Los dos primeros (U3 y U4) amplifican la diferencia, y su ganancia se ajusta con la resistencia R15 de 510 Ω. El tercero (U5) es un restador que pasa esa diferencia a un voltaje respecto a tierra y le suma la referencia. La salida queda así:

$$

V_{out} = G\,(V_b - V_a) + V_{ref}, \qquad G = \left(1 + \frac{2\cdot 10\,\mathrm{k\Omega}}{510\,\Omega}\right)\cdot\frac{200\,\mathrm{k\Omega}}{10\,\mathrm{k\Omega}} \approx 40{,}2 \cdot 20 \approx 804

$$

La referencia sale de un divisor de voltaje con R20 y R23, y vale 5 V · 100 / (2400 + 100) = 0,2 V. Se suma a la salida para que 0 g no sea justo 0 V. Con los 5 mV de plena carga la salida sube 5 mV · 804 ≈ 4,02 V, así que va de 0,2 V a 4,22 V, dentro del rango de 0 a 5 V del conversor. Los OP07 se alimentan con +12 V y −12 V.

Conversión y pantalla
La salida pasa por un filtro RC (R22 de 10 kΩ y C5 de 100 nF) que corta el ruido por encima de unos 160 Hz y llega al canal ADC0 del ATmega328P. Ese conversor es de 10 bits y usa 5 V de referencia, o sea que cada cuenta vale 4,88 mV. Como cada gramo cambia la salida en 0,804 mV, el programa usa una constante k de 0,1648 cuentas por gramo, y N0 = 41 cuentas para el cero, que son los 0,2 V de la referencia. El peso se calcula con el promedio N de 16 lecturas:

$$
m = \frac{N - N_0}{k}, \qquad k = 0{,}1648\ \text{cuentas/g}, \quad N_0 = 41

$$

Con esta constante cada cuenta equivale a unos 6 g. Ese es el límite de un conversor de 10 bits, y en el prototipo no aplica porque el HX711 convierte con 24 bits.
El programa también tiene un botón de tara (BTN2), que toma el valor del momento como nuevo cero. Muestra 0 cuando el peso queda dentro de ±3 g, para que el número no se mueva por el ruido, y escribe sobrecarga si se pasa de 5000 g. La pantalla LM016L se maneja en modo de 4 bits, la primera línea muestra el peso en gramos y la segunda las cuentas del ADC y el voltaje, y se actualiza cuatro veces por segundo.

Valores esperados
La siguiente tabla muestra lo que debería pasar con cada peso, calculado con una simulación del circuito y con el programa. Para probarlo en Proteus se mueve RV1 al porcentaje indicado.


<img width="930" height="315" alt="image" src="https://github.com/user-attachments/assets/34a3c872-04a8-4ce0-adcf-df74cd9cd4a8" />


Las pequeñas diferencias, de hasta 4 g, se deben a que el conversor entrega cuentas enteras.

Simulación del circuito en Proteus
El circuito se armó en Proteus con un segundo ATmega328P (U6) cargado con el programa de la balanza, la pantalla LM016L, el botón de tara, el puente con RV1 y los tres amplificadores OP07 con sus alimentaciones de +12 V y −12 V.

En el prototipo el amplificador y el conversor son un solo chip, el HX711, que tiene un conversor de 24 bits. Este módulo se usa mucho como amplificador de bajo costo para celdas de carga, y en un estudio de fuerza muscular sus mediciones se parecieron a las de un acondicionador comercial y a las de un dinamómetro, con correlaciones entre 0,95 y 0,99 [14]. El HX711 lo lee la ESP32-S3, que muestra el peso en la pantalla OLED. Como cada celda real es distinta, en el prototipo no se usa la sensibilidad supuesta, sino una calibración con masas conocidas que se explica en la Parte B.


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


La guía pide construir una incubadora a escala con una cubierta transparente que pierda poco calor y se pueda abrir y cerrar, un ventilador y una resistencia para calentar por convección, un panel de tres LEDs y un sensor de fuerza con pantalla para el peso. La siguiente tabla resume cada requisito y cómo lo cumplimos.


<img width="1018" height="465" alt="image" src="https://github.com/user-attachments/assets/de41c186-f4ee-4b8b-860b-b90670b0bb94" />


Cabina
La cabina es una caja de acrílico de doble pared, una piel interior de 3 mm, una cámara de 10 mm de aire y una piel exterior de 3 mm. El aire encerrado ayuda a que se pierda menos calor, y el acrílico deja ver el interior en todo momento. Las 32 piezas se mandaron a cortar a medida. El interior queda de 45 × 21 × 23 cm y el exterior de 48,2 × 24,2 cm de planta.

Se armó en este orden, primero la caja interior, pegada por las esquinas; luego las tiras separadoras verticales y horizontales, que mantienen los 10 mm de aire; y al final las pieles exteriores, las largas y las de los extremos. Para pegar se usó cloruro de metileno y después se dejó secar 24 horas.

La tapa es un sándwich de 1,6 cm, una lámina grande abajo, un marco de cuatro tiras en el medio y una lámina más pequeña arriba. La lámina grande cubre todo el borde de la cabina y aprieta un burlete de espuma, que sella la unión. La tapa se abre con dos bisagras en el lado trasero y una perilla en el frente.

La cabina va sobre una base de icopor de 40 mm. La base tiene un agujeros de 10 mm.  para los cables de potencia (calefactor y ventilador) y  para los cables de sensores (NTC y celda de carga). 

Calefacción y ventilación
El calor lo producen dos resistencias de 10 Ω y 20 W en paralelo, que dan 5 Ω y 28,8 W a 12 V, es decir, una corriente de 2,4 A. Se usaron dos de 10 Ω porque en la tienda local solo había resistencias de potencia de ese valor. Cada una trabaja a 14,4 W, el 72 % de su potencia nominal. Con tres en paralelo la corriente sería de 3,6 A, y eso pasa de los 3 A de la fuente. Las resistencias se atornillan sobre una placa de aluminio de 2 mm que reparte el calor, y la placa queda elevada 10 mm del piso con separadores.

El ventilador  de 12 V  sopla sobre el calefactor y reparte el aire caliente por toda la cabina por convección y el ventilador está siempre encendido. El sensor de temperatura es un termistor NTC de 10 kΩ (β = 3950) soldado a un cable de dos hilos, y se fija dentro de la cabina.

Electrónica y alimentación
Se usaron dos fuentes de 12 V y 3 A. La fuente 1 alimenta solo el calefactor, para que su corriente no afecte a la electrónica. La fuente 2 alimenta el ventilador y, a través de un reductor LM2596 ajustado a 5 V, la ESP32-S3. Los LEDs, el divisor del NTC, el HX711 y la OLED trabajan a 3,3 V.

Es el mismo circuito de la temperatura con la ESP32-S3 en lugar del ATmega328P de Proteus. El NTC con su resistencia de 10 kΩ entra al conversor analógico de la ESP32, y una salida PWM maneja la compuerta de un MOSFET de nivel lógico IRLB8721 (con una resistencia de 100 Ω en serie y otra de 10 kΩ a tierra) que conecta y desconecta el calefactor. En paralelo con el calefactor hay un diodo 1N4007 de protección. Entre +12 V y tierra, junto al MOSFET, hay un condensador de 2200 µF que estabiliza la línea cuando el PWM enciende y apaga el calefactor. El ventilador lo maneja un transistor BC337 con su diodo.

Como protecciones, la línea de 12 V del calefactor lleva un fusible de 3 A y cada fuente tiene su interruptor y el corte por sobretemperatura (38,5 °C) y por falla del sensor que ya hace el programa.

Los circuitos  van en una caja plástica o de carton,  frente van la OLED, los LEDs, los interruptores y el botón de tara. Los cables de la cabina entran por dos prensaestopas. La siguiente tabla muestra las conexiones de la ESP32-S3.


<img width="802" height="312" alt="image" src="https://github.com/user-attachments/assets/f729ab47-66a9-427a-84ba-55533ae14cbb" />


Panel de LEDs
El panel tiene tres LEDs de 5 mm con resistencias de 220 Ω. El verde se enciende cuando la temperatura está entre 36 °C y 37,5 °C, el rojo de abajo cuando está por debajo de 36 °C y el rojo de arriba cuando está por encima de 37,5 °C. Si el sensor falla se encienden los dos rojos, y así se nota que algo anda mal. Los LEDs tienen una histeresis de 0,1 °C para que no parpadeen cuando la temperatura está justo en el borde.

Base y celda de carga
Para pesar se armó una unidad con dos placas, 1 de acrílico y otra de madera de acrílico de 25 × 15 cm. La placa de abajo es de madera y  se apoya en la base de icopor y la de arriba es el acrílico y  es la plataforma donde va el peso. Entre las dos queda la celda de carga de 5 kg: un extremo se atornilla a la placa de abajo y el otro a la plataforma, y cada uno lleva separadores de 8 mm. Así, cuando algo pesa sobre la plataforma, la celda se flexiona un poco y el HX711 convierte esa flexión en un número. Los separadores tienen que ser rígidos, porque con goma o espuma la lectura se mueve sola. Sobre la plataforma va un colchón de espuma.

La unidad se probó primero fuera de la cabina y después se instaló adentro, centrada, a unos 10 cm del extremo del calefactor y a más de 5 cm de él. La plataforma y el colchón quedan a más de 1 cm de las paredes, para que nada se apoye en la cabina. El cable de la celda sale por el agujero hecho en un extremo de la cabina. 

La celda se conecta al HX711 y este a la ESP32-S3, que muestra la temperatura y el peso en una pantalla OLED. El botón tiene dos funciones, una pulsación corta con la plataforma vacía hace la tara, y mantenerlo 3 segundos con una pesa patrón encima calibra. La tara y la calibración quedan guardadas en la memoria, así que siguen ahí después de apagar el equipo. Como cada celda es distinta, se calibra con la pesa más pesada que se tenga, porque una pesa muy liviana da una calibración menos precisa. 



2. Calcule el costo total del sistema desarrollado especificando también el costo
parcial de cada una de las partes. Compare esto con soluciones comerciales
ofrecidas por proveedores como, por ejemplo, Dräguer, Instrumentalia S.A.S. y
LEEX Medical.

Para el costo se agruparon los gastos por las partes principales del montaje, porque poner cada componente por separado haría la tabla muy larga. 


<img width="917" height="340" alt="image" src="https://github.com/user-attachments/assets/d77a16d2-05aa-4c40-bdaa-941dc023434d" />

Comparación con soluciones comerciales

La siguiente tabla compara el prototipo con tres incubadoras comerciales, la Isolette 8000 de Dräger, la YP2000 de Instrumentalia S.A.S. y la B3 de LEEX Medical. Los datos salen de las fichas que publican los fabricantes o sus distribuidores. Lo que no aparece en la ficha consultada se deja indicado así, sin suponerlo. Las especificaciones de la Isolette 8000 se tomaron de la ficha de un distribuidor [17], y la información de su regulación PID y su modo piel, de la página de Dräger [12].

<img width="1608" height="626" alt="image" src="https://github.com/user-attachments/assets/ce03cb6e-e54a-4ce0-8f77-c4503a225c9c" />

Lo primero que se nota es el alcance. Los equipos comerciales trabajan en modo aire y en modo piel, miden más variables y tienen alarmas más completas. La Isolette 8000 y la YP2000 pueden regular el calor a partir de la temperatura de la piel del bebé, algo que el prototipo no hace porque solo mide el aire de la cabina [12], [15], [17]. Además controlan humedad y oxígeno, y el equipo de LEEX ofrece incluso el cambio de posición del colchón [16]. En lo que sí se parece el prototipo es en la estructura básica, una cabina cerrada de doble pared, un calefactor con control PI o PID, un ventilador y el pesaje del bebé sin sacarlo de la incubadora.

Las limitaciones del prototipo frente a estos equipos son varias. No tiene modo piel ni control de humedad u oxígeno, las alarmas son solo visuales con los LEDs, y la báscula se usa únicamente hasta unos pocos kg de la celda de 5kg, mientras que la de la YP2000 declara un rango de 100 a 8000 g con resolución de 1 g [15]. Tampoco tiene la certificación que exige la norma para un equipo médico [11], por lo que no se puede usar con un bebé real. Se hizo para entender cómo funcionan el lazo de control y la medición de peso, no para reemplazar un equipo. 

En el precio es donde más se separan. El prototipo costó unos $410.000 COP y la YP2000 se vende en $53.391.350 COP con impuestos [15], es decir, unas 130 veces más. Para LEEX, un distribuidor colombiano vende un B3 de Comen, con la misma descripción técnica que la ficha de LEEX, en $30.710.000 COP sin IVA [21]. De Dräger no hay precio público de un equipo nuevo y solo se encontraron ofertas de equipos usados o reacondicionados, desde unos USD 1.600 hasta USD 3.000 [18], [19], con una oferta de €4.500 [20]. Esa diferencia no se puede leer como un sobrecosto, porque en un equipo comercial el valor incluye la certificación, el control por piel, la humedad, el oxígeno, las alarmas y el soporte técnico. La garantía de la YP2000 y la del equipo de LEEX es de un año [15], [16].


Parte C

Preguntas de Discusión y Resultados

Para ver si el control funciona se hizo una prueba de calentamiento en el prototipo, con la tapa cerrada, y se comparó con la simulación de la Parte A. La prueba se hizo en una casa, con una temperatura ambiente de unos 23 °C, y la temperatura de la cabina se midió con la termocupla de un multímetro. No se llevó un registro escrito cada cierto tiempo, así que los valores de la prueba real son aproximados.


<img width="900" height="1600" alt="image" src="https://github.com/user-attachments/assets/3a3017c7-74be-4b43-a440-ff0702fdb3c2" />



<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/d265eb12-1403-42b2-a6a4-3ec68a138c7f" />



<img width="662" height="206" alt="image" src="https://github.com/user-attachments/assets/d8808c98-0325-4c76-9625-bb50f82a9679" />


La prueba confirma que el calefactor y el control llevan la cabina hasta el rango de 36 a 37,5 °C, pero mucho más despacio de lo que dijo la simulación. Eso llama la atención porque la prueba empezó 7 °C más caliente que la simulación y aun así tardó más de dos veces y media el tiempo. Lo que sí se parece es el ritmo en la mitad del calentamiento: de 28 a 31 °C la simulación tardó 8 minutos y el prototipo unos 10. La diferencia grande está al principio, cuando la simulación ya iba en 33,3 °C a los 30 minutos y el prototipo apenas en 27 o 28 °C, y en que los últimos grados fueron los más lentos.

Lo más probable es que el modelo térmico de la simulación sea optimista, porque sus valores fueron estimados y no medidos. El prototipo real tiene más masa que calentar, ya que además del aire se calientan las paredes de acrílico de doble pared, la base de icopor y la placa. También pierde calor por lugares que el modelo no incluye, como las bisagras, los agujeros de los cables y el borde de la tapa. Además, la potencia que llega al calefactor puede ser menor que los 28,8 W calculados, por la caída de voltaje en los cables, el fusible y el MOSFET. A medida que la cabina se calienta, la diferencia con el ambiente crece y se pierde más calor, por eso los últimos grados tardan tanto. Estas causas son explicaciones probables.

Para medir el error de la báscula se usan masas conocidas, que hacen de patrón. Primero se hace la tara con la plataforma vacía y se calibra con la masa más pesada. Después se pone cada una de las otras masas, por ejemplo de 100 g, 500 g, 1 kg y 2 kg, centradas sobre la plataforma, y se anota la lectura de la pantalla. Cada masa se mide varias veces para sacar un promedio. En cada caso se calcula el error absoluto, que es la lectura menos la masa patrón, y el error relativo, que es ese error dividido entre la masa patrón y multiplicado por 100. La prueba se debe repetir con la masa en las esquinas de la plataforma, para ver si la lectura cambia con la posición, y con la cabina caliente, para ver si la temperatura afecta la celda.

En lo que el prototipo hace, que es calentar por convección con un control PI, mantener la cabina entre 36 y 37,5 °C y pesar, cumple la misma función básica que un equipo comercial. Lo que no tiene son los controles y las validaciones que hacen confiable a un equipo real, modo piel, humedad, oxígeno, alarmas completas y certificación. Por ejemplo, la YP2000 declara que la variación de su sensor de piel es menor de 0,5 °C y que su báscula tiene un error de ±1 % [15], y el prototipo todavía no tiene una medición equivalente. La diferencia de precio, de unos $410.000 contra más de $53 millones, se explica en buena parte por todo eso. El prototipo sirve para entender cómo funciona el lazo de control y la medición, pero no como reemplazo de un equipo.



• Pregunta 1: ¿Qué otras variables (y por qué) además de las aquí mencionadas son críticas en el monitoreo neonatal?

Aparte de la Temperatura y el peso del neonato, en una incubadora hospitalaria también es necesario tener en cuenta la humedad relativa del ambiente dentro de la incubadora, esto debido a la humedad tan elevada que expenden de la piel, de la mano con esta, temperatura de la piel, para esta se suelen utilizar parches para medir la temperatura del bebe, en lugar de únicamente la ambiente, también un control de concentración de oxigeno, importante para casos muy comunes asociados a la respiración del bebe, donde se requieren concentraciones variadas dependiendo la deficiencia del proceso y medidos del flujo de aire para evitar estancamientos de calor y falsas lecturas por parte de los termistores.

• Pregunta 2: ¿Qué haría falta para convertir el sistema desarrollado en una incubadora neonatal real?
Las variables a medir y controlar establecidas en el punto anterior, una calibracion adecuada con pesos estandarizados y no medidos a ultimo momento, ademas de patrones confiables de peso, y un armado estable y fijado ademas de un  caparazon que recubra la zona del circuito y asi evitar la facilidad de interaccion entre el operador y el circuito, aislar completamente el circuito del calentamiento de la zona del neonato sin que el aire deje de circular y finalmente pasar oficialmente las pruebas de calidad estandarizadas.

• Pregunta 3: ¿Qué semejanzas hay entre una incubadora neonatal y una servo-cuna?
Lo que cambia usualmente es el método de calentamiento son las formas de acceso el bebe, pues una esta cerrada y la servocuna no, ambos manejan un metodo calefactor para mantener la temperatura deseada, diferenciándose en  el metodo, y por ende, aunque cumplen la misma función, el porque es necesario varia el  uso de una u otra. 


Conclusión 

Mantener la temperatura de un recién nacido y saber cuánto pesa son dos tareas que una incubadora no puede hacer mal, y en este laboratorio se quiso ver qué hace falta para lograrlas en un modelo a escala. El resultado fue una incubadora a escala que lleva la cabina al rango de 36 a 37,5 °C con un control PI, y que se protege con un corte del calefactor a 38,5 °C y con tres LEDs que avisan si la temperatura está por debajo, dentro o por encima del rango. En la prueba real la cabina tardó 1 h 46 min en llegar a 36 °C, mucho más que los 42 min de la simulación, porque el modelo térmico se estimó y resultó optimista. Esto quiere decir que el control funciona, pero que la simulación sirve como guía y no como predicción del tiempo real. Para el peso se diseñó y simuló un circuito con amplificador de instrumentación y se armó una unidad de pesaje con celda de carga de 5 kg y HX711, calibrada con una masa patrón, cuyo error se evalúa con masas conocidas. Todo el sistema costó unos $410.000 COP, cerca de 130 veces menos que una incubadora comercial como la YP2000, pero sin modo piel, humedad, oxígeno ni certificación, que son lo que haría confiable a un equipo real. Estos resultados muestran que un controlador sencillo y sensores de bajo costo alcanzan para entender cómo trabaja un equipo médico, y dejan ver qué le falta a un prototipo antes de pensar en usarlo con un paciente. El siguiente paso sería repetir la prueba de calentamiento guardando la temperatura cada minuto para medir cuánto se pasa y qué tan estable queda, agregar el modo piel y el control de humedad, y validar la báscula con un patrón certificado.

Referencias

[1] C. G. K. Tran et al., “Designing a low-cost multifunctional infant incubator,” J. Lab. Autom., vol. 19, no. 3, pp. 332–337, jun. 2014. doi: 10.1177/2211068214530391. https://doi.org/10.1177/2211068214530391
[2] L. Restrepo-Pérez, N. Durango-Londoño, N. E. Gómez-Suárez, F. González-Ramírez y N. Rivera-Bonilla, “Prototipo de incubadora neonatal,” Rev. Ing. Biomed., vol. 1, no. 1, pp. 55–59, 2007. http://www.scielo.org.co/pdf/rinbi/v1n1/v1n1a12.pdf
[3] M. van Leeuwen et al., European Standards of Care for Newborn Health: Temperature management in newborn infants. EFCNI, nov. 2018. https://newborn-health-standards.org/standards/standards-english/care-procedures/temperature-management-in-newborn-infants/
[4] R. B. Knobel-Dail, “Role of effective thermoregulation in premature neonates,” Res. Rep. Neonatol., vol. 4, pp. 147–156, 2014. doi: 10.2147/RRN.S52377. https://doi.org/10.2147/RRN.S52377
[5] R. E. Black et al., “Global, regional, and national causes of child mortality in 2008: a systematic analysis,” Lancet, vol. 375, pp. 1969–1987, jun. 2010. doi: 10.1016/S0140-6736(10)60549-1. https://doi.org/10.1016/S0140-6736(10)60549-1
[6] E. A. Dunne, C. P. F. O’Donnell, B. Nakstad y L. K. McCarthy, “Thermoregulation for very preterm infants in the delivery room: a narrative review,” Pediatr. Res., vol. 95, no. 6, pp. 1448–1454, 2024. doi: 10.1038/s41390-023-02902-w. https://doi.org/10.1038/s41390-023-02902-w
[7] M. Kyokan, F. Rosa-Mangeret, M. Gani y R. E. Pfister, “Neonatal warming devices: what can be recommended for low-resource settings when skin-to-skin care is not feasible?,” Front. Pediatr., vol. 11, art. 1171258, 2023. doi: 10.3389/fped.2023.1171258. https://doi.org/10.3389/fped.2023.1171258
[8] R. Cuervo, M. A. Rodríguez-Lázaro, R. Farré, D. Gozal, G. Solana y J. Otero, “Low-cost and open-source neonatal incubator operated by an Arduino microcontroller,” HardwareX, vol. 15, art. e00457, sep. 2023. doi: 10.1016/j.ohx.2023.e00457. https://doi.org/10.1016/j.ohx.2023.e00457
[9] Z. Liu et al., “Design of a negative temperature coefficient temperature measurement system based on a resistance ratio model,” Sensors, vol. 24, no. 9, art. 2780, 2024. doi: 10.3390/s24092780. https://doi.org/10.3390/s24092780
[10] K. J. Åström y T. Hägglund, Advanced PID Control. Research Triangle Park, NC, EE. UU.: ISA, 2006.
[11] IEC 60601-2-19:2020, Medical electrical equipment – Part 2-19: Particular requirements for the basic safety and essential performance of infant incubators, ed. 3.0, International Electrotechnical Commission, Ginebra, 2020. https://webstore.iec.ch/en/publication/89860
[12] Drägerwerk AG & Co. KGaA, “Isolette 8000 plus,” ficha del producto. https://www.draeger.com/es_es/Products/isolette-8000-plus (fuente comercial, solo para la comparación de la Parte B).
[13] G. Noreiks, D. August, M. Lai y M. W. Davies, “Ceasing or gradually reducing incubator humidity after 7 days for extremely preterm infants: a randomised clinical trial,” Eur. J. Pediatr., vol. 184, no. 1, art. 66, 2024. doi: 10.1007/s00431-024-05893-w. https://doi.org/10.1007/s00431-024-05893-w
[14] J. Gaudet y G. Handrigan, “Assessing the validity and reliability of a low-cost microcontroller-based load cell amplifier for measuring lower limb and upper limb muscular force,” Sensors, vol. 20, no. 17, art. 4999, 2020. doi: 10.3390/s20174999. https://doi.org/10.3390/s20174999
[15] Instrumentalia S.A.S., “Incubadora Neonatal YP2000,” ficha del producto. https://instrumentalia.com.co/medicina/61297-incubadora-yp2000.html (consultado el 13 de octubre de 2026; fuente comercial, solo para la comparación de la Parte B).
[16] LEEX Medical, “B3 Infant Incubator,” ficha del producto. https://leexmedical.com/en/product/b3-infantincubator/ (consultado el 13 de octubre de 2026; fuente comercial, solo para la comparación de la Parte B).
[17] Soma Technology, Inc., “Dräger Isolette 8000,” especificaciones. https://www.somatechnology.com/incubators/drager-isolette-8000 (consultado el 13 de octubre de 2026; ficha de un distribuidor, solo para la comparación de la Parte B).
[18] Bimedis, “DRAGER Isolette 8000 for Sale,” listado de equipos usados y reacondicionados. https://bimedis.com/drager-isolette-8000-m29517 (consultado el 13 de octubre de 2026; solo para la comparación de la Parte B).
[19] First Medical International, “Dräger Isolette 8000 Incubator,” equipo reacondicionado. https://firstmedicalint.com/products/isolette-8000-incubator (consultado el 13 de octubre de 2026; solo para la comparación de la Parte B).
[20] DOTmed, “Used DRAEGER Isolette 8000plus Infant Incubator For Sale, Listing #4891306.” https://www.dotmed.com/listing/infant-incubator/draeger/isolette-8000plus/4891306 (consultado el 13 de octubre de 2026; solo para la comparación de la Parte B).
[21] IMEDS Colombia (Import Medical Supplies S.A.S.), “Incubadora neonatal B3,” tienda en línea. https://www.imedscolombia.com/tienda-on-line-1/incubadoras/ (consultado el 13 de octubre de 2026; precio sin IVA, solo para la comparación de la Parte B).
[22] N. Kumar, G. Akangire, B. Sullivan, K. Fairchild y V. Sampath, “Continuous vital sign analysis for predicting and preventing neonatal diseases in the twenty-first century: big data to the forefront,” Pediatr. Res., vol. 87, no. 2, pp. 210–220, 2020. doi: 10.1038/s41390-019-0527-0. https://doi.org/10.1038/s41390-019-0527-0

