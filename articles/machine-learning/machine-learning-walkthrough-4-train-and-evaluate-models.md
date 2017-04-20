<properties
    pageTitle="Étape 4 : Former et d’évaluer les modèles d’analyse prédictives | Microsoft Azure"
    description="Étape 4 de la développer une procédure pas à pas solution prédictive : Train, score et évaluer plusieurs modèles dans un Studio de formation de Machine Azure."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Procédure pas à pas, étape 4 : Formation et évaluer les modèles d’analyse prédictives

Cette rubrique contient la quatrième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Charger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Former et d’évaluer les modèles**
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Un des avantages de l’utilisation de Studio d’apprentissage Machine Azure pour créer des modèles d’apprentissage machine consiste à essayer de plus d’un type de modèle à la fois d’une expérience et de comparer les résultats. Ce type d’expérimentation vous aide à trouver la meilleure solution à votre problème.

Dans l’expérience que nous mettons au point dans cette procédure pas à pas, nous créer deux types de modèles et ensuite comparer leurs résultats score pour décider quel algorithme que nous souhaitons utiliser notre expérience finale.  

Il existe différents modèles que nous aurions pu choisir à partir de. Pour visualiser les modèles disponibles, développez le nœud de **Formation de Machine** dans la palette de module et puis **Initialiser le modèle** et les nœuds en dessous. Aux fins de cette expérimentation, sélectionnez la Machine de vecteur de prise en charge (SVM) et les modules de classe de deux arborescences de décision amplifié.    

> [AZURE.TIP] Pour obtenir de l’aide à déterminer le problème particulier que vous tentez de résoudre le mieux à l’algorithme d’apprentissage automatique meilleur, consultez [comment choisir les algorithmes pour la formation de Machine Microsoft Azure](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Les modèles du train
Tout d’abord, nous allons définir le modèle d’arborescence de décision prioritaire :  

1.  Trouver l' [Arbre de décision amplifiée deux-classe] [ two-class-boosted-decision-tree] module dans la palette de module et faites-le glisser vers la zone de dessin.
2.  Rechercher le [Modèle de Train] [ train-model] module, faites-le glisser vers la zone de dessin et connectez la sortie du module d’arborescence de décision amplifiée pour le port d’entrée gauche (« modèle non formé ») du [Modèle de Train] [ train-model] module.
    
    L' [Arbre de décision amplifiée deux-classe] [ two-class-boosted-decision-tree] module initialise le modèle générique et [Former le modèle] [ train-model] utilise des données d’apprentissage pour former le modèle. 
     
3.  Connectez la sortie de gauche (« résultat Dataset ») de la gauche [D’exécuter un Script R] [ execute-r-script] module à droite d’entrée port (« Dataset ») du [Modèle de Train] [ train-model] module.

    > [AZURE.TIP] Nous ne devons deux des entrées, une des sorties d' [Exécuter un Script R] [ execute-r-script] module de cette expérience, afin que nous les laisser non attachée. 

4.  Sélectionnez le [Modèle de Train] [ train-model] module. Dans le volet **Propriétés** , cliquez sur **lancer le sélecteur de colonne**, sélectionnez **Tous les Types** dans la liste déroulante vers le bas, sous **Colonnes disponibles** et entrez « Risque de crédit » dans le champ de texte. Sélectionnez **Tous les Types** dans la liste déroulante sous les **Colonnes sélectionnées**. Sélectionnez « Risque de crédit » et cliquez sur la flèche en surbrillance pour le déplacer vers les **Colonnes sélectionnées**. 
5.  Cliquez sur **Enregistrer**.


Cette partie de l’expérience ressemble maintenant à ceci :  

![Formation d’un modèle][1]

Ensuite, nous définissons le modèle SVM.  

Tout d’abord, une petite explication sur SVM. Les arbres de décision amplifiée fonctionnent bien avec les fonctions de n’importe quel type. Toutefois, étant donné que le module SVM génère un classifieur linéaire, le modèle qu’il génère a l’erreur de test meilleur lorsque toutes les fonctions numériques ont la même échelle. Pour convertir toutes les fonctions numériques à la même échelle, nous utilisons une transformation « Tanh » (avec la [Normalisation des données] [ normalize-data] module.) Cela transforme les numéros de la plage [0,1]. Fonctions de chaîne sont converties par le module SVM aux fonctionnalités par catégorie, puis à des fonctions binaires 0/1, nous n’avez pas besoin de transformer manuellement les fonctions de chaîne. En outre, nous ne voulons pas transformer la risque de crédit colonne (21) - il est numérique, mais c’est la valeur que nous allons formation du modèle à prévoir, donc nous devons le laisse.  

Pour définir le modèle SVM, effectuez les opérations suivantes :

1.  Rechercher la [Classe deux Machine à vecteur Support] [ two-class-support-vector-machine] module dans la palette de module et faites-le glisser vers la zone de dessin.
2.  Cliquez sur le [Modèle de Train] [ train-model] module, sélectionnez la **copie**, puis avec le bouton droit de la zone de dessin et sélectionnez **Coller**. La copie du [Modèle de Train] [ train-model] module a la même sélection de colonne que l’original.
3.  La sortie du module SVM de se connecter au port d’entrée gauche (« modèle non formé ») du deuxième [Modèle de Train] [ train-model] module.
4.  Rechercher les [Normaliser les données] [ normalize-data] module et faites-le glisser vers la zone de dessin.
5.  Connecter l’entrée de ce module dans la sortie de gauche de la gauche [D’exécuter un Script R] [ execute-r-script] module (Notez que le port de sortie d’un module peut être connecté à plus d’un autre module).
6.  Connectez le port de sortie gauche (« transformé Dataset ») de la [Normalisation des données] [ normalize-data] module à droite d’entrée port (« Dataset ») du deuxième [Modèle de Train] [ train-model] module.
7.  Dans le volet **Propriétés** pour [Normaliser les données] [ normalize-data] module, sélectionnez **Tanh** pour le paramètre de la **méthode de Transformation** .
8.  Cliquez sur **lancer le sélecteur de colonne**« Aucune colonne » pour **Commencer avec**, sélectionnez **inclure** dans la liste déroulante du premier, sélectionnez le **type de colonne** dans la liste déroulante du deuxième et sélectionnez **numérique** dans la liste déroulante tiers. Cette option spécifie que toutes les colonnes numériques (et numérique uniquement) sont transformés.
9.  Cliquez sur le signe plus (+) à droite de cette ligne, cela crée une rangée de menus déroulants. Sélectionnez **Exclure** dans la première liste déroulante de **noms de colonne** dans la deuxième liste déroulante et cliquez sur le champ de texte et sélectionnez « Risque de crédit » dans la liste des colonnes. Spécifie que la colonne de risque de crédit doit être ignorée (nous cela est nécessaire, car cette colonne est numérique et dans le cas contraire sera transformé).
10. Cliquez sur **OK**.  


La [Normalisation des données] [ normalize-data] module est maintenant défini pour effectuer une transformation de Tanh sur toutes les colonnes numériques, à l’exception de la colonne de risque de crédit.  

Cette partie de notre expérience doit maintenant se présenter comme suit :  

![Le deuxième modèle de formation][2]  

##<a name="score-and-evaluate-the-models"></a>Score et évaluer les modèles

Nous utilisons les données de tests qui a été séparées par les [Données fractionnées] [ split] module pour évaluer nos modèles bien formés. Nous pouvons ensuite comparer les résultats des deux modèles pour voir qui a généré des résultats meilleurs.  

1.  Rechercher le [Modèle de Score] [ score-model] module et faites-le glisser vers la zone de dessin.
2.  Connecter le [Modèle de Train] [ train-model] module qui est connecté à l' [Arbre de décision amplifiée deux-classe] [ two-class-boosted-decision-tree] module à gauche d’entrée port du [Modèle de Score] [ score-model] module.
3.  Connectez le port d’entrée droit du [Modèle de Score] [ score-model] module à la sortie gauche du droit [D’exécuter un Script R] [ execute-r-script] module.

    Le [Modèle de Score] [ score-model] module peut maintenant prendre les informations de crédit à partir des données de tests, l’exécuter via le modèle et comparer les prévisions le modèle génère avec la colonne de risque de crédit réelle dans les données de test.

4.  Copier et coller le [Modèle de Score] [ score-model] module pour créer une seconde copie ou un nouveau module de faire glisser la zone de dessin.
5.  Connectez le port d’entrée gauche de ce module au modèle SVM (c'est-à-dire, se connecter à la voie de sortie du [Modèle de Train] [ train-model] module qui est connecté à la [Machine à vecteur Support deux-classe] [ two-class-support-vector-machine] module).
6.  Pour le modèle SVM, nous avons à faire la même transformation sur les données de test, comme nous l’avons fait pour les données d’apprentissage. Ainsi, de copier et de coller les [Normaliser les données] [ normalize-data] module pour créer une deuxième copie et connectez-le à la sortie gauche du droit [D’exécuter un Script R] [ execute-r-script] module.
7.  Connectez le port d’entrée droit du [Modèle de Score] [ score-model] module à la sortie de gauche de la [Normalisation des données] [ normalize-data] module.  

Pour évaluer les résultats du score deux, nous utilisons un [Modèle d’évaluation] [ evaluate-model] module.  

1.  Rechercher le [Modèle d’évaluation] [ evaluate-model] module et faites-le glisser vers la zone de dessin.
2.  Connectez le port d’entrée gauche à la voie de sortie du [Modèle de Score] [ score-model] module associé avec le modèle d’arborescence de décision amplifiées.
3.  Connectez le port d’entrée droit de [Modèle de Score] [ score-model] module.  

Pour exécuter l’essai, cliquez sur le bouton **exécuter** au-dessous de la toile. Il peut prendre quelques minutes. Un indicateur de rotation sur chaque module montre qu’il est en cours d’exécution, puis une coche verte lorsque le module est terminé. Lorsque tous les modules ont une coche, l’expérience a fini de s’exécuter.

L’expérience doit maintenant ressembler à ceci :  

![Évaluation des modèles][3]

Pour vérifier les résultats, cliquez sur le port de sortie du [Modèle d’évaluation] [ evaluate-model] module et sélectionnez **visualiser**.  

Le [Modèle d’évaluation] [ evaluate-model] module génère une paire de courbes et de mesures qui vous permettent de comparer les résultats des deux modèles évaluées. Vous pouvez afficher les résultats sous forme de courbes de caractéristique d’opérateur destinataire (ROC), précision et rappel courbes ou courbes d’ascenseur. Affichée des données supplémentaires comprennent une matrice de confusion, les valeurs cumulées pour la zone sous la courbe (AUC) et d’autres mesures. Vous pouvez modifier la valeur de seuil en déplaçant le curseur de gauche ou de droite et voir comment il affecte l’ensemble des mesures.  

À droite du graphique, cliquez sur **groupe de données Scored** ou **Scored le groupe de données à comparer** en surbrillance la courbe associée et d’afficher les attributs mesurables associés ci-dessous. Dans la légende pour les courbes, « Score dataset » correspond au port d’entrée gauche du [Modèle d’évaluation] [ evaluate-model] module - dans notre cas, c’est le modèle d’arborescence de décision amplifiées. « Score de groupe de données à comparer » correspond au port d’entrée droite - le modèle SVM dans notre cas. Lorsque vous cliquez sur un de ces libellés, la courbe de ce modèle est mis en surbrillance et afficher les mesures correspondantes, comme illustré dans le graphique suivant.  

![Courbes ROC pour les modèles][4]

En examinant ces valeurs, vous pouvez choisir le modèle qui est le plus proche de vous donner les résultats que vous recherchez. Vous pouvez revenir en arrière et effectuer une itération sur votre expérience en modifiant les valeurs dans les différents modèles. 

> [AZURE.TIP] Chaque fois que vous exécutez l’expérience un enregistrement de cette itération est conservé dans l’historique de l’exécution. Vous pouvez afficher ces itérations et revenir à une d'entre elles, en cliquant sur **Afficher l’historique de s’exécuter** sous la zone de dessin. Vous pouvez également cliquer sur **Exécuter précédente** dans le volet **Propriétés** pour revenir à l’itération qui précède immédiatement le que vous avez ouvert.
> 
Vous pouvez effectuer une copie d’une itération de votre expérience en cliquant sur **Enregistrer sous** au-dessous de la toile. Propriétés de **Résumé** et la **Description** de l’expérimentation permet de conserver une trace de ce que vous avez essayé dans les itérations de votre expérience.

>  Pour plus d’informations, consultez [Gérer expérimenter des itérations dans Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md).  


----------

**Suivant : [déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
