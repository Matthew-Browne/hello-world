Tout d'abord, on installe bind9 :
```
$ sudo apt update
$ sudo apt install -y bind9 bind9utils bind9-doc dnsutils
```
Ensuite, on va configurer bind9, à savoir les fichiers dans le dossier `/etc/bind`.

Dans le fichier `/etc/bind/named.conf.options`, on édite pour avoir quelque chose de la sorte:

```
acl internal-network {
192.168.0.0/24;
};
options {
        directory "/var/cache/bind";
        allow-query { localhost; internal-network; };
        allow-transfer { localhost; };
        forwarders { 8.8.8.8; };
        recursion yes;
        dnssec-validation auto;
        listen-on-v6 { any; };
};
```
Ceci paramètre notre réseau privé. Dans mon cas, les deux dernières lignes dans "options" étaient déjà présentes, donc à adapter en fonction du fichier que l'on récupère.

Ensuite, dans le fichier `/etc/bind/named.conf.local`, ajouter les zones suivantes :

```
zone "wilders.lan" IN {
        type master;
        file "/etc/bind/forward.wilders.lan";
        allow-update { none; };
};
zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "/etc/bind/reverse.wilders.lan";
        allow-update { none; };
};
```
Dans ce fichier, on a défini deux zones pour notre domaine, à savoir la zone de recherche directe "wilders.lan" et la zone de recherche inversée.
Il faut maintenant les configurer plus précisément. Pour cela, les deux zones ont besoin de leur propre fichiers, que l'on peut créer en copiant `/etc/bind/db.local`.

Pour le fichier `/etc/bind/forward.wilders.lan`, on aura :

```
$TTL 604800
@ IN SOA primary.wilders.lan. root.primary.wilders.lan. (
                                   2022072651 ; Serial
                                   3600 ; Refresh
                                   1800 ; Retry
                                   604800 ; Expire
                                   604600 ) ; Negative Cache TTL
;Name Server Information
@       IN  NS    primary.wilders.lan.

;IP address of Your Domain Name Server(DNS)
primary IN  A     192.168.0.40

;Mail Server MX (Mail exchanger) Record
wilders.lan. IN MX 10   mail.wilders.lan.

;A Record for Host names
www     IN  A    192.168.0.50
mail    IN  A    192.168.0.60

;CNAME Record
ftp     IN CNAME www.wilders.lan.
```
Ceci paramètre notre zone de recherche directe, avec notamment les caractéristiques de notre zone, l'adresse IP de notre serveur DNS, un mailer, nos hôtes et un CNAME.

De même, le fichier `/etc/bind/reverse.wilders.lan` aura :

```
$TTL 86400
@ IN SOA wilders.lan. root.wilders.lan. (
                           2022072752 ;Serial
                           3600 ;Refresh
                           1800 ;Retry
                           604800 ;Expire
                           86400 ;Minimum TTL
)

;Your Name Server Info
@ IN NS primary.wilders.lan.
primary   IN  A    192.168.0.40

;Reverse Lookup for Your DNS Server
40        IN PTR   primary.wilders.lan.

;PTR Record IP address to HostName
50        IN PTR   www.wilders.lan.
60        IN PTR   mail.wilders.lan.
```
Ici il faut bien s'assurer de pointer vers notre zone de recherche directe.

Pour que notre service DNS écoute sur IPv4, on édite le fichier `/etc/default/named` en changeant la ligne "OPTIONS" de la sorte :

`OPTIONS="-u bind -4"`

Maintenant on peut lancer le service pour le mettre en fonctionnement :

```
$ sudo systemctl start named
$ sudo systemctl enable named
```
La deuxième commande permet de faire en sorte que le service se lance automatiquement lorsque la machine redémarre.

`sudo systemctl status named` permet de confirmer la condition du service.

Par précaution, on peut lancer la commande `sudo ufw allow 53` pour permettre au pare-feu d'autoriser les flux sur le port 53 que le DNS utilise.

`sudo named-checkconf /etc/bind/named.conf.local` permet de vérifier les erreurs de syntaxe, retournant rien s'il n'y a aucune erreur.

`sudo named-checkzone wilders.lan /etc/bind/forward.wilders.lan` et `sudo named-checkzone wilders.lan /etc/bind/reverse.wilders.lan` permettent de vérifier les erreurs de configuration des zones.

Maintenant, on peut tester le service avec une machine client.
D'abord, on change le DNS qu'utilise la machine client en éditant le fichier `/etc/resolv.conf` en mettant à la place :

```
search linuxtechi.local
nameserver 192.168.0.40
```

la commande `dig primary.linuxtechi.local` sur la machine client permet de confirmer que l'on arrive à trouver le domaine. S'il y a une erreur, il faut vérifier si la configuration DNS, le service DNS ou le service réseau du serveur DNS fonctionnent bien.
La recherche inversée est de la forme `dig -x 192.168.0.40`.
`nslookup primary.wilders.lan` permet aussi de confirmer que l'on arrive bien à trouver notre serveur DNS. 


