\section{OceanStore}

\subsection{Présentation}

OceanStore est un projet développé par l'Université de Berkeley. Il s'agit d'un système de stockage persistant conçu à l'échelle de milliards d'utilisateurs, il fournit un stockage durable, à haute disponibilité composés de serveurs non sécurisés. Tout ordinateur peut adhérer à l'infrastructure.\\

OceanStore met en cache les données, tout serveur peut créer une réplique locale. Celles-ci permettent de fournir un accès plus rapide et plus robuste au réseau. Il permet aussi de réduire la congestion du réseau par la surveillance du traffic autour de ces ressources.\\

OceanStore utilise un protocole de tolérance de panne strict pour assurer une bonne cohérence entres les répliques. L'API permet aux applications d'affaiblir cette tolérance afin d'obtenir de meilleures performances et disponibilités.\\

Actuellement, de nombreux composants d'OceanStore sont fonctionnels, et un Prototype est en cours de développement.\\

\subsection{Problèmes Rencontrés}

Tout d'abord, nous avons essayé de comprendre le fonctionnement interne de OceanStore.\\

Pour se faire, nous avons commencé par télécharger les documentations techniques proposées sur le site du projet. Ensuite, nous avons tenté de faire tourner le prototype disponible sur ce même site. Le lancer ne fut pas le plus compliqué, mais celui-ci ne semblait pas faire grand chose. Nous avions à disposition des dizaines de script Perl qui avaient pour but de lancer le prototype, sans aucun commentaire pour expliquer ce que faisais chaque bloc d'instructions. Nous ne savions donc pas comment configurer les exécutions. Nous avons bien tenté de connecter deux machines afin de les faire communiquer, mais nous ne savions pas comment interagir avec le système - qui semblait fonctionner en mode console -.\\

Nous avons, bien sûr, essayé de réaliser le HelloWorld proposé, néanmoins celui-ci n'avait rien de simple, tenait sur plusieurs pages Internet, nécéssitait de créer/modifier plusieurs fichiers... Au final, nous n'avons pas réussi à faire fonctionner ce HelloWorld. Nous avons montré la procédure à suivre à Gerson Sunyé ainsi qu'à Eduardo Cunha de Ameda qui nous ont eux aussi dit n'avoir jamais vu un HelloWorld aussi complexe.\\

\subsection{Changement de DHT}

Après plusieurs journées consacrées au module d'Initiation à la Recherche, à cause de ce blocage nous n'avions quasiment pas avancé. Nous avons donc demandé à nos encadrants s'il était pas possible de changer l'orientation de notre projet. Une solution aurait pu être de se concentrer uniquement sur le fonctionnement de OceanStore, afin de créer une documentation et un HowTo bien plus simple. L'autre solution était de changer la DHT que nous allions tester.\\

C'est sur cette second solution que nous nous sommes dirigé.\\

Il nous fallait donc un programme écrit en Java afin de le tester facilement à l'aide de PeerUnit. Ayant connaissance du logiciel de pair-à-pair Azureus utilisant le protocole BitTorrent pour l'échange de données, et le protocole Kademlia pour la recherche de pair et la localisation de sources, nous avons donc proposé d'utiliser ce programme pour valider son fonctionnement à l'aide de PeerUnit.