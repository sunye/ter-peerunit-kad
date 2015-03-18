# DHT(distributed hash table) #

Une DHT permet l'identification et l'obtention d'une information dans un système reparti principalement dans les réseau P2P.

Cette technologie permet de bénéficier des avantages des tables indexées tout en réduisant le coût mémoire. En effet, les données sont reparties entre tous les nœuds du réseau. Cependant, il faut introduire de la redondance afin d'éviter la perte de données si l'un des nœuds n'est plus accessible.


Parmi les protocoles utilisant les tables de hachage reparties nous pouvons citer Chord ou Tapestry.
Dans le cadre de notre module D'IR nous nous sommes intéressés au protocole Kademlia.

# Kademlia #

Kademlia est un protocole de communication pour les réseaux P2P, il est basé sur une DHT.


Un réseau Kademlia se compose de plusieurs Nœuds, chaque Noeud communique avec les autres en échangeant et en stockant des informations,

Chaque Nœud possède un identifiant unique NodeID permettant de le distinguer des autres Nœuds du réseau. **Comment est garanti le fait que ce NodeID est unique ?**

## NodeID ##

Un nodeID: est un nombre binaire de longueur B=160 bits, chaque nœud choisi son NodeId par une procédure aléatoire au moment de rejoindre le réseau.

## Clé ##

Les Clés: les informations stockées sur un réseau Kademlia ont aussi une clé de longueur B=160bits.

## Distance ##

Afin de trouver une paire (clé,valeur) Kademlia se base sur la notion de distance entre deux IDnode, cette distance est calculée en appliquant la métrique XOR (ou exclusif) qui présente les propriétés suivantes:

Soit x et y deux identifiants alors:

distance(x,y) =  x ⊕ y

> d(x,x)=0

> d(x,y)>0 si w<>y

> ∀x, y : d(x, y) = d(y, x)

> d(x, y) + d(y, z) ≥ d(x, z) propriété du triangle


Soit ∆ > 0, une distance, il existe alors un seul point y tel que d(x,y)=∆. Cette propriété assure que toutes les recherches d'une clé donnée convergent vers le même chemin...


## K-Buckets ##

Chaque nœud stocke différentes informations sur les autres nœuds afin de pouvoir acheminer les requêtes, ces informations sont stockées dans des listes appelées K-buckets. Les information y sont stockéees sous forme de triplet (adresse IP,port UDP,NodeID).
Chaque K-bucket peut contenir jusqu'à 160 triplets qui sont triées ainsi : les nœuds vus le plus récemment sont en queue de liste, les nœuds vue le moins récemment sont au sommet.
Le paramètre k des k-buckets correspond au nombre d'éléments contenus dans chacune de ces listes généralement K=20.

Pour 0 <= i < 160, les K-bucket stockent ces informations pour les nœuds dont la distance est entre 2<sup>i et 2</sup>(i+1) du nœud concerné.


### La mise à jour des K-bucket ###

A la réception d'un message (requête ou réponse) le nœud met à jour le k-bucket correspondant à l'envoyeur. Si celui-ci y est déjà présent, alors il est déplace vers la queue de la liste, sinon il est inséré à la queue.
Cependant, si le k-bucket est déjà plein alors on vérifie si le nœud se trouvant en tète de liste - c-a-d celui qui a été vu le moins récemment - est toujours disponible. Si il l'est, il est déplacé vers la queue de la liste et l'envoyeur du message n'est pas pris en compte sinon on insère le nœud envoyeur a la place.
Cette politique est choisie en se basant sur l'idée que plus un nœud reste connecté plus il a de chance de rester connecté encore plus longtemps.

## Remote Procedure Calls ##

Kadmelia se base sur 4 RPC (remote procedure calls)

### PING ###
Envoie d'un ping d'un nœud à un autre, le destinataire met à jour le k-bucket correspondant à la source. En cas de réponse, la source met à jour aussi le k-bucket correspondant.

### STORE(clé,données) ###
Le destinataire stocke les données qui peuvent ensuite être retrouvées grâce a la clé

### FIND\_NODE(clé) ###
Le destinataire retourne jusqu'à k triplet correspondant aux nœuds les plus proche de la clé. Les valeurs retournées ne doivent jamais contenir les NodeID de la source ou du destinataire.

### FIND\_VALUE(clé) ###
Le destinataire renvoie les données correspondant à la clé. Si il ne trouve pas de données alors il renvoie k triplet(même fonctionnement que FIND\_NODE).



## Trouver un noeud ##

Kademlia utilise un algorithme itératif pour localiser les k nœuds les plus proche d'une clé donnée, pour cela il choisi un nombre « alpha » de contact à partir du k-bucket le plus proche de la clé cherchée.
Les alpha contacts ainsi sélectionnés forment une liste appelée Shortlist à partir de laquelle s'effectuera la recherche.

> -envoie de requêtes de type FIND\_NODE de façon parallèle et asynchrone vers tout les nœud de la liste.
> -chaque nœud présent répond par k triplets, si l'un des nœud ne répond pas , il est enlevé temporairement de la Shortlist.
> -la Shortlist est remplie avec les contacts reçues en réponse.
> -à partir de la nouvelle liste on sélectionne alpha contact à nouveau, à condition qu'il n'aient pas été sélectionnés avant
> -envoie de requêtes de type FIND\_NODE vers les nouveaux nœud sélectionnés.
> -...

A chaque recherche le nœud le plus proche est marqué ClosestNode. Si aucun nœud n'est trouvé, on choisi d'autres nœuds de la ShortList et relance la recherche.

La recherche continue ainsi jusqu'à ce que les valeur retournées ne contiennent plus de nœud plus proche que "ClosestNode" ou jusqu'à ce que le nœud initiant la recherche ait accumulé k nœud actifs.

### Valeur de Alpha ###

Généralement alpha=3 ce qui corresponde au nombre de requêtes parallèles. chaque itération s'effectue après un délai raisonnable (valeur non spécifiée) ainsi les valeurs retournées restent multiples de alpha sans pour autant devenir trop grands.

## Trouver une ressource ##

Pour cela on utilise le même algorithme que pour localiser un nœud en remplaçant FIND\_NODE par FIND\_VALUE, mais celui ci s'arrête dès que la ressource est trouvée. pour assurer la disponibilité d'une ressource celle ci est stockée simultanément sur plusieurs nœuds..