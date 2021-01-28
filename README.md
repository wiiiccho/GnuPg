<div align="center">
  <h1>¿Qué es GnuPg?</h1>
</div>

<div align="center">
  <p>
Gpg es el acrónimo de GNU Privacy Guard, y es un sustituto del PGP (Pretty Good Privacy) creado 
por Werner Koch como software libre multiplataforma para el cifrado simétrico y asimétrico.
  </p>
</div>

## ¿Qué es la criptografía asimétrica?
<div align="center">
  <p>
Es un método para cifrar datos en el que existe un par de claves asociadas entre sí: La clave pública y la clave privada.
Es necesario un par de claves por cada usuario para lograr una comunicación en los dos sentidos.

La pública se puede compartir con tantas personas como deseemos, y sirve para cifrar los mensajes.

La privada es aquella a la que sólo el propietario de los datos cifrados puede tener acceso, por tanto no podemos compartirla con nadie. Esta clave únicamente puede descifrar aquellos datos que fueron cifrados con su clave pública asociada. Es además un clave única, el algoritmo para generarla garantiza que no puede generarse otra igual.

Un ejemplo de uso sería el siguiente: Alguien desea enviarnos información de acceso a un servicio web, por lo tanto información muy sensible, y para ello cifra un archivo de texto con dicha información usando nuestra clave pública (que previamente hemos compartido con él).

De este modo, cuando nos mande el fichero por correo electrónico solo nosotros podremos descifrar su contenido gracias a que sólo nosotros poseemos la clave privada asociada a la pública con que fue cifrado. No importaría si el correo es interceptado porque no tendrían la clave privada.

Por lo tanto la principal ventaja de la criptografía asimétrica frente a la clásica criptografía simétrica (la misma clave se usa la cifrar y también para descifrar), es que nunca compartimos la clave que descifra el mensaje.

  </p>
</div>


<div align="center">
  <p>
### Generar claves pública y privada

Para poder cifrar asimétricamente primero tenemos que crear la pareja de claves (pública y privada) con el siguiente comando
Y el siguiente paso es rellenar información personal para identificar la clave: Nombre, email y un comentario opcional.

Para terminar debemos poner una clave(passphrase) para proteger la clave privada.
  </p>
</div>

```sh
gpg --gen-key
```

<div align="center">
  <p>
Tras generar las claves podemos verlas con el siguiente comando, que nos muestra nuestro anillo de claves, lo importante de este paso es que veremos la identificación de cada una, que es necesaria para poderlas exportar y enviar.
  </p>
</div>

```sh
gpg -k
```
<div align="center">
  <p>
Habitualmente vamos a ejecutar este comando para ver el identificador alfanumérico de la clave pública, que viene indicado justo después de la palabra pub, un carácter y el número de bits que posee. También podemos ver el hash de la clave privada después de la palabra sub. Sub, en realidad hace referencia a subclave privada. Y el [ID de a clave pública] que estaria por debajo de la llave publica.
  </p>
</div>

```sh
pub   rsa3072 1212-01-00 [SC] [caduca: 1210-01-00]
      39451213212312312312312121231231231322B4D
uid        [  absoluta ] nolose
sub   rsa3072 1212-01-19 [E] [caduca: 1000-01-19]
```




<div align="center">
  <p>
### Crear certificado de revocación
Es un fichero que generamos como medida de seguridad para que ante la situación de haber olvidado la passphrase de nuestra clave privada o si ha sido comprometida, podamos bloquear el uso de la clave pública asociada. Por ello recomiendo generar uno justo después de haber creado nuestro par de claves, aunque podríamos hacerlo en cualquier momento si tenemos acceso a la clave privada y su passphrase.

Al igual que la clave privada hay que mantenerlo en un lugar seguro, pues quién lo posea tiene el poder de cancelar tus claves. No obstante no sirve para cifrar ni descifrar mensajes.

Lo generamos así:

  </p>
</div>

```sh
gpg -a --output cert-revocacion.txt --gen-revoke XXXXXXXX

// la opción -a es opcional, para generar el certificado en ascii, para poder guardarlo en 
un gestor de contraseñas, por ejemplo //XXXXXXXX puede ser el identificador de clave pública 
o el de la clave privada, ambos identifican el par de claves que se revocaría el certificado.
```

<div align="center">
  <p>
**Revocar el par de claves**
Para revocar efectivamente un par de claves, simplemente tenemos que importar a nuestro llavero el certificado de revocación y después subir la clave pública al servidor de claves.
  </p>
</div>


```sh
gpg --import cert-revocacion.txt
gpg --send-keys --keyserver urlservidor XXXXXXXX
```

Una vez hemos revocado una clave, si hacemos un listado veremos que sale indicado del siguiente modo:

```sh
pub   2048R/XXXXXXXX 2018-12-16 [revoked: 2018-12-17]
uid                  prueba2 (dd) <prueba2@gmail.com>
```


<div align="center">
  <p>
**Exportar y enviar la clave prublica**
El objetivo de esta pareja de claves es que cualquiera nos pueda mandar un archivo cifrado que solo veremos nosotros y esto se hace difundiendo la clave pública que acabamos de crear (la pública, nunca la privada), para exportarla en un archivo usaremos el comando gpg -output [archivo destino] --export [ID de a clave pública] (la clave pública generada antes tiene la ID 18384645).
  </p>
</div>

```sh
gpg --output CPub.gpg --export 18384645
ls
```

