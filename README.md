# protocole-tcp

### Qu'est-ce que le protocole TCP?
Le protocole TCP (Transmission Control Protocol) est un des principaux acteurs de la couche TRANSPORT du modèle TCP/IP. Il permet au niveau des applications, de gérer les données en provenance ou à destination de la couche inférieure (c’est-à-dire du protocole IP).

Le protocole TCP a pour tâche de :

- Remettre en ordre les datagrammes en provenance du protocole IP.
- Vérifier le flot de données afin d’éviter une saturation du réseau.
- Formater les données en segments de longueur variable pour les remettre au protocole IP.
- Initialiser et terminer une communication.

Ainsi, le protocole TCP assure le transfert des données de façon fiable, bien qu’il s’appuie sur le protocole de niveau inférieur : IP, qui lui n’intègre aucun contrôle de livraison de datagramme.

En fait, TCP possède un système d’accusé de réception permettant au client et au serveur de s’assurer de la bonne réception mutuelle des données (un peu comme on le fait pour la réception d’un colis postal). Lors de l’émission d’un segment, un numéro d’ordre (aussi appelé numéro de séquence), lui est associé. De même, à réception d’un segment de données, la machine réceptrice retourne un segment d’information dont le drapeau (aussi appelé flag) est positionné à 1. Cela signifie qu’il s’agit d’un accusé de réception. Ce flag est accompagné d’un numéro d’accusé de réception prenant alors la valeur du numéro d’ordre précédent :

![image](https://user-images.githubusercontent.com/83721477/165297381-c677ef86-a9d7-49bf-aaca-b5f3901c026b.png)
Après quoi, grâce à une minuterie déclenchée dès la réception d’un segment, au niveau de l’émetteur, le segment est réexpédié dès lors que le délai imparti est écoulé. En effet, dans ce cas, le protocole considère que le segment est perdu :
![image](https://user-images.githubusercontent.com/83721477/165297403-65212acd-5262-4c7c-a06c-49b9b35caacf.png)

REMARQUE : mais, si le segment n’était pas perdu et qu’il arrive malgré tout à destination, le récepteur saura, grâce au numéro d’ordre qu’il s’agit d’un doublon et ne conservera alors que le dernier segment arrivé à destination.

IMPORTANT : étant donné que le processus de communication se fait via une émission de données et d’un accusé de réception, basé sur ce fameux numéro de séquence, il est nécessaire que les machines émettrice et réceptrice (c’est-à-dire, respectivement le client et le serveur), connaissent le numéro d’ordre initial de la transmission effectuée par l’autre machine.

Il est donc convenu que l’établissement d’une connexion entre deux applications s’effectue de la manière suivante :

- Les ports de service doivent être ouverts.
- L’application du serveur est à l’écoute (en mode passif), en attente d’une connexion entrante.
- L’application sur le client émet une requête de connexion vers le serveur. L’application du client est alors dite en ouverture active.

Donc, les deux machines en communication doivent synchroniser leurs séquences. Cela se fait par le mécanisme appelé "three way handshake" (traduit en poignée de main à trois temps – c’est le protocole que l’on a communément, nous humain, l’habitude d’utiliser pour se dire "bonjour").

NOTE : ce mode "three way handshake" est également utilisé lors de la clôture de session.

Ce dialogue établi, permet d’initier la communication et se déroule (comme le libellé le laisse supposer), en trois étapes :

- L’émetteur (le client), transmet un segment dont le drapeau est valorisé à 1 (afin de signifier qu’il s’agit d’un segment de synchronisation), avec un numéro d’ordre C, appelé numéro d’ordre initial du client.

- Le récepteur (le serveur), reçoit le segment initial en provenance du client et lui envoie un accusé de réception. Ce segment ACK de synchronisation contient également le numéro d’ordre du serveur incrémenté de 1.

- Enfin, le client transmet au serveur, un accusé de réception (avec le flag ACK à 1 et celui de SYN à 0 – car il ne s’agit plus d’un segment de synchronisation). Le numéro d’ordre S, récupéré du serveur est alors incrémenté de 1, à son tour :

![image](https://user-images.githubusercontent.com/83721477/165297474-a9749eaf-cb3a-4f1d-a791-8d0166019c9b.png)

À la suite de ce premier échange, entre deux machines, comportant trois séquences, les deux protagonistes sont alors synchronisés et la communication effective peut commencer. Des petits malins ont alors trouvé un moyen de détourner ce mécanisme et en ont fait un outil de piratage appelé IP Spoofing. En fait, cela permet de corrompre la relation d’approbation établie, à des fins malicieuses.

Afin d’empêcher ce détournement, on peut limiter le nombre d’accusés de réception pour désengorger le trafic réseau, en fixant le nombre de séquence, au bout duquel un accusé de réception est nécessaire. Cette valeur est stockée dans le champ "fenêtre" de l’entête TCP/IP.

Ce système, appelé "méthode de la fenêtre glissante", définit une fourchette de séquences n’ayant nul besoin d’un accusé de réception et se déplace au fur et à mesure que les accusés de réception sont détectés.

Exemple : après une ouverture de communication, le n° de séquence est 3 et autorise jusqu’à la séquence 5 :

![image](https://user-images.githubusercontent.com/83721477/165297560-362962ab-ec06-459b-806a-96544008af9e.png)
IMPORTANT : la taille de cette fenêtre glissante n’est pas fixe. Ainsi, le serveur peut inclure (toujours dans le champ "fenêtre", la taille de la fenêtre qui lui semble la plus adaptée. De la sorte, en cas d’accusé de réception indiquant une demande d’augmentation de la taille de la fenêtre, le client peut déplacer celle-ci vers la droite. Mais, en cas de réduction, le client attend que la fenêtre se déplace d’elle-même.

En ce qui concerne la fin d’une connexion, le protocole prévoit que le client demande lui-même à mettre fin à la transmission, au même titre que le serveur. La terminaison s’effectue alors de la façon suivante :

- Une des machines envoie un segment avec le drapeau FIN à 1. L’application se met en attente du signal de fin. Ainsi, elle termine de recevoir le segment en cours et ignorera les suivants.

- Après réception de ce segment, l’autre machine envoie également un accusé de réception avec le drapeau FIN à 1 et expédie les segments en cours. À la suite de quoi, la machine informe l’application qu’un segment FIN a été reçu et envoie aussi un segment FIN à son vis-à-vis, clôturant ainsi la communication.

Ainsi, l’association des deux protocoles TCP et IP permettent d’acheminer les messages de bout-en-bout. On a très souvent l’habitude de schématiser l’utilisation de ces protocoles par le schéma suivant, démontrant la rapidité du premier protocole (IP avec remise en "best effort") et la rigueur de l’autre (TCP avec remise négociée) :

![image](https://user-images.githubusercontent.com/83721477/165297602-c7528356-d543-409c-bc86-26bd6b0397b7.png)

Lorsque l’on souhaite privilégier la rapidité par rapport à la sécurité de transmission, il est possible d’utiliser le protocole UDP, orienté sans connexion, plutôt que TCP.

En effet, dans le cas de l’utilisation du protocole UDP, lorsqu’une machine émettrice diffuse des paquets à destination d’une autre, ce flux est unidirectionnel. La transmission des données se fait sans en avertir le destinataire et ce dernier reçoit les informations sans effectuer d’accusé de réception à l’intention de la première machine.

Pour pouvoir fonctionner ainsi, il suffit donc que l’encapsulation des données envoyées par le protocole UDP ne transmette pas les informations concernant l’émetteur. Ainsi, ce dernier ne connaitra pas non plus l’émetteur des données, à l’exception de son adresse IP.

REMARQUE : on compare très souvent le protocole TCP au protocole régissant les communications téléphoniques (connectées) et le protocole UDP au protocole régissant la distribution de messages (par facteur interposé, donc sans connexion notoire).

