# Honeypot Cowrie Dashboard

Proyecto en que se visualizan logs de honeypot Cowrie en una instancia en AWS mediante ELK.

###### Basado en la documentación oficial.

Revisión previa: [Configurar Honeypot Cowrie](/Honeypot_Cowrie/README.md).

# 1. Crear máquina virtual en AWS

En este caso utilizaremos una instancia distinta al honeypot Cowrie para revisar los logs. Creamos una VM en [Amazon EC2](https://aws.amazon.com/es/ec2/) utilizando una imagen Debian 12 del tipo t2.micro apta para la capa gratuita, además permitimos el tráfico SSH desde nuestra IP para configurar la máquina.

# 2. Instalar ElasticSearch

```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt update -y
sudo apt-get install openjdk-8-jdk -y
```

```
version java
```
