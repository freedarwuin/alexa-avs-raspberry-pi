# Proyecto: Raspberry Pi + Alexa Voice Service

## Acerca del proyecto
Este proyecto demuestra cómo acceder y probar el Alexa Voice Service mediante un cliente Java (que se ejecuta en una Raspberry Pi) y un servidor Node.js. Utilizarás el servidor Node.js para obtener un código de autorización de inicio de sesión con Amazon visitando un sitio web con el navegador web de tu computadora (Raspberry Pi en este caso).

Esta guía proporciona instrucciones paso a paso para obtener el código de muestra, las dependencias y el hardware que necesitas para ejecutar la implementación de referencia en tu Pi.

![](assets/rpi-5.jpg)

___

## Primeros pasos

### Hardware que necesitas

1. **Raspberry Pi 2 (modelo B)** -[Buy at Amazon](http://amzn.com/B00T2U7R7I)
2. **Cable de alimentación micro-USB** para Raspberry Pi (incluido con Raspberry Pi)
3. **Tarjeta Micro SD**: para comenzar a usar Raspberry Pi, necesita un sistema operativo. NOOBS (New Out Of the Box Software) es un administrador de instalación de sistemas operativos fácil de usar para Raspberry Pi. La forma más sencilla de obtener NOOBS es comprar una tarjeta SD con NOOBS preinstalado. [Raspberry Pi 8GB Preloaded (NOOBS) Micro SD Card](https://www.amazon.com/gp/product/B00ENPQ1GK/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1) 
4. Un **cable Ethernet**
5. **Mini micrófono USB 2.0**: Raspberry Pi no tiene un micrófono incorporado; para interactuar con Alexa necesitarás uno externo para conectar. [Buy at Amazon](http://amzn.com/B00IR8R7WQ)
6. Un **teclado y ratón USB** y un **monitor HDMI** externo: también recomendamos tener a mano un teclado y un ratón USB, así como un monitor HDMI, si por alguna razón no puedes conectarte por SSH a tu Raspberry Pi. Más adelante hablaremos más sobre SSH.
7. Adaptador inalámbrico WiFi (opcional) [Buy at Amazon](http://www.amazon.com/CanaKit-Raspberry-Wireless-Adapter-Dongle/dp/B00GFAN498/)

### Habilidades que necesitas

1. Experiencia básica en programación
2. Familiaridad con el shell

---

## 0 - Configuración de la Raspberry Pi
![](assets/raspberry-pi-b-plus3info.jpg)

1. Inserte la tarjeta micro SD con NOOBS preinstalado en la ranura para tarjeta micro SD de su Raspberry Pi. 
![](assets/rpi-3.jpg)
2. Conecte el mini micrófono USB 2.0 y el adaptador inalámbrico WiFi (opcional).
3. Conecte el teclado y el mouse USB.
4. Conecte el monitor mediante el puerto HDMI.

![](assets/rpi-2.jpg)
![](assets/rpi-4.jpg)


## 1 - Arranque de la Raspberry Pi

1. Ahora conecte el cable de alimentación USB a su Pi.
2. Su Raspberry Pi se iniciará y aparecerá una ventana con una lista de diferentes sistemas operativos que puede instalar.
3. Marque la casilla junto a **Raspbian** y haga clic en **Instalar**.

	![](assets/noobs_setup.png)

4. A continuación, Raspbian ejecutará el proceso de instalación. *Nota: esto puede tardar un rato*.
5. Cuando se haya completado el proceso de instalación, se cargará el menú de configuración de Raspberry Pi (raspi-config). Aquí puede configurar la fecha y la hora de su región y habilitar una placa de cámara Raspberry Pi, o incluso crear usuarios. Puede salir de este menú utilizando la tecla Tab en su teclado para ir a **Finalizar**.
	![](assets/raspi-config.jpg)
6. Una vez reiniciado, inicia sesión en tu Raspberry Pi. El nombre de usuario predeterminado para Raspbian es **pi** y la contraseña **raspberry**.

**NOTA**: Para cargar la interfaz gráfica de usuario en cualquier momento, escribe **startx** en la línea de comandos. 

Más información: [raspberrypi.org](https://www.raspberrypi.org/help/noobs-setup/)

___

## 2 - Instalación de utilidades y dependencias

**NOTA**: Utilizarás la utilidad **Terminal** en la Raspberry Pi para instalar las utilidades que necesitas para este tutorial del Servicio de voz de Alexa. La Terminal viene preinstalada en la Raspberry Pi y puedes acceder a ella desde el escritorio. Puedes obtener más información sobre la Terminal [here](https://www.raspberrypi.org/documentation/usage/terminal/).

![](assets/raspberry-pi-terminal-icon.png)

![](assets/raspberry-pi-terminal.png)

### 2.1 - Habilitar SSH en Raspberry Pi
SSH le permite obtener acceso remoto a la línea de comandos de una Raspberry Pi desde otra computadora (siempre que ambas estén en la misma red). Esto elimina el requisito de tener un monitor externo conectado a su Raspberry Pi.

SSH está **habilitado de manera predeterminada** en Raspberry Pi. Si tiene problemas para que funcione SSH, asegúrese de que esté habilitado. Esto se hace usando el [raspi-config](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) utilidad. 

Escriba lo siguiente en la terminal: 

 	sudo raspi-config

Luego navegue hasta SSH, presione Enter y seleccione Habilitar servidor SSH.

![](assets/ssh_raspi-config.png)

### 2.2 - Acceda a la Raspberry Pi por SSH

Ahora, acceda a su Raspberry Pi por SSH. Para ello, debe conocer la dirección IP de su Raspberry Pi.

Escriba este comando en la terminal: 

	hostname -I
	> 192.168.1.10 //Este es un ejemplo de nombre de host de Raspberry Pi, sería diferente para usted

Si está en una PC con Windows, siga las instrucciones aquí para [SSH Using windows](https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md)

Ahora que ya conoce la dirección IP de su Raspberry Pi, está listo para conectarse a ella de forma remota mediante SSH. Para ello, abra la utilidad de terminal en la computadora desde la que desea conectarse y escriba lo siguiente:

	pi@<YOUR Raspberry Pi IP ADDRESS>

Le solicitará su contraseña. *NOTA*: la contraseña predeterminada para el usuario pi es **raspberry**

¡Listo! Ya estás conectado de forma remota a tu Raspberry Pi. Ahora instalarás todas las utilidades mientras estás conectado de forma remota a través de SSH.

### 2.3 Instalar el servidor VNC

VNC es un sistema gráfico de uso compartido del escritorio que te permitirá controlar de forma remota la interfaz del escritorio de tu Raspberry Pi desde otra computadora. Esto te resultará muy útil cuando te deshagas del monitor externo conectado a tu Raspberry Pi.

	sudo apt-get install tightvncserver

**Iniciar el servidor VNC**

Para iniciar el servidor VNC, escriba: 
	tightvncserver

**Ejecutar VNCServer al iniciar**

Quiere asegurarse de que el servidor VNC se ejecute automáticamente después de reiniciar Raspberry Pi, de modo que no tenga que iniciarlo manualmente cada vez con el comando *tightvncserver* a través de SSH. Para ello, escriba lo siguiente en la terminal:

	cd /home/pi
	cd .config

Tenga en cuenta el "." al comienzo del nombre de la carpeta. Esto la convierte en una carpeta oculta que no aparecerá cuando escriba "ls".

	mkdir autostart
	cd autostart

Cree una nueva configuración escribiendo el siguiente comando:

	nano tightvnc.desktop

Edite el contenido del archivo con el siguiente texto:

	[Desktop Entry]
	Type=Application
	Name=TightVNC
	Exec=vncserver :1
	StartupNotify=false

Escriba **ctrl-X** y luego **Y** para guardar los cambios en el archivo.

Eso es todo. La próxima vez que reinicie, el servidor VNC se reiniciará automáticamente.

**Conexión a Raspberry Pi a través de VNC**

- **Mac**: Ver https://www.raspberrypi.org/documentation/remote-access/vnc/mac.md
- **Windows**: https://www.raspberrypi.org/documentation/remote-access/vnc/windows.md
- **Linux**: https://www.raspberrypi.org/documentation/remote-access/vnc/linux.md

**Ahora puedes desconectar el monitor, el teclado y el mouse (si lo deseas)**. Ahora que tienes instalado SSH (permite el acceso remoto a la terminal) y VNC (permite controlar de forma remota la interfaz de escritorio de Raspberry Pi), el monitor externo es opcional. Puedes desconectarlo de Raspberry Pi sin problemas.

### 2.4 - Instalar VLC

Obtenga el reproductor multimedia VLC escribiendo:

	sudo apt-get install vlc-nox vlc-data


**NOTA**: Si está utilizando Raspberry Pi y ya tiene VLC instalado, deberá eliminar dos bibliotecas en conflicto ejecutando los siguientes comandos:

	sudo apt-get remove --purge vlc-plugin-notify
	sudo rm /usr/lib/vlc/plugins/codec/libsdl_image_plugin.so

**Errores de no poder obtener la información**
Si te encuentras con algunos errores de "No poder obtener la información" al intentar instalar VLC, prueba lo siguiente: 	

	sudo apt-get update
	sudo apt-get upgrade
	sudo apt-get install vlc-nox vlc-data

Fuente: https://www.raspberrypi.org/forums/viewtopic.php?f=66&t=67399

**Asegúrese de que VLC esté instalado correctamente**

	whereis vlc

Esto le dirá dónde está instalado VLC.

La mayoría de los programas se almacenan en `/usr/bin`. En mi Raspberry Pi, veo:

	vlc: /usr/bin/vlc /usr/lib/vlc /usr/share/vlc /usr/share/man/man1/vlc.1.gz

**Establecer las variables de entorno para VLC**

Escriba lo siguiente en la terminal:

	export LD_LIBRARY_PATH=/usr/lib/vlc
	export VLC_PLUGIN_PATH=/usr/lib/vlc/plugins

**Comprueba si las variables de entorno se configuraron correctamente**

	echo $LD_LIBRARY_PATH
	> /usr/lib/vlc
	
	echo $VLC_PLUGIN_PATH
	> /usr/lib/vlc/plugins


### 2.5 Descargue e instale Node.js

Verifique que Node no esté instalado. Debería aparecer el mensaje "comando no encontrado".

	node —version
	> command not found

Ahora escribe: 

	sudo apt-get update 
	sudo apt-get upgrade

Configurar la fuente del repositorio apt-get:

	curl -sL https://deb.nodesource.com/setup | sudo bash -

Instalar Node en sí:

	sudo apt-get install nodejs	

### 2.6 Instalar el kit de desarrollo de Java

Debe tener instalado en la Raspberry Pi la versión 8 o superior del Java Development Kit (JDK).

**Paso 1: Descargar JDK**
Suponiendo que se trata de una Raspberry Pi nueva y que aún no tiene instalado el JDK, deberá descargar el JDK 8 desde [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). El binario que estás buscando es **Linux ARM 32 Hard Float ABI**.

Descargue el archivo tar.gz jdk-8u73-linux-arm32-vfp-hflt.tar.gz desde el enlace de Oracle anterior.

**NOTA**: Si bien existe un ARMv8 de 64 bits que Apple y otros teléfonos inteligentes utilizan, aún no hay procesadores ARM de 64 bits para Raspberry Pi en los PSI. Más información: [Raspberry Piblog.com](http://www.Raspberry Piblog.com/2014/03/installing-oracle-jdk-8-on-raspberry-pi.html)

**Paso 2: Extraer el contenido**
Extraiga el contenido del archivo tar al directorio /opt:

	sudo tar zxvf jdk-8u73-linux-arm32-vfp-hflt.tar.gz -C /opt

Establezca java y javac predeterminados en el nuevo jdk8 instalado.

	sudo update-alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_73/bin/javac 1
	
	sudo update-alternatives --install /usr/bin/java java /opt/jdk1.8.0_73/bin/java 1

	sudo update-alternatives --config javac
	sudo update-alternatives --config java

**NOTA**: Si se le pide que elija una alternativa, escriba el número correspondiente a la versión de jdk que acaba de instalar, por ejemplo, jdk1.8.0_73

Ahora verifique los comandos con la opción -version:

	java -version
	javac -version

### 2.7 Instalar Maven

**Paso 1: Descargar Maven**

Descarga el archivo binario tar.gz apache-maven-3.3.9-bin.tar.gz desde https://maven.apache.org/download.cgi 

**Paso 2: Extraer el contenido**
Extraiga el contenido del archivo tar al /opt directory

	sudo tar zxvf apache-maven-3.3.9-bin.tar.gz	-C /opt

**Paso 3: Indica a tu shell dónde encontrar Maven**
Harás esto en la configuración del perfil del sistema para que esté disponible para todos los usuarios.

Cree un nuevo archivo /etc/profile.d/maven.sh y escriba lo siguiente dentro de él:

	export M2_HOME=/opt/apache-maven-3.3.9
	export PATH=$PATH:$M2_HOME/bin

Guarde el archivo. Cierre sesión y vuelva a iniciarla en Raspberry Pi para que el script de perfil surta efecto. Puede probar que funciona con el siguiente comando:

	mvn -version

---

## 3 - Introducción al servicio de voz de Alexa

### 3.1 Regístrese para obtener una cuenta gratuita de desarrollador de Amazon
[Get a free Amazon developer account](https://developer.amazon.com/login.html) Si aún no tienes uno.

![](assets/login-amazon-dev-portal.png)
### 3.2 Descargue el código de la aplicación de muestra y las dependencias en Raspberry Pi

[Download](https://github.com/amzn/alexa-avs-raspberry-pi.git) El archivo zip de aplicaciones de muestra. Al descargar este paquete, acepta los términos y condiciones. [Alexa Voice Service Agreement](https://developer.amazon.com/edw/avs_agreement.html).

### 3.3 Copia y expande el archivo .zip en tu Raspberry Pi

1. A menos que hayas descargado el archivo zip directamente en tu Raspberry Pi, copia y luego expande el archivo zip en tu Raspberry Pi.
2. Toma nota de su ubicación en tu Raspberry Pi. En las instrucciones posteriores, esta ubicación se mencionará como \<REFERENCE_IMPLEMENTATION>

![](assets/sample-code-file-list.png)

### 3.4 Registre su producto y cree un perfil de seguridad.

1. Inicie sesión en el Portal para desarrolladores de Amazon - [developer.amazon.com](https://developer.amazon.com/)
2. Haga clic en la pestaña Aplicaciones y servicios -> Alexa -> Servicio de voz de Alexa -> Comenzar
![](assets/avs-navigation.png)
3. En el menú Registrar un tipo de producto, seleccione **Dispositivo**.
	![](assets/avs-choose-device.png)
4. Complete y guarde los siguientes valores:

**Información del tipo de dispositivo**

1. ID del tipo de dispositivo: **my_device**
2. Nombre para mostrar: **My Device**
3. Haga clic en **Next**

![](assets/avs-device-type-info.png)

**Perfil de seguridad**

1. Haga clic en el menú desplegable Perfil de seguridad y seleccione “**Crear un nuevo perfil**”
	![](assets/avs-create-new-security-profile.png)

2. **Pestaña General**
- **Nombre del perfil de seguridad**: Perfil de seguridad de la aplicación de muestra del servicio de voz de Alexa
- **Descripción del perfil de seguridad**: Descripción del perfil de seguridad de la aplicación de muestra del servicio de voz de Alexa
- Haga clic en **Siguiente**
	
	![](assets/avs-security-profile.png)
	
Client ID and Client Secret will be generated for you. 
	
![](assets/avs-security-profile-creds.png)

3. Now click on the **Web Settings Tab**
	- Make sure the security profile you just created is selected in the drop-down menu, then click the **"Edit"** button.

	![](assets/avs-web-settings.png)
	- **Allowed Origins**: Click "**Add Another**" and then enter **https://localhost:3000** in the text field that appears.
	- **Allowed Return URLs**: Click "Add Another" and then enter **https://localhost:3000/authresponse** in the text field that appears.
	- Click **Next**

![](assets/avs-web-settings-filled.png)

**Device Details**

1. Image: Save the following test image to your computer, then upload it:
![](https://developer.amazon.com/public/binaries/content/gallery/developerportalpublic/solutions/alexa/alexa-voice-service/images/reference-implementation-image.png)
2. Category: **Other**
3. Description: **Alexa Voice Service sample app test**
4. What is your expected timeline for commercialization?: **Longer than 4 months / TBD**
5. How many devices are you planning to commercialize?: **0**
6. Click **Next**

![](assets/avs-device-details-filled.png)

**Amazon Music**

1. Enable Amazon Music?: No (You may optionally select Yes and fill in the required fields if you want to experiment with Amazon Music. However, Amazon Music is not required for basic use of the Alexa Voice Service.)
2. Click the Submit button

![](assets/avs-amazon-music.png)

![](assets/avs-your-device.png)

You are now ready to generate self-signed certificates.

___

## 4 - Generate self-signed certificates.

**Step 1: Install SSL**

	sudo apt-get install openssl


**Verify install**

	whereis openssl
	> openssl: /usr/bin/openssl /usr/share/man/man1/openssl.lssl.gz

Change directories to \<REFERENCE_IMPLEMENTATION>/samples/javaclient.

	cd <REFERENCE_IMPLEMENTATION>/samples/javaclient - //your sample apps location


**Step 2**: Edit the text file ssl.cnf, which is an SSL configuration file. Fill in appropriate values in place of the placeholder text that starts with YOUR_. 

Note that **countryName** must be two characters. If it is not two characters, certificate creation will fail. 

**Step 3**: Make the certificate generation script executable by typing:

	chmod +x generate.sh

**Step 4**: Run the certificate generation script:
	
	./generate.sh
	
**Step 5**: You will be prompted for some information:

1. When prompted for a product ID, enter **my_device**
2. When prompted for a serial number, enter **123456**
3. When prompted for a password, enter any password and remember what you entered 
	1. Password: **talktome** (you can even leave it blank)


**Step 6: Edit the configuration file for the Node.js server**  

The configuration file is located at: 

	<REFERENCE_IMPLEMENTATION>/samples/companionService/config.js. 

Make the following changes:

- Set **sslKey** to \<REFERENCE_IMPLEMENTATION>/samples/javaclient/certs/server/node.key
- Set **sslCert** to \<REFERENCE_IMPLEMENTATION>/samples/javaclient/certs/server/node.crt
- Set **sslCaCert** to \<REFERENCE_IMPLEMENTATION>/samples/javaclient/certs/ca/ca.crt

**IMP**: **Do not** use **~** to denote the home directory. Use the absolute path instead. So, instead of ~/documents/samples, use /home/pi/documents/samples.

**Step 7: Edit the configuration file for the Java client** 

The configuration file is located at: 

	<REFERENCE_IMPLEMENTATION>/samples/javaclient/config.json. 

Make the following changes:

- Set **companionApp.sslKeyStore** to \<REFERENCE_IMPLEMENTATION>/samples/javaclient/certs/server/jetty.pkcs12
- Set **companionApp.sslKeyStorePassphrase** to the passphrase entered in the certificate generation script in step 5 above.
- Set **companionService.sslClientKeyStore** to \<REFERENCE_IMPLEMENTATION>/samples/javaclient/certs/client/client.pkcs12
- Set **companionService.sslClientKeyStorePassphrase** to the passphrase entered in the certificate generation script in step 5 above.
- Set **companionService.sslCaCert** to \<REFERENCE_IMPLEMENTATION>/samples/javaclient/certs/ca/ca.crt

---

## 5 - Install the dependencies

Change directories to \<REFERENCE_IMPLEMENTATION>/samples/companionService

	cd <REFERENCE_IMPLEMENTATION>/samples/companionService

Install the dependencies by typing:

	npm install

___

## 6 - Enable Security Profile

1. Open a web browser, and visit [https://developer.amazon.com/lwa/sp/overview.html](https://developer.amazon.com/lwa/sp/overview.html).
![](assets/avs-lwa-new-security-profile.png)

2. Near the top of the page, select the security profile you created earlier from the drop down menu and click **Confirm**.
![](assets/avs-lwa-choose-security-profile.png)
3. Enter a privacy policy URL beginning with http:// or https://. For this example, you can enter a fake URL such as http://example.com. 
4. [Optional] You may upload an image as well. The image will be shown on the Login with Amazon consent page to give your users context. 
5. Click Save.

	![](assets/avs-privacy-url.png)

6. Next to the Alexa Voice Service Sample App Security Profile, click Show Client ID and Client Secret. This will display your client ID and client secret. Save these values. You’ll need these. 
![](assets/avs-show-creds.png)
![](assets/avs-view-security-profile-creds.png)

## 7 - Updating the config files

**Login to the Raspberry Pi via VNC**

**Step 1: Update config.js**
Navigate to the following file and open it in a text editor.

-

	<REFERENCE_IMPLEMENTATION>/samples/companionService/config.js 	

![](assets/rpi-open-text-editor.png)
Edit the following values in this file -

- **clientId**: Paste in the client ID that you noted in the previous step as a string.
- **clientSecret**: Paste in the client secret that you noted in the previous step as a string.
- **products**: The product's object consists of a key that should be the same as the product type ID that you set up in the developer portal and a value that is an array of unique product identifiers. If you followed the instructions above, the product type ID should be my_device. The unique product identifier can be any alphanumeric string, such as 123456. Example products JSON is: `products: {"my_device": ["123456"]}`

![](assets/avs-config.js.png)

**Save** the file.

**Step 2: Update config.json**
Navigate to the following file, and open it in  a text editor.

-

	<REFERENCE_IMPLEMENTATION>/samples/javaclient/config.json	

Edit the following values in this file:

- **productId**: Enter **my_device** as a string.
- **dsn**: Enter the alphanumeric string that you used for the unique product identifier in the products object in the server's config.js. For example: **123456**.
- **provisioningMethod**: Enter **companionService**.

![](assets/avs-config.json.png)

**Save** the file.

**Step 3: Preparing the pom.xml file**

Navigate to the following file and open it in a text editor.

-

	<REFERENCE_IMPLEMENTATION>/samples/javaclient/pom.xml	

Add the following to the pom.xml in the **< dependencies >** section:

	<dependency>
	  <groupId>net.java.dev.jna</groupId>
	  <artifactId>jna</artifactId>
	  <version>4.1.0</version>
	  <scope>compile</scope>
	</dependency>

![](assets/avs-pom-xml.png)
___

## 8 - Run the server

**Login to the Raspberry Pi via VNC**

In your terminal window or from the command prompt, type: 

	cd <REFERENCE_IMPLEMENTATION>/samples/companionService
	npm start

![](assets/start-server.png)
![](assets/server-running.png)

The server is now running on port 3000 and you are ready to start the client.

___

## 9 - Start the client

Open a new terminal window/tab (SHIFT+CTRL+TAB in Raspbian)

![](assets/start-client.png)

	cd <REFERENCE_IMPLEMENTATION>/samples/javaclient


**Upgrade your Java version**

Make the script executable by typing:

	chmod +x generate.sh

Run the installation script:
	
	./install-java8.sh

![](assets/avs-upgrade-java.png)

![](assets/java-installation-tos-1.png)

You will get a message from Oracle Java installer that you must accept the Terms of Service for Java SE Platform, press Enter.

![](assets/java-installation-tos-2.png)

Press **Tab**, and then **Enter** to say “**Yes**” to the Terms of Service.

**Build the app**

Before you build the app, let’s validate to make sure the project is correct and that all necessary information is available. You do that by running:

	mvn validate

![](assets/mvn-validate.png)

Download dependencies and build the app by typing: 

	mvn install	

When the installation is completed, you will see a “Build Success” message in the terminal. 

![](assets/mvn-install-success.png)

**Run the client app**:

You are now ready to run the client app by typing:

	mvn exec:exec


## 10 - Obtain Authorization from Login with Amazon

1. When you run the client, a window should pop up with a message that says something similar to:  

	*Please register your device by visiting the following website on any system and following the instructions: https://localhost:3000/provision/d340f629bd685deeff28a917 Hit OK once completed*.

	![](assets/client-running.png)

	**Copy** the URL from the popup window and **paste** it into a **web browser**. In this example, the URL to copy and paste is https://localhost:3000/provision/d340f629bd685deeff28a917. 

	![](assets/paste-url-browser.png)
**NOTE:** Due to the use of a self-signed certificate, you will see a warning about an insecure website. This is expected. It is safe to ignore the warnings during testing.

2. You will be taken to a Login with Amazon web page. Enter your Amazon credentials.

	![](assets/lwa-signin.png)
	
3. You will be taken to a Dev Authorization page, confirming that you’d like your device to access the Security Profile created earlier. 
	
	![](assets/avs-device-permission.png)
	
	Click **Okay**. 
	
4. You will now be redirected to a URL beginning with https://localhost:3000/authresponse followed by a query string. The body of the web page will say **device tokens ready**.

	![](assets/avs-device-tokens-ready.png)

5. **Return to the Java application** and click the OK button. The client is now ready to accept Alexa requests.
	![](assets/avs-click-ok.png)

6. Click the **Start Listening** button and wait for the **audio cue** before beginning to speak. It may take a second or two for the connection to be made before you hear the audio cue.

	![](assets/avs-start-listening.png)
	
Press the **Stop Listening** button when you are done speaking.


![](assets/avs-stop-listening.png)	

___

## Let’s talk to Alexa  

**Ask for Weather**: 
*Click the Start Listening button*.
**You**: What's the weather in Seattle?
*Click the Stop Listening button*.
**Alexa**: Current weather report for Seattle
	
**Some other fun questions you can ask Alexa**

Once you hear the audio cue after clicking “Start Listening” button, here are a few things you can try saying -

- **Request Music Playback**: Play Bruce Springsteen
- **General Knowledge**: What's the mass of the sun in grams?
- **Geek**: What are the three laws of robotics?
- **Fun**: Can you rap?
- **Set a Timer**: Set the timer for 2 minutes.
- **Set Alarm**: Set the alarm for 7:30 a.m.

**More on Music Playback**
The "previous", "play/pause", and "next" buttons at the bottom of the Java client UI are to demonstrate the music button events. Music button events allow you to initiate changes in the playback stream without having to speak to Alexa. For example, you can press the "play/pause" button to pause and restart a track of music. 

To demonstrate the "play/pause" button, you can speak the following command: Play DC101 on iHeartRadio, then press the "play/pause" button. The music will pause in response to the button click. Press the "play/pause" button again to restart the music.

___

## 11 - FAQs

### I got the Raspberry Pi working with AVS, but I can’t hear the audio response from Alexa

Check to see if you are seeing the response coming through on the Terminal and if you see the response cards on your Alexa app. If yes, you probably need to force audio through local 3.5mm jack, instead of the HDMI output (this can happen even if you don’t have an HDMI monitor plugged in). 

To force audio through local 3.5 mm jack, pen Terminal, and type

	sudo raspi-config 

See [Raspberry Pi Audio Configuration](https://www.raspberrypi.org/documentation/configuration/audio-config.md)


### How do I find the IP address of my Raspberry Pi?
	
	hostname -I
	
### Unable to fetch errors - 
If you run into some "Unable to fetch" errors while trying to install VLC, try the following - 	

	sudo apt-get update
	sudo apt-get upgrade
	sudo apt-get install vlc-nox vlc-data