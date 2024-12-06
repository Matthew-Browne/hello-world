Après avoir installé le service DNS, il faut désactiver le pare-feu sur la machine serveur.
Il faut aussi en amont aller dans les paramètres réseau et mettre une adresse IP statique sur la machine serveur. Dans mon cas, l'adresse est `10.0.0.253`.

Suite à cela, on ouvre le gestionnaire DNS. Le menu à gauche contient notre serveur avec différents dossiers dans lequel on peut paramétrer notre DNS.

Pour la recherche directe, on fait un clic droit sur "Zone de recherche directe" et on sélectionne "Nouvelle zone". Pour cet exercice, on crée à une zone principale avec le nom "wilders.lan".
Suite à sa création, cette zone apparaît dans le menu à gauche en dessous de "Zone de recherche directe". 
Dans cette nouvelle zone, il faut définir un hôte en faisant un clic droit sur "wilders.lan" et en choisissant "Nouvel hôte (A ou AAAA)". Le formulaire qui suit demande un nom et une adresse IP. Pour cet exercice, on laisse le nom vide, ce qui fait que notre hôte aura le nom "wilders.lan" par défaut et on met l'adresse de notre serveur, à savoir `10.0.0.253`.

Sur la machine client, on paramètre une adresse IP (ici j'ai mis `10.0.0.100`) et surtout un DNS préféré, sans lequel notre client ne saura pas quel adresse DNS utiliser pour effectuer les recherches DNS. Ici, on met `10.0.0.253`.
Suite à cela, on peut ouvrir PowerShell et entrer la commande `nslookup wilders.lan`, ce qui permet de confirmer si la recherche DNS a bien trouvé notre domaine et l'adresse IP de notre serveur. Si le message `DNS request timed out` apparaît, cela signifie que la configuration est incorrecte.

Pour l'alias, clic droit sur la zone "wilders.lan" (pas l'hôte) et sélectionner "Nouvel alias (CNAME)". On indique l'alias (le champ en dessous indique le nom entier, à savoir notre alias greffé au nom de zone) et on indique enfin à quoi réfère notre alias. Dans le cadre de l'exercice, l'alias "dns" est ajouté pour "wilder.lan", donnant l'alias complet "dns.wilders.lan". Quand cet alias est créé, il s'affiche en dessous de notre hôte.
Sur la machine client, `nslookup dns.wilders.lan` permet de confirmer que l'alias fonctionne et nous renvoie bien sur "wilders.lan".

Enfin, pour la recherche inversée, on créé une nouvelle zone avec un clic droit sur le dossier "Zone de recherche inversée". Le formulaire qui suit nous demande de le remplir en indiquant la partie réseau de notre adresse IP, donc pour nous c'est `10.0.0`. Dans le dossier, il y aura à présent une zone nommée quelque chose de la forme "0.0.10.in-addr.arpa".
Clic droit sur la zone affiche l'option "Nouveau pointeur (PTR)", où l'on indique l'adresse de l'hôte et le nom de domaine. Ici, ça sera `10.0.0.253` et "wilders.lan" respectivement. Ce pointeur s'ajoute dans la zone de recherche inversée.
Un dernier test avec `nslookup 10.0.0.253` permet de confirmer que l'on trouve bien notre serveur et affiche bien le domaine "wilders.lan".