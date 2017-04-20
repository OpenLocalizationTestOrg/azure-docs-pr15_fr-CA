Azure déterminera la version de Python à utiliser pour son environnement virtuel avec l’ordre de priorité suivant :

1. version spécifiée dans runtime.txt dans le dossier racine
1. version spécifiée par le paramètre de Python dans la configuration d’application web (les **paramètres** > lame de**Paramètres d’Application** pour votre application web dans le portail Azure)
1. Python-2.7 est la valeur par défaut si aucun d'entre eux sont spécifiés

Valeurs valides pour le contenu de 

    \runtime.txt

sont les suivantes :

- Python-2.7
- 3.4-Python

Si la version micro (le troisième chiffre) est spécifié, il est ignoré.
