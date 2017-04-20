<properties
    pageTitle="Mise en route avec Explorateur de stockage (aperçu) | Microsoft Azure"
    description="Gérer les ressources de stockage Azure avec Explorateur de stockage (aperçu)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Mise en route avec Explorateur de stockage (aperçu)

## <a name="overview"></a>Vue d’ensemble 

Explorateur de stockage Microsoft Azure (aperçu) est une application autonome qui vous permet de travailler facilement avec des données de stockage Azure sur Windows, OS X et Linux. Dans cet article, vous allez apprendre les différentes façons de se connecter à et la gestion de vos comptes de stockage Azure.

![Explorateur de stockage Microsoft Azure (aperçu)][15]

## <a name="prerequisites"></a>Conditions préalables

- [Télécharger et installer Explorateur de stockage (aperçu)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Se connecter à un service ou un compte de stockage

Explorateur de stockage (aperçu) fournit une multitude de manières différentes pour se connecter à des comptes de stockage. Cela inclut la connexion à des comptes de stockage associés à vos abonnements Azure, connexion à des comptes de stockage et des services partagés à partir d’autres abonnements Azure, la même connexion et gestion du stockage local à l’aide de l’émulateur de stockage Azure :

- [Se connecter à un abonnement Azure](#connect-to-an-azure-subscription) - gestion des ressources de stockage appartenant à votre abonnement Azure.
- [Travail avec stockage de développement local](#work-with-local-development-storage) - gestion du stockage local à l’aide de l’émulateur de stockage Azure. 
- [Attacher au stockage externe](#attach-or-detach-an-external-storage-account) - gestion des ressources de stockage appartenant à un autre abonnement Azure à l’aide du nom de compte du compte de stockage et la clé.
- [Compte de stockage attacher à l’aide de SAS](#attach-storage-account-using-sas) - gestion des ressources de stockage appartenant à un autre abonnement Azure à l’aide d’un SAS.
- Le [service de l’attacher à l’aide de SAS](#attach-service-using-sas) - gestion d’un service de stockage spécifique (conteneur blob, file d’attente ou un tableau) appartenant à un autre abonnement Azure à l’aide d’un SAS.

## <a name="connect-to-an-azure-subscription"></a>Se connecter à un abonnement Azure

> [AZURE.NOTE] Si vous n’avez pas un compte Azure, vous pouvez [vous inscrire à une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer vos avantages d’abonné de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Dans l’Explorateur de stockage (aperçu), sélectionnez **les paramètres de compte Azure**. 

    ![Paramètres de compte Azure][0]

1. Le volet de gauche affiche maintenant tous les comptes Microsoft que vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte**et suivez les boîtes de dialogue pour vous connecter avec un compte Microsoft qui est associé au moins un abonnement actif à Azure.

    ![Ajouter un compte][1]

1. Une fois votre inscription terminée avec un compte Microsoft, le volet de gauche s’affichent les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure avec lequel vous souhaitez travailler, puis sélectionnez **Appliquer**. (Sélection active ou désactive **tous les abonnements** sélectionner tous ou aucun des abonnements Azure répertoriés.)

    ![Sélectionnez abonnements Azure][3]

1. Le volet de gauche affiche maintenant les comptes de stockage associés à l’abonnement Azure sélectionné.

    ![Abonnements Azure sélectionnés][4]

## <a name="work-with-local-development-storage"></a>Fonctionne avec le stockage de développement local

Explorateur de stockage (aperçu) vous permet de travailler sur le stockage local à l’aide de l’émulateur de stockage Azure. Cela vous permet d’écrire du code pour test de stockage sans nécessairement disposer d’un compte de stockage déployé sur Azure (dans la mesure où le compte de stockage émulé par l’émulateur de stockage Azure).

>[AZURE.NOTE] L’émulateur de stockage Azure est actuellement pris en charge uniquement pour les fenêtres. 

1. Dans le volet gauche de l’Explorateur de stockage (aperçu), développez le **(Local et associés** > **Comptes de stockage** > nœud**(Development)** .

    ![Nœud de développement local][21]

1. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous devrez le faire via une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la version la plus récente**et installer l’émulateur. 

    ![Télécharger l’invite de l’émulateur de stockage Azure][22]

1. Une fois que l’émulateur est installé, vous aurez la possibilité de créer et manipuler des tables, des files d’attente et des objets BLOB local. Pour apprendre à travailler avec chaque type de compte de stockage, sélectionnez le lien approprié ci-dessous :

    - [Gérer les ressources de stockage blob Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Gérer les ressources de stockage de fichier Azure partage - *bientôt disponible*
    - Gérer les ressources de stockage Azure de file d’attente - *bientôt disponible*
    - Gérer les ressources de stockage de table Azure - *bientôt disponible*

## <a name="attach-or-detach-an-external-storage-account"></a>Attacher ou détacher un compte de stockage externe

Explorateur de stockage (aperçu) offre la possibilité de joindre aux comptes de stockage externe, afin que les comptes de stockage pouvant être facilement partagés. Cette section explique comment attacher à (et détacher) comptes de stockage externe.

### <a name="get-the-storage-account-credentials"></a>Obtenir les informations d’identification du compte de stockage

Pour partager un compte de stockage externe, le propriétaire de ce compte doit tout d’abord obtenir les informations d’identification - nom de compte et clé - pour le compte et puis partager ces informations avec la personne que vous souhaitez associer à ce compte (externe). Obtenir les informations d’identification du compte de stockage peut être effectué via le portail Azure en suivant ces étapes : 

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
1.  Sélectionnez **Parcourir**.
1.  Sélectionnez les **comptes de stockage**.
1.  De la lame de **Comptes de stockage** , sélectionnez le compte de stockage de votre choix.
1.  Dans la lame de **paramètres** pour le compte de stockage sélectionné, sélectionnez les **touches d’accès rapide**.

    ![Option de touches d’accès][5]
    
1.  De la lame de **touches d’accès rapide** , copier les valeurs de **Nom de compte de stockage** et de la **touche 1** pour utilisation lors de l’attachement pour le compte de stockage. 

    ![Touches d’accès rapide][6]

### <a name="attach-to-an-external-storage-account"></a>Joindre à un compte de stockage externe
Pour lier à un compte de stockage externe, vous aurez besoin de nom du compte et la clé. La section *obtenir les informations d’identification du compte de stockage* explique comment obtenir ces valeurs à partir du portail Azure. Notez toutefois que, dans le portail, la clé du compte est appelée « clé 1 » donc dans le cas où l’Explorateur de stockage (aperçu) demande une clé de compte, vous allez entrer (ou la coller) la valeur « clé 1". 
 
1.  Dans l’Explorateur de stockage (aperçu), sélectionnez **se connecter au stockage Azure**.

    ![Option de stockage Azure se connecter][23]

1.  Dans la boîte de dialogue **se connecter au stockage Azure** , spécifier la clé de compte (valeur « clé 1 » à partir du portail Azure) et puis cliquez sur **suivant**.

    ![Se connecter à la boîte de dialogue stockage Azure][24] 

1.  Dans la boîte de dialogue **Attacher un stockage externe** , entrez le nom de compte de stockage dans la zone **nom du compte** , spécifiez les autres paramètres de votre choix et cliquez sur **suivant** lorsque vous avez terminé. 

    ![Joindre la boîte de dialogue stockage externe][8]

1.  Dans la boîte de dialogue **Résumé de connexion** , vérifiez les informations. Si vous souhaitez modifier, sélectionnez **précédent** et entrez à nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **se connecter**.

1.  Une fois connecté, le compte de stockage externe sera affiché avec le texte **(externe)** ajouté au nom du compte de stockage. 

    ![Résultat de la connexion à un compte de stockage externe][9]

### <a name="detach-from-an-external-storage-account"></a>Détacher un compte de stockage externe

1.  Double-cliquez sur le compte de stockage externe que vous souhaitez détacher et - dans le menu contextuel - Sélectionnez **Détacher**.

    ![Détacher de l’option de stockage][10]

1.  Lorsque le message de confirmation s’affiche, cliquez sur **Oui** pour confirmer le détachement à partir du compte de stockage externe.

## <a name="attach-storage-account-using-sas"></a>Joindre le compte de stockage à l’aide du SAS

Un [SAS (Signature de l’accès partagé)](storage/storage-dotnet-shared-access-signature-part-1.md) donne à l’administrateur d’un abonnement Azure la possibilité d’accorder l’accès à un compte de stockage temporaire sans avoir à fournir ses informations d’identification de l’abonnement Azure. 

Pour illustrer cela, supposons que l’UtilisateurA est un administrateur d’un abonnement Azure et UtilisateurA souhaite autoriser l’UtilisateurB pour accéder à un compte de stockage pour une durée limitée avec certaines autorisations :

1. L’UtilisateurA génère un SAS (comprenant la chaîne de connexion pour le compte de stockage) pour une période de temps spécifique et avec les autorisations souhaitées.
1. L’utilisateur a partage les associations de sécurité avec la personne souhaitant avoir accès au compte de stockage - UtilisateurB, dans notre exemple.  
1. UtilisateurB utilise Explorateur de stockage (aperçu) pour attacher le compte appartenant à l’UtilisateurA utilisant les associations de sécurité fournies. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obtenir un SAS pour le compte que vous voulez partager

1.  Dans l’Explorateur de stockage (aperçu), cliquez sur le compte de stockage que vous souhaitez partager et - dans le menu contextuel - Sélectionnez **Obtenir la Signature des accès partagés**.

    ![Obtenir l’option de menu contextuel SAS][13]

1. Dans la boîte de dialogue de **Signature de l’accès partagé** , spécifiez la plage horaire et les autorisations que vous souhaitez pour le compte et sélectionnez **créer**.

    ![Boîte de dialogue SAS][14]
 
1. Une deuxième boîte de dialogue de **Signature de l’accès partagé** apparaît affichant les associations de sécurité. Sélectionnez la **copie** à côté de la **Chaîne de connexion** pour le copier dans le Presse-papiers. Sélectionnez **Fermer** pour faire disparaître la boîte de dialogue.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Joindre au compte partagé avec le contrôleur SAS

1.  Dans l’Explorateur de stockage (aperçu), sélectionnez **se connecter au stockage Azure**.

    ![Option de stockage Azure se connecter][23]

1.  Dans la boîte de dialogue **se connecter au stockage Azure** , spécifier la chaîne de connexion et puis cliquez sur **suivant**.

    ![Se connecter à la boîte de dialogue stockage Azure][24] 

1.  Dans la boîte de dialogue **Résumé de connexion** , vérifiez les informations. Si vous souhaitez modifier, sélectionnez **précédent** et entrez à nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **se connecter**.

1.  Une fois attaché, le compte de stockage sera affiché avec le texte (SAS) est ajouté au nom du compte que vous avez fournies.

    ![Le résultat d’associé à un compte à l’aide du SAS][17]

## <a name="attach-service-using-sas"></a>Attacher à l’aide des associations de sécurité de service

La section [compte de stockage attacher à l’aide de SAS](#attach-storage-account-using-sas) illustre comment un administrateur d’abonnement Azure peut accorder l’accès à un compte de stockage temporaire en générant (et partage) un SAS pour le compte de stockage. De même, un SAS peut être générée pour un service spécifique (conteneur blob, file d’attente ou un tableau) dans un compte de stockage.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Générer un SAS pour le service que vous voulez partager

Dans ce contexte, un service peut être un conteneur blob, une file d’attente ou un tableau. Les sections suivantes expliquent comment générer les associations de sécurité pour le service de liste :

- [Obtenir les associations de sécurité pour un conteneur d’objet blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obtenir les associations de sécurité pour un partage de fichiers - *bientôt disponible*
- Obtenir les associations de sécurité pour une file d’attente - *bientôt disponible*
- Obtenir les associations de sécurité pour une table - *bientôt disponible*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Joindre au service compte partagé avec le contrôleur SAS

1.  Dans l’Explorateur de stockage (aperçu), sélectionnez **se connecter au stockage Azure**.

    ![Option de stockage Azure se connecter][23]

1.  Dans la boîte de dialogue **se connecter au stockage Azure** , spécifiez l’URI de SAS et puis cliquez sur **suivant**.

    ![Se connecter à la boîte de dialogue stockage Azure][24] 

1.  Dans la boîte de dialogue **Résumé de connexion** , vérifiez les informations. Si vous souhaitez modifier, sélectionnez **précédent** et entrez à nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **se connecter**.

1.  Une fois attaché, le service récemment attaché s’affiche sous le nœud de **Service SAS ()** .

    ![Résultat de l’attachement à un service partagé à l’aide du SAS][20]

## <a name="search-for-storage-accounts"></a>Recherche des comptes de stockage

Si vous avez une longue liste de comptes de stockage, un moyen rapide de rechercher un compte de stockage particulier est d’utiliser la zone de recherche en haut du volet gauche. 

Lorsque vous tapez dans la zone de recherche, le volet de gauche affiche uniquement les comptes de stockage qui correspondent à la valeur de recherche qu'ont été saisis jusqu'à qui pointent. La capture d’écran suivante illustre un exemple dans lequel j’ai recherché tous les comptes de stockage dans lequel le nom de compte de stockage contient le texte « tarcher ».

![Recherche de compte de stockage][11]
    
Pour effacer la recherche, cliquez sur le bouton **x** dans la zone de recherche.

## <a name="next-steps"></a>Étapes suivantes
- [Gérer les ressources de stockage blob Azure avec Explorateur de stockage (aperçu)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
