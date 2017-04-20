<properties
   pageTitle="La navigation et la gestion des ressources de stockage avec l’Explorateur de serveurs | Microsoft Azure"
   description="La navigation et la gestion des ressources de stockage avec l’Explorateur de serveurs"
   services="visual-studio-online"
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
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="browsing-and-managing-storage-resources-with-server-explorer"></a>La navigation et la gestion des ressources de stockage avec l’Explorateur de serveurs

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Vue d’ensemble
Si vous avez installé les outils d’Azure pour Visual Studio de Microsoft, vous pouvez afficher des blob, les files d’attente et les données de la table à partir de vos comptes de stockage Azure. Le nœud de stockage Azure dans l’Explorateur de serveurs affiche les données qui se trouve dans votre compte d’émulateur de stockage local et de vos autres comptes de stockage Azure.

Pour afficher l’Explorateur de serveurs dans Visual Studio, la barre de menus, sélectionnez **affichage**, **Explorateur de serveurs**. Le nœud de stockage affiche tous les comptes de stockage qui existent sous chaque abonnement Azure/certificat que vous êtes connecté à. Si votre compte de stockage n’apparaît pas, vous pouvez l’ajouter en suivant les instructions [plus loin dans cette rubrique](#add-storage-accounts-by-using-server-explorer).

Démarrage dans Azure SDK 2.7, vous pouvez également utiliser le nouvel Explorateur de nuage pour afficher et gérer vos ressources Azure. Pour plus d’informations, consultez [Gestion des ressources avec le Cloud Explorer Azure](./vs-azure-tools-resources-managing-with-cloud-explorer.md) .


## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Permet d’afficher et de gérer les ressources de stockage dans Visual Studio

Explorateur de serveurs affiche automatiquement une liste des objets BLOB, les files d’attente et les tables dans votre compte d’émulateur de stockage. Le compte de l’émulateur de stockage est répertorié dans l’Explorateur de serveurs, sous le nœud de stockage, comme nœud de **développement** .

Pour afficher les ressources du compte émulateur de stockage, développez le nœud de **développement** . Si l’émulateur de stockage n’a pas été démarré lorsque vous développez le nœud de **développement** , il démarre automatiquement. Cette opération peut prendre plusieurs secondes. Vous pouvez continuer à travailler dans d’autres zones de Visual Studio pendant le démarrage de l’émulateur de stockage.

Pour afficher les ressources dans un compte de stockage, développez le nœud du compte de stockage dans l’Explorateur de serveurs. Les sous-noeuds suivants s’affichent :

- Objets BLOB

- Files d’attente

- Tables

## <a name="work-with-blob-resources"></a>Travailler avec des ressources de Blob

Le nœud de BLOB affiche une liste de conteneurs pour le compte de stockage sélectionné. Conteneurs BLOB contiennent des fichiers blob, et vous pouvez organiser ces objets BLOB en dossiers et sous-dossiers. Pour plus d’informations, voir [comment utiliser le stockage Blob à partir de .NET](./storage/storage-dotnet-how-to-use-blobs.md) .

### <a name="to-create-a-blob-container"></a>Pour créer un conteneur de blob

1. Ouvrir le menu contextuel pour le nœud de **BLOB** et choisissez **Créer un conteneur Blob**.

1. Entrez le nom du nouveau conteneur dans la boîte de dialogue de **Création d’un conteneur Blob** , puis choisissez **Ok**

    >[AZURE.NOTE] Le nom du conteneur blob doit commencer par un chiffre (0-9) ou d’une minuscule (a à z).

### <a name="to-delete-a-blob-container"></a>Pour supprimer un conteneur de blob

- Ouvrez le menu contextuel pour le conteneur blob que vous souhaitez supprimer et choisissez **Supprimer**.

### <a name="to-display-a-list-of-the-items-contained-in-a-blob-container"></a>Pour afficher une liste des éléments contenus dans un conteneur de blob

- Ouvrir le menu contextuel pour un nom de conteneur blob dans la liste, puis choisissez **Affichage Blob conteneur**.

    Lorsque vous affichez le contenu d’un conteneur blob, il s’affiche dans un onglet appelé l’affichage conteneur blob.

    ![VST_SE_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Vous pouvez effectuer les opérations suivantes sur les objets BLOB en utilisant les boutons dans le coin supérieur droit de l’affichage du conteneur blob :

    - Entrez une valeur de filtre et l’appliquer

    - Actualiser la liste des objets BLOB dans le conteneur

    - Télécharger un fichier

    - Supprimer un objet blob

      >[AZURE.NOTE] Suppression d’un fichier à partir d’un conteneur d’objet blob ne supprime pas le fichier sous-jacent ; Il supprime uniquement à partir du conteneur de l’objet blob.

    - Ouvrez un objet blob

    - Enregistrer un objet blob à l’ordinateur local

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Pour créer un dossier ou un sous-dossier dans un conteneur de blob

1. Dans l’Explorateur de serveurs, choisissez le conteneur d’objet blob. Dans la fenêtre de conteneur, cliquez sur le bouton **Charger les Blob** .

    ![Téléchargement d’un fichier dans un dossier de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. Dans la boîte de dialogue **Télécharger un nouveau fichier** , cliquez sur le bouton **Parcourir** pour spécifier le fichier que vous souhaitez télécharger et puis entrez un nom de dossier dans la zone **dossier (facultatif)** .

    Vous pouvez ajouter des sous-dossiers dans les dossiers de conteneur en suivant la même procédure. Si vous ne spécifiez pas un nom de dossier, le fichier sera téléchargé vers le niveau supérieur du conteneur blob. Le fichier apparaît dans le dossier spécifié dans le conteneur.

    ![Dossier ajouté à un conteneur de blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Double-cliquez sur le dossier, ou appuyez sur ENTRÉE pour afficher le contenu du dossier. Lorsque vous êtes dans le dossier du conteneur, vous pouvez naviguer à la racine du conteneur en cliquant sur le bouton **Open Directory Parent** (flèche haut).

### <a name="to-delete-a-container-folder"></a>Pour supprimer un dossier conteneur

 - Supprimer tous les fichiers dans le dossier

    >[AZURE.NOTE] Étant donné que les dossiers dans des conteneurs blob sont des dossiers virtuels, vous ne pouvez pas créer un dossier vide, ni vous pouvez supprimer un dossier pour supprimer le contenu du fichier. Vous devez supprimer tout le contenu d’un dossier pour supprimer le dossier.

### <a name="to-filter-blobs-in-a-container"></a>Pour filtrer des objets BLOB dans un conteneur

Vous pouvez filtrer les objets BLOB qui sont affichés en spécifiant un préfixe commun.

Par exemple, si vous entrez le préfixe `hello` dans le texte du filtre zone, puis cliquez sur l' **exécution** (**!**) bouton, seuls les objets BLOB qui commencent par « hello » s’affichent.

![VST_SE_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE] Le champ de filtre respecte la casse et ne prend en charge le filtrage avec des caractères génériques. BLOB peut être filtrée par un préfixe. Le préfixe peut-être inclure un délimiteur si vous utilisez un séparateur pour organiser des objets BLOB dans une hiérarchie virtuelle. Par exemple, le filtrage sur le préfixe HelloFabric / renvoie tous les BLOB commençant par cette chaîne.

### <a name="to-download-blob-data"></a>Pour transférer des données blob

- Dans l' **Explorateur de serveurs**, ouvrir le menu contextuel pour un ou plusieurs objets de BLOB et puis cliquez sur **Ouvrir**, ou cliquez sur le nom de l’objet blob et puis cliquez sur le bouton **Ouvrir** ou double-cliquez sur le nom de l’objet blob.

    La progression d’un téléchargement de blob s’affiche dans la fenêtre du **Journal d’activité Azure** .

    Le blob s’ouvre dans l’éditeur par défaut pour ce type de fichier. Si le système d’exploitation reconnaît le type de fichier, le fichier s’ouvre dans une application installée localement ; dans le cas contraire, vous êtes invité à choisir une application qui est appropriée pour le type de fichier de l’objet blob. Le fichier local qui est créé lorsque vous téléchargez un objet blob est marqué en lecture seule.

    Données d’objet BLOB sont mis en cache localement et vérifiées par rapport à heure de dernière modification de l’objet blob dans le service d’objet Blob. Si le blob a été mis à jour dans la mesure où il a été transféré pour la dernière fois, il sera téléchargé à nouveau ; dans le cas contraire le blob est chargé à partir du disque local. Par défaut, un objet blob est téléchargé vers un répertoire temporaire. Pour télécharger des objets BLOB dans un répertoire spécifique, ouvrir le menu contextuel pour les noms d’objet blob sélectionnée et choisissez **Enregistrer sous**. Lorsque vous enregistrez un objet blob de cette manière, le fichier blob n’est pas ouvert, et le fichier local est créé avec les attributs en lecture-écriture.

### <a name="to-upload-blobs"></a>Pour charger des objets BLOB

- Cliquez sur le bouton **Charger les Blob** lorsque le conteneur est ouvert pour l’affichage dans la vue du conteneur blob.

    Vous pouvez choisir un ou plusieurs fichiers à télécharger, et vous pouvez télécharger des fichiers de n’importe quel type. Le **Journal d’activité Azure** affiche la progression du téléchargement. Pour plus d’informations sur la façon de travailler avec les données blob, voir [comment utiliser le Service de stockage de Blob Azure dans .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Pour afficher les journaux transférés vers des objets BLOB

- Si vous utilisez Azure Diagnostics pour enregistrer des données à partir de votre application Azure et vous avez transféré des journaux à votre compte de stockage, vous verrez les conteneurs qui ont été créés par Azure pour ces journaux. Afficher ces fichiers journaux dans l’Explorateur de serveurs d’est un moyen facile d’identifier les problèmes avec votre application, en particulier s’il a été déployé sur Azure. Pour plus d’informations sur les Diagnostics d’Azure, reportez-vous à la section [Collecter des données de journalisation à l’aide des diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Pour obtenir l’URL d’un objet blob

- Ouvrir le menu de raccourci de l’objet blob, puis choisissez **Copier l’URL**.

### <a name="to-edit-a-blob"></a>Pour modifier un objet blob

- Sélectionnez l’objet blob et cliquez sur le bouton **Ouvrir Blob** .

    Le fichier est téléchargé dans un emplacement temporaire et ouvert sur l’ordinateur local. Vous devez télécharger le blob à nouveau après avoir apporté des modifications.

## <a name="work-with-queue-resources"></a>Travailler avec des ressources de la file d’attente

Files d’attente de services de stockage sont hébergés dans un compte de stockage Azure et vous pouvez les utiliser pour autoriser votre nuage des rôles de service communiquer les uns avec les autres et avec d’autres services par un mécanisme de passage de message. Vous pouvez accéder par programme à la file d’attente via un service de cloud et via un service web pour les clients externes. Vous pouvez également accéder à la file d’attente directement en utilisant l’Explorateur de serveurs dans Visual Studio.

Lorsque vous développez un service en nuage qui utilise les files d’attente, vous pouvez souhaiter utiliser Visual Studio pour créer des files d’attente et de travailler avec eux de manière interactive lorsque vous développez et testez votre code.

Dans l’Explorateur de serveurs, vous pouvez afficher les files d’attente dans un compte de stockage créer et supprimer des files d’attente, ouvrir une file d’attente pour afficher ses messages et ajouter des messages à une file d’attente. Lorsque vous ouvrez une file d’attente pour l’affichage, vous pouvez afficher les messages individuels, et vous pouvez effectuer les actions suivantes sur la file d’attente en utilisant les boutons dans le coin supérieur gauche :

- Actualiser l’affichage de la file d’attente

- Ajouter un message à la file d’attente

- Le premier message en file d’attente.

- Effacer la file d’attente entier

L’image suivante montre une file d’attente qui contient les deux messages.

![Affichage d’une file d’attente](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Pour plus d’informations sur le stockage des files d’attente de services, consultez [Comment : utiliser le Service de stockage de file d’attente](http://go.microsoft.com/fwlink/?LinkID=264702). Pour plus d’informations sur le service web pour les files d’attente de services de stockage, consultez [Concepts de Service de file d’attente](http://go.microsoft.com/fwlink/?LinkId=264788). Pour plus d’informations sur la façon d’envoyer des messages à une file d’attente des services de stockage à l’aide de Visual Studio, consultez [Envoi de Messages à une file d’attente des Services de stockage](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE] Files d’attente de services de stockage sont différents des files d’attente de bus de service. Pour plus d’informations sur les files d’attente de bus de service, voir files d’attente de Bus de Service, des rubriques et des abonnements.

## <a name="work-with-table-resources"></a>Travailler avec des ressources de la Table

Le service de stockage Azure Table stocke de gros volumes de données structurées. Le service est un magasin de données NoSQL qui accepte authentifiés des appels à partir d’à l’intérieur et à l’extérieur du nuage Azure. Les tables Azure sont idéales pour le stockage des données structurées et non relationnelles.

### <a name="to-create-a-table"></a>Pour créer une table

1. Dans l’Explorateur de serveurs, sélectionnez le nœud **Tables** du compte de stockage et puis cliquez sur **Créer une Table**.

1. Dans la boîte de dialogue **Créer une Table** , entrez un nom pour la table.

### <a name="to-view-table-data"></a>Pour afficher les données de la table

1. Dans l’Explorateur de serveurs, ouvrez le nœud **Azure** et puis ouvrez le nœud de **stockage** .

1. Ouvrez le nœud de compte de stockage qui vous intéressez et ouvrez le nœud **Tables** pour afficher une liste des tables pour le compte de stockage.

1. Ouvrir le menu contextuel pour une table, puis choisissez **Afficher la Table**.

    ![Une table Azure dans l’Explorateur de solutions](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

La table est organisée en entités (lignes) et propriétés (affichées dans les colonnes). Par exemple, l’illustration suivante montre les entités répertoriées dans le **Concepteur de tables**:

### <a name="to-edit-table-data"></a>Pour modifier les données de la table

1. Dans le **Concepteur de tables**, ouvrez le menu contextuel pour une entité (une seule ligne) ou d’une propriété (une seule cellule) et choisissez **Modifier**.

    ![Ajouter ou modifier une entité de Table](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Les entités d’une même table ne sont pas tenues d’avoir le même jeu de propriétés (colonnes). Gardez à l’esprit les restrictions suivantes concernant l’affichage et la modification des données de la table.
    - Impossible d’afficher ou de modifier des données binaires (type byte[]), mais vous pouvez l’enregistrer dans une table.

    - Vous ne pouvez pas modifier les valeurs **PartitionKey** ou **RowKey** , car le stockage de table dans Azure ne prend pas en charge cette opération.

    - Vous ne pouvez pas créer une propriété appelée Timestamp, les services de stockage Azure utilisent une propriété portant le même nom.

    - Si vous entrez une valeur de type DateTime, vous devez suivre un format qui est approprié pour les paramètres de langue et de région de votre ordinateur (par exemple, hh : mm : de JJ/MM/AAAA [AM | PM] pour l’anglais américain).

### <a name="to-add-entities"></a>Pour ajouter des entités

1. Dans le **Concepteur de tables**, cliquez sur le bouton **Ajouter une entité** , qui se trouve près de l’angle supérieur droit de l’affichage de la table.

    ![Ajouter l’entité](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Dans la boîte de dialogue **Ajouter une entité** , entrez les valeurs des propriétés **PartitionKey** et **RowKey** .

    ![Ajouter la boîte de dialogue d’entité](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Entrez les valeurs avec précaution, car vous ne pouvez pas les modifier une fois que vous fermez la boîte de dialogue, sauf si vous supprimez l’entité et l’ajoutez à nouveau.

### <a name="to-filter-entities"></a>Pour filtrer des entités

Vous pouvez personnaliser le jeu d’entités qui apparaissent dans un tableau si vous utilisez le Générateur de requêtes.

1. Pour ouvrir le Générateur de requête, ouvrez la table pour l’affichage.

1. Cliquez sur le bouton plus à droite sur la barre d’outils de la vue tableau.

    La boîte de dialogue **Générateur de requêtes** s’affiche. L’illustration suivante présente une requête qui est générée dans le Générateur de requêtes.

    ![Générateur de requêtes](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Lorsque vous avez terminé la création de la requête, fermez la boîte de dialogue. Le formulaire de texte résultant de la requête s’affiche dans une zone de texte sous la forme d’un filtre de Services de données WCF.

1. Pour exécuter la requête, cliquez sur l’icône de triangle vert.

    Vous pouvez également filtrer les données d’entité qui apparaissent dans le **Concepteur de tables** si vous entrez une chaîne de filtrage des Services de données WCF directement dans le champ filtre. Ce type de chaîne est similaire à une clause SQL WHERE mais est envoyé au serveur une requête HTTP. Pour plus d’informations sur la façon de construire des chaînes de filtre, consultez [Construction de chaînes de filtrage pour le Concepteur de tables](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    L’illustration suivante montre un exemple d’une chaîne de filtre valides :

    ![VST_SE_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### <a name="refresh-storage-data"></a>Actualiser les données de stockage

Lors de l’Explorateur de serveurs se connecte à ou obtient des données à partir d’un compte de stockage, il peut prendre jusqu'à une minute avant que l’opération se termine. S’il ne peut pas se connecter, l’opération peut avoir expiré. Alors que les données sont récupérées, vous pouvez continuer à travailler dans d’autres parties de Visual Studio. Pour annuler l’opération si elle prend trop de temps, cliquez sur le bouton **Arrêter l’actualisation** sur la barre d’outils de l’Explorateur de serveurs.

#### <a name="to-refresh-blob-container-data"></a>Pour actualiser les données de conteneur blob

- Sélectionnez le nœud de **BLOB** sous **stockage** , cliquez sur le bouton **Actualiser** dans la barre d’outils de l’Explorateur de serveurs.

- Pour actualiser la liste des objets BLOB qui s’affiche, cliquez sur le bouton **exécuter** .

#### <a name="to-refresh-table-data"></a>Pour actualiser les données de la table

- Sélectionnez le nœud de **Tables** sous **stockage** , cliquez sur le bouton **Actualiser** .

- Pour actualiser la liste des entités qui s’affiche dans le **Concepteur de tables**, cliquez sur le bouton **exécuter** dans le **Concepteur de tables**.

#### <a name="to-refresh-queue-data"></a>Pour actualiser les données de file d’attente

- Sélectionnez le nœud **files d’attente** et cliquez sur le bouton **Actualiser** .

#### <a name="to-refresh-all-items-in-a-storage-account"></a>Pour actualiser tous les éléments dans un compte de stockage

- Cliquez sur le nom du compte et puis cliquez sur le bouton **Actualiser** dans la barre d’outils de l’Explorateur de serveurs.

### <a name="add-storage-accounts-by-using-server-explorer"></a>Ajouter des comptes de stockage en utilisant l’Explorateur de serveurs

Il existe deux façons d’ajouter des comptes de stockage en utilisant l’Explorateur de serveurs. Vous pouvez créer un nouveau compte de stockage de votre abonnement Azure, ou vous pouvez associer un compte de stockage existant.

#### <a name="to-create-a-new-storage-account-by-using-server-explorer"></a>Pour créer un nouveau compte de stockage en utilisant l’Explorateur de serveurs

1. Dans l’Explorateur de serveurs, ouvrez le menu contextuel pour le nœud de stockage et puis cliquez sur Créer un compte de stockage.

    ![Créer un nouveau compte de stockage Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Sélectionnez ou entrez les informations suivantes pour le nouveau compte de stockage dans la boîte de dialogue **Créer un compte de stockage** .

    - L’abonnement Azure auquel vous voulez ajouter le compte de stockage.

    - Le nom que vous souhaitez utiliser pour le nouveau compte de stockage.

    - La région ou le groupe d’affinité (par exemple, ouest des Etats-Unis ou d’Asie de l’est).

    - Le type de réplication que vous souhaitez utiliser pour le compte de stockage, comme Geo redondant.

1. Cliquez sur **créer**.

    Le nouveau compte de stockage apparaît dans la liste de **stockage** dans l’Explorateur de solutions.

#### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Pour associer un compte de stockage existant en utilisant l’Explorateur de serveurs

1. Dans l’Explorateur de serveurs, ouvrez le menu contextuel pour le nœud de stockage Azure et puis cliquez sur **Attacher un stockage externe**.

    ![Ajout d’un compte de stockage existant](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Sélectionnez ou entrez les informations suivantes pour le nouveau compte de stockage dans la boîte de dialogue **Créer un compte de stockage** .

    - Le nom du compte de stockage existant que vous souhaitez joindre. Vous pouvez entrer un nom ou sélectionnez-le dans la liste.

    - La clé pour le compte de stockage sélectionné. Cette valeur est généralement fournie pour vous lorsque vous sélectionnez un compte de stockage. Si vous souhaitez que Visual Studio n’oubliez pas de la clé de compte de stockage, sélectionnez la case Mémoriser le compte clée.

    - Le protocole à utiliser pour se connecter sur le compte de stockage, tels que HTTP, HTTPS ou un point de terminaison personnalisé. Pour plus d’informations sur les points de terminaison personnalisés, reportez-vous à la section [comment configurer des chaînes de connexion](https://msdn.microsoft.com/library/azure/ee758697.aspx) .

### <a name="to-view-the-secondary-endpoints"></a>Pour afficher les points de terminaison secondaires

- Si vous avez créé un compte de stockage à l’aide de l’option de réplication **Redondante de géo-accès en lecture** , vous pouvez afficher ses points de terminaison secondaires. Ouvrir le menu contextuel pour le nom du compte et choisissez **Propriétés**.

    ![Points de terminaison de stockage secondaires](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Pour supprimer un compte de stockage à partir de l’Explorateur de serveurs

- Dans l’Explorateur de serveurs, ouvrez le menu contextuel pour le nom du compte et choisissez **Supprimer**. Si vous supprimez un compte de stockage, les informations de clé enregistrées pour ce compte sont également supprimées.

    >[AZURE.NOTE] Si vous supprimez un compte de stockage à partir de l’Explorateur de serveurs, il n’affecte pas votre compte de stockage ou de toutes les données qu’il contient ; Il supprime simplement la référence à partir de l’Explorateur de serveurs. Pour supprimer définitivement un compte de stockage, utilisez l' [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir que plus sur la façon d’utilisent les services de stockage Azure, consultez [accès aux Services stockage Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).
