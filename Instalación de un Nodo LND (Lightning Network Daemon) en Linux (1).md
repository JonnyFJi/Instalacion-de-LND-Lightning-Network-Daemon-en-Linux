---
title: Instalación de un Nodo LND (Lightning Network Daemon) en Linux
tags: [bitcoin, cli, nodo, node, macos]

---

# Instalación de LND (Lightning Network Daemon) en Linux
###### tags: `bitcoin` `cli` `nodo` `node` `Linux` `LND`

**Instalar un nodo LND**
LND, es la abreviatura de "Lightning Network Daemon", un software de implementación para operar nodos en la red Lightning de Bitcoin. Esta herramienta permite a los usuarios abrir y gestionar canales de pago, facilitar transacciones instantáneas y de bajo costo, y contribuir a la escalabilidad de Bitcoin mediante pagos fuera de la cadena principal.

- LND es desarrollado principalmente por Lightning Labs y es uno de los clientes más populares y avanzados para interactuar con la Lightning Network, especialmente utilizado por desarrolladores y operadores de nodos que buscan integrar pagos rápidos y eficientes con Bitcoin en sus aplicaciones y servicios.
- Aquí tienes una guía paso a paso para instalar un nodo Lightning (usando LND como ejemplo) en Linux.

**Tabla de contenido**
[TOC]

## Autores
Jonny_Ji. 
Twitter para correcciones, comentarios o sugerencias: [@JonnyJi50127056](https://x.com/JonnyJi50127056)

El presente tutorial fue elaborado para el curso [Nodes from zero to hero](https://libreriadesatoshi.com/courses/nodes-from-zero-to-hero) a través de [@libreriadesatoshi](https://twitter.com/libdesatoshi).

En el siguiente enlace puedes encontrar la documentación de referencia:
[Run LND](https://github.com/alexbosworth/run-lnd#install-lnd)

[LND](https://github.com/lightningnetwork/lnd)


## LND - "Lightning Network Daemon"
LND, o "Lightning Network Daemon", es una de las implementaciones más avanzadas y utilizadas para operar nodos en la Lightning Network de Bitcoin, desarrollada por Lightning Labs. 
Funciona como un software de segundo nivel (Layer 2) que permite enviar y recibir transacciones de Bitcoin casi instantáneamente y con tarifas muy bajas, abriendo canales de pago bidireccionales y permitiendo un gran volumen de pagos fuera de la cadena principal mediante contratos multi-firma.

**Características principales de LND**

- Permite la creación, gestión y cierre de canales de pago Lightning Network, incluyendo recuperación de fondos y manejo de estados excepcionales.
- Proporciona enrutamiento automático de pagos a través de una red de nodos interconectados, utilizando multisignature y contratos con tiempo bloqueado (HTLCs) para seguridad y atomicidad.
- Incorpora herramientas como "autopilot" para gestionar canales automáticamente, opciones de backup de canales (Static Channel Backups), y soporte para pagos multi-ruta (AMP).
- Cuenta con APIs gRPC y REST para integración en carteras, servicios y aplicaciones que requieran pagos rápidos con Bitcoin.
- Emplea "onion routing" para privacidad de los pagos y es compatible con otras implementaciones de Lightning Network mediante los estándares BOLT.

**Uso y consideraciones de seguridad**

- LND actúa como proceso en segundo plano (daemon), requiere operar junto a un nodo completo de Bitcoin (bitcoind, btcd o el cliente ligero Neutrino).
- Opera con claves privadas en caliente ("hot wallet"), por lo que es importante asegurar la infraestructura y mantener altos tiempos de actividad para evitar cierres maliciosos de canales.
- Los backups y la gestión adecuada de canales son fundamentales para evitar pérdidas de fondos.

LND representa el núcleo tecnológico para quienes desean operar nodos Lightning, ofrecer servicios de pagos instantáneos, o desarrollar aplicaciones sobre la red Lightning de Bitcoin.

## Instalar Programas
Primero necesitamos instalar `GO`
Podemos comprobar la última versión estable de `go` en su página oficial: https://golang.org/ 
1. Descargamos el archivo `.tar`:
```shell
wget https://go.dev/dl/go1.21.1.linux-amd64.tar.gz
```
y lo descomprimimos en la carpeta
```shell
tar xvf go1.21.1.linux-amd64.tar.gz
```
- La versión 1.21.1 es la más reciente en el momento en que se escribe esta guía. 

Ahora podemos borrar el archivo `.tar`, ya no lo nesecitamos.
```shell
rm go1.12.7.linux-amd64.tar.gz
```
2. Copiamos la carpeta go a /usr/lib/.
```shell
sudo cp -r go /usr/lib/
```
También se suele copiar a `/usr/local/` como alternativa.
```shell
sudo cp -r go /usr/local/
```
3. Luego copiamos `go` en nuestro `path`.
```shell
sudo nano ~/.profile
```
Como últimas líneas del archivo, pegamos:
```shell
export GOROOT=/usr/lib/go
export GOPATH=$HOME/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOROOT/bin:$GOBIN
```
Guardamos, salimos.

Después ejecutaremos los siguientes comandos que permitirán exportar el path o ruta a donde se encuentras los binarios de `Go`:
```shell
export PATH=$PATH:/usr/local/go/bin
export GOPATH=~/gocode
export PATH=$PATH:$GOPATH/bin
```
Ahora escribimos el siguiente comando:
```shell
source ~/.profile
```
Comprobamos la instalación con 
```shell
go version
```
Deberíamos recibir el siguiente output:
>`go version go1.12.7 linux/amd64` o la versión que tengas instalada.

## Instalar Dependencias
Actualizamos el sistema para instalar las dependencias.
```shell
sudo apt-get update
sudo apt-get install -y autoconf automake build-essential git libtool libgmp-dev libsqlite3-dev python3 python3-mako python3-pip net-tools zlib1g-dev libsodium-dev gettext
sudo pip3 install --user mrkd
```

## Instalar LND
Ahora vamos a proceder con la instalación de LND, para ello es necesario descargar el código fuente ejecutando el siguiente comando:
```shell
git clone https://github.com/lightningnetwork/lnd
```
Una vez descargado, nos cambiamos al directorio lnd:
```shell
cd lnd
```
Revisamos la última versión estable con
```shell
git tag
```
Para el momento de escribir esta guía, la última versión estable es `v0.15.0-beta` cambiamos a esa versión:
```shell
git checkout v0.15.0-beta  # esto nos cambia a esa versión para que sea la versión a instalar
```
Una vez que ya estamos en la instancia de la versión seleccionada, corremos el siguiente comando:
```shell
make install   # este comando inicia la instalación con la versión seleccionada previamente
```
Después de un rato, se abra instalado LND.

Comprobamos la instalación con
```shell
lnd --version
```
Si todo ha ido bien, ya tenemos instalado el software necesario para ejecutar `lnd`, sin embargo, antes tenemos que terminar de realizar algunas configuraciones necesarias para que `lnd` pueda comunicarse con `bitcoind`.

Así que ejecutaremos el siguiente comando para crear nuestro directorio de trabajo para `lnd` y dentro de ese directorio crearemos un archivo con las configuraciones necesarias para que `lnd` trabaje sobre `mainnet`, la red principal de Bitcoin.
```shell
mkdir ~/.lnd
cd .lnd
```
Ahora crearemos el archivo `lnd.conf`, este archivo contiene lo mínimo necesario para ejecutar nuestro nodo, puedes modificar cualquier opción y adaptar a tu preferencia.

En un terminal ejecutamos:
```shell
sudo nano lnd.conf
```
A continuación nos mostrará el editor y puedes seleccionar todo el texto que ves aquí en nuestro ejemplo, copiarlo y en la terminal pulsar el botón derecho y seleccionar la opción “pegar” para copiarlo de manera más inmediata.
```shell
 GNU nano 6.2                                                             lnd.conf  
; Configuration for lnd.
; The default location for this file is in ~/.lnd/lnd.conf
; Boolean values can be specified as true/false or 1/0.

[Application Options]

; The directory that lnd stores all wallet, chain, and channel related data
; within The default is ~/.lnd/data
datadir=~/.lnd/data

; The directory that logs are stored in. The logs are auto-rotated by default.
; Rotated logs are compressed in place.
logdir=~/.lnd/logs

; Number of logfiles that the log rotation should keep. Setting it to 0 disables deletion of old log files.
maxlogfiles=3

; Max log file size in MB before it is rotated.
maxlogfilesize=10

; Path to TLS certificate for lnd's RPC and REST services.
tlscertpath=~/.lnd/tls.cert

; Path to TLS private key for lnd's RPC and REST services.
tlskeypath=~/.lnd/tls.key

; Specify the interfaces to listen on for gRPC connections.  One listen
; address per line.
; Only ipv4 localhost on port 10009:
rpclisten=localhost:10009
rpclisten=[::1]:10010

; Specify the interfaces to listen on for REST connections.  One listen
; address per line.
; All ipv4 interfaces on port 8080:
restlisten=localhost:8080

; Debug logging level.
; Valid levels are {trace, debug, info, warn, error, critical}
; You may also specify <global-level>,<subsystem>=<level>,<subsystem2>=<level>,... 
; to set log level for individual subsystems.  Use lncli debuglevel --show to 
; list available subsystems.
debuglevel=debug,PEER=info

; The smallest channel size (in satoshis) that we should accept. Incoming
; channels smaller than this will be rejected, default value 20000.
minchansize=100000

; The largest channel size (in satoshis) that we should accept. Incoming
; channels larger than this will be rejected. For non-Wumbo channels this 
; limit remains 16777215 satoshis by default as specified in BOLT-0002.
; For wumbo channels this limit is 1,000,000,000 satoshis (10 BTC).
; Set this config option explicitly to restrict your maximum channel size
; to better align with your risk tolerance
maxchansize=10000000

; If true, spontaneous payments through keysend will be accepted. [experimental]
accept-keysend=true

; The alias your node will use, which can be up to 32 UTF-8 characters in
; length.
alias=<tu_alias ⚡>

[Bitcoin]

; If the Bitcoin chain should be active. Atm, only a single chain can be
; active.
bitcoin.active=true

; Use Bitcoin's main network.
bitcoin.mainnet=true

; Use Bitcoin's test network.
; bitcoin.testnet=true

; Use the bitcoind back-end
bitcoin.node=bitcoind

[Bitcoind]

; The base directory that contains the node's data, logs, configuration file,
; etc.
bitcoind.dir=/home/admon/.bitcoin

; The host that your local bitcoind daemon is listening on. By default, this
; setting is assumed to be localhost with the default port for the current
; network.
bitcoind.rpchost=localhost

; Username for RPC connections to bitcoind. By default, lnd will attempt to
; automatically obtain the credentials, so this likely won't need to be set
; (other than for a remote bitcoind instance).
bitcoind.rpcuser=<admon>

; Password for RPC connections to bitcoind. By default, lnd will attempt to
; automatically obtain the credentials, so this likely won't need to be set
; (other than for a remote bitcoind instance).
bitcoind.rpcpass=<1P4sswordFuer7e&D1ficil.,>

; ZMQ socket which sends rawblock and rawtx notifications from bitcoind. By
; default, lnd will attempt to automatically obtain this information, so this
; likely won't need to be set (other than for a remote bitcoind instance).
bitcoind.zmqpubrawblock=tcp://127.0.0.1:28332
bitcoind.zmqpubrawtx=tcp://127.0.0.1:28333

[tor]
; Allow outbound and inbound connections to be routed through Tor
tor.active=true

; The port that Tor's exposed SOCKS5 proxy is listening on. Using Tor allows
; outbound-only connections (listening will be disabled) -- NOTE port must be
; between 1024 and 65535
tor.socks=9050

; Enable Tor stream isolation by randomizing user credentials for each
; connection. With this mode active, each connection will use a new circuit.
; This means that multiple applications (other than lnd) using Tor won't be mixed
; in with lnd's traffic.
tor.streamisolation=true

; Automatically set up a v3 onion service to listen for inbound connections
; tor.v3=true

[healthcheck]
; The number of times we should attempt to query our chain backend before
; gracefully shutting down. Set this value to 0 to disable this health check.
healthcheck.chainbackend.attempts=0
```
Antes de iniciar, hay que modificar el archivo `bitcoin.conf` y agregar los datos de la red `zmq`:
```shell
cd
cd .bitcoin
sudo nano bitcoin.conf
```
Agregamos estas líneas:
```shell
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333
```
Guardamos y cerramos el archivo.

Ya está... `LND` está instalado y listo...

## Iniciar lnd

Para iniciar el nodo escribimos:
```shell
lnd
```
y veremos iniciar el nodo y nos pedira o que creemos una nueva billetera o que ingresemos el password para iniciar una billetera existente.

- Al correr el comando, la última línea que verás debería mostrar algo como lo siguiente:

>[INF] LTND: Waiting for wallet encryption password. Use `lncli create` to create a wallet, `lncli unlock` to unlock an existing wallet, or `lncli changepassword` to change the password of an existing wallet and unlock it.

En este caso vamos a asumir que no tenemos wallet y es necesario crearla.

Dejamos corriendo nuestro nodo en la terminar que tenemos abierta y abrimos una nueva conexión en otra terminal para continuar con el proceso.

> Antes de crear nuestra wallet, te recomiendo crear una contraseña fuerte, lo puedes hacer con un generador de contraseñas o utilizando el siguiente comando que creará una contraseña random de 21 caracteres en hexadecimal (o base64 cambiando `-hex` por `-base64`) y lo guardará en un archivo en el servidor para poder iniciar nuestro nodo automáticamente.

```shell
openssl rand -hex 21 > ~/.lnd/wallet_password
```

Y para proteger el archivo, cambiemos sus permisos a 400 con el siguiente comando:

```shell
chmod 400 ~/.lnd/wallet_password
```

>**Toma nota del password porque lo ocuparemos en el siguiente paso para crear nuestra wallet**

Para crear nuestra wallet corre el siguiente comand:

```shell
lncli create
```
Sigue los pasos que se te indican
>Input wallet password: (Anotamos el password que generamos el paso anterior)
>
>Confirm password: 

Para crear una nueva seed introduce la opción `n`:

>Do you have an existing cipher seed mnemonic or extended master root key you want to use?
Enter 'y' to use an existing cipher seed mnemonic, 'x' to use an extended master root key 
or 'n' to create a new seed (Enter y/x/n): n


En el siguiente paso podrás encriptar tu cipher seed utilizando un passphrase o puedes sólo presionar enter para continuar

>Your cipher seed can optionally be encrypted.
Input your passphrase if you wish to encrypt it (or press enter to proceed without a cipher seed passphrase): 

#### Success: Haz creado tu wallet

Si todo funcionó correctamente, verás algo parecido que **deberás guardar de manera segura** ya que es tu seed para restaurar tu wallet.

---
>Generating fresh cipher seed...
!!!YOU MUST WRITE DOWN THIS SEED TO BE ABLE TO RESTORE THE WALLET!!!
>---------------BEGIN LND CIPHER SEED---------------
```
1. *******    2. ****      3. *******   4. ******* 
5. *******    6. ***       7. *****     8. ******* 
9. ******    10. ****     11. ****     12. ****
13. ******   14. ****     15. ******   16. *****   
17. *****    18. ***      19. *****    20. ***** 
21. *****    22. ****     23. ******   24. *********  
```
>---------------END LND CIPHER SEED-----------------
>!!!YOU MUST WRITE DOWN THIS SEED TO BE ABLE TO RESTORE THE WALLET!!!
---
Ahora vamos a detener un momento nuestro nodo LND, regresando a la terminal donde se está ejecutando `LND`, simplemente deteniendo el proceso con `ctrl + c` si es que sigue corriendo, y vamos a agregar el password de nuestra wallet al archivo de configuración para que se pueda iniciar automáticamente.

Abre el archivo de configuración para editarlo
```shell
sudo nano .lnd/lnd.conf
```
Y ahora vamos a agregar al final del archivo la siguiente línea
```shell
wallet-unlock-password-file=~/.lnd/wallet_password
```
Ahora corremos nuevamente el comando `lnd` en la terminal y se iniciará nuestro nodo automáticamente, ya que tenemos una wallet y el nodo de bitcoin corriendo. (*Es importante recordar que el nodo `bitcoind`, debe estar corriendo y sincronizado*)

En otra terminal, podemos verificar que nuestro nodo `lnd` está corriendo correctamente solicitando su información utilizando `lncli`

```shell
lncli getinfo
```
Deberás ver algo como la siguiente información:

---
>{
    "version": "0.15.0-beta commit=v0.15.0-beta",
    "commit_hash": "6bd30047c1b1188029e8af6ee8a135cf86e7dc4b",
    "identity_pubkey": "027c2c4c6f08e7a93e694786fd17b3db7719985c9d65f6be38f2d081ed0209e5b4",
    "alias": "027c2c4c6f08e7a93e69",
    "color": "#3399ff",
    "num_pending_channels": 0,
    "num_active_channels": 0,
    "num_inactive_channels": 0,
    "num_peers": 2,
    "block_height": 2442286,
    "block_hash": "0000000000000015008f3181d704b3f862a287fb94bc90bbf244a7b5adf9ca5c",
    "best_header_timestamp": "1689566771",
    "synced_to_chain": true,
    "synced_to_graph": false,
    "testnet": true,
    "chains": [
        {
           "chain": "bitcoin",
           "network": "mainnet"
        }
    ],
    "uris": [
    ],
    "features": { … }
}
---
El único problema hasta aquí es que para este momento tenemos cuando menos 2 terminales abiertas, una con el nodo de `lnd` mostrando todos sus procesos y otra para operarlo con `lncli`, y debemos dejarlas abiertas para poder operar el nodo.

Podemos trabajar así, no hay problema con que esten abiertas las terminales, o podemos crear un shell script para indicarle al servidor que cada vez que inice (es decir si se reinicia, se apague por algún error y prenda automáticamente), inicie primero el nodo de bitcoin, espere unos momentos y después inicie el nodo LND.

---

Si has llegado hasta aquí, felicitaciones, ahora tienes un nodo Bitcoin y Lightning listos para usar. Este es el primer pilar para comenzar a construir tu soberanía monetaria.

# Team "Librería de Satoshi"
