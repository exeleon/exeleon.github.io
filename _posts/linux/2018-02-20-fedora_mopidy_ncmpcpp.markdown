---
layout: post
title:  "Instalar Mopidy(MPD) + Spotify + ncmpcpp en Fedora 27"
date:   2018-02-20 16:21:30 -0500
categories: linux
comments: true
excerpt: 
---

### Pre-requisitos:

Verifíca que tengas la versión 2.7 de Python:

`python --version`

Para descargar Mopidy usarémos `pip`, posiblemente ya lo tengas instalado, de lo contrario usa:

`sudo yum install -y gcc python-devel python-pip`

Instala GStreamer:

`sudo yum install -y python-gstreamer1 gstreamer1-plugins-good gstreamer1-plugins-ugly-free`

### 1. Instalar Mopidy

`pip install -U mopidy`

### 2. Instalar libspotify (obsoleta)

Descargar la librería oficial de Spotify [libspotify][libspotify], descomprimir e instalar:

`wget http://developer.spotify.com/download/libspotify/libspotify-12.1.51-Linux-x86_64-release.tar.gz`

`tar -xvzf libspotify-12.1.51-Linux-x86_64-release.tar.gz`

`rm libspotify-12.1.51-Linux-x86_64-release.tar.gz`

`cd libspotify-12.1.51-Linux-x86_64-release`

`sudo make install prefix=/usr/local`

Ve directo al paso 3, si la instalación de Mopidy-Spotify falla, regresa al paso 2.1 para agregar libspotify al PATH de librerias. 

#### 2.1 Configurar PKG_CONFIG_PATH para libspotify

`ldconfig -v 2>&1 | grep /usr/local`

Si el comando anterior no arroja ningún resultado, debes crear el siguiente archivo con la única linea `/usr/local/lib`:

```
cat <<EOF | sudo tee /etc/ld.so.conf.d/local.conf
/usr/local/lib
EOF
```

Vuelve a ejecutar el comando `ldconfig` anterior y debes ver una salida que es precisamente `/usr/local/lib`.

Busca la ubicación de libspotify:

`find /usr/local -name libspotify.pc`

El comando anterior te debe arrojar un resultado como `/usr/local/lib/pkgconfig/libspotify.pc`; u otro dependiendo de la ubicación donde esta alojado `libspotify.pc`. Ten presente ésta ubicación para usarla en breve si es necesario. 

El siguiente comando te debe arrojar una salida, indicando que libspotify está agregada a PKG_CONFIG_PATH:
`pkg-config --list-all | grep libspotify`

Si el comando anterior no mostró resultados debes agregar el directorio donde se encuentra `libspotify.pc` a la variable de entorno PKG_CONFIG_PATH, en mi caso `/usr/local/lib/pkgconfig`:

`export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH`

### 3. Instalar la extensión de Spotify para Mopidy

`pip install Mopidy-Spotify`

### 4. Instalar ncmpcpp

`sudo yum install ncmpcpp`

### 5. Configuración
Esta configuración no corre Mopidy como servicio, esto significa que siempre que desees usarlo debes ejecutarlo explicitamente. Para configurar Mopidy como servicio sigue la [guia oficial][mopidy_guide].

En un terminal ejecuta `mopidy`, esto crea un archivo de configuración por defecto si no existía ninguno. Abre este archivo con cualquier editor:

`vi ~/.config/mopidy/mopidy.conf`

y agrega al final las creadenciales y otros ajustes de Spotify como se menciona en la [guía oficial del plugin][mopidy_spotify_conf]:

```
[spotify]
username = SPOTIFY_USERNAME
password = SPOTIFY_PASSWORD
client_id = client_id value you got from mopidy.com
client_secret = client_secret value you got from mopidy.com
bitrate = 320
```

Vuelve a ejecutar `mopidy`, y en otra terminal ejecuta `ncmpcpp`. Ingresa al sitio de [ncmpcpp][ncmpcpp] para obtener la lista de comandos que puedes usar para controlar tu nuevo reproductor.

Eso es todo. Disfrutalo!

[libspotify]: http://developer.spotify.com/download/libspotify/libspotify-12.1.51-Linux-x86_64-release.tar.gz

[mopidy_guide]:https://docs.mopidy.com/en/latest/service/#service

[mopidy_spotify_conf]:https://github.com/mopidy/mopidy-spotify#configuration

[ncmpcpp]: https://wiki.archlinux.org/index.php/ncmpcpp