On ouvre l'interface "Gestion de stratégie de groupe" avec la barre de recherche Windows, où l'on peut trouver notre domaine.
Dans notre domaine, sur notre groupe `Wilders_students` (que l'on a créé avec l'interface "Utilisateurs et ordinateurs Active Directory") , on fait clic droit -> "Créer un objet GPO \[...\]" pour créer notre GPO que l'on nomme `Wilders`.

Sur ce nouveau GPO, on fait clic droit -> "Modifier", ce qui ouvre l'éditeur de stratégie locale pour le GPO.
Ici, on a un menu à gauche et le détail à droit. on navigue le menu  de la manière suivante :

> "Configuration utilisateur" -> "Stratégies" -> "Modèles d'administration" -> "Panneau de configuration"

Dans ce dossier, on double-clic sur "Interdire l'accès au Panneau de configuration et à l'application Paramètres du PC", ce qui ouvre la fenêtre de configuration de cette stratégie, puis on sélectionne "Activé" et on valide.

Maintenant, tout les utilisateurs qui sont dans le groupe `Wilders_students` n'ont plus accès au panneau de configuration, mais les autres utilisateurs y ont encore accès. Ceci est parce que l'on a modifié les stratégies locales pour le GPO, mais les stratégies locales du GPO n'affecte que le groupe `Wilders_students` et les stratégies locales en dehors du GPO n'ont pas étés modifiés.

On peut vérifier ceci en testant l'ouverture du panneau de configuration avec les comptes et résultats suivants si tout est bien configuré :
- Utilisateur hors-domaine : peut l'ouvrir
- Utilisateur du domaine hors-groupe : peut l'ouvrir
- Utilisateur du domaine dans le groupe `Wilders_students` : ne peut pas l'ouvrir



