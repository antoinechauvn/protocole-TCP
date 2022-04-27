# protocole-tcp

### Qu'est-ce que le protocole TCP?
```
Le protocole TCP (Transmission Control Protocol) est un des principaux acteurs de la couche TRANSPORT du modèle TCP/IP.
Il permet au niveau des applications, de gérer les données en provenance ou à destination de la couche inférieure
(c’est-à-dire du protocole IP).
```
Le protocole TCP a pour tâche de :

- Remettre en ordre les datagrammes en provenance du protocole IP.
- Vérifier le flot de données afin d’éviter une saturation du réseau.
- Formater les données en segments de longueur variable pour les remettre au protocole IP.
- Initialiser et terminer une communication.

## Structure d'un segment TCP
![image](https://user-images.githubusercontent.com/83721477/165390864-68c9fffa-d70d-4cce-96b6-de87e96ba0db.png)
![image](https://user-images.githubusercontent.com/83721477/165391150-f7edd59b-640b-474d-86ce-f162fc0c3e5f.png)


## Principe de fonctionnement
### Établissement d'une connexion (Three Way Handshake)

![image](https://user-images.githubusercontent.com/83721477/165554871-a3192506-a448-4d1a-8317-1070bbd4fd95.png)

Comme son nom l'indique, le three-way handshake se déroule en trois étapes :

1. `SYN`: Le client qui désire établir une connexion avec un serveur va envoyer un premier paquet SYN au serveur. <br>Le numéro de séquence `SEQ` de ce paquet est un nombre aléatoire X (ici 0).<br>
2. `SYN-ACK`: Le serveur va répondre au client à l'aide d'un paquet SYN-ACK.<br>Le numéro `ACK` est égal au numéro de séquence du paquet précédent `SEQ` incrémenté de un (X + 1) <br>Le numéro de séquence `SEQ` du paquet SYN-ACK est un nombre aléatoire Y (ici 0).<br>
3. `ACK`: Pour terminer, le client va envoyer un paquet ACK au serveur qui va servir d'accusé de réception.<br>Le numéro `SEQ` de ce paquet est défini selon le numéro `ACK` du packet reçu précédemment (ici 1).<br>Le numéro `ACK` est égal au numéro de séquence `SEQ` du paquet précédent (SYN-ACK) incrémenté de 1 (ici 1).

<h2 align="center">Mémo </h2>

### `SEQ` = `ACK` du dernier packet

### `ACK` = `SEQ` du dernier packet + nombre d'octets

*Note: Le phantom byte compte comme un octet de data*
<h2></h2>

Afin d'assurer la fiabilité de TCP, le destinataire doit acquitter les segments reçus en indiquant qu'il a reçu toutes les données du flux d'octets jusqu'à un certain numéro de séquence.

**Une communication `full-duplex` est maintenant établie entre le client et le serveur.**

### Terminaison d'une connexion
La phase de terminaison d'une connexion utilise un handshaking en quatre temps, chaque extrémité de la connexion effectuant sa terminaison de manière indépendante. Ainsi, la fin d'une connexion nécessite une paire de segments FIN et ACK pour chaque extrémité.

![image](https://user-images.githubusercontent.com/83721477/165391699-9a676927-cd18-4f30-9c8f-d690423f87ff.png)

## Transfert de données
A la suite d'un établissement de connexion:

![image](https://user-images.githubusercontent.com/83721477/165555715-6c87b7e8-2852-486f-b789-2e5271805c2a.png)

<h2 align="center">Mémo</h2>

### `SEQ` = `ACK` du dernier packet

### `ACK` = `SEQ` du dernier packet + nombre d'octets

<h2></h2>

* Afin de garantir que les segments arrivent dans le bon ordre, les deux participants utilisent des numéros de séquence `SEQ` et des numéros d'acquittement `ACK`.

![image](https://user-images.githubusercontent.com/83721477/165556670-aab79640-36ae-478b-839a-9b9391bfbd5c.png)



### Contrôle de flux
WINDOW_SIZE = 2904 OCTETS
![image](https://user-images.githubusercontent.com/83721477/165499247-06fe22b4-29b2-489d-9fcd-912c30209e9d.png)
![image](https://user-images.githubusercontent.com/83721477/165499360-8b1d3580-5737-4d0e-8230-989a49d322cc.png)

<h2 align="center">Mémo</h2>

### `SEQ` = `ACK` du dernier packet

### `ACK` = `SEQ` du dernier packet + nombre d'octets

### DANS LE CADRE DE MULTIPLES SEGMENTS POUR UN SEGMENT DIFFERENT DU PREMIER
### `SEQ` = `SEQ` du dernier packet + nombre d'octets

### `ACK` = `ACK` du dernier packet

<h2></h2>

*Note: Lorsque l’on souhaite privilégier la rapidité par rapport à la sécurité de transmission, il est possible d’utiliser le protocole UDP, orienté sans connexion, plutôt que TCP.*

## Notes
* TCP est fiable. En effet, le protocole garantit la transmission de toutes les données dans leur intégralité, ainsi que la possibilité, pour le destinataire, de les remettre dans le bon ordre.
* TCP permet de multiplexer les données, c'est-à-dire de faire circuler simultanément des informations provenant de sources (applications par exemple) distinctes sur une même ligne
* TCP permet de formater les données en segments de longueur variable afin de les "remettre" au protocole IP
* TCP prévoit l’envoi des données par segments individuels d’un volume maximum de 1 500 octets (en-tête compris).
* TCP se base, dans la plupart des cas, sur le protocole Internet (IP). C’est pourquoi, on parle souvent de la pile de protocoles TCP/IP.
* Le volume par défaut de l’en-tête TCP est de 20 octets. Des options supplémentaires pouvant atteindre jusqu’à 40 octets peuvent lui être rajoutées.

