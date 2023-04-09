QEMU-GDB-DASHBOARD Survival Kit

El dashboard se instala como .gdbinit en la carpeta raiz del usuario actual
descargarlo de : https://github.com/cyrus-and/gdb-dashboard

luego para que el DASHBOARD muestre por defecto los registros de la arquitectura ARM, hay que agregar a mano en una carpeta para que levante automaticamente esta configuracion el DASHBOARD:

en el ~/ del usaurio actual hacer:

$ mkdir .gdbinit.d
$ cd .gdbinit.d
$ echo set architecture arm
$ echo dashboard registers -style list "'r0 r1 r2 r3 r4 r5 r6 r7 r8 r9 r10 r11 r12 sp lr pc cpsr'" > init  

(ojo las comillas simples y dobles!)

Para utilizarlo con QEMU, luego de compilar nuestro programa, lanzar el QEMU con las opciones en linea d ecomando:

- en la linea de comnando del qemu agregar los switches -s y -S (start stopped y escucha de conexion por port 1234)
	esto es independiente del monitoreo por la opcoin server en el otro puerto que se elije para conectar por telnet
- en otra terminal iniciar el gdb usando el .gdbinit descargado del gihub de dashboard (ver arriba)
- en el gdb ejecutar: 


    si no se especifico el archivo de programa a depuerar en linea de comando:

    file <mi-objeto.o> 
si tiene tabla de simbolos, mejor
    target remote localhost:1234 
(es el puerto en que escucha el qemu)

	
al cargar el fuente del programa la configuracion de DASHBOARD nos va a mostrar automaticamente por defecto varias areas con informacion variada (registros, memoria, etc) esto es configurable, ver la documentacion de dashboard para eso.

- se pueden utilizan los comandos estandar de gdb de todas formas:

    continue o c
: continua el programa hasta el breakpoint siguiente

    si 
: step instruction, avanza una instruccion

    set $<reg>=valor 
: setea valor en el registro <reg>, ej: 
    set $pc=0x70010000 
setea el program counter a direccion 0x70010000

x [/fmt] [<addr>]: examina memoria en varios formatos
    - donde:
        - /fmt es el formateo de datos de la siguiente forma:
		es una cantidad de elementos seguido de una letra de formato y una letra de tamanio
		Format letters are 
        - o(octal), 
        - x(hex)
        - d(decimal)
        - u(unsigned decimal),
        - t(binary)
        - f(float)
        - a(address)
        - i(instruction)
        - c(char)
        - s(string)
        - z(hex, zero padded on the left).
		Size letters are:
        - b(byte)
        - h(halfword)
        - w(word), 
        - g(giant, 8 bytes).

    break *<addr>
: pone el breakpoint en la direccion <addr>

    maint info breakpoints 
: listado de breakpoints y watchpoints activos

    delete <num>
: remueve el brea(watch)kpoint numero <num> (listado con maint info)

    watch <expr> [mask]
: agrega watchpoint sobre el resultado de <expr> puede ser una pos de mem un registro, un calculo, etc, siempre casteandolo a un tipo de dato, ej:
     watch *(int)$r1
: agrega un watchpoint que detiene ejecucion al cambiar de valor r1

tambien se puede agregar una mascara (opcional) como campo de bits

    i r [lista de regs] 
: info de registros, se puede agregar cuales ver sino muestra todos

para agregar un watch sobre una area de memoria:

dashboard memory watch (type cast)POSMEM [<tamanio>]

el argumento opcional <tamanio> puede ser incluso una expresion, por ejemplo:

si se quiere monitorear una posicion de memoria con una variable de programa tipo int (VAR1)  castear a puntero a entero la posmem de la variable, caso contrario mostrara el casillero de memoria que corresopnde al nro almacenado en la variable

	dashboard memory watch (int*)&VAR1 sizeof(int)

para limpiar todos los watch de memoria:

	dashboard memory clear

esto los borra tdos, no se puede elegir uno para descartar
