cet exercice a été effectué avec une machine Windows Server 2022.
### Installer Active Directory
Sur une machine Windows server, ouvrir Powershell en tant qu'administrateur.

Pour installer Active Directory (AD), utiliser la commande suivante:
`Install-WindowsFeature Ad-Domain-Services`

Quand c'est installé, installer les modules de gestion avec la commande suivante:
`Install-WindowsFeature -name AD-Domain-Services -IncludeManagementTools`
### Créer le domaine
Pour créer et configurer notre domaine AD, on exécute la commande suivante :
`Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath "C:\Windows\NTDS" -DomainMode 7 -DomainName "wilders.lan" -DomainNetbiosName "DOMTEST" -ForestMode 7 -InstallDNS:$true -LogPath "C:\Windows\NTDS" -NoRebootOnCompletion:$false -SysvolPath "C:\Windows\SYSVOL"`

Ici on crée et configure à la fois la forêt Active Directory, le niveau logique supérieur d'AD, puis notre domaine, une partie de la forêt.

Il faut noter que pour `-DomainMode` et `-ForestMode`, la valeur à mettre dépend de l'OS serveur que l'on utilise, donc cette valeur niveau fonctionnel est à adapter à votre machine.
Suite à cette exécution, parce qu'on a mis `-NoRebootOnCompletion:$false`, après une demande du mot de passe administrateur et une confirmation de ce mot de passe, le serveur redémarre.
### Vérifications
On constate que suite au redémarrage, la connexion au compte administrateur affiche maintenant "DOMTEST/Administrateur".

Une fois connecté, dans une terminal Powershell, on peut exécuter différentes commandes qui permettent de confirmer que notre installation a été faite ainsi que les caractéristiques de celle-ci:

- `Get-ADDomainController` : informations sur notre contrôleur de domaine
- `-netdom query FSMO /Domain domtest` : les différents rôles FSMO (Flexible Single Master Operation) de notre contrôleur de domain
- `Get-ADForest` : informations sur notre forêt AD