# Internet

1. Pregunta al primer servidor que tenga nuestro pc en **/etc/resolv.conf**
2. Si no tiene la respuesta pregunta al siguiente servidor que esté por encima de él.

Al servidor primero, que va preguntando a niveles superiores, se llama servidor recursivo. A su vez, un servidor DNS con autoridad, es dedir, que responde, también puede ser recursivo. Estos, además, pueden guardar información de direcciones durante un periodo de tiempo. De estaa forma, un servidor DNS puede no volver a preguntar sobre una direccion, que anteriormente ha preguntado, durante un periodo de tiempo.

El tipo de servidor DNS **bind9**, el que vamos a utilizar, es recursivo y con autoridad. Existen otros como **dusmasq** que no es recursivo sino fordward y no tiene autoridad.


# Intranet

Necesito resolver los nombres del dominio de la intranet, en primer lugar, para resolver las direcciones en internet. Además, para que todos los usuarios de una misma intranet resuelvan los nombres. 

El servidor DHCP, además de otorgar la IP, manda la dirección del servidor DNS de la intranet. Este mismo servidor puede ser el único que salga a internet, y de esta forma aumenta la seguridad. Y, puede tener la opción de guardar información de sitios de internet. 


> Ejemplo de una configuración DNS

# Fichero de configuración para la resolución directa
Tenemos estas máquinas:
  iesgn.org	192.168.0.0/24
- macaco	192.168.0.1
- babuino	192.168.0.2
- papion	192.168.0.3
- www
- mysql
- correo	192.168.0.4

Esto se configura de la siguiente manera:
~~~
			iesgn.org
			NS		macaco.iesgn.org
macaco.iesgn.org	A		192.168.0.1
babuino.iesgn.org	A		192.168.0.2
papion.iesgn.org	A		192.168.0.3
correo.iesgn.org	A		192.168.0.4
~~~

El registro NS es el servername, la máquina que domina la zona. Los registros A son los servidores y máquinas. Esta configuración puede guardarse con rutas inclompletas (macaco, babuino, papion...) en vez de rutas completas (macaco.iesgn.org, etc)

Ya están nombrados los equipos. Lo siguiente es nombrar los servicios:
~~~
www.iesgn.org		A		192.168.0.5
~~~

Pero esto no suele hacerse así, sino que se nombra como alias:
~~~
www.iesgn.org		CNAME		babuino.esgn.org
mysql.iesgn.org		CNAME		papion.iesg.org
~~~

De esta forma, si los servicios tiene como dirección máquinas en vez de IPs, si algunas de las máquinas cambia de IP solo hay que modificar uno de los registros, el de la máquina, no el de la máquina y el servicio. 

También se puede añadir direcciones que estén fuera de mi zona:
~~~
pag.iesgn.org 		CNAME		prueba.herokuapp.com
~~~

En la opción anterior, cuando al servidor se le pregunta por pag.iesgn.org responde que está en prueba.herokuapp.com. Cuando se pregunta porprueba.herokuapp.com, como no tiene datos sobre esa dirección, pregunta fuera y ya saldríamos de nuestra intranet. 

Configuración del correo:
~~~
			MX	10	correo.iesgn.org
~~~

Los servidores DNS no comprueban nada, ni que las máquinas estén encendidas, ni que los servicios funcionen correctamente, etc. Simplemente guarda la información de todo eso.


# Fichero de configuración para la resolución inversa
Mientras que en el ejemplo anterior las direcciones son dominios asociados a IPs, en este caso son IPs asociadas a dominios.

Por ejepmlo:
192.168.0.0/24 -- 0.168.192.in-addr.arp

Las direcciones inversas se utiliza en ocasiones para la seguridad. Lo primero que hay que hacer es indicar el servidor que tiene control sobre la zona (NS). 
~~~
				NS	macaco.iesgn.org
1.0.168.192.in-addr.arp		PTR	macaco.iesgn.org
2.0.168.192.in-addr.arp		PTR	babuino.iesgn.org
3.0.168.192.in-addr.arp		PTR	papion.iesgn.org
4.0.168.192.in-addr.arp		PTR	correo.iesgn.org
~~~

Los servicios no tienen resolución inversa. 


