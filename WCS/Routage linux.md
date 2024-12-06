Suite à l'exercice 2, nous avons :
- 2 hôtes (les machines pc10 et pc11)
- Un routeur (nommé "routeur" chez moi) avec 2 interfaces (ens3 et ens4 chez moi)
- un switch lié aux 3 nœuds

Maintenant, on demande d'ajouter 2 autres réseaux :
- Un routeur r1 et une machine pc12
- Un routeur r2 et une machine pc13

Sachant qu'entre les 3 routeurs, il nous faut un switch pour permettre l'utilisation d'une seule interface sur "routeur".

Dans le fichier `/etc/network/interfaces` de r1, on a les lignes suivantes :

```
auto ens3 #lancement automatique sur restart/reboot
iface ens3 inet static
	address 10.0.1.1
	netmask 255.255.255
auto ens4
iface ens4 inet static
	address 192.168.0.251
	netmask 255.255.255.0
```

Ces lignes représentent les interfaces en communication avec pc12 et "routeur" respectivement. Elles sont en IPv4, mais pour de l'IPv6 il suffit d'ajouter les équivalents :
```
iface [interface] inet6 static
	address [addresse en format IPv6]
	netmask 64
```

Suite à la modification de ce fichier, comme précédemment il faut lancer la commande `service networking restart`.

Pour la machine pc12, il faut aussi modifier `/etc/network/interfaces` en mettant :
```
auto ens3
iface ens3 inet static
	address 10.0.1.12
	netmask 255.255.255.0
	gateway 10.0.1.1
```

Cette modification permet à r1 et pc12 de communiquer, ainsi que de définir que par défaut, la passerelle de recherche d'adresses IP de pc12 est r1.

De plus, comme pour "routeur", il faut activer le forwarding IPv4 en décommentant la ligne `net.ipv4.ip_forward=1` dans le fichier `/etc/sysctl.conf`. Il faut ensuite lancer la commande `sysctl -p /etc/sysctl.conf` pour que la modification soit prise en compte.

Enfin, il faut permettre à chaque routeur d'accéder à la deuxième interface de l'autre. En effet, dans notre cas, les ens4 (du type 192.168.0.XXX) de chaque routeur sont en communication et peuvent se ping, mais il n'est pas encore possible de ping l'autre interface.
Pour ce faire, on utilise une commande de la forme suivante :

`ip route add [réseau à atteindre] via [ip de l'interface intermédiaire]`

Par exemple, si je veux que "routeur" et r1 puisse communiquer entièrement, il faut les commandes respectives :
- Dans le terminal de "routeur" (de "routeur" vers r1) : `ip route add 10.0.1.0/24 via 192.168.0.251`
- Dans le terminal de r1 (de r1 vers "routeur") : `ip route add 10.0.0.0/24 via 192.168.0.250`

Il faut bien les deux, sinon la communication ne peut s'effectuer dans un seul sens, alors qu'un ping demande un retour. Avec cela, les machines pc10 et pc11 peuvent ping et être ping par pc12.

Attention, ces deux dernières commandes ne sont pas permanentes. Au reboot ou après un restart de networking, ils seront perdus. Pour que ces changements soient permanents, on édite le fichier `/etc/network/interfaces` de la machine à paramétrer en ajoutant une ligne de la sorte :

`up ip route add [réseau à atteindre] via [ip de l'interface intermédiaire]`

Pour finir le réseau, il suffit d'ajouter r2 lié à pc13, ce qui demande exactement les même étapes mais avec les adresses IP adaptées. Il faudra juste s'assurer de configurer les additions de routes entre chacun des 3 routeurs, donc il faudra ajouter les liens :
- de "routeur" à r2
- de r2 à "routeur"
- de r1 à r2
- de r2 à r1

Suite à cela, toutes les machines pourront communiquer entres elles.