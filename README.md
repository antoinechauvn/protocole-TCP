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
Comme son nom l'indique, le three-way handshake se déroule en trois étapes :

1. `SYN`: Le client qui désire établir une connexion avec un serveur va envoyer un premier paquet SYN (synchronized) au serveur. Le numéro de séquence de ce paquet est un nombre aléatoire X.<br>
2. `SYN-ACK`: Le serveur va répondre au client à l'aide d'un paquet SYN-ACK (synchronize, acknowledge).<br>Le numéro du ACK est égal au numéro de séquence du paquet précédent (SYN) incrémenté de un (X + 1) tandis que le numéro de séquence du paquet SYN-ACK est un nombre aléatoire Y.<br>
3. `ACK`: Pour terminer, le client va envoyer un paquet ACK au serveur qui va servir d'accusé de réception.<br>Le numéro d'acquittement de ce paquet est défini selon le numéro de séquence reçu précédemment (par exemple : X + 1) et le numéro du ACK est égal au numéro de séquence du paquet précédent (SYN-ACK) incrémenté de un (Y + 1).

* Les numéros de séquence sont utilisés pour décompter les données dans le flux d'octets
* Le numéro de séquence représente le propre numéro de séquence de l'émetteur TCP
* Le numéro d'acquittement représente le numéro de séquence du destinataire.

Afin d'assurer la fiabilité de TCP, le destinataire doit acquitter les segments reçus en indiquant qu'il a reçu toutes les données du flux d'octets jusqu'à un certain numéro de séquence.


**Une communication `full-duplex` est maintenant établie entre le client et le serveur.**

![image](https://user-images.githubusercontent.com/83721477/165389106-fcebe854-aa1c-4330-b206-bd4797f677e8.png)

### Terminaison d'une connexion
La phase de terminaison d'une connexion utilise un handshaking en quatre temps, chaque extrémité de la connexion effectuant sa terminaison de manière indépendante. Ainsi, la fin d'une connexion nécessite une paire de segments FIN et ACK pour chaque extrémité.

![image](https://user-images.githubusercontent.com/83721477/165391699-9a676927-cd18-4f30-9c8f-d690423f87ff.png)

## Transfert de données

![image](https://user-images.githubusercontent.com/83721477/165497974-9a060346-4721-463d-ac24-e310da00e8a2.png)
![image](https://user-images.githubusercontent.com/83721477/165498530-09937297-258d-4877-adea-9bb2e52be077.png)

### Contrôle de flux
![image](https://user-images.githubusercontent.com/83721477/165499247-06fe22b4-29b2-489d-9fcd-912c30209e9d.png)
![image](https://user-images.githubusercontent.com/83721477/165499360-8b1d3580-5737-4d0e-8230-989a49d322cc.png)


*Note: Lorsque l’on souhaite privilégier la rapidité par rapport à la sécurité de transmission, il est possible d’utiliser le protocole UDP, orienté sans connexion, plutôt que TCP.*

## Notes
* TCP est fiable. En effet, le protocole garantit la transmission de toutes les données dans leur intégralité, ainsi que la possibilité, pour le destinataire, de les remettre dans le bon ordre.
* TCP permet de multiplexer les données, c'est-à-dire de faire circuler simultanément des informations provenant de sources (applications par exemple) distinctes sur une même ligne
* TCP permet de formater les données en segments de longueur variable afin de les "remettre" au protocole IP
* TCP prévoit l’envoi des données par segments individuels d’un volume maximum de 1 500 octets (en-tête compris).
* TCP se base, dans la plupart des cas, sur le protocole Internet (IP). C’est pourquoi, on parle souvent de la pile de protocoles TCP/IP.
* Le volume par défaut de l’en-tête TCP est de 20 octets. Des options supplémentaires pouvant atteindre jusqu’à 40 octets peuvent lui être rajoutées.

