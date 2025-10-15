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

-----------------------------------------------------------------------------------------------------------------------------------------------------------

Queremos un programa “Boletín de Notas” que pida al usuario:

una cadena (su nombre) y un entero (nota del examen).

Con valores predeterminados (nota_parcial, practicas, peso_examen) el programa debe:

calcular total y promedio_base con enteros (suma, resta, multiplicación y división),

mostrar entero, doble, carácter y cadena,

realizar una suma en doble precisión (promedio_base + bono),

clasificar la nota en A/B/C con ramas (beq/blt/bgt),

utilizar mfhi/mflo tras div/mult,

definir dos funciones con jal (NuevaLinea y Operacion(a0,a1,a2)),

usar sw/move y un bloque de pila que guarda dos registros en dos pasos.

Estructura de archivos/segmentos

.data: variables, cadenas, buffers y constantes.

.text: código ejecutable.

Etiquetas principales: main, NuevaLinea, Operacion.

Comentarios con referencias [a]…[j] junto a cada bloque para que el profe vea que cumples el enunciado.

Asignación de registros (sugerida)

$s0: nota_examen (persistente).

$s1: total (opcional).

$s2: promedio_base (persistente).

$t0..$t9: temporales (nota_parcial, practicas, peso_examen, productos, etc.).

FPU: $f2 (double de promedio), $f4 (bono), $f6 (resultado double).

Convención llamadas: argumentos en $a0,$a1,$a2; retorno en $v0; guardar $ra en la pila en cada jal.

Sección .data (qué declarar y por qué)

Variables por defecto [h]:

nota_parcial .word 60

practicas .word 20

peso_examen .word 2

Constante double [d]: bono .double 0.5.

Buffer nombre [a]: buf_nombre .space 64.

Resultados (para sw) [i]: res_total, res_resta_demo, res_promedio_base, res_resto_div .word, res_letra .byte.

Mensajes (cadenas) [c]: textos para pedir/mostrar datos.

c_nl .byte 10 (salto de línea para NuevaLinea).

Sección .text – plan de construcción por pasos

Paso 1 – E/S básica [a][c]

Imprime msj_pide_nombre (syscall 4).

Lee cadena a buf_nombre (syscall 8, a0=dir, a1=tam).

Imprime msj_pide_nota (syscall 4).

Lee entero (syscall 5) y mueve a $s0 con move [i].

Paso 2 – Cargar valores predeterminados [h]

lw $t0, nota_parcial

lw $t1, practicas

lw $t2, peso_examen

Paso 3 – Función aritmética genérica [g][f][e][b]

Diseña Operacion(a0,a1,a2)->v0 con beq en modo “switch”:

a2=0 suma, 1 resta, 2 mul, 3 div.

Para mul: mult a0,a1 → mflo v0 [e].

Para div: div a0,a1 → mflo v0 (cociente) y opcional mfhi (resto) [e].

Protocolo de pila: en prólogo guarda $ra (y algún callee-save si quieres) con addiu sp,-N ; sw. En epílogo restaura y jr $ra.

Uso: desde main, prepara a0=nota_examen ($s0), a1=peso_examen ($t2), a2=2 (mul).

Guarda $ra en pila, jal Operacion, restaura $ra.

Copia $v0 → $t3 (producto) con move [i].

Paso 4 – Aritmética entera completa [b][i]

total = t0 + t1 + t3 con addu. Guarda en res_total (sw).

resta_demo = total - 10 con addiu t5, t4, -10. Guarda en res_resta_demo.

promedio_base = total / 3 con div t4,3 → mflo s2, mfhi t7.

Guarda s2 en res_promedio_base y t7 en res_resto_div [e][i].

Paso 5 – Cálculo en doble precisión [d]

mtc1 s2, f0 (mueve entero a FPU).

cvt.d.w f2, f0 (entero→double).

l.d f4, bono y add.d f6, f2, f4.

f6 queda como promedio con bono para imprimir luego.

Paso 6 – Clasificación por letra [f]

Compara s2 con 85 y 70 usando ramas:

blt s2,85 -> menor85 (si no, letra='A').

blt s2,70 -> ponerC (si no, letra='B').

Finalmente 'C'.

Guarda el carácter en res_letra con sb.

Extra: beq s2,100 -> NuevaLinea para demostrar otro salto condicional.

Paso 7 – #TRATAMIENTO DE PILA [j]

Guardar en dos pasos separados:

addiu sp,-4 ; sw t0,0(sp)

addiu sp,-4 ; sw t1,0(sp)

Haz una jal Operacion cualquiera mientras la pila está “ocupada”.

Restaurar en orden inverso:

lw t1,0(sp) ; addiu sp,4

lw t0,0(sp) ; addiu sp,4

Este bloque debe estar claramente comentado como #TRATAMIENTO DE PILA.

Paso 8 – Impresiones finales [c]

Llama NuevaLinea (con pila correcta) [g].

Imprime:

“Alumno: ” + buf_nombre (dos syscall 4).

“Total: ” + res_total (syscall 1).

“Resta: ” + res_resta_demo (syscall 1).

“Promedio base: ” + res_promedio_base (syscall 1) + “Resto: ” + res_resto_div (syscall 1).

“Promedio con bono: ” + f6 (mov.d f12,f6 ; syscall 3).

“Letra final: ” + res_letra (syscall 11).

Mensaje de cierre y syscall 10 (exit).

Contrato de funciones (documentación en ensamblador)

NuevaLinea

Entrada: ninguna.

Efecto: imprime '\n' con syscall 11.

Convención: guarda y restaura $ra en la pila.

Operacion(a0,a1,a2) → v0

a2=0 suma, 1 resta, 2 mul, 3 div.

Mul/Div usan LO/HI con mflo/mfhi.

beq se usa como switch (requisito de ramas).

Guarda $ra (y, si se desea, un s*) en la pila; restaura y retorna con jr $ra.

Plantillas de comentarios para pegar en tu código

# (a) Lectura de nombre y nota

# (b) Aritmética entera: + - * /

# (c) Salida: string, int, double, char

# (d) Doble precisión: add.d + print_double

# (e) HI/LO: mfhi/mflo tras div/mult

# (f) Ramas: beq, blt, bgt

# (g) Funciones con jal

# (h) Valores predeterminados

# (i) sw / move

# (j) #TRATAMIENTO DE PILA

Checklist de verificación (antes de probar)

Ejecuta paso a paso y valida que:

buf_nombre contiene la cadena leída y se imprime completa [a][c].

res_total, res_promedio_base, res_resto_div cambian tras div y mfhi/mflo [e].

f6 tiene el double correcto (observa FPU en MARS) [d].

res_letra cambia según ramas y umbrales [f].

La pila vuelve a su valor inicial tras #TRATAMIENTO DE PILA (mira $sp) [j].

Guía rápida de pantallazos (punto 3 del enunciado)

Pantallazo 1 (d): justo después de calcular add.d f6,f2,f4 e imprimir “Promedio con bono”.

Pantallazo 2 (f): después de fijar res_letra (A/B/C) e imprimirlo.

Pantallazo 3 (g): al usar Operacion (por ejemplo, el MUL inicial o la suma demo del bloque de pila).

Pantallazo 4 (j): tras guardar/restaurar en #TRATAMIENTO DE PILA (muestra $sp y registros).

Notas de implementación (MARS 4.5)

Para blt/bgt puedes usar las pseudo-instrucciones de MARS sin problema.

Para print_double: mueve el resultado a $f12/$f13 con mov.d $f12,$f6 antes del syscall 3.

Alinea después de res_letra con .align 2 para que las cargas de word no fallen.

Mantén siempre la convención: guardar $ra antes de cada jal y restaurar después.
