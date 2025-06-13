# Comandos Detallados para las Guías GNU/Linux

A continuación te entrego los comandos con explicaciones detalladas para cada paso:

## Actividad 3.1 - Comandos Esenciales

### Parte II - Comandos Esenciales de Administración

```bash
# Crear estructura de directorios con opción -p para crear padres si no existen
mkdir -p /inacap/sistemaoperativo/networking
mkdir -p /inacap/taller
mkdir -p /inacap/basedatos

# Crear archivos vacíos
touch /inacap/sistemaoperativo/certamen.txt  # Crea archivo certamen.txt
touch /inacap/sistemaoperativo/test.txt      # Crea archivo test.txt
touch /inacap/simulacion.txt                 # Crea archivo simulacion.txt
touch /inacap/taller/practico.txt            # Crea archivo practico.txt en taller
touch /inacap/basedatos/practico.txt         # Crea archivo practico.txt en basedatos
touch /inacap/basedatos/trabajo.txt          # Crea archivo trabajo.txt en basedatos

# Copiar archivos para crear respaldos
cp /inacap/sistemaoperativo/certamen.txt /inacap/basedatos/          # Copia certamen.txt a basedatos
cp /inacap/sistemaoperativo/certamen.txt /inacap/sistemaoperativo/networking/  # Copia certamen.txt a networking

# Renombrar archivos
mv /inacap/sistemaoperativo/test.txt /inacap/sistemaoperativo/evaluacion.txt    # Renombra test.txt a evaluacion.txt
mv /inacap/basedatos/trabajo.txt /inacap/basedatos/evaluacion.txt               # Renombra trabajo.txt a evaluacion.txt

# Agregar texto a todos los archivos
# tee escribe el texto en múltiples archivos
echo "Bienvenido al Semestre Primavera 2025" | tee /inacap/sistemaoperativo/certamen.txt /inacap/sistemaoperativo/networking/certamen.txt /inacap/sistemaoperativo/evaluacion.txt /inacap/simulacion.txt /inacap/taller/practico.txt /inacap/basedatos/practico.txt /inacap/basedatos/evaluacion.txt
```

### Parte III - Usuarios y Grupos

```bash
# Crear directorios de usuarios en /home
mkdir -p /home/{luis,marco,leandro,andres,carolina,victor}

# Crear usuarios con UID específicos (-u) y crear su directorio home (-m)
useradd -u 1101 -m marcelo
echo "inacap-2025" | passwd --stdin marcelo  # Establece contraseña

useradd -u 1201 -m fabian
echo "inacap-2025" | passwd --stdin fabian

useradd -u 1301 -m ana
echo "inacap-2025" | passwd --stdin ana

# Verificar usuarios creados
cat /etc/passwd | grep -E 'marcelo|fabian|ana'  # Muestra entradas de estos usuarios

# Crear grupos con GID específicos (-g)
groupadd -g 1100 finanzas
groupadd -g 1200 rrhh
groupadd -g 1300 informatica

# Agregar usuarios a grupos (-aG agrega a grupos secundarios)
usermod -aG finanzas marcelo
usermod -aG finanzas luis
usermod -aG rrhh fabian
usermod -aG rrhh andres
usermod -aG informatica ana
usermod -aG informatica luis
usermod -aG informatica andres

# Verificar grupos y miembros
cat /etc/group | grep -E 'finanzas|rrhh|informatica'

# Cambiar dueño (owner) y grupo de los directorios
chown marcelo:finanzas /inacap/sistemaoperativo  # Dueño: marcelo, Grupo: finanzas
chown fabian:rrhh /inacap/taller                # Dueño: fabian, Grupo: rrhh
chown ana:informatica /inacap/basedatos         # Dueño: ana, Grupo: informatica
```

### Parte IV - Permisos UGO

```bash
# Permisos numéricos (rwx):
# 7 = rwx (lectura, escritura, ejecución)
# 6 = rw- (lectura y escritura)
# 5 = r-x (lectura y ejecución)
# 4 = r-- (solo lectura)
# 0 = --- (sin permisos)

# Sistemaoperativo: Dueño(rwx), Grupo(r-x), Otros(---)
chmod 750 /inacap/sistemaoperativo

# Taller: Dueño(rwx), Grupo(rw-), Otros(rw-)
chmod 766 /inacap/taller

# Basedatos: Dueño(rwx), Grupo(rwx), Otros(r--)
chmod 774 /inacap/basedatos
```

### Parte V - Modificación del Sistema

```bash
# Cambiar runlevel (nivel de ejecución) a 3 (multiusuario sin GUI)
systemctl set-default multi-user.target

# Reiniciar el sistema
reboot

# Iniciar modo gráfico temporalmente (después de reiniciar)
startx

# Cambiar nombre del host (servidor)
hostnamectl set-hostname Server-Aplication
```

## Actividad 3.2 - Administración Avanzada

### Parte I - Particiones

```bash
# Particionar disco /dev/sdb (30GB)
fdisk /dev/sdb
# Comandos dentro de fdisk:
# n = nueva partición
# p = partición primaria
# 1 = número de partición
# +5G = tamaño de 5GB
# Repetir para las otras particiones según especificaciones
# t = cambiar tipo de sistema de archivos (para xfs/ext4)
# w = escribir cambios y salir

# Formatear particiones con sistemas de archivos específicos
mkfs.ext3 /dev/sdb1  # Formatea con ext3 (/videos)
mkfs.ext2 /dev/sdb2  # Formatea con ext2 (/descargas)
mkfs.ext3 /dev/sdb3  # Formatea con ext3 (/software)
mkfs.xfs /dev/sdb5   # Formatea con xfs (/juegos)
mkfs.ext4 /dev/sdb6  # Formatea con ext4 (/películas)

# Crear puntos de montaje
mkdir /videos /descargas /software /juegos /películas

# Montar particiones
mount /dev/sdb1 /videos
mount /dev/sdb2 /descargas
mount /dev/sdb3 /software
mount /dev/sdb5 /juegos
mount /dev/sdb6 /películas

# Configurar montaje automático al arranque (/etc/fstab)
# Formato: dispositivo punto_montaje tipo_fs opciones dump fsck
echo "/dev/sdb1 /videos ext3 defaults 0 0" >> /etc/fstab
echo "/dev/sdb2 /descargas ext2 defaults 0 0" >> /etc/fstab
echo "/dev/sdb3 /software ext3 defaults 0 0" >> /etc/fstab
echo "/dev/sdb5 /juegos xfs defaults 0 0" >> /etc/fstab
echo "/dev/sdb6 /películas ext4 defaults 0 0" >> /etc/fstab

# Verificar montaje
df -h  # Muestra sistemas de archivos montados y espacio usado
```

### Parte II - Configuración de Red

```bash
# Configurar red estática con nmcli (NetworkManager)
nmcli con mod "Conexión cableada 1" \
  ipv4.method manual \
  ipv4.addresses 176.44.3.10/24 \
  ipv4.gateway 176.44.3.10 \
  ipv4.dns "8.8.8.8"  # Opcional: configurar DNS

# Activar conexión
nmcli con up "Conexión cableada 1"

# Verificar configuración
ip a  # Muestra interfaces de red y sus configuraciones
```

### Parte III - Repositorio Local

```bash
# Montar DVD de instalación
mkdir /mnt/dvd  # Crear punto de montaje
mount /dev/sr0 /mnt/dvd  # Montar dispositivo de CD/DVD

# Copiar repositorios
cp -r /mnt/dvd/BaseOS /  # Copia recursiva de BaseOS a raíz
cp -r /mnt/dvd/AppStream /  # Copia recursiva de AppStream a raíz

# Crear archivo de configuración de repositorio
cat > /etc/yum.repos.d/repositorio.repo <<EOF
[BaseOS]
name=BaseOS
baseurl=file:///BaseOS
enabled=1
gpgcheck=0

[AppStream]
name=AppStream
baseurl=file:///AppStream
enabled=1
gpgcheck=0
EOF

# Actualizar lista de paquetes
yum clean all  # Limpiar caché
yum makecache  # Reconstruir caché
yum update -y  # Actualizar todos los paquetes
```

### Parte IV - Servicio DHCP

```bash
# Instalar servidor DHCP
yum install dhcp-server -y

# Configurar DHCP (editar archivo de configuración)
cat > /etc/dhcp/dhcpd.conf <<EOF
# Configuración global
default-lease-time 2700;  # 45 minutos en segundos
max-lease-time 2700;
authoritative;

# Configuración de subred
subnet 176.44.3.0 netmask 255.255.255.0 {
    range 176.44.3.1 176.44.3.9;  # Rango de 9 IPs
    option domain-name-servers 176.44.3.10;  # DNS (misma IP del servidor)
    option domain-name "sunombre.cl";  # Nombre de dominio
    option routers 176.44.3.10;  # Puerta de enlace
    option broadcast-address 176.44.3.255;  # Dirección de broadcast
    
    # Reserva de IP por MAC
    host reservado {
        hardware ethernet 08:00:27:2c:13:0b;  # Dirección MAC
        fixed-address 176.44.3.5;  # IP reservada (quinta del rango)
    }
}
EOF

# Habilitar e iniciar servicio
systemctl start dhcpd  # Iniciar servicio
systemctl enable dhcpd  # Habilitar inicio automático

# Configurar para niveles de ejecución 1, 3 y 5
ln -s /usr/lib/systemd/system/dhcpd.service /etc/systemd/system/multi-user.target.wants/dhcpd.service
systemctl daemon-reload

# Verificar estado
systemctl status dhcpd  # Debe mostrar "active (running)"
```

Todos estos comandos deben ejecutarse con privilegios de root (usando `sudo` o iniciando sesión como root).
