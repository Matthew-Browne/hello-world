# DHCP linux server
Pour mon cas, mes machines clients et serveur sont des machines Ubuntu 24.04, contrairement aux machines du guide. Certains répertoires, fichiers, commandes ou éditions seront potentiellement différents en fonction des systèmes opératoires utilisés.
Les machines doivent être en réseau interne
(NB: à faire après les installations, en réseau interne `sudo apt update` ou `sudo apt install` n'arrive pas à récupérer les paquets demandés)

### Interfaces et netplan

D'abord, on va configurer l'interface que l'on va utiliser pour gérer le service DHCP. Pour savoir quelles interfaces sont disponibles, on fait la commande `ip a`. Dans mon cas, à part le loopback/localhost (interface référant la machine elle-même), j'ai l'interface `enp0s3` de disponible.
Pour configurer cette interface, on va modifier les fichier `/etc/netplan/01-network-manager-all.yaml` pour ajouter les informations suivantes :
```
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - 172.20.0.254/24
      routes:
        - to: default
          via: 172.20.0.1
      nameservers:
        addresses: [8.8.8.8]
```
Ici, nous faisons plusieurs choses :
- On indique que notre interface enp0s3 a comme adresse IP `172.20.0.254/24`
- Et que notre routeur a l'adresse IP `172.20.0.1`

La ligne avec `[8.8.8.8]` indique les adresses DNS. Pour cet exercice, ce n'est pas une ligne qui aura de l'importance autre que pour éviter une erreur potentielle (cette adresse en particulier correspond au DNS Google, donc une valeur sûre).
Pour que les modifications de ce fichier soient pris mis à jour dans notre environnement de travail, il faut entrer la commande `sudo netplan apply` dans la console.

### Service isc-dhcp-server

Après l'installation de isc-dhcp-server avec `sudo apt install isc-dhcp-server`, il faut modifier le fichier `/etc/default/isc-dhcp-server` pour :
- décommenter la ligne `DHCPDv4_CONF=/etc/dhcp/dhcpd.conf`
- décommenter la ligne ``DHCPDv4_PID=/var/run/dhcpd.pid
- éditer la ligne d'interface IPv4 de la manière suivante : `INTERFACESv4="interface"`

Décommenter ces 2 lignes permet au service DHCP de trouver et utiliser les fichiers nécessaires, à savoir le fichier de configuration et le fichier du processus. 
Le "interface" ici prend la valeur de l'interface que l'on souhaite utiliser, donc enp0s3 dans mon cas (les guillemets présents doivent rester).

### dhcpd.conf

Ensuite, il faut modifier la configuration du dhcp en modifiant le fichier `/etc/dhcp/dhcpd.conf`. Ici, on peut modifier les lignes `option domain-name` et/ou `option domain-name-servers` pour indiquer des noms de domaines propres si l'on a besoin de leur donner des noms en particulier. Ici, ces changements n'ont pas beaucoup d'importance.
On peut aussi augmenter la valeur de `default-lease-time`, ce qui augmente le temps du bail.

Impérativement, il faut décommenter la ligne `authoritative`, pour que la machine soit considérer comme le responsable DHCP, sinon ça sera laisser à un autre nœud. 

Pour configurer l'attribution automatique DHCP, on écrit les lignes suivantes :
```
subnet 172.20.0.0 netmask 255.255.255.0 {
	option routers 172.20.0.1;
	option subnet-mask 255.255.255.0;
	range 172.20.0.100 172.20.0.200;
}
```
Ceci défini le sous-réseau qui va permettre d'affecter les IP à des machines hôtes par rapport à cette plage. `172.20.0.0` correspond à l'adresse IP de notre réseau, `172.20.0.1` à l'adresse IP de notre routeur et le `range` indique notre plage d'attribution.
En indiquant notre IP de routeur, la machine qui se fait attribuer le DHCP par notre machine serveur aura quand même l'information sur le routeur. Sauf contre-indication, le routeur s'occupe du service DHCP.

Pour réserver une IP, on ajoute les lignes suivantes :
```
host [nom hôte] {
	hardware ethernet [adresse MAC]
	fixed address 172.20.0.10
}
```
le `[nom hôte]` est à titre indicatif, représentant la machine qui aura cette adresse IP attribuée
Ceci permet de réserver l'adresse 172.20.0.10 à la machine avec l'adresse MAC fournie. L'adresse MAC est au format hexadécimal avec des `:` entre chaque octet (donc par paires de symboles hexadécimaux).

### Exécution

Pour exécuter le service, on entre en ligne de commande :
`sudo systemctl start isc-dhcp-server.service`

Pour que le service se relance automatique au démarrage de l'ordinateur, on entre :
`sudo systemctl start isc-dhcp-server.service`

Enfin, pour vérifier l'état du service, on utilise la commande :
`sudo systemctl start isc-dhcp-server.service`

Les erreurs avec le service sont dans un log, à savoir  `/var/log/syslog`

Enfin, on peut lancer les machines clients de test pour vérifier que l'attribution DHCP fonctionne, ainsi que la réservation d'adresse IP.


