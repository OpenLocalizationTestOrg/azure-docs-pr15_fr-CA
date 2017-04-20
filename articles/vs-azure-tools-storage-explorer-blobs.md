<properties
    pageTitle="Gérer les ressources de stockage d’objets Blob Azure avec Explorateur de stockage (aperçu) | Microsoft Azure"
    description="Gérer les conteneurs Blob Azure et BLOB avec Explorateur de stockage (aperçu)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Gérer les ressources de stockage d’objets Blob Azure avec Explorateur de stockage (aperçu)

## <a name="overview"></a>Vue d’ensemble

[Stockage des objets Blob Azure](./storage/storage-dotnet-how-to-use-blobs.md) est un service permettant de stocker de grandes quantités de données non structurées, telles que du texte ou des données binaires, qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS.
Vous pouvez utiliser le stockage Blob pour exposer des données au public dans le monde, ou pour stocker des données d’application à titre privé. Dans cet article, vous allez apprendre à utiliser l’Explorateur de stockage (aperçu) pour travailler avec des conteneurs blob et les objets BLOB.

## <a name="prerequisites"></a>Conditions préalables

Pour terminer la procédure décrite dans cet article, vous devez les éléments suivants :

- [Télécharger et installer Explorateur de stockage (aperçu)](http://www.storageexplorer.com)
- [Se connecter à un service ou un compte de stockage Azure](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Créer un conteneur de blob

Tous les BLOB doivent résider dans un conteneur blob, qui est simplement un regroupement logique des objets BLOB. Un compte peut contenir un nombre illimité de conteneurs et chaque conteneur peut stocker un nombre illimité d’objets BLOB.

Les étapes suivantes montrent comment créer un conteneur de blob dans Explorateur de stockage (aperçu).

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez le compte de stockage dans lequel vous souhaitez créer le conteneur de l’objet blob.
1.  Droit de **Conteneurs Blob**et - dans le menu contextuel - Sélectionnez la **Création d’un conteneur Blob**.

    ![Créer un menu contextuel de conteneurs blob][0]

1.  Une zone de texte s’affichera sous le dossier **Conteneurs Blob** . Entrez le nom de votre conteneur blob. Consultez la section [règles d’attribution de noms de conteneur](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) pour une liste des règles et des restrictions de dénomination des blob conteneurs.

    ![Créer la zone de texte de conteneurs Blob][1]

1.  Appuyez sur la touche **entrée** lorsque vous avez terminé de créer le conteneur de l’objet blob ou **Echap** pour annuler. Une fois le conteneur blob a été créé, il s’affiche sous le dossier **Conteneurs Blob** pour le compte de stockage sélectionné.

    ![Conteneur BLOB créé][2]

## <a name="view-a-blob-containers-contents"></a>Afficher le contenu du conteneur d’un objet blob

Conteneurs BLOB contiennent des objets BLOB et dossiers (qui peuvent également contenir des objets BLOB).

Les étapes suivantes montrent comment afficher le contenu d’un conteneur de blob dans Explorateur de stockage (aperçu) :

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob que vous souhaitez afficher.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Cliquez sur le conteneur blob que vous souhaitez afficher et - dans le menu contextuel - Sélectionnez **Ouvrir l’éditeur de conteneur Blob**.
Vous pouvez également double-cliquer sur le conteneur blob que vous souhaitez afficher.

    ![Menu contextuel de blob ouvrir conteneur éditeur][19]

1.  Le volet principal affiche le blob contenu du conteneur.

    ![Éditeur de conteneur BLOB][3]

## <a name="delete-a-blob-container"></a>Supprimer un conteneur de blob

Conteneurs BLOB peuvent facilement créés et supprimés si nécessaire. (Pour savoir comment supprimer des blobs, reportez-vous à la section, [Gestion des BLOB dans un conteneur blob](./#managing-blobs-in-a-blob-container).)

Les étapes suivantes illustrent comment supprimer un conteneur de blob dans Explorateur de stockage (aperçu) :

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob que vous souhaitez afficher.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Cliquez sur le conteneur blob que vous souhaitez supprimer et - dans le menu contextuel - Sélectionnez **Supprimer**.
Vous pouvez également appuyer sur **Supprimer** pour supprimer le conteneur de l’objet blob actuellement sélectionné.

    ![Supprimer le menu contextuel de conteneur blob][4]

1.  La boîte de dialogue de confirmation, sélectionnez **Oui** .

    ![Supprimer la confirmation de conteneur de blob][5]

## <a name="copy-a-blob-container"></a>Copier un conteneur blob

Explorateur de stockage (aperçu) vous permet de copier un conteneur d’objet blob dans le Presse-papiers, puis collez ce conteneur blob dans un autre compte de stockage. (Pour savoir comment copier des objets BLOB individuelles, reportez-vous à la section, [Gestion des BLOB dans un conteneur blob](./#managing-blobs-in-a-blob-container).)

Les étapes suivantes illustrent comment copier un conteneur blob à partir du compte de stockage à un autre.

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez du compte de stockage contenant le conteneur blob que vous souhaitez copier.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Cliquez sur le conteneur blob que vous souhaitez copier et - dans le menu contextuel - Sélectionnez **Conteneur Blob de copie**.

    ![Copier du menu contextuel de conteneur blob][6]

1.  Double-cliquez sur le compte de stockage cible « souhaité » dans lequel vous souhaitez coller le conteneur blob et - dans le menu contextuel - Sélectionnez **Coller un conteneur Blob**.

    ![Menu contextuel de coller blob conteneur][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obtenir les associations de sécurité pour un conteneur d’objet blob

Une [signature d’accès partagé (SAS)](./storage/storage-dotnet-shared-access-signature-part-1.md) fournit un accès délégué aux ressources de votre compte de stockage.
Cela signifie que vous pouvez accorder à qu'un client des autorisations limitées d’objets dans votre compte de stockage pour une période donnée de temps et avec un jeu d’autorisations spécifié sans avoir à partager les touches d’accès de votre compte.

Les étapes suivantes montrent comment créer un SAS pour un conteneur d’objet blob :

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob pour lequel vous souhaitez obtenir un SAS.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Droit du conteneur blob souhaité et - dans le menu contextuel - Sélectionnez **Obtenir la Signature des accès partagés**.

    ![Obtenir le menu contextuel SAS][8]

1.  Dans la boîte de dialogue de **Signature de l’accès partagé** , spécifiez la stratégie, les dates de début et d’expiration, le fuseau horaire et accéder aux niveaux souhaités pour la ressource.

    ![Accéder aux options de SAS][9]

1.  Lorsque vous avez terminé de spécifier les options de SAS, sélectionnez **créer**.

1.  Une deuxième boîte de dialogue de **Signature de l’accès partagé** affiche qui répertorie le conteneur blob et l’URL de requête que vous pouvez utiliser pour accéder à la ressource de stockage.
Sélectionnez la **copie** à côté de l’URL que vous souhaitez copier dans le Presse-papiers.

    ![Copier l’URL SAS][10]

1.  Lorsque vous avez terminé, sélectionnez **Fermer**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gérer les stratégies d’accès pour un conteneur d’objet blob

Les étapes suivantes montrent comment gérer (ajouter et supprimer) accéder aux stratégies d’un conteneur d’objet blob :

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob dont vous souhaitez gérer les stratégies d’accès.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Sélectionnez le conteneur blob souhaité et - dans le menu contextuel - Sélectionnez **Gérer les stratégies d’accès**.

    ![Accéder au menu de contexte de stratégies de gestion][11]

1.  La boîte de dialogue des **Stratégies d’accès** affiche la liste des stratégies d’accès déjà créés pour le conteneur de l’objet blob sélectionné.

    ![Accéder aux options de stratégie][12]        

1.  Suivez ces étapes en fonction de la tâche de gestion de stratégie de l’accès :

    - **Ajouter une nouvelle stratégie d’accès** - sélectionnez **Ajouter**. Une fois généré, la boîte de dialogue des **Stratégies d’accès** affiche la stratégie d’accès qui vient d’être ajouté (avec les paramètres par défaut).
    - **Modifier une stratégie d’accès** - apporter les modifications souhaitées et cliquez sur **Enregistrer**.
    - **Supprimer une stratégie d’accès** - sélectionnez **Supprimer** en regard de la stratégie d’accès que vous souhaitez supprimer.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Définir le niveau d’accès Public pour un conteneur d’objet blob

Par défaut, chaque conteneur blob est défini à « Aucun accès public ».

Les étapes suivantes illustrent comment spécifier un niveau d’accès public pour un conteneur d’objet blob.

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob dont vous souhaitez gérer les stratégies d’accès.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Sélectionnez le conteneur blob souhaité et - dans le menu contextuel - Sélectionnez le **Niveau d’accès Public défini**.

    ![Définir le menu de contexte au niveau d’accès public][13]

1.  Dans la boîte de dialogue **Définir le niveau de l’accès Public du conteneur** , spécifiez le niveau d’accès souhaité.

    ![Définir les options de niveau d’accès public][14]

1.  Sélectionnez **Appliquer**.

## <a name="managing-blobs-in-a-blob-container"></a>Gestion des objets BLOB dans un conteneur de blob

Une fois que vous avez créé un conteneur blob, vous pouvez télécharger un blob à ce conteneur blob, télécharger un blob sur votre ordinateur local, ouvrez un blob sur votre ordinateur local et bien plus encore.

Les étapes suivantes montrent comment gérer les objets BLOB (et les dossiers) dans un conteneur d’objet blob.

1.  Ouvrez l’Explorateur de stockage (aperçu).
1.  Dans le volet gauche, développez du compte de stockage contenant le conteneur blob que vous souhaitez gérer.
1.  Développez les **Conteneurs Blob**du compte de stockage.
1.  Double-cliquez sur le conteneur blob que vous souhaitez afficher.
1.  Le volet principal affiche le blob contenu du conteneur.

    ![Conteneur de blob d’affichage][3]

1.  Le volet principal affiche le blob contenu du conteneur.

1.  Suivez ces étapes en fonction de la tâche que vous souhaitez effectuer :

    - **Télécharger des fichiers vers un conteneur blob**

        1.  Barre d’outils du volet principal, sélectionnez **Télécharger**, puis sur **Télécharger les fichiers** à partir du menu déroulant.

            ![Télécharger les menu fichiers][15]

        1.  Dans la boîte de dialogue **téléchargement de fichiers** , cliquez sur le bouton de sélection (**...**) sur le côté droit de la zone de texte pour sélectionner les fichiers que vous souhaitez télécharger des **fichiers** .

            ![Options de fichiers de téléchargement][16]

        1.  Spécifiez le type de **type Blob**. L’article [mise en route de stockage Azure Blob à l’aide de .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explique les différences entre les divers types d’objet blob.

        1.  Le cas échéant, spécifiez un dossier cible dans lequel les fichiers sélectionnés seront téléchargés. Si le dossier cible n’existe pas, il sera créé.

        1.  Sélectionnez **Télécharger**.

    - **Télécharger un dossier vers un conteneur blob**

        1.  Barre d’outils du volet principal, sélectionnez **Télécharger**, puis sur **Télécharger le dossier** dans le menu déroulant.

            ![Menu dossier de téléchargement][17]

        1.  Dans la boîte de dialogue **dossier de chargement** , cliquez sur le bouton de sélection (**...**) sur le côté droit de la zone de texte **dossier** pour sélectionner le dossier dont vous souhaitez télécharger le contenu.

            ![Options du dossier de téléchargement][18]

        1.  Spécifiez le type de **type Blob**. L’article [mise en route de stockage Azure Blob à l’aide de .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explique les différences entre les divers types d’objet blob.

        1.  Le cas échéant, spécifiez un dossier cible dans lequel le contenu du dossier sélectionné est téléchargé. Si le dossier cible n’existe pas, il sera créé.

        1.  Sélectionnez **Télécharger**.

    - **Télécharger un blob sur votre ordinateur local**

        1.  Sélectionnez le blob que vous souhaitez télécharger.

        1.  Barre d’outils du volet principal, sélectionnez **Télécharger**.

        1.  Dans la boîte de dialogue **spécifier l’emplacement où enregistrer le blob téléchargé** , spécifiez l’emplacement où vous souhaitez le blob téléchargé et le nom que vous souhaitez lui attribuer.  

        1.  Cliquez sur **Enregistrer**.

    - **Ouvrir un blob sur votre ordinateur local**

        1.  Sélectionnez le blob que vous souhaitez ouvrir.

        1.  Barre d’outils du volet principal, sélectionnez **Ouvrir**.

        1.  Le blob sera téléchargé et ouvert à l’aide de l’application associée au type de fichier sous-jacent de l’objet blob.

    - **Copier un objet blob dans le Presse-papiers**

        1.  Sélectionnez le blob que vous souhaitez copier.

        1.  Barre d’outils du volet principal, sélectionnez **Copier**.

        1.  Dans le volet de gauche, accédez à un autre conteneur blob et double-cliquez dessus pour l’afficher dans le volet principal.

        1.  Barre d’outils du volet principal, cliquez sur **Coller** pour créer une copie de l’objet blob.

    - **Supprimer un objet blob**

        1.  Sélectionnez le blob que vous souhaitez supprimer.

        1.  Barre d’outils du volet principal, sélectionnez **Supprimer**.

        1.  La boîte de dialogue de confirmation, sélectionnez **Oui** .

## <a name="next-steps"></a>Étapes suivantes

- Permet d’afficher l' [Explorateur de stockage plus récents (aperçu) notes de publication et vidéos](http://www.storageexplorer.com).
- Découvrez comment [créer des applications à l’aide des fichiers, des tables, des files d’attente et BLOB Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png