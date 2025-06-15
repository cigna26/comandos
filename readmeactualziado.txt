####################################################
###          INSTALACION DE LINUX 		###
####################################################

linux elegir la primera opcion
-español chile

seleccion de software (servidor con GUI)

para linux se necesitan 2 particiones 

destino de la instalacion

sd = disco de conexion sata

hd = disco de conexion ide

sda = disco duro

sdb = la maquina tiene 2 discos duros

sdc = 3 discos duros

en linux las particiones no son letras, se trabaja con carpetas

por cada disco duro se puede crear 4 particiones primarias y una primaria puedo crear una extendida y esa extendida puedo crear 64 particiones logicas 

config de almacenamiento personalizada - particion estandar - + - raiz / capacidad 20480 (20gb) - xfs a ext4 

+ - swap capacidad 16384 y dejarlo como esta (no cambiar nada), aceptar cambios

contraseña inacap-2025

crear usuario - mi nombre y la misma contraseña - iniciar instalacion 

aceptar la licencia y finalizar config

trabajar con la cuenta root seleccionar no esta mi cuenta y nombre: root contra: inacap-2025

dentro de linux ir a actividades y seleccionar terminal 



####################################################
### COMANDOS DE ADMINISTRACIÓN DE SISTEMAS LINUX ###
####################################################


# = privilegio de admin
$ = privilegio de usuario


=======================
PARTE 1 - CREACIÓN DE ARCHIVOS
=======================
# Crear archivos en directorios específicos
sudo touch /inacap/sistemaoperativo/certamen.txt  
sudo touch /inacap/sistemaoperativo/test.txt      
sudo touch /inacap/simulacion.txt                 
sudo touch /inacap/taller/practico.txt          
sudo touch /inacap/basedatos/practico.txt         
sudo touch /inacap/basedatos/trabajo.txt    

=======================
PARTE 2 - COPIA Y RENOMBRE
=======================
# Copiar archivos para respaldos
sudo cp /inacap/sistemaoperativo/certamen.txt /inacap/basedatos/   
sudo cp /inacap/sistemaoperativo/certamen.txt /inacap/sistemaoperativo/networking/  

# Renombrar archivos
sudo mv /inacap/sistemaoperativo/test.txt /inacap/sistemaoperativo/evaluacion.txt   
sudo mv /inacap/basedatos/trabajo.txt /inacap/basedatos/evaluacion.txt               

# Agregar texto a todos los archivos
echo "Bienvenido al Semestre Primavera 2025" | sudo tee /inacap/sistemaoperativo/certamen.txt /inacap/sistemaoperativo/networking/certamen.txt /inacap/sistemaoperativo/evaluacion.txt /inacap/simulacion.txt /inacap/taller/practico.txt /inacap/basedatos/practico.txt /inacap/basedatos/evaluacion.txt

=======================
PARTE 3 - USUARIOS Y GRUPOS
=======================
# Crear directorios de usuarios en /home
sudo mkdir -p /home/{luis,marco,leandro,andres,carolina,victor}

# Crear usuarios con UID específicos
sudo useradd -u 1101 -m marcelo
echo "inacap-2025" | sudo passwd --stdin marcelo   # Establece contraseña

sudo useradd -u 1201 -m fabian
echo "inacap-2025" | sudo passwd --stdin fabian

sudo useradd -u 1301 -m ana
echo "inacap-2025" | sudo passwd --stdin ana

sudo useradd -u luis
echo "inacap-2025" | sudo passwd --stdin luis

sudo useradd -u andres
echo "inacap-2025" | sudo passwd --stdin andres

# Verificar usuarios creados - Muestra entradas de estos usuarios
sudo cat /etc/passwd | sudo grep -E 'marcelo|fabian|ana'

# Crear grupos
sudo groupadd -g 1100 finanzas
sudo groupadd -g 1200 rrhh
sudo groupadd -g 1300 informatica

# Agregar usuarios a grupos
sudo usermod -aG finanzas marcelo
sudo usermod -aG finanzas luis 
sudo usermod -aG rrhh fabian
sudo usermod -aG rrhh andres 
sudo usermod -aG informatica ana
sudo usermod -aG informatica luis 
sudo usermod -aG informatica andres 

# Verificar grupos
cat /etc/group | sudo grep -E 'finanzas|rrhh|informatica'

# Cambiar dueños de directorios
sudo chown marcelo:finanzas /inacap/sistemaoperativo
sudo chown fabian:rrhh /inacap/taller
sudo chown ana:informatica /inacap/basedatos

=======================
PARTE 4 - PERMISOS UGO
=======================

# Permisos numéricos (rwx):
# 7 = rwx (lectura, escritura, ejecución)
# 6 = rw- (lectura y escritura)
# 5 = r-x (lectura y ejecución)
# 4 = r-- (solo lectura)
# 0 = --- (sin permisos)


# Asignar permisos
# Sistemaoperativo: El Dueño(rwx), el Grupo(r--), los Otros(---)
chmod 740 /inacap/sistemaoperativo

chmod 766 /inacap/taller
chmod 774 /inacap/basedatos

# Comprobar permisos
sudo su - marcelo
touch /inacap/sistemaoperativo/prueba.txt   #debe funcionar
ls -l /inacap/sistemaoperativo/     	    #debe mostrar el contenido
exit

=======================
PARTE 5 - MODIFICACIÓN DEL SISTEMA
=======================
# Cambiar runlevel 3 (Sin GUI por defecto)
systemctl set-default multi-user.target
reboot

# Iniciar GUI temporal
startx

# Cambiar hostname (segun requerimiento en este caso: Server-Aplication)
hostnamectl set-hostname Server-Aplication

#################
### GUÍA 3.2 - 
#################

=======================
PARTE  1 - Particiones Linux
=======================

agregar disco (vm apagada)
click derecho - config- almacenamiento - controlador sata - añadir disco - crear - seleccionarlo y listo

# Verificar disco nuevo
lsblk

# Particionar /dev/sdb con fdisk
sudo fdisk /dev/sdb

# Comandos dentro de fdisk:
n - nueva partición
p - primaria
1 - número de partición
Primer sector - presiona ENTER
Último sector - +5G para /videos
---------------------------------
# n - p - 1 - +5G (para /videos)
# n - p - 2 - +18G (para /descargas)
# n - p - 3 - +2G (para /software)
# n - e - enter - enter (extendida con lo restante) #terminal: oarticion 4 tipo 'Esxtended'
# n - enter - +1G (para /juegos)
# n - enter - +1G (para /películas)

# p - (verificar) - w - (guardar)
Deberiamos ver:
/dev/sdb1   5G
/dev/sdb2   18G
/dev/sdb3   2G
/dev/sdb4   5G   (extendida)
/dev/sdb5   1G   (lógica dentro de sdb4)
/dev/sdb6   1G   (lógica dentro de sdb4)

# Finalmente, escribe cambios:
w - write & exit

# Formatear particiones
sudo mkfs.ext3 /dev/sdb1 # /videos
sudo mkfs.ext2 /dev/sdb2 # /descargas
sudo mkfs.ext3 /dev/sdb3 # /software
sudo mkfs.xfs /dev/sdb5  # /juegos
sudo mkfs.ext4 /dev/sdb6 # /peliculas

# Crear y montar directorios
mkdir /videos /descargas /software /juegos /peliculas

# Montamos cada partición en su carpeta correspondiente
mount /dev/sdb1 /videos
mount /dev/sdb2 /descargas
mount /dev/sdb3 /software
mount /dev/sdb5 /juegos
mount /dev/sdb6 /peliculas

# Verificar montaje
df -h

# Configurar montaje automático (OPCIONAL)
sudo nano /etc/fstab
# Añadir:
/dev/sdb1   /videos     ext3    defaults  0 0
/dev/sdb2   /descargas  ext2    defaults  0 0
/dev/sdb3   /software   ext3    defaults  0 0
/dev/sdb5   /juegos     xfs     defaults  0 0
/dev/sdb6   /peliculas  ext4    defaults  0 0

CTRL+O = GUARDAR 
ENTER  
CTRL+X = SALIR


=======================
PARTE  2 - CONFIGURACIÓN DE RED
=======================

Verifica el nombre de tu tarjeta de red
nmcli device status
buscar
enp0s3    connected   ...

# Configurar IP estática
sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
# Contenido:
DEVICE=enp0s3
BOOTPROTO=none
ONBOOT=yes
IPADDR=176.44.3.10
NETMASK=255.255.255.0
GATEWAY=176.44.3.10
DNS1=8.8.8.8
NAME=enp0s3

# Aplicar cambios
nmcli connection reload
nmcli connection up enp0s3

# Verificar conexión
ip a
ping -c 4 8.8.8.8

# Restaurar DHCP (si es necesario opcional)
sudo nmcli connection delete enp0s3
sudo nmcli connection add type ethernet ifname enp0s3 con-name enp0s3 autoconnect yes ipv4.method auto
sudo nmcli connection up enp0s3


=======================
PARTE  3 - Repositorio Local
=======================

# 1. Montar manualmente la ISO del DVD (en VirtualBox)

-Inserta la ISO del DVD de RHEL 8.4 en VirtualBox
-Apaga tu máquina virtual (si está encendida).
-Ve a Configuración > Almacenamiento.
-Selecciona el ícono del CD.
-Elige el archivo ISO del RHEL 8.4 (el que tiene las carpetas BaseOS y AppStream).
-Marca la opción "Live CD/DVD" si está disponible.
-Inicia la máquina virtual.


# Verificar si existe el sr0 o sr1
lsblk
# Deberiamos ver algo como
sr1   /run/media/carlos/RHEL-8-4-0-BaseOS-x86_64

# PASO 1: Copiar las carpetas BaseOS y AppStream al directorio raíz

# Ahora- esto se demora en cada uno
sudo cp -r /run/media/carlos/RHEL-8-4-0-BaseOS-x86_64/BaseOS /
sudo cp -r /run/media/carlos/RHEL-8-4-0-BaseOS-x86_64/AppStream /

# PASO 2: Crear el archivo del repositorio local

sudo nano /etc/yum.repos.d/repositorio.repo

# Agrega este contenido:
[BaseOS]
name=Repositorio BaseOS Local
baseurl=file:///BaseOS
enabled=1
gpgcheck=0

[AppStream]
name=Repositorio AppStream Local
baseurl=file:///AppStream
enabled=1
gpgcheck=0

# guardar y salir

# PASO 3: Limpiar metadatos y probar

sudo dnf clean all
sudo dnf repolist

# deberiamos ver:
repo id          repo name
BaseOS           Repositorio BaseOS Local
AppStream        Repositorio AppStream Local


# PASO 4: Probar el repositorio
sudo dnf update -y



=======================
PARTE 4  - Servicio DHCP
=======================






