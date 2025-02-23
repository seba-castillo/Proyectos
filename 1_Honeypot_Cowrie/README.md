# Honeypot Cowrie

Proyecto personal basado en la autoría de Cody Gula.

## 1. Crear máquina virtual en AWS

Creamos una VM en [Amazon EC2](https://aws.amazon.com/es/ec2/) utilizando una imagen Debian 12 del tipo t2.micro apta para la capa gratuita, además permitimos el tráfico SSH desde nuestra IP para configurar la máquina.

Nos conectamos mediante SSH con el siguiente código utilizando la llave correspondiente.

```
ssh -i "keypair.pem" admin@public_ip
```
