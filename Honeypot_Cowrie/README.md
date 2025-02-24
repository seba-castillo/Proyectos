# Honeypot [Cowrie](http://github.com/cowrie/cowrie)🍯

Proyecto en que se configura honeypot Cowrie en una instancia en AWS para estudio de comportamiento malicioso.

###### Basado en la documentación oficial y Cody Gula.

## 1. Crear máquina virtual en AWS

Creamos una VM en [Amazon EC2](https://aws.amazon.com/es/ec2/) utilizando una imagen Debian 12 del tipo t2.micro apta para la capa gratuita, además permitimos el tráfico SSH desde nuestra IP para configurar la máquina. Además debemos agregar otro puerto TCP adicional que posteriormente utilizaremos, por ejemplo el puerto TCP 9922.

## 2. Cambiar puerto SSH

Cowrie es un honeypot SSH por lo que no podremos utilizar el puerto 22 una vez lo inicialicemos, por lo tanto cambiaremos el puerto por el cual nos conectaremos por SSH.

Nos conectamos mediante SSH con el siguiente código utilizando la llave correspondiente.

```
ssh -i "keypair.pem" admin@public_ip
```

Editamos el archivo de configuración ubicado en "/etc/ssh/sshd_config". Descomentamos la línea que nos indica el puerto y la cambiamos por ejemplo a 9922.

![Cambio_puerto](/Honeypot_Cowrie/images/1.png)

Luego reiniciamos el servicio para aplicar el cambio.

```
sudo systemctl restart ssh
```

Corroboramos el cambio.

```
sudo systemctl status ssh
```

![Comprobar_puerto](/Honeypot_Cowrie/images/2.png)

Cuando cerremos sesión y queramos conectarnos por SSH entonces lo hacemos con el siguiente comando.

```
ssh -i "keypair.pem" admin@public_ip -p 9922
```

## 3. Instalar Cowrie

En primer lugar actualizamos Debian e instalamos algunas dependencias necesarias para Cowrie. En caso que nos pregunte por el archivo de configuración sshd_config, mantendremos la la versión local modificada.

```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt update -y
sudo apt-get install lnav git python3-virtualenv libssl-dev libffi-dev build-essential libpython3-dev authbind -y
```

Luego crearemos un nuevo usuario. Nos solicitará algunas opciones pero podemos dejarlas en blanco.

```
sudo adduser --disabled-password cowrie
sudo su - cowrie
```

Clonamos el repositorio oficial y creamos un entorno virtual utilizando virtualenv para ejecutar python en un entorno aislado.

```
git clone http://github.com/cowrie/cowrie
cd cowrie
virtualenv cowrie-env
source cowrie-env/bin/activate
```

Y procedemos a realizar la instalación.

```
pip install --upgrade pip
pip install --upgrade -r requirements.txt
```

## 4. Configurar Cowrie

Ahora cambiaremos el hostname a uno distinto del predeterminado para no evidenciar tan facilmente que es un honeypot, trataremos de utilizar algun nombre mas tentador. Para esto creamos un archivo llamado "cowrie.cfg" y copiaremos el archivo "cowrie.cfg.dist" en él.

```
cd etc
cp cowrie.cfg.dist cowrie.cfg
```

![Cambiar_hostname](/Honeypot_Cowrie/images/3.png)

Luego procedemos a salir del entorno virtual.

```
exit
```

A continuación debemos redirigir el tráfico TCP del puerto 22 al puerto 2222, el cual es el utilizado por Cowrie para emular el cliente SSH falso.

```
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```

## 5. Ejecutar Cowrie y observar Logs

Para ejecutar Cowrie volvemos al usuario creado previamente y ejecutamos.

```
sudo su - cowrie
cd ~/cowrie/bin
./cowrie start
#./cowrie stop para detener
#./cowrie status para ver status
```

Para observar los registros podemos usar "lnav" con el archivo cowrie.log.

```
cd var/log/cowrie
lnav cowrie.log
```

![Logs](/Honeypot_Cowrie/images/4.png)

A continuación debemos actualizar la regla de firewall para nuestra máquina virtual que nos permitía solo a nosotros conectarnos al puerto 22. Debemos permitir que todo internet se conecte.

## 6. Conexión de prueba

Finalmente, para probar por nuestra parte, probamos la conexión.

```
ssh root@public_ip
```

###### Corroborar reglas de firewall en caso de error de conexión.

## Material referencia

- [Documentación oficial](https://docs.cowrie.org/en/latest/INSTALL.html)
- [Cody Gula](https://www.codygula.com/posts/setting-up-a-cowrie-ssh-honeypot-on-aws/)
