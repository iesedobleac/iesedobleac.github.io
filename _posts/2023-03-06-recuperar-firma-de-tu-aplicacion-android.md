---
layout: post
title: "Recuperar la firma de tu aplicación Android"
author: isaac
categories: [Android]
image: assets/images/post_images/recover_key_cover.webp
---

Hace unos años, si perdías la llave firma de tu aplicación de Google Play, tenías que darla por perdida, por lo que nunca más podías actualizarla.
Por suerte, **hoy en día, Google ya ofrece una solución a esto** y a continuación te explico como hacerlo por si te ves en un caso similar, que
no cunda el pánico ⌛🫠

**Antes de comenzar: esta guía ha sido creada usando el sistema operativo Windows 10**, en otros sistemas debería ser similar, pero variando los
atajos de teclado y las rutas de carpetas. Ha sido creada en la misma fecha del post, por lo que no te aseguro que dentro de un año o unos meses
a Google le de por cambiar el procedimiento 🤔

### El formulario de Google

Google nos ofrece <a href="https://support.google.com/googleplay/android-developer/contact/key">un formulario de contacto</a> para estos casos.
Es importante que saber que formulario lo tendremos que rellenar en **alguno de los siguientes idiomas: inglés, chino, japonés o coreano**; de
lo contrario, es probable que no recibamos respuesta 💡

{% include images.html url="https://i.imgur.com/fZpKTl4.png" description="Formulario de apelación de firma" %}

### **Datos personales**

Tenemos que rellenar este apartado con nuestros datos personales. **Deben de ser los que tenemos asociados a la cuenta de desarrollador donde se
encuentra alojada la aplicación:**

- **First name:** nuestro nombre.
- **Last name:** nuestros apellidos.
- **Email address:** cuenta de correo asociada.
- **Your location:** el país asociado a tu cuenta.

### Datos de desarrollador

Aquí te pide datos de tu cuenta de desarrollador. **Es importante que todo esté correcto**, ya que es la forma que tienen de identificar que somos
los propietarios de la aplicación que queremos recuperar:

- **Developer name:** tu nombre de desarrollador que aparece en Google Play.
- **Developer account ID:** el ID de tu cuenta de desarrollador. Búscalo en la consola de Google Play, en el apartado Información de la cuenta,
  o desde <a href=" https://play.google.com/console/developers/contact-details">este enlace.</a>
- **App package name:** el id o nombre del paquete de nuestra aplicación que estamos tratando de recuperar.

### Firma de Google Play

En el siguiente apartado nos pregunta si nuestra aplicación está inscrita en **Play App Signing by Google Play**, que citando a la propia documentación
de Google:

> Google usa la firma de aplicaciones de Play para gestionar y proteger la clave de firma de tu aplicación, así como para firmar los APKs de distribución
> optimizados que se generan a partir de tus app bundles. La firma de aplicaciones de Play almacena la clave de firma de aplicación en la infraestructura
> segura de Google y ofrece opciones de actualización para aumentar la seguridad.

Puedes comprobar esta información **desde la consola de Google Play, en el apartado _Integridad de la aplicación_** 💡

![](https://i.imgur.com/1NHFBPn.jpg)

**Debemos tener marcado el tic verde que dice: _Versiones firmadas por Google Play_**, ya que de lo contrario, no podremos seguir con el resto de la guía.

A continuación, tienes que marcar el motivo por el cual necesitas asistencia, en este caso **marca _I have an upload-key-related issue_ y a continuación
_I lost my upload key_.**

#### **Generando la nueva firma**

Cuando seleccionamos el motivo de la incidencia, vamos a ver unas instrucciones que se van a desplegar.

> Please generate a new upload key in .pem format and attach it here; we cannot process a key in any other format. Here’s how:
>
> Follow the instructions in the Android Studio Help Center to generate a new key. It must be different from any previous keys. Alternatively, you can use the following command line to generate a new key:
>
> keytool -genkeypair -alias upload -keyalg RSA -keysize 2048 -validity 9125 -keystore keystore.jks (\*This key must be a 2048 bit RSA key and have 25-year validity.)
>
> Export the certificate for that key to PEM format:
>
> keytool -export -rfc -alias upload -file upload_certificate.pem -keystore keystore.jks

**Se nos pide generar una nueva firma.** Se puede hacer a través de comandos, aunque yo prefiero hacerlo a través de Android Studio. **Para esto vamos a irnos al IDE 
y vamos a generar una nueva firma** 📋

{% include images.html url="https://i.imgur.com/u3PJ46c.png" description="Opción para generar la firma desde Android Studio" %}

{% include images.html url="https://i.imgur.com/qviZFgu.png" description="Plantilla de datos vacía" %}

Rellenamos todos los datos requeridos para generar la firma y nos aseguramos de guardar los archivos resultantes en un lugar seguro donde no se vayan
a perder, **haz tantas copias como sean necesarias**.

#### **El archivo PEM**

> PEM es un formato de archivo contenedor que se utiliza a menudo para almacenar claves criptográficas. Se utiliza para muchas cosas diferentes, ya que simplemente
> define la estructura y el tipo de codificación del archivo utilizado para almacenar algunos datos

Para generar el archivo que tiene que adjuntar en el formulario, **necesitamos la herramienta** `keytool`, que si tienes instalado el SDK de Java, 
la tienes disponible en la siguiente ruta 🛣️

```
C:\Program Files\Java\jre1.8.0_331\bin
```

**Vamos a copiar la firma generada a esta carpeta**, de lo contrario puede que la consola nos dé algún tipo de error de permisos.

Una vez copiada vamos a hacer `shift + click` dentro de la carpeta y seleccionamos la opción "Abrir la ventana de PowerShell aquí" desde ahí.

![](https://i.imgur.com/2GZiNlc.png)

Una vez estamos en esta ruta con el terminal abierto **introducimos el siguiente comando:**

```bash
keytool -export -rfc -alias key_store_alias -file upload_certificate.pem -keystore key_store_name.jks
```

**Te pedirá la contraseña del almacén de claves** y, una vez introducida, generará el archivo en formato PEM que necesitas para adjuntar al reporte 📄

Solo queda por cumplimentar el campo donde tienes decir el motivo del reporte. No hay que poner nada muy elaborado. **Puedes poner algo similar a:**

> Hello,
>
> I wanted to update my application, but I noticed that I can't find the key signature to generate it from Android Studio, and I would like to generate a new one and update it.
>
> Thank you very much,
> Regards.

**No es necesario adjuntar ninguna captura**. Dale a Submit y espera a recibir una respuesta.

HDesde que recibes la confirmación de que la firma ya ha sido actualizada, hasta que puedas subir una release con la nueva, **pueden pasar hasta 48 horas** ⌛

## Respuesta de Google

![](https://i.imgur.com/zyVBGVu.jpg =1000x)

**A los días recibirás una respuesta de Google** parecida a esta en la que indica los nuevos datos del certificado y una fecha a partir de la 
cual podrás actualizar nuevamente la app.

**Si intentas subir la aplicación firmada antes de ese tiempo**, mostrará un error en Google Play Console indicandote que la firma ha sido cambiada recientemente, y 
que hay que esperar hasta el día que índica el correo.
