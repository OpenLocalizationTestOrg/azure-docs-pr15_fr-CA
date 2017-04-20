<properties
    pageTitle="Étape 3 : Créer une nouvelle expérience d’apprentissage automatique | Microsoft Azure"
    description="Étape 3 de la développer une procédure pas à pas une solution : créer une nouvelle expérience de formation dans un Studio de formation de Machine Azure."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Procédure pas à pas, étape 3 : Créer une expérience d’apprentissage automatique de Azure nouveau

Il s’agit de la troisième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Charger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  **Créer une nouvelle expérience**
4.  [Former et d’évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

L’étape suivante de cette procédure pas à pas est de créer une expérience dans le Studio d’apprentissage Machine qui utilise le groupe de données que nous avons téléchargés.  

1.  Studio, cliquez sur **+ Nouveau** en bas de la fenêtre.
2.  Sélectionnez **essai**et puis sélectionnez « Essai à blanc ». Sélectionnez le nom d’expérimentation par défaut en haut de la zone de dessin et d’attribuer un nom significatif

    > [AZURE.TIP] Il est recommandé de remplir dans le **Résumé** et la **Description** de l’expérience dans le volet **Propriétés** . Ces propriétés vous offrent la possibilité de documenter l’expérimentation afin que quiconque examine ensuite comprendre vos objectifs et la méthodologie.

3.  Dans la palette de module à gauche de l’expérimentation de canevas, développez **Groupes de données enregistrées**.
4.  Trouver le groupe de données que vous avez créé sous **Mes groupes de données** et faites-la glisser vers la zone de dessin. Vous trouverez également le groupe de données en entrant le nom dans la zone de **recherche** au-dessus de la palette.  

##<a name="prepare-the-data"></a>Préparer les données
Vous pouvez afficher les 100 premières lignes de données et des informations statistiques pour le groupe de données entier en cliquant sur le port de sortie du groupe de données (le petit cercle en bas) et en sélectionnant les **visualiser**.  

Étant donné que le fichier de données a été livré avec des en-têtes de colonne, Studio a fourni les rubriques génériques (Col1, Col2, *etc.*). Bonne en-têtes ne sont pas essentielles à la création d’un modèle, mais ils rendent plus facile de travailler avec les données de l’expérimentation. En outre, lorsque nous publions finalement ce modèle dans un service web, les en-têtes permettra d’identifier les colonnes à l’utilisateur du service.  

Nous pouvons ajouter des en-têtes de colonne à l’aide de [Modifier les métadonnées] [ edit-metadata] module.
Vous utilisez les [Métadonnées de modifier] [ edit-metadata] module pour modifier les métadonnées associées à un groupe de données. Dans ce cas, il peut fournir plusieurs noms conviviaux pour les en-têtes de colonne. 

Permet de [Modifier les métadonnées][edit-metadata], vous d’abord spécifiez les colonnes à modifier (dans ce cas, chacun d'entre eux.) Ensuite, vous spécifiez l’action à effectuer sur les colonnes (dans ce cas, la modification des en-têtes de colonne.)

1.  Dans la palette de module, tapez « métadonnées » dans la zone de **recherche** . Vous verrez [Modifier les métadonnées] [ edit-metadata] apparaissent dans la liste du module.
2.  Cliquez et faites glisser le [Modifier les métadonnées] [ edit-metadata] module sur le canevas et déposez-le sous le groupe de données que nous avons ajoutés précédemment.
3.  Connecter le groupe de données pour [Modifier les métadonnées][edit-metadata]: cliquez sur le port de sortie du groupe de données (le petit cercle au bas du groupe de données.) Ensuite, faites glisser vers le port d’entrée, de [Modifier les métadonnées] [ edit-metadata] (le petit cercle en haut du module), puis relâchez le bouton de la souris. Le groupe de données et d’un module de restent connectés même si vous déplacez une de la zone de dessin.

    L’expérience doit maintenant ressembler à ceci :  

    ![Ajout de métadonnées de modification][2]
    
    Le point d’exclamation rouge indique que nous n’avons pas encore défini les propriétés de ce module. Nous allons le faire maintenant.
    
    > [AZURE.TIP] Vous pouvez ajouter un commentaire à un module en double-cliquant sur le module et de saisie de texte. Vous pouvez voir en un coup de œil sur ce que fait le module dans votre expérience. Dans ce cas, double-cliquez sur [Modifier les métadonnées] [ edit-metadata] module et le type de commentaire « ajouter en-têtes de colonne ». Cliquez sur la zone de dessin pour fermer la zone de texte. Pour afficher le commentaire, cliquez sur la flèche vers le bas sur le module.

4.  Sélectionnez [Modifier les métadonnées][edit-metadata], puis, dans le volet de **Propriétés** à droite de la zone de dessin, cliquez sur **lancer le sélecteur de colonne**.
5.  Dans la boîte de dialogue **Sélectionner des colonnes** , sélectionnez toutes les lignes dans **Les colonnes disponibles** et cliquez sur > pour déplacer les colonnes **Sélectionnées**.
La boîte de dialogue doit ressembler à ceci : ![sélecteur de colonne avec toutes les colonnes sélectionnées][4]
7.  Cliquez sur la coche **OK** .
8.  Dans le volet des **Propriétés** , recherchez le paramètre de **nouveaux noms de colonne** . Dans ce champ, entrez une liste de noms pour les 21 colonnes, dans le groupe de données, séparées par des virgules et dans l’ordre de la colonne. Vous pouvez obtenir les noms de colonnes à partir de la documentation du jeu de données sur le site Web UCI, ou pour plus de commodité, vous pouvez copier et coller la liste suivante :  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Le volet des propriétés ressemble à ceci :

    ![Propriétés pour modifier les métadonnées][1]

> [AZURE.TIP] Si vous souhaitez vérifier les en-têtes de colonne, exécutez l’expérimentation (au-dessous de la toile de l’expérimentation, sur **exécuter** ). Lorsqu’elle termine en cours d’exécution (une coche verte apparaît sur [Modifier les métadonnées][edit-metadata]), cliquez sur le port de sortie de [Modifier les métadonnées] [ edit-metadata] module et sélectionnez **visualiser**. Vous pouvez afficher la sortie de n’importe quel module de la même façon pour afficher la progression des données par le biais de l’expérimentation.

##<a name="create-training-and-test-datasets"></a>Formation de créer et de tester des groupes de données

L’étape suivante de l’expérimentation est de générer des groupes de données séparé qui nous permettra de formation et le test de notre modèle.

Pour ce faire, nous utilisons les [Données fractionnées] [ split] module.  

1.  Rechercher les [Données fractionnées] [ split] module, faites-le glisser vers la zone de dessin et le connecter à la dernière [Modification de métadonnées] [ edit-metadata] module.
2.  Par défaut, le rapport de division est 0,5 et le paramètre **Randomized fractionnée** est défini. Cela signifie qu’une partie aléatoire des données est la sortie via un seul port de [Données fractionnées] [ split] module et l’autre moitié à l’autre. Vous pouvez ajuster ces éléments, ainsi que le paramètre de **valeur de départ aléatoire** , modifier la séparation entre la formation et de test des données. Pour cet exemple, nous laisserons comme-est.
    
    > [AZURE.TIP] La **Fraction de lignes dans le premier groupe de données de sortie** de la propriété détermine la quantité de données est de sortie via le port de sortie gauche. Par exemple, si vous fixez le ratio à 0,7, 70 % des données est sortie via le port gauche et 30 % par la voie de droite.  
    
3. Double-cliquez sur les [Données fractionnées] [ split] module et entrez le commentaire, « données de formation et les tests fractionnement 50 % ». 

Nous pouvons utiliser les sorties de [Données fractionnées] [ split] module nous comme, mais nous allons choisir d’utiliser la sortie de gauche comme données d’apprentissage et de sortie des données comme tests.  

Comme indiqué sur le site Web de UCI, le coût d’un risque élevé de crédit comme faible de classer par erreur entrées est cinq fois plus grand que le coût de classer par erreur entrées à un faible risque élevé. Pour tenir compte de cela, nous allons générer un nouveau groupe de données qui reflète cette fonction de coût. Dans le nouveau groupe de données, chaque exemple de risque élevé est répliquée cinq fois, tandis que chaque exemple de risque faible n’est pas répliqué.   

Nous pouvons faire cette réplication à l’aide du code de R :  

1.  Rechercher et faites glisser l' [Exécuter un Script R] [ execute-r-script] module sur l’expérience de canevas et connecter le port de sortie gauche des [Données fractionnées] [ split] module sur le premier port d’entrée (« Dataset1 ») de l' [Exécution de Script de R] [ execute-r-script] module.
2. Double-cliquez sur le [Script de R exécuter] [ execute-r-script] module et entrez le commentaire, « Jeu d’ajustement des coûts ».
2.  Dans le volet **Propriétés** , supprimer le texte par défaut dans le paramètre de **Script de R** et entrez ce script :

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Nous devons faire cette opération de réplication même pour chaque sortie de [Données fractionnées] [ split] module afin que les données de formation et de tests ont le même ajustement des coûts.

1.  Droit d' [Exécuter un Script R] [ execute-r-script] module et sélectionnez **Copier**.
2.  Avec le bouton droit de la zone d’essai et sélectionnez **Coller**.
3.  Connectez le premier port d’entrée de ce [Script de R exécuter] [ execute-r-script] port des [Données] de sortie de module à droite[ split] module. 
4.  Au bas de la zone de dessin, cliquez sur **exécuter**. 

> [AZURE.TIP] La copie du module d’exécution de Script de R contient le même script que le module d’origine. Lorsque vous copiez et collez un module sur la toile, la copie conserve toutes les propriétés de l’original.  

Notre expérience ressemble maintenant à ceci :

![Ajout de scripts de R et de module de fractionnement][3]

Pour plus d’informations sur l’utilisation de scripts de R dans vos expériences, consultez [étendre votre expérience avec R](machine-learning-extend-your-experiment-with-r.md).

**Suivant : [Train et d’évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
