<properties
    pageTitle="Liste des comptes de stockage Azure"
    description="Gérer les paramètres de votre compte de stockage à l’aide de la Shared Computer Toolkit Azure pour Éclipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Liste des comptes de stockage Azure #

Les comptes de stockage Azure permettent à des emplacements de téléchargement à utiliser pour votre JDK, serveur d’applications et composants arbitraires, ainsi que pour le stockage de l’état lors de l’utilisation de la mise en cache. Eclipse conserve une liste des comptes de stockage connus qui sont disponibles pour vos projets dans votre espace de travail Eclipse. Pour ouvrir la boîte de dialogue **Comptes de stockage** , qui est utilisé pour gérer cette liste, au sein d’Eclipse, cliquez sur **la fenêtre**, cliquez sur **Préférences**, développez **Azure**, puis cliquez sur **Comptes de stockage**.

L’exemple suivant montre la boîte de dialogue **Comptes de stockage** .

![][ic719496]

Vous pouvez également ouvrir cette boîte de dialogue à partir d’un lien de **comptes** dans les boîtes de dialogue qui utilisent des comptes de stockage, telles que les suivantes :

* L’onglet **JDK** de la boîte de dialogue **Configuration du serveur** .
* L’onglet **serveur** de la boîte de dialogue **Configuration du serveur** .
* La boîte de dialogue **Ajouter un composant** .
* La boîte de dialogue Propriétés de **mise en cache** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Pour importer vos comptes de stockage à l’aide d’un fichier de paramètres de publication ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur **Importer depuis un fichier de paramètres de publication**.
2. (Ignorez cette étape si vous avez déjà enregistré un fichier de paramètres de publication sur votre machine locale.) Dans la boîte de dialogue **Informations sur l’abonnement importation** , cliquez sur **Télécharger un fichier de paramètres de publication**. Si vous n’êtes pas encore connecté à votre compte Azure, vous serez invité à ouvrir une session. Puis vous serez invité à enregistrer un Azure publier le fichier de paramètres. (Vous pouvez ignorer les instructions qui en résulte dans les pages d’ouverture de session - qu’ils sont fournis par le portail Azure et sont destinées aux utilisateurs de Visual Studio.) L’enregistrer sur votre ordinateur local.
3. Toujours dans la boîte de dialogue **Informations sur l’abonnement importation** , cliquez sur le bouton **Parcourir** et sélectionnez le fichier de paramètres de publication que vous avez précédemment enregistré localement puis cliquez sur **Ouvrir**.
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Importer des informations de souscription** .

## <a name="to-create-a-new-storage-account"></a>Pour créer un nouveau compte de stockage ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur **Ajouter**.
2. Dans la boîte de dialogue **Ajouter un compte de stockage** , cliquez sur **Nouveau**.
3. Dans la boîte de dialogue **Nouveau compte de stockage** , spécifiez des valeurs pour les éléments suivants :
    * Nom du compte de stockage.
    * Emplacement du compte de stockage.
    * Description du compte de stockage.
    * L’abonnement à laquelle appartient le compte de stockage.
4. Cliquez sur **OK** pour fermer la boîte de dialogue **Nouveau compte de stockage** .

Elle peut prendre plusieurs minutes pour votre compte de stockage doit être créé. Après sa création, cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un compte de stockage** , et de votre nouveau compte de stockage sera ajouté à la liste des comptes de stockage disponible.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Pour ajouter un compte de stockage existant à la liste ##

1. Si vous ne disposez pas déjà d’un compte de stockage Azure, créez-en un en suivant les étapes décrites dans **pour créer une nouvelle section de compte de stockage** ci-dessus. (Vous pouvez également créer un nouveau compte de stockage sur le [Portail de gestion Azure][].)
2. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur **Ajouter**.
3. Dans la boîte de dialogue **Ajouter un compte de stockage** , entrez des valeurs pour le **nom** et la **Clé d’accès**. La clé d’accès et le nom de compte doit être un compte de stockage Azure existante. La section de **stockage** du [Portail de gestion Azure][] permet d’afficher les noms de compte de stockage et les clés. La boîte de dialogue **Ajouter un compte de stockage** sera semblable à la suivante.

    ![][ic719497]

4. Cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un compte de stockage** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Pour modifier un compte de stockage pour utiliser une nouvelle clé d’accès ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur le compte de stockage que vous souhaitez modifier, puis sur **Modifier**.
2. Dans la boîte de dialogue **Modifier la clé accès compte de stockage** , de modifier la valeur de la **Touche d’accès rapide** .
3. Cliquez sur **OK** pour fermer la boîte de dialogue **Modifier la clé accès compte de stockage** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Pour supprimer un compte de stockage à partir de la liste tenus à jour dans Éclipse ##

1. Dans la boîte de dialogue **Comptes de stockage** , cliquez sur le compte de stockage que vous souhaitez modifier, puis sur **Supprimer**.
2. Cliquez sur **OK** lorsque vous êtes invité à supprimer le compte de stockage.

>[AZURE.NOTE] Suppression du compte de stockage par le biais de la boîte de dialogue **Comptes de stockage** supprime uniquement à partir de la liste des comptes de stockage visibles au sein d’Eclipse. Elle ne supprime pas le compte de stockage de votre abonnement Azure. En outre, le compte de stockage peut apparaître à nouveau dans votre liste après que Eclipse recharge les détails de votre abonnement.

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portail de gestion Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
