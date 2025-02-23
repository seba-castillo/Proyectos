# Honeypot Cowrie

Proyecto personal basado en la autoría de Cody Gula.

## 1. Crear máquina virtual en AWS

Creamos una VM en [Amazon EC2](https://aws.amazon.com/es/ec2/) utilizando una imagen Debian 12 del tipo t2.micro apta para la capa gratuita, además permitimos el tráfico SSH desde nuestra IP para configurar la máquina.

## 2. Cambiar puerto SSH

Cowrie es un honeypot SSH por lo que no podremos utilizar el puerto 22 una vez lo inicialicemos, por lo tanto cambiaremos el puerto por el cual nos conectaremos por SSH.

Nos conectamos mediante SSH con el siguiente código utilizando la llave correspondiente.

```
ssh -i "keypair.pem" admin@public_ip
```

Editamos el archivo de configuración ubicado en "/etc/ssh/sshd_config". Descomentamos la línea que nos indica el puerto y la cambiamos por ejemplo a 9922.

![Cambio_puerto](/1_Honeypot_Cowrie/images/1.png)

Luego reiniciamos el servicio para aplicar el cambio.

```
sudo systemctl restart ssh
```
