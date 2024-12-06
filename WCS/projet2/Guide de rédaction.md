# Préface
Ce document sert de guide pour décrire le format d'écriture de la documentation.
___
# Parties
Chaque document est divisé en différentes "parties". Une partie a un titre h1 (# en markdown) et les différentes parties sont séparées par des lignes (3 \_ ou plus en markdown).
___
# Sous-parties
Toutes sous-parties au sein d'une partie ont un titre plus petit que h1, allant dans l'ordre de h3 à h5 (donc ### à ##### en markdown). Bien évidemment, les sous-parties dans une sous-partie ont un titre plus petit que la sous-partie qui les contient. Par exemple, une sous-partie h3 peut avoir des sous-parties h4 et h5, mais des sous-parties h4 ou h5 ne peuvent pas avoir de sous-parties h3.
___
# Contenu
Dans les parties et sous-parties, le contenu a proprement parlé est écrit en texte simple, sauf :
- Les listes d'éléments à mettre préférentiellement sous une liste à puces ( "- texte" en markdown). L'affichage correct montre une liste avec un cercle rempli en début de chaque élément de la liste (comme celle-ci)
- les commandes de type code ou configuration sont à mettre entre 2 \`
- Les longs segments de code ou de configuration sont à afficher entre 2 \`\`\`
- Pour le Product Backlog, les tâches faites sont à rayer (texte entouré par \~~)

La rédaction doit être en français et doit être compréhensible par n'importe quelle personne qui y a accès. Si l'on utilise des abréviations, la première utilisation de celle-ci doit expliciter son sens.
___
# Noms de fichiers
Les noms de fichiers doivent être compréhensibles et intuitifs.

Les titres de réunion d'avancement (stand-up interne) ont un nom de fichier dans le format suivant: 
"avancement_JJ_MM_AAAA", avec :
- JJ = le jour (exemple : 13)
- MM = le mois (exemple : 11)
- AAAA = l'anée (exemple : 2024)

Dans cette même logique, le nom de fichier pour les rétrospectives sont de la forme:
"retrospective_JJ_MM_AAAA"

___
# Compte rendus
Pour les compte rendus, il faut s'assurer d'indiquer la date ainsi que les participants pour avoir une trace de qui était présent.

