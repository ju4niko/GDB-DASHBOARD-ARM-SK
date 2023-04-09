QEMU toolchain for MAC survival kit (Apple silicon Mx)

(ENGLISH VERSION, para la version en ESPÑOL buscar al final del documento)
It can be used in two ways:
* Directly on macOS
* Bye means of virtualized VM running UBUNTU for ARM64 arch

First case, native macOS:
* install gcc-arm-none-eabi
download package gcc-arm-none-eabi-10.3-2021.10-mac.pkg (or the most recent version available) from: https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads.
it'll be installed more probably in:

/Applications/ARM/bin/

but could be in another location as well, in any case it is very important to note down this directory because it must be added to PATH environment var in .Xshrc (bash, zsh, or whatever shell you want to use).
In case you already have homebrew installed for x86 arch Mac (probably migrating an intel mac to an M1 mac i.e.) in your M1 mac, remove homebrew following instructions in homebrew's site.
Then reinstal homebrew (also following install instructions from homebrew's site), and probably you have to install again some other packages with homebrew (like python, etc.)

* install qemu with homebrew

$ brew install qemu

Second case, VM with UBUNTU 22.04-LTS aArch64
As a test case it was installed using UTM (Universal Turing Machine: https://mac.getutm.app) virtualizing on Apple M2 silicon.
Once ubuntu VM is ready, install then qemu and gcc-arm-none-eabi packages:

$ sudo apt install qemu 
$ sudo apt install gcc-arm-none-eabi

-------------------------------------------------------------------------
(SPANISH VERSION)

se puede utilizar de dos formas:
* Directamente sobre el macOS
* en una VM virtualizada correindo UBUNTU para ARM64


se puede utilizar de dos formas:
- Directamente sobre el macOS
- en una VM virtualizada correindo UBUNTU para ARM64


Primer caso, macOS nativo:
---------------------------
 - primero instalar gcc-arm-none-eabi

descargar el paquete : gcc-arm-none-eabi-10.3-2021.10-mac.pkg (o loa version mas receinte) desde:
https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads.

esto se instala por lo general en 

/Applications/ARM/bin/

pero puede llegar a ser en otra ubicacion, es importante averiguar esto, porque hay que agregar al PATH del .Xshrc (bash, zsh, o el shell que se quiera usar) este directorio

en caso de tener instalado homebrew para intel en mac con M1, remover el homebrew con las instrucciones de la pagina de homebrew

luego volverlo a instalar (tambien con las instrucciones de la pagina de homebrew)

puede que haya que volver a instalar con homebrew algunos paquetes (python, etc), hacerlo

 - intalar qemu con homebrew: 

$ brew install qemu


Segundo caso, VM con UBUNTU 22.04-LTS aArch64
---------------------------------------------

Como ejemplo se instalo el Ubuntu en UTM (Universal Turing Machine: https://mac.getutm.app) virtualizando sobre procesador Apple M2

Una vez instalado el ububtu, instalar luego los paquetes de qemu y gcc-arm-none-eabi:

$ sudo apt install qemu
$ sudo apt install gcc-arm-none-eabi



