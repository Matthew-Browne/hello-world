# DHCP Windows server (2022)
Pour la carte réseau en "réseau interne", il faut aller dans les paramètres de la machine virtuelle dans la section "Réseau", puis on change de NAT à Réseau Interne. De plus, parce que nous travaillons avec des VM, dans la section "Mode promiscuité", il faut autoriser les machines virtuelles. Il faut faire ceci pour la machine serveur et les machines de test qui vont recevoir une adresse IP, en s'assurant d'avoir le même nom de réseau interne pour les deux (sur VirtualBox on laisse simplement le nom du réseau interne par défaut).

Pour l'installation de DHCP, les étapes sont sensiblement identiques à celles du guide fourni dans la quête (avec quelques différences car je n'utilisais pas Windows Server 2025 ou 2019).

### Adresse IP de la machine serveur

Pour ma part, avant de pouvoir installer le DJCP, j'ai dû paramétrer l'adresse IP statique de ma machine serveur en suivant les menus et liens suivants :

"Panneau de configuration" -> "Réseau et Internet" -> "Centre Réseau et Partage" -> "Modifier les paramètres de la carte" (sur un menu vertical à gauche) -> Clic droit sur la carte réseau de notre machine -> Double-clic sur "Protocole Internet version 4 (TCP/IPv4)"

Dans cette fenêtre, on coche "Utiliser l'adresse IP suivante" et on rempli les champs de la manière suivante :
- Adresse IP : 172.20.0.254 (adresse IP de notre machine serveur)
- Masque de sous-réseau : 255.255.255.0
- Passerelle par défaut : 172.20.0.1 (adresse IP de notre routeur)

Dans cette fenêtre, on peut aussi renseigner les adresses DNS si on le souhaite ou laisser en automatique.

### Installation DHCP

Ici on suit les étapes du guide, à savoir les différentes étapes du III du guide :

Sur l'interface graphique d'administration serveur, cliquer sur "Gérer" (en haut à droite) -> "Ajouter des rôles et fonctionnalités" -> "Installation basée sur un rôle ou une fonctionnalité" -> Choisir le serveur -> Cocher le rôle "Serveur DHCP" -> Pour le sous-menu DHCP, cocher la case "Inclure les outils de gestion (si applicable)" -> Confirmer jusqu'à l'installation

### Configuration DHCP

Quand l'installation est finie, il y a une icône d'alerte à côté d'un drapeau. Cliquer dessus et ensuite sur "Terminer la configuration  DHCP".
Dans mon cas, l'écran sur les autorisations ne sera pas présent. Confirmer avec "Suivant" à chaque écran. Quand c'est terminé, cliquer sur "Fermer".

Ensuite, dans le champ de recherche windows, chercher "dhcp" pour ouvrir la console dhcp. Une interface graphique du service DHCP s'ouvre.
On déroule les menus déroulants de notre serveur pour afficher "IPv4". Faire un clic droit et choisir "Nouvelle étendue" pour créer une nouvelle étendue IPv4. Ceci ouvre une suite de menus à remplir :
- Le nom de notre étendue (par exemple WINDHCP)
- Les plages d'adresses, dans l'ordre :
	- l'adresse de début : 172.20.0.100
	- l'adresse de fin : 172.20.0.200
	- longueur : 24 (longueur du masque de réseau)
	- masque de sous-réseau : 255.255.255.0
- le menu suivant est pour les plages d'exclusions, qui ne sont pas nécessaires ici
- mettre une durée de bail non nulle
- Choisir "Oui, je veux configurer ces options maintenant"
- Ajouter "172.20.0.1" pour l'adresse de routeur
- la fenêtre DNS n'est pas importante pour notre exercice, on peut laisser les paramètres par défaut.
- Ignorer la fenêtre WINS
- Choisir "Oui, je veux activer cette étendue maintenant"

Suite à cela, l'étendue est active et on peut déjà tester avec une machine client en réseau interne pour confirmer que l'attribution automatique d'adresse IP est fonctionnelle.

Pour la réservation d'adresse IP, on peut l'effectuer après la création de l'étendue DHCP. Dans la console DHCP, sur le dossier "Réservations" de notre étendue, on fait un clic droit pour choisir de créer une nouvelle réservation (option "Nouvelle réservation"). 
Ici, on va indiquer que l'adresse à réserver est 172.20.0.10, l'adresse MAC de la machine que l'on veut et on coche en bas l'option "DHCP". Pour tester si cela fonctionne, on peut reprendre notre machine hôte de test comme adresse MAC à utiliser. 
Après validation, si on relance cet hôte, dans les paramètres réseau, on verra que notre nouvelle adresse IP est 172.20.0.10 et non plus 172.20.0.100. Avec cette configuration, si sur notre machine de test on entre la commande `ipconfig /renew` dans PowerShell, on aura toujours l'adresse IP 172.20.0.10 tant que la réservation est active.



