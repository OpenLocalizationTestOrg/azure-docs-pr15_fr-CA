<properties
    pageTitle="Explorateur de scripts DocumentDB, un éditeur JavaScript | Microsoft Azure"
    description="Obtenir des informations sur l’Explorateur de scripts DocumentDB, un outil de portail Azure pour gérer les artefacts programmation côté serveur DocumentDB, y compris les procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur."
    keywords="éditeur JavaScript"
    services="documentdb"
    authors="kirillg"
    manager="jhubbard"
    editor="monicar"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="kirillg"/>

# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-documentdb-script-explorer"></a>Créer et exécuter des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur à l’aide de l’Explorateur de scripts DocumentDB

Cet article fournit une vue d’ensemble de l’Explorateur de scripts [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) , qui est un éditeur JavaScript dans le portail Azure qui vous permet de visualiser et d’exécuter les artefacts programmation côté serveur DocumentDB, y compris les procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur. Pour en savoir plus sur la programmation côté serveur de DocumentDB dans l’article de [procédures stockées, les déclencheurs de base de données et les fichiers UDF](documentdb-programming.md) .

## <a name="launch-script-explorer"></a>Lancer l’Explorateur de scripts

1. Dans le portail d’Azure, dans le Jumpbar, cliquez sur **DocumentDB (NoSQL)**. Si **Les comptes de DocumentDB** n’est pas visible, cliquez sur **Plus de Services** , puis sur **DocumentDB (NoSQL)**.

2. Dans le menu ressources, cliquez sur **Explorateur de scripts**.

    ![Capture d’écran de la commande de l’Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    Les zones de liste déroulante **base de données** et de la **Collection** sont remplis au préalable en fonction du contexte dans lequel vous lancez l’Explorateur de scripts.  Par exemple, si vous lancez à partir d’une lame de la base de données, la base de données en cours est renseignée au préalable.  Si vous lancez à partir d’une lame de collection, la collection en cours est alors préremplie.

4.  Les zones de liste déroulante **base de données** et de **Collection** permet de modifier facilement la collection à partir de laquelle les scripts sont en cours d’affichage sans avoir à fermer et à relancer Explorateur de scripts.  

5. Explorateur de scripts prend également en charge le filtrage par leur propriété id le jeu actuellement chargé de scripts.  Il suffit de taper dans la zone de filtre et les résultats dans la liste de l’Explorateur de scripts sont filtrés en fonction de vos critères fournis.

    ![Capture d’écran de Script Explorer résultats filtrés](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


    > [AZURE.IMPORTANT] L’Explorateur de scripts filtrer uniquement les filtres des fonctionnalités du jeu ***actuellement*** chargé de scripts et n’actualise pas automatiquement la collection actuellement sélectionnée.

5. Pour actualiser la liste des scripts chargés par le Script de l’Explorateur, cliquez simplement sur la commande **Actualiser** en haut de la lame.

    ![Commande d’actualisation d’écran d’Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>Créer, afficher et modifier des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur

Explorateur de scripts vous permet d’exécuter facilement les opérations sur les objets de programmation côté serveur DocumentDB.  

- Pour créer un script, il suffit de cliquer sur applicable Créer commande au sein de l’Explorateur de scripts, fournir un id, entrez le contenu du script et cliquez sur **Enregistrer**.

    ![Capture d’écran de Script Explorer créer option, afficher le code JavaScript de l’éditeur](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Lors de la création d’un déclencheur, vous devez également spécifier l’opération de type et le déclenchement du déclencheur

    ![Capture d’écran de Script Explorer créer d’option de déclencheur](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Pour afficher un script, cliquez simplement sur le script qui vous intéressent.

    ![Expérience de script de capture d’écran de Script Explorer affichage](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Pour modifier un script, il suffit d’apportez les modifications souhaitées dans le code JavaScript à l’éditeur et cliquez sur **Enregistrer**.

    ![Expérience de script de capture d’écran de Script Explorer affichage](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Pour ignorer les modifications en attente à un script, cliquez simplement sur la commande **Annuler** .

    ![Rejet de la capture d’écran de Script Explorer modifie l’expérience de](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- Explorateur de scripts vous permet également de visualiser facilement les propriétés système de script actuellement chargé en cliquant sur la commande **Propriétés** .

    ![Permet d’afficher les propriétés de script de capture d’écran d’Explorateur de scripts](./media/documentdb-view-scripts/scriptproperties.png)

    > [AZURE.NOTE] La propriété timestamp (DTS) est représentée en interne sous forme d’heure de l’époque, mais Explorateur de scripts affiche la valeur dans un format lisible GMT.

- Pour supprimer un script, sélectionnez-le dans l’Explorateur de scripts et cliquez sur la commande **Supprimer** .

    ![Commande de suppression de capture d’écran d’Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Cliquez sur **Oui** pour confirmer l’action supprimer ou annuler l’action de suppression en cliquant sur **non**.

    ![Commande de suppression de capture d’écran d’Explorateur de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>Exécuter une procédure stockée

> [AZURE.WARNING] Exécution de procédures stockées dans l’Explorateur de scripts n’est pas encore prise en charge pour les collections de partitionné de côté serveur. Pour plus d’informations, visitez le [partitionnement et la mise à l’échelle dans DocumentDB](documentdb-partition-data.md).

Explorateur de scripts vous permet d’exécuter des procédures stockées de côté serveur à partir du portail Azure.

- Lors de l’ouverture d’une nouvelle lame de procédure stockée de créer, un script par défaut (*préfixe*) sera déjà fourni. Pour exécuter le script de *préfixe* ou de votre propre script, ajoutez un *code* et les *entrées*. Pour les procédures stockées qui acceptent des paramètres multiples, toutes les entrées doivent être dans un tableau (par exemple *[« foo », « bar »]*).

    ![Lame de capture d’écran de Script Explorer des procédures stockées pour ajouter des entrées et d’exécuter une procédure stockée](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- Pour exécuter une procédure stockée, cliquez simplement sur la commande **Enregistrer et exécuter** dans le volet de l’éditeur de script.

    > [AZURE.NOTE] La commande **Enregistrer et exécuter** enregistrera votre procédure stockée avant l’exécution, ce qui signifie qu’il va remplacer la version précédemment enregistrée de la procédure stockée.

- Succès exécutions de procédures stockées présente l’état *enregistré avec succès et de l’exécution de la procédure stockée* et les résultats renvoyés seront répercutées dans le volet de *résultats* .

    ![Lame de capture d’écran de Script Explorer des procédures stockées, pour exécuter une procédure stockée](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- Si l’exécution rencontre une erreur, l’erreur sera remplie dans le volet de *résultats* .

    ![Afficher les propriétés de script de capture d’écran d’Explorateur de scripts. Exécuter une procédure stockée avec des erreurs](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>Travailler avec des scripts à l’extérieur du portail.

L’Explorateur de scripts dans le portail Azure est tout simplement un moyen de travailler avec des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur dans DocumentDB. Vous pouvez également travailler avec des scripts à l’aide de la l’API REST et le [SDK de client](documentdb-sdk-dotnet.md). La documentation de l’API REST comprend des exemples pour l’utilisation avec [reste d’à l’aide de procédures stockées](https://msdn.microsoft.com/library/azure/mt489092.aspx), [reste d’à l’aide de fonctions définies par l’utilisateur](https://msdn.microsoft.com/library/azure/dn781481.aspx)et des [déclencheurs à l’aide de reste](https://msdn.microsoft.com/library/azure/mt489116.aspx). Exemples sont également disponible montrant comment [travailler avec des scripts à l’aide de C#](documentdb-dotnet-samples.md#server-side-programming-examples) et de [travailler avec des scripts à l’aide de Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la programmation côté serveur de DocumentDB dans l’article de [procédures stockées, les déclencheurs de base de données et les fichiers UDF](documentdb-programming.md) .

Le [cursus](https://azure.microsoft.com/documentation/learning-paths/documentdb/) est également une ressource utile pour vous guider pendant que vous en savoir plus sur DocumentDB.  
