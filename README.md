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
### Transfert de données
Pendant la phase de transferts de données, certains mécanismes clefs permettent d'assurer la robustesse et la fiabilité de TCP. En particulier, les numéros de séquence sont utilisés afin d'ordonner les segments TCP reçus et de détecter les données perdues, les sommes de contrôle permettent la détection d'erreurs.

* Lors de l’émission d’un segment, un numéro de séquence lui est associé.
* De même, à réception d’un segment de données, la machine réceptrice retourne un segment d’information dont le flag est positionné à 1. <br>Cela signifie qu’il s’agit d’un accusé de réception.
* Ce flag est accompagné d’un numéro d’accusé de réception (ACK) prenant alors la valeur du numéro de séquence précédent :

![image](https://user-images.githubusercontent.com/83721477/165297381-c677ef86-a9d7-49bf-aaca-b5f3901c026b.png)

Après quoi, grâce à une minuterie déclenchée dès la réception d’un segment, au niveau de l’émetteur, le segment est réexpédié dès lors que le délai imparti est écoulé.<br> En effet, dans ce cas, le protocole considère que le segment est perdu

#### Exemple:
![image](https://user-images.githubusercontent.com/83721477/165297403-65212acd-5262-4c7c-a06c-49b9b35caacf.png)

*Note: si le segment n’était pas perdu et qu’il arrive malgré tout à destination, le récepteur saura, grâce au numéro d’ordre qu’il s’agit d’un doublon et ne conservera alors que le dernier segment arrivé à destination.*

```
Etant donné que le processus de communication se fait via une émission de données et d’un accusé de réception,
basé sur ce fameux numéro de séquence, il est nécessaire que les machines émettrice et réceptrice
(c’est-à-dire, respectivement le client et le serveur), connaissent le numéro d’ordre initial de
la transmission effectuée par l’autre machine.
```
#### Donc, les deux machines en communication doivent synchroniser leurs séquences.<br>Cela se fait par le mécanisme appelé `Three Way Handshake` (traduit en poignée de main à trois temps).<br><br><br>


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

### Limites
```
À la suite de ce premier échange, entre deux machines, comportant trois séquences, les deux protagonistes
sont alors synchronisés et la communication effective peut commencer. Des petits malins ont alors trouvé un
moyen de détourner ce mécanisme et en ont fait un outil de piratage appelé IP Spoofing. En fait, cela permet
de corrompre la relation d’approbation établie, à des fins malicieuses.
```

### Solution
Afin d’empêcher ce détournement, on peut limiter le nombre d’accusés de réception pour désengorger le trafic réseau, en fixant le nombre de séquence, au bout duquel un accusé de réception est nécessaire.<br>

Cette valeur est stockée dans le champ `window size` de l’entête TCP/IP.

Ce système, appelé `sliding window method`, définit une fourchette de séquences n’ayant nul besoin d’un accusé de réception et se déplace au fur et à mesure que les accusés de réception sont détectés.

### Fenêtre Fixe

![image](https://user-images.githubusercontent.com/83721477/165487070-fffb2eeb-6e46-4557-b028-849ab19175a8.png)
![image](https://user-images.githubusercontent.com/83721477/165487285-c2683f82-3387-484c-8274-e50ae77f27e1.png)

### Fenêtre Dynamique
![image](https://user-images.githubusercontent.com/83721477/165487481-c5909b52-eb22-4e53-a131-0d2df2c60e65.png)
![image](https://user-images.githubusercontent.com/83721477/165487632-5d17aeeb-fd4b-4ba0-90a6-777b42b0407f.png)
![image](https://user-images.githubusercontent.com/83721477/165487806-479c32a8-ac30-400f-8981-7292f54a0b28.png)
![image](https://user-images.githubusercontent.com/83721477/165488151-38fee7f9-f64f-49e3-89a1-9f698ce9f754.png)
![image](https://user-images.githubusercontent.com/83721477/165488354-01ff1e46-2c22-4a5a-8bd5-600eee9ff714.png)

Ainsi, l’association des deux protocoles TCP et IP permettent d’acheminer les messages de bout-en-bout.

*Note: Lorsque l’on souhaite privilégier la rapidité par rapport à la sécurité de transmission, il est possible d’utiliser le protocole UDP, orienté sans connexion, plutôt que TCP.*

## Notes
* TCP est fiable. En effet, le protocole garantit la transmission de toutes les données dans leur intégralité, ainsi que la possibilité, pour le destinataire, de les remettre dans le bon ordre.
* TCP permet de multiplexer les données, c'est-à-dire de faire circuler simultanément des informations provenant de sources (applications par exemple) distinctes sur une même ligne
* TCP permet de formater les données en segments de longueur variable afin de les "remettre" au protocole IP
* TCP prévoit l’envoi des données par segments individuels d’un volume maximum de 1 500 octets (en-tête compris).
* TCP se base, dans la plupart des cas, sur le protocole Internet (IP). C’est pourquoi, on parle souvent de la pile de protocoles TCP/IP.
* Le volume par défaut de l’en-tête TCP est de 20 octets. Des options supplémentaires pouvant atteindre jusqu’à 40 octets peuvent lui être rajoutées.

