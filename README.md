QEMU-GDB-DASHBOARD Survival Kit

NOTA IMPORTANTE:
	Para invocar al gdb de forma que pueda debuggear codigo de ARM se bebe ejecutar como "gdb-multiarch" y no "gdb" en la linea de comandos, la unica excepcion es en el caso de correr gdb en un S.O. que este ejecutando en un host ARM nativo (caso BBB o RasPI, tablet con ARM o MacBook con M1/2, etc)

El dashboard se instala como un .gdbinit en la carpeta raiz del usuario actual, se debe
descargar de : https://github.com/cyrus-and/gdb-dashboard

luego para que el DASHBOARD muestre por defecto los registros de la arquitectura ARM hay que indicarle esto con un archivo para personalizarlo y que levante automaticamente esta configuracion, esto se indica agregando una linea al comienzo del .gdbinit que descargamos antes, entonces abrirlo con un editor (nano p/ej.) y agregarle lo siguiente:

	add-auto-load-safe-path ./.gdbinit.d/

como primera linea.
Ahora en el ~/ (dir. home, por ej /home/juan) del usaurio actual hacer:

	$ mkdir .gdbinit.d
	$ cd .gdbinit.d
	$ echo set architecture arm > init
	$ echo dashboard registers -style list "'r0 r1 r2 r3 r4 r5 r6 r7 r8 r9 r10 r11 r12 sp lr pc cpsr'" >> init  

(ojo las comillas simples y dobles!)
esto crea un archivo de configuracion llamado "init" que agrega lo que necesitamos para usar el set de registros de la arq. ARM en DASHBOARD.
Para utilizarlo con QEMU, luego de compilar nuestro programa, hay que iniciar el QEMU agregando las siguientes opciones en linea de comando:

- los switches -S y -s (start stopped y escucha de conexion por port 1234) esto es independiente del monitoreo por la opcion "server" en el otro puerto que se elije para conectar por telnet.
- en otra terminal iniciar el gdb (ahora va a cargar el .gdbinit descargado del github de dashboard y que modificamos nosotros)
- invocar el gdb con el nombre del programa .elf que compilamos, si no se especifica el archivo de programa a depuerar en linea de comando, usar en la onterfaz de gdb el siguiente comando:

	file <mi-prog.o>|<mi-prog.elf> 

si tiene tabla de simbolos, mejor, esto va a depender de como hayamos compilado y/o ensamblado el programa (opcion -g del "as" y "gcc").
luego, conectarnos al proceso de emulacion de QEMU con el siguiente comando en gdb:

	target remote localhost:1234 

(1234 es el puerto en que escucha el qemu por defecto)

	
al cargar el fuente del programa (si es que incluimos esta opcion al compilar) la configuracion de DASHBOARD nos va a mostrar automaticamente por defecto varias areas con informacion variada (registros, memoria, breakpoints, watchpoints, etc) esto es configurable, ver la documentacion de dashboard para eso.

- se pueden utilizan los comandos estandar de gdb de todas formas:

	continue o c

: continua el programa hasta el breakpoint siguiente

    	si 

: step into instruction, avanza una instruccion

    	set $<reg>=valor
	
: setea valor en el registro <reg>, ej: 
	
    	set $pc=0x70010000 

setea el program counter a direccion 0x70010000, de paso esto produce que la emulacion continue la ejecucion de instrucciones en esa direccion de memoria.

x [/fmt] [<addr>]: examina memoria en varios formatos
    - donde:
        - /fmt es el formateo de datos de la siguiente manera:
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

: agrega un watchpoint que detiene ejecucion al cambiar de valor r1 tambien se puede agregar una mascara (opcional) como campo de bits

    i r [lista de regs] 

: info de registros, se puede agregar cuales ver, sino muestra todos

para agregar un watch sobre una area de memoria:

	dashboard memory watch [(casteo de tipo)]<POSMEM> [<tamanio>]

el argumento opcional <tamanio> puede ser incluso una expresion e indica cuantos bytes mostrara a partir de la POSMEM, por ejemplo:

si se quiere monitorear una posicion de memoria con una variable de programa tipo int (por caso digamos VAR1)  castear a puntero a entero la POSMEM de la variable, caso contrario mostrara el casillero de memoria que corresopnde al nro almacenado en la variable, es decir si la variable VAR1 esta en el casillero 0x700103b7 y contiene el valor 0x3a, si no casteamos a tipo puntero a entero y pedimos la direccion de la VAR1, nos mostraria el contenido del casillero de memoria 0x0000003a en lugar de mostrarnos el contenido de 0x700103b7 

	dashboard memory watch (int*)&VAR1 sizeof(int)

para limpiar todos los watch de memoria:

	dashboard memory clear

esto los borra tdos, no se puede elegir uno para descartar.

Independientemente a toda la informacion anterior, los paneles de DASHBOARD se pueden configurar para usarse en multiples terminales, esto es util cuando se pueden abrir varias ventanas con pseudo terminaltes (pts/N), la forma de utilizarlo es abrir todas las terminales que se desee y en cada una de ellas ejecutar el comando "tty" esto arrojara algo como:
	
	/dev/pts/0

entonces, una ved hayamos arrancado el gdb con DASBOARD, alli con el comando:

	> dashboard regosters -output /dev/tty/0

redireccionamos la visualizacion del panel con los registros en dicha terminal pts. Esto se puede hacer con cualquiera de los paneles de DASHBOARD.
