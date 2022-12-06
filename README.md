# Curso Ansible TT 2022 - Clara García

En primer lugar configuramos todo el entorno de pruebas dentro de nuestra MV, configurando las IPs fijas para los diferentes nodos.

## Ejercicio 1

Configuramos en los inventorios las ips y host de nuestras máquinas. Para comprobar si es correcto podemos lanzar el módulo ping con los siguientes comandos:

```
ansible all -i inventory -m ping
```

Salida:
```
10.218.144.233 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
10.218.144.221 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```


```
ansible all -i inventory-dns -m ping
```

Salida:
```
u1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
u2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```



## Ejercicio 2

Agrupamos los host usando patrones, asignándoles un nombre usando los corchetes ([])

Para probarlo utilizamos el siguiente comando, en el cual indicamos sobre que hosts lo queremos ejecutar:

```
ansible ubuntu -i inventory-dns -m ping
```

Salida:
```
u2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
u1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Si lo quisieramos ejecutar sobre el grupo otra, solo tendriamos que cambiar en el comando anterior *ubuntu* por *otras*.

## Ejercicio 3

Creamos un playbook que instale y arranque nginx, abra los puertos necesarios (22,80 y 443) y nos copie el fichero index.html en /var/www/.

Lanzamos el siguiete comando para lanzar el playbook:

```
ansible-playbook tasks.yaml
```

Salida:
```
BECOME password:

PLAY [Instalación de Nginx] ****************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [u1]
ok: [u3]
ok: [u2]

TASK [Instalando el paquete nginx] *********************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [Servicio Nginx arrancado] ************************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [Instalando elinks] *******************************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [Habilitar UFW] ***********************************************************************************************************
ok: [u2]
ok: [u1]
ok: [u3]

TASK [Habilitar el log] ********************************************************************************************************
ok: [u1]
ok: [u3]
ok: [u2]

TASK [abrir el firewall para 22] ***********************************************************************************************
ok: [u1]
ok: [u3]
ok: [u2]

TASK [abrir el firewall para 80] ***********************************************************************************************
ok: [u2]
ok: [u1]
ok: [u3]

TASK [abrir el firewall para 443] **********************************************************************************************
ok: [u1]
ok: [u3]
ok: [u2]

TASK [Copia el fichero index.html] *********************************************************************************************
changed: [u3]
changed: [u1]
changed: [u2]

PLAY RECAP *********************************************************************************************************************
u1                         : ok=10   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
u2                         : ok=10   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
u3                         : ok=10   changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

Ahora si accedemos desde nuestro navegador a cualquiera de las direcciones url htpp://u1, htpp://u2 o htpp://u3, deberíamos ver algo como lo siguiente:
![Servidores nginx funcionando en u1, u2 y u3](/assets/nginx_servers.png)

## Ejercicio 4

Instalamos MariaDB en la máquina u3.

```
ansible-playbook task.yaml
```

Salida
```
BECOME password:

PLAY [Install Databases] *******************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [u3]

TASK [Update repositories] *****************************************************************************************************
fatal: [u3]: FAILED! => {"changed": false, "msg": "Failed to update apt cache: W:Updating from such a repository can't be done securely, and is therefore disabled by default., W:See apt-secure(8) manpage for repository creation and user configuration details., W:https://dlm.mariadb.com/repo/mariadb-server/10.9/repo/ubuntu/dists/jammy/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details., W:GPG error: https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY CE1A3DD5E3C94F49, E:The repository 'https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease' is not signed."}
...ignoring

TASK [Install Mariadb Requirements for Ubuntu jammy] ***************************************************************************
ok: [u3]

TASK [Add MariaDB Repository Key for Ubuntu jammy] *****************************************************************************
ok: [u3]

TASK [Add MariaDB Repository for Ubuntu jammy] *********************************************************************************
ok: [u3]

TASK [Add MariaDB Repository debug for Ubuntu jammy] ***************************************************************************
ok: [u3]

TASK [Update repositories] *****************************************************************************************************
fatal: [u3]: FAILED! => {"changed": false, "msg": "Failed to update apt cache: W:Updating from such a repository can't be done securely, and is therefore disabled by default., W:See apt-secure(8) manpage for repository creation and user configuration details., W:https://dlm.mariadb.com/repo/mariadb-server/10.9/repo/ubuntu/dists/jammy/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details., W:GPG error: https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY CE1A3DD5E3C94F49, E:The repository 'https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease' is not signed."}
...ignoring

TASK [Install Mariadb server for Ubuntu jammy] *********************************************************************************
changed: [u3]

TASK [Servicio arrancado] ******************************************************************************************************
ok: [u3]

TASK [Habilitar UFW] ***********************************************************************************************************
ok: [u3]

TASK [Habilitar el log] ********************************************************************************************************
ok: [u3]

TASK [abrir el firewall para 22] ***********************************************************************************************
ok: [u3]

TASK [abrir el firewall para 3306] *********************************************************************************************
changed: [u3]

TASK [Set MariaDB root password for 127.0.0.1, localhost] **********************************************************************
changed: [u3] => (item=127.0.0.1)
changed: [u3] => (item=localhost)

TASK [Remove all anonymous user] ***********************************************************************************************
ok: [u3]

TASK [Flush Priviliges] ********************************************************************************************************
changed: [u3]

TASK [Create Database] *********************************************************************************************************
changed: [u3]

TASK [Create mysql User defined database] **************************************************************************************
changed: [u3]

PLAY RECAP *********************************************************************************************************************
u3                         : ok=18   changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=2
```

Para probarlo, accedemos a la bbdd con el siguiente comando

```
mysql -u test -p
```

## Ejercicio 5

Creamos una máquina con un servidor en una distribución distinta. En mi caso, tras probar con RockyLinux, volví a Ubuntu.

En primer lugar, nos creamos una máquina nueva basada en Ubuntu 20.04. Para ello usamos el siguiente comando:

```
lxc launch ubuntu:20.04 ubuntuNueva
```

Una vez tenemos la máquina creada ejecutamos el playbook que instala el servidor nginx, copia el fichero html, instala mariadb y crea la bbdd test_clara.

```
ansible-playbook tasks.yaml
```

Salida:

```
BECOME password: 

PLAY [Install Databases] *******************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [nueva]

TASK [Instalamos nginx] ********************************************************************************************************
changed: [nueva]

TASK [Arrancamos nginx] ********************************************************************************************************
ok: [nueva]

TASK [Copiamos el fichero index.html] ******************************************************************************************
changed: [nueva]

TASK [Update repositories] *****************************************************************************************************
changed: [nueva]

TASK [Install Mariadb Requirements for Ubuntu jammy] ***************************************************************************
changed: [nueva]

TASK [Add MariaDB Repository Key for Ubuntu jammy] *****************************************************************************
changed: [nueva]

TASK [Add MariaDB Repository for Ubuntu jammy] *********************************************************************************
changed: [nueva]

TASK [Add MariaDB Repository debug for Ubuntu jammy] ***************************************************************************
changed: [nueva]

TASK [Update repositories] *****************************************************************************************************
changed: [nueva]

TASK [Installamos mariadb for Ubuntu jammy] ************************************************************************************
changed: [nueva]

TASK [Arrancamos mariadb] ******************************************************************************************************
ok: [nueva]

TASK [Habilitar UFW] ***********************************************************************************************************
changed: [nueva]

TASK [Habilitar el log] ********************************************************************************************************
ok: [nueva]

TASK [abrir el firewall para 22] ***********************************************************************************************
changed: [nueva]

TASK [abrir el firewall para 80] ***********************************************************************************************
changed: [nueva]

TASK [abrir el firewall para 443] **********************************************************************************************
changed: [nueva]

TASK [abrir el firewall para 3306] *********************************************************************************************
changed: [nueva]

TASK [Establecemos la password root de mariadb] ********************************************************************************
changed: [nueva] => (item=127.0.0.1)
changed: [nueva] => (item=localhost)

TASK [Remove all anonymous user] ***********************************************************************************************
ok: [nueva]

TASK [Flush Priviliges] ********************************************************************************************************
changed: [nueva]

TASK [Creamos la base de datos test] *******************************************************************************************
changed: [nueva]

TASK [Create mysql User defined database] **************************************************************************************
changed: [nueva]

PLAY RECAP *********************************************************************************************************************
nueva                      : ok=23   changed=18   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Ahora si accedemos a la url http://nueva tendremos nuestro servidor nginx funcionando, y si listamos las bbdd tendremos la bd test_clara.

![Servidor nginx y mariadb funcionando en nueva](/assets/nginx_mariadb.png)


## Ejercicio 6

En este ejercicio vamos a dividir el contenido de un playbook en diferentes ficheros tanto de tareas como de variables y hacer uso de los facts, para realizar un despligue completo. En mi caso voy a instalar el servidor nginx en los nodos u1, u2 y u3, y además en el nodo u3 voy a instalar mariabd.

Con el siguiente comando lo ejecutamos:
```
ansible-playbook completo.yaml
```

Salida 
```
BECOME password: 

PLAY [all] *********************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [u3]
ok: [u1]
ok: [u2]

TASK [debug] *******************************************************************************************************************
ok: [u1] => {
    "msg": "play1"
}
ok: [u2] => {
    "msg": "play1"
}
ok: [u3] => {
    "msg": "play1"
}

PLAY [webservers] **************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [u2]
ok: [u1]
ok: [u3]

TASK [Instalando nginx] ********************************************************************************************************
ok: [u3]
ok: [u1]
ok: [u2]

TASK [Servicio arrancado] ******************************************************************************************************
ok: [u3]
ok: [u1]
ok: [u2]

TASK [Instalando elinks] *******************************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [Habilitar UFW] ***********************************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [Habilitar el log] ********************************************************************************************************
ok: [u2]
ok: [u1]
ok: [u3]

TASK [abrir el firewall para 22] ***********************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [abrir el firewall para 80] ***********************************************************************************************
ok: [u2]
ok: [u1]
ok: [u3]

TASK [abrir el firewall para 443] **********************************************************************************************
ok: [u1]
ok: [u2]
ok: [u3]

TASK [Copia el fichero index.html modificado con variables] ********************************************************************
ok: [u2]
ok: [u1]
ok: [u3]

PLAY [databases] ***************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************
ok: [u3]

TASK [Update repositories] *****************************************************************************************************
fatal: [u3]: FAILED! => {"changed": false, "msg": "Failed to update apt cache: W:Updating from such a repository can't be done securely, and is therefore disabled by default., W:See apt-secure(8) manpage for repository creation and user configuration details., W:https://dlm.mariadb.com/repo/mariadb-server/10.9/repo/ubuntu/dists/jammy/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details., W:GPG error: https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY CE1A3DD5E3C94F49, E:The repository 'https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease' is not signed."}
...ignoring

TASK [Install Mariadb Requirements for Ubuntu jammy] ***************************************************************************
ok: [u3]

TASK [Add MariaDB Repository Key for Ubuntu jammy] *****************************************************************************
ok: [u3]

TASK [Add MariaDB Repository for Ubuntu jammy] *********************************************************************************
ok: [u3]

TASK [Update repositories] *****************************************************************************************************
fatal: [u3]: FAILED! => {"changed": false, "msg": "Failed to update apt cache: W:Updating from such a repository can't be done securely, and is therefore disabled by default., W:See apt-secure(8) manpage for repository creation and user configuration details., W:https://dlm.mariadb.com/repo/mariadb-server/10.9/repo/ubuntu/dists/jammy/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details., W:GPG error: https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY CE1A3DD5E3C94F49, E:The repository 'https://dlm.mariadb.com/repo/maxscale/latest/apt jammy InRelease' is not signed."}
...ignoring

TASK [Install Mariadb server for Ubuntu jammy] *********************************************************************************
ok: [u3]

TASK [Servicio arrancado] ******************************************************************************************************
ok: [u3]

TASK [Habilitar UFW] ***********************************************************************************************************
ok: [u3]

TASK [Habilitar el log] ********************************************************************************************************
ok: [u3]

TASK [abrir el firewall para 22] ***********************************************************************************************
ok: [u3]

TASK [abrir el firewall para 3306] *********************************************************************************************
ok: [u3]

TASK [Set MariaDB root password for 127.0.0.1, localhost] **********************************************************************
ok: [u3] => (item=127.0.0.1)
ok: [u3] => (item=localhost)

TASK [Remove all anonymous user] ***********************************************************************************************
changed: [u3]

TASK [Create Database] *********************************************************************************************************
changed: [u3]

TASK [Create mysql User defined database] **************************************************************************************
changed: [u3]

RUNNING HANDLER [Flush Priviliges] *********************************************************************************************
changed: [u3]

PLAY RECAP *********************************************************************************************************************
u1                         : ok=12   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
u2                         : ok=12   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
u3                         : ok=29   changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=2  

```

Si accedemos desde nuestro navegador a cualquiera de las direcciones url htpp://u1, htpp://u2 o htpp://u3, deberíamos ver algo como lo siguiente. Además si accedemos a u3 y vemos las bbdd tenemos la que acabamos de crear:
![Servidores nginx funcionando en u1, u2 y u3. Mariadb instalado y funcionando en u3](/assets/playbook_dividido.png)


## Ejercicio 7 (Chispas)

El objetivo del ejercicio, es que a partir de un playbook génerico, conseguir que se ejecuten diferentes acciones según la distribución. En mi caso voy a hacer un playbook que sea diferente para distribuciones Debian, Rocky u otras. En mi caso he hecho un playbook que primero muestre la familia, y luego pare las máquinas Debian, muestre un mensaje para máquinas Rocky y otro para máquinas que no sean ni Debian ni Rocky.

Salida:
```
BECOME password: 

PLAY [debian] *********************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************
ok: [u2]
ok: [u3]
ok: [u1]

TASK [debug ansible_facts.os_family] **********************************************************************************************************
ok: [u1] => {
    "msg": "OS Family: Debian"
}
ok: [u2] => {
    "msg": "OS Family: Debian"
}
ok: [u3] => {
    "msg": "OS Family: Debian"
}

TASK [Máquinas Debian] ************************************************************************************************************************
changed: [u2]
changed: [u3]
changed: [u1]

TASK [Máquinas Rocky] *************************************************************************************************************************
skipping: [u1]
skipping: [u2]
skipping: [u3]

TASK [Otras máquinas] *************************************************************************************************************************
skipping: [u1]
skipping: [u2]
skipping: [u3]

PLAY RECAP ************************************************************************************************************************************
u1                         : ok=3    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
u2                         : ok=3    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
u3                         : ok=3    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0  
```









## Ejercicio 8
Instalación de Docker en un nodo manjeado, creamos el perfil docker (no pongo los pasos porque era literalmente lo mismo que estaba en los ejemplos). Para instalar wordpress tuve el problema de espacio que no he conseguido solucionar.


