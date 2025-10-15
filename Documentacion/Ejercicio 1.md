## Boletín de Notas MIPS

Desarrollo del problema.
El programa que haré esta en ensamblador MIPS tiene como objetivo simular un pequeño “Boletín de Notas”. La idea es que el usuario introduzca su nombre y una nota del examen, y el sistema calcule diferentes resultados, mostrando algunos en formato entero, otros en doble precisión y también caracteres.
Además, se busca practicar el uso de la pila, el paso de argumentos a funciones con jal, el uso de mfhi y mflo, y las instrucciones de salto condicional como beq, blt o bgt.

Para simplificar, algunas variables estarán definidas en la sección .data, como la nota parcial, las prácticas o el peso del examen. Solo la nota del usuario se pedirá por teclado. Con esa información, el programa hará operaciones básicas (sumas, restas, multiplicaciones y divisiones) y mostrará un promedio final que se ajustará con un pequeño bono en formato double. Al final, el sistema determinará una letra (A, B o C) en función de la nota obtenida.

Estructura del programa
Primero, en la sección .data, declararé todas las variables necesarias: cadenas para los mensajes, las notas fijas, el bono en double y las zonas de memoria donde guardaré los resultados. También reservaré un espacio para el nombre del alumno con .space.

En la sección .text comenzaré con la etiqueta main, donde se pedirá el nombre y la nota del alumno usando los syscalls de lectura de cadena y entero. Luego cargaré las variables por defecto y llamaré a una función Operacion (usando jal) que se encargará de realizar diferentes operaciones aritméticas según el parámetro que reciba. En este caso la usaré primero para multiplicar la nota por su peso.

Con los valores obtenidos, el programa calculará el total de puntos, una resta de prueba, y el promedio base. Para la división usaré div, guardando el cociente con mflo y el resto con mfhi, que después también se imprimirá. A continuación, convertiré el promedio base a formato doble (cvt.d.w) y le sumaré el bono con add.d para mostrar el resultado en coma flotante.

Después, vendrá la parte de clasificación, donde mediante comparaciones con blt y beq se asignará la letra correspondiente. En esa zona también se aprovecha para mostrar cómo funcionan los saltos condicionales.

Antes de imprimir todo, el programa ejecutará una sección llamada #TRATAMIENTO DE PILA, donde se guardan y restauran registros en dos pasos diferentes para cumplir el requisito del uso de pila. Dentro de esa misma parte haré una llamada a la función Operacion para demostrar cómo funciona cuando hay datos guardados en la pila.

Finalmente, se imprimen todos los resultados: el nombre del alumno, los valores enteros, el doble, el carácter y un mensaje final. Cada bloque estará comentado en el código con la letra correspondiente del enunciado (de la a hasta la j), para dejar claro qué punto se cumple en cada parte.

Funciones utilizadas
Se crean dos funciones con jal:

NuevaLinea, que solo imprime un salto de línea para mejorar la presentación en consola.

Operacion(a0, a1, a2), que realiza sumas, restas, multiplicaciones o divisiones dependiendo del valor del tercer argumento, usando beq como selector. En el caso de multiplicación y división se emplean los registros especiales LO y HI junto con mflo y mfhi.

Conclusión sobre el diseño
El programa combina casi todos los elementos básicos de MIPS: entrada y salida, operaciones aritméticas, funciones, saltos, pila y conversión de tipos. La idea no es hacer algo complejo, sino reunir en un mismo ejercicio las estructuras más comunes para dominar la sintaxis y el funcionamiento del simulador MARS.
De esta forma, el código final cumple todos los puntos que pide la práctica y es fácil de ejecutar y entender paso a paso.
