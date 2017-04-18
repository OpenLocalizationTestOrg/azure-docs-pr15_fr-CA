En raison du développement en cours, la version de SDK Android installée dans le Studio Android ne peut pas correspondre la version dans le code. Le Kit de développement Android référencé dans ce didacticiel est la version 23, plus tard au moment de la rédaction. Le numéro de version peut augmenter ainsi apparaissent de nouvelles versions du Kit de développement, nous vous recommandons d’utiliser la dernière version disponible.

Deux problèmes d’incompatibilité de version sont les suivants :

1. Lorsque vous générez ou régénérez le projet, vous pouvez obtenir Gradle les messages d’erreur comme «**Impossible de trouver la cible Google Inc.:Google APIs:n**».

2. Les objets Android standard dans le code qui doit résoudre basés sur `import` instructions peuvent générer des messages d’erreur.

Si un de ces apparaît, la version du SDK Android installé dans Android Studio ne peut pas correspondre la cible du Kit de développement du projet téléchargé.  Pour vérifier la version, apportez les modifications suivantes :


1. Dans un Studio Android, cliquez sur **Outils** => **Android** => **Manager du Kit de développement logiciel**. Si vous n’avez pas installé la dernière version de la plate-forme SDK, puis cliquez sur pour l’installer. Prenez note du numéro de version.

2. Dans l’onglet Explorateur de projets, sous **Scripts de Gradle**, ouvrez le fichier **build.gradle (modeule : app)**. Assurez-vous que le **compileSdkVersion** et le **buildToolsVersion** sont définies avec la dernière version de kit de développement logiciel installée. Les balises peuvent ressembler à ceci :
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. Dans l’Explorateur de projet Studio Android droit sur le nœud du projet, choisissez **Propriétés**et dans la colonne de gauche, choisissez **Android**. Assurez-vous que la **Cible Build de projet** est définie à la même version de kit de développement logiciel que le **targetSdkVersion**.

4. Dans Studio Android, le fichier manifeste est n’est plus utilisé pour spécifier la cible SDK et la version minimale de SDK, contrairement à la casse avec Eclipse.
