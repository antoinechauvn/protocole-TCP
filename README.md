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

Ce système, appelé "sliding window method", définit une fourchette de séquences n’ayant nul besoin d’un accusé de réception et se déplace au fur et à mesure que les accusés de réception sont détectés.

#### Exemple: 
Après une ouverture de communication, le n° de séquence est 3 et autorise jusqu’à la séquence 5 :

![image](https://user-images.githubusercontent.com/83721477/165297560-362962ab-ec06-459b-806a-96544008af9e.png)

La taille de `sliding window` n’est pas fixe.
Ainsi, le serveur peut inclure (toujours dans le champ `window size`, la taille de la fenêtre qui lui semble la plus adaptée. De la sorte, en cas d’accusé de réception indiquant une demande d’augmentation de la taille de la fenêtre, le client peut déplacer celle-ci vers la droite.
Mais, en cas de réduction, le client attend que la fenêtre se déplace d’elle-même.

En ce qui concerne la fin d’une connexion, le protocole prévoit que le client demande lui-même à mettre fin à la transmission, au même titre que le serveur. La terminaison s’effectue alors de la façon suivante :

- Une des machines envoie un segment avec le drapeau FIN à 1. L’application se met en attente du signal de fin. Ainsi, elle termine de recevoir le segment en cours et ignorera les suivants.

- Après réception de ce segment, l’autre machine envoie également un accusé de réception avec le drapeau FIN à 1 et expédie les segments en cours. À la suite de quoi, la machine informe l’application qu’un segment FIN a été reçu et envoie aussi un segment FIN à son vis-à-vis, clôturant ainsi la communication.

Ainsi, l’association des deux protocoles TCP et IP permettent d’acheminer les messages de bout-en-bout.

*Note: Lorsque l’on souhaite privilégier la rapidité par rapport à la sécurité de transmission, il est possible d’utiliser le protocole UDP, orienté sans connexion, plutôt que TCP.*

