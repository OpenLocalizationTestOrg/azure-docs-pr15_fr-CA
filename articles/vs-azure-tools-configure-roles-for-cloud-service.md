<properties
   pageTitle="Configurez les rôles pour un Service Cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment installer et configurer les rôles des services en nuage Azure à l’aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Configurez les rôles pour un Service Cloud Azure avec Visual Studio

Un service cloud Azure peut avoir un ou plusieurs travailleurs ou rôles web. Pour chaque rôle, vous devez également configurer l’exécution de ce rôle et de définir le paramétrage de ce rôle. Pour en savoir plus sur les rôles dans les services en nuage, consultez vidéo [Introduction aux Services de Cloud Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). Les informations relatives à votre service cloud sont stockés dans les fichiers suivants :

- **ServiceDefinition.csdef**

    Le fichier de définition de service définit les paramètres d’exécution pour votre service cloud, y compris les rôles qui sont requis, points de terminaison et la taille de la machine virtuelle. Aucune des données stockées dans ce fichier peut être modifié lors de l’exécution de votre rôle.

- **ServiceConfiguration.cscfg**

    Le fichier de configuration de service configure le nombre d’instances d’un rôle est exécuté et les valeurs des paramètres définis pour un rôle. Les données stockées dans ce fichier peuvent être modifiées pendant l’exécution de votre rôle.

Pour être en mesure de stocker des valeurs différentes pour ces paramètres pour l’exécution de votre rôle, vous pouvez avoir plusieurs configurations de service. Vous pouvez utiliser une configuration de service différent pour chaque environnement de déploiement. Par exemple, vous pouvez définir votre chaîne de connexion de compte de stockage à utiliser l’émulateur de stockage Azure local dans une configuration de service local et en créer une autre configuration de service pour utiliser le stockage Azure dans le nuage.

Lorsque vous créez un service en nuage Azure dans Visual Studio, deux configurations de service sont créées par défaut. Ces configurations sont ajoutées à votre projet Azure. Les configurations sont nommées :

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Configurer un service cloud Azure

Vous pouvez configurer un service cloud Azure à partir de l’Explorateur de solutions dans Visual Studio, comme indiqué dans l’illustration suivante.

![Configurer le Service en nuage](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Pour configurer un service cloud Azure

1. Pour configurer chaque rôle dans votre projet Azure à partir de **L’Explorateur de solutions**, ouvrez le menu contextuel pour le rôle dans le projet d’Azure et puis cliquez sur **Propriétés**.

    Une page avec le nom du rôle s’affiche dans l’éditeur de Visual Studio. La page affiche les champs de l’onglet **Configuration** .

1. Dans la liste de la **Configuration du Service** , cliquez sur le nom de la configuration du service que vous souhaitez modifier.

    Si vous souhaitez apporter des modifications à toutes les configurations de service pour ce rôle, vous pouvez choisir **Toutes les Configurations**.

    >[AZURE.IMPORTANT] Si vous choisissez une configuration de service spécifiques, certaines propriétés sont désactivées car elles ne peuvent être définies pour toutes les configurations. Pour modifier ces propriétés, vous devez choisir toutes les Configurations.

    Vous pouvez maintenant choisir un onglet mettre à jour les propriétés enabled sur cette vue.

## <a name="change-the-number-of-role-instances"></a>Modifier le nombre d’instances de rôle

Pour améliorer les performances de votre service cloud, vous pouvez modifier le nombre d’instances d’un rôle qui sont en cours d’exécution, en fonction du nombre d’utilisateurs ou la charge attendue pour un rôle particulier. Un ordinateur virtuel distinct est créé pour chaque instance d’un rôle lorsque le service en nuage s’exécute dans Azure. Ceci affectera la facturation pour le déploiement de ce service en nuage. Pour plus d’informations sur la facturation, voir [comprendre votre facture Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-number-of-instances-for-a-role"></a>Pour modifier le nombre d’instances pour un rôle

1. Cliquez sur l’onglet **Configuration** .

1. Dans la liste de la **Configuration du Service** , choisissez la configuration du service que vous souhaitez mettre à jour.

    >[AZURE.NOTE] Vous pouvez définir le nombre d’instances d’une configuration de service spécifique ou pour toutes les configurations de service.

1. Dans la zone de texte **nombre de l’Instance** , entrez le nombre d’instances que vous souhaitez démarrer pour ce rôle.

    >[AZURE.NOTE] Chaque instance est exécutée sur un ordinateur virtuel distinct lorsque vous publiez votre service cloud dans Azure.

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gérer les chaînes de connexion pour les comptes de stockage

Vous pouvez ajouter, supprimer ou modifier des chaînes de connexion pour vos configurations de service. Vous pouvez définir les différentes chaînes de connexion pour configurations de services différentes. Par exemple, vous souhaitez une chaîne de connexion local pour une configuration de service local qui a une valeur de `UseDevelopmentStorage=true`. Vous pourriez également configurer une configuration de service cloud qui utilise un compte de stockage dans Azure.

>[AZURE.WARNING] Lorsque vous entrez les informations de clé de compte de stockage Azure pour une chaîne de connexion de compte de stockage, ces informations sont stockées localement dans le fichier de configuration de service. Toutefois, ces informations ne sont actuellement pas stockées sous forme de texte crypté.

À l’aide d’une valeur différente pour chaque configuration de service, il est inutile d’utiliser différentes chaînes de connexion dans votre service cloud ou de modifier votre code lorsque vous publiez votre service cloud dans Azure. Vous pouvez utiliser le même nom pour la chaîne de connexion dans votre code, et la valeur sera différente, en fonction de la configuration du service que vous sélectionnez lorsque vous générez votre service cloud ou lorsque vous le publiez.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Pour gérer les chaînes de connexion pour les comptes de stockage

1. Cliquez sur l’onglet **paramètres** .

1. Dans la liste de la **Configuration du Service** , choisissez la configuration du service que vous souhaitez mettre à jour.

    >[AZURE.NOTE] Vous pouvez mettre à jour les chaînes de connexion pour une configuration de service spécifiques, mais si vous avez besoin d’ajouter ou de supprimer une chaîne de connexion, vous devez sélectionner toutes les Configurations.

1. Pour ajouter une chaîne de connexion, cliquez sur le bouton **Ajouter un paramètre** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour la chaîne de connexion.

1. Dans la liste déroulante **Type** , sélectionnez la **Chaîne de connexion**.

1. Pour modifier la valeur de la chaîne de connexion, cliquez sur le bouton de sélection (...). La boîte de dialogue **Chaîne de connexion de stockage de créer** s’affiche.

1. Pour utiliser l’émulateur de compte de stockage local, cliquez sur le bouton d’option **émulateur de stockage Microsoft Azure** et cliquez sur le bouton **OK** .

1. Pour utiliser un compte de stockage dans Azure, cliquez sur le bouton d’option de **votre abonnement** et sélectionnez le compte de stockage de votre choix.

1. Pour utiliser les informations d’identification personnalisées, cliquez sur le bouton options **entré manuellement les informations d’identification** . Entrez le nom du compte de stockage et la clé principale ou de la deuxième. Pour plus d’informations sur la création d’un compte de stockage et comment entrer les détails pour le compte de stockage dans la boîte de dialogue **Chaîne de connexion de stockage de créer** , voir la rubrique [Prepare à publier ou à déployer une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Pour supprimer une chaîne de connexion, sélectionnez la chaîne de connexion et cliquez sur le bouton **Supprimer le paramètre** .

1. Cliquez sur l’icône **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

1. Pour accéder à la chaîne de connexion dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration. Le code suivant illustre un exemple dans lequel est créé le stockage des objets blob et des données téléchargées à l’aide d’une chaîne de connexion `MyConnectionString` à partir du fichier de configuration de service lorsqu’un utilisateur choisit de **Button1** sur la page Default.aspx dans le rôle web pour un service cloud Azure. Ajoutez le code suivant à l’aide d’instructions Default.aspx.cs :

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Ouvrez Default.aspx.cs en mode Création et ajoutez un bouton à partir de la boîte à outils. Ajoutez le code suivant à la `Button1_Click` méthode. Ce code utilise `GetConfigurationSettingValue` pour obtenir la valeur à partir du fichier de configuration de service pour la chaîne de connexion. Un blob est créée dans le compte de stockage qui est référencé dans la chaîne de connexion `MyConnectionString` et enfin le programme ajoute du texte à l’objet blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Ajouter des paramètres personnalisés à utiliser dans votre service cloud Azure

Paramètres personnalisés dans le fichier de configuration de service vous permettent d’ajouter un nom et une valeur pour une chaîne d’une configuration de service spécifique. Vous pouvez choisir d’utiliser ce paramètre pour configurer une fonctionnalité dans votre service cloud par la lecture de la valeur du paramètre et de l’utilisation de cette valeur pour contrôler la logique de votre code. Vous pouvez modifier ces valeurs de configuration de service sans avoir à reconstruire votre package de services ou lorsque votre service cloud est en cours d’exécution. Votre code peut vérifier les notifications de lorsqu’un paramètre est modifié. Consultez [L’événement RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Vous pouvez ajouter, supprimer ou modifier des paramètres personnalisés pour vos configurations de service. Vous pouvez définir les différentes valeurs pour ces chaînes pour les configurations de services différentes.

À l’aide d’une valeur différente pour chaque configuration de service, il est inutile d’utiliser des chaînes différentes dans votre service cloud ou modifier votre code lorsque vous publiez votre service cloud dans Azure. Vous pouvez utiliser le même nom pour la chaîne dans votre code, et la valeur sera différente, en fonction de la configuration du service que vous sélectionnez lorsque vous générez votre service cloud ou lorsque vous le publiez.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Pour ajouter des paramètres personnalisés à utiliser dans votre service cloud Azure

1. Cliquez sur l’onglet **paramètres** .

1. Dans la liste de la **Configuration du Service** , choisissez la configuration du service que vous souhaitez mettre à jour.

    >[AZURE.NOTE] Vous pouvez mettre à jour des chaînes d’une configuration de service spécifiques, mais si vous avez besoin d’ajouter ou de supprimer une chaîne, vous devez sélectionner **Toutes les Configurations**.

1. Pour ajouter une chaîne, cliquez sur le bouton **Ajouter un paramètre** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour la chaîne.

1. Dans la liste déroulante **Type** , cliquez sur **chaîne**.

1. Pour ajouter ou modifier la valeur de la chaîne, dans la zone de texte **valeur** , tapez la nouvelle valeur.

1. Pour supprimer une chaîne, sélectionnez la chaîne et cliquez sur le bouton **Supprimer le paramètre** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

1. Pour accéder à la chaîne dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration.

    Vous devez vous assurer que le suivant en utilisant les instructions sont déjà ajoutées au Default.aspx.cs comme vous l’avez fait dans la procédure précédente.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Ajoutez le code suivant à la `Button1_Click` méthode pour accéder à cette chaîne de la même façon que vous accédez à une chaîne de connexion. Votre code peut ensuite effectuer un code spécifique en fonction de la valeur de la chaîne de paramètres pour le fichier de configuration de service qui est utilisé.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gérer le stockage local pour chaque instance de rôle

Vous pouvez ajouter le stockage de système de fichiers local pour chaque instance d’un rôle. Vous pouvez stocker des données de locales ici qui n’a pas besoin d’être accessible par les autres rôles. Les données que vous n’avez pas besoin d’enregistrer dans la table, blob ou le stockage de base de données de SQL peuvent être stockées ici. Par exemple, vous pouvez utiliser ce système de stockage local en cache des données qui devront être utilisés à nouveau. Ces données enregistrées ne sont pas accessibles par les autres instances d’un rôle. 

Paramètres de stockage local s’appliquent à toutes les configurations de service. Vous ne pouvez ajouter, supprimer ou modifier le stockage local pour toutes les configurations de service.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Pour gérer le stockage local pour chaque instance de rôle

1. Cliquez sur l’onglet de **Stockage Local** .

1. Dans la liste de la **Configuration du Service** , sélectionnez **Toutes les Configurations**.

1. Pour ajouter une entrée de stockage local, cliquez sur le bouton **Ajouter un stockage Local** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour ce système de stockage local.

1. Dans la zone de texte **taille** , entrez la taille en Mo dont vous avez besoin pour ce système de stockage local.

1. Pour supprimer les données de ce stockage local lorsque l’ordinateur virtuel pour ce rôle est recyclé, activez la case à cocher **Nettoyer sur le rôle de la Corbeille** .

1. Pour modifier une entrée de stockage local existant, sélectionnez la ligne dont vous avez besoin pour mettre à jour. Vous pouvez ensuite modifier les champs, comme décrit dans les étapes précédentes.

1. Pour supprimer une entrée de stockage local, sélectionnez l’entrée de stockage dans la liste et cliquez sur le bouton **Supprimer le stockage Local** .

1. Pour enregistrer ces modifications dans les fichiers de configuration du service, cliquez sur l’icône **Enregistrer** dans la barre d’outils.

1. Pour accéder au stockage local que vous avez ajoutés dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration ressource locale. Utilisez les lignes de code suivantes pour accéder à cette valeur et créez un fichier appelé **MyStorageTest.txt** et écrire une ligne de données de test dans ce fichier. Vous pouvez ajouter ce code dans le `Button_Click` méthode que celle utilisée dans les procédures précédentes :

1. Vous devez vous assurer que le suivant en utilisant instructions sont ajoutées dans Default.aspx.cs :

    ```
    using System.IO;
    using System.Text;
    ```

1. Ajoutez le code suivant à la `Button1_Click` méthode. Cela crée le fichier dans le stockage local et écrit les données de test dans ce fichier.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Facultatif) Pour afficher ce fichier que vous avez créé lorsque vous exécutez votre service cloud localement, procédez comme suit :

  1. Exécuter le rôle web et sélectionnez **Button1** pour s’assurer que le code dans `Button1_Click` est appelée.

  1. Dans la zone de notification, ouvrez le menu contextuel de l’icône Azure et choisir **d’Afficher l’interface utilisateur émulateur de calcul**. La boîte de dialogue **Émulateur de calcul Azure** s’affiche.

  1. Sélectionnez le rôle du web.

  1. Dans la barre de menus, cliquez sur **Outils**, **Ouvrir magasin local**. Une fenêtre de l’Explorateur Windows s’affiche.

  1. Dans la barre de menu, entrez **MyStorageTest.txt** dans la zone de texte **Rechercher** et puis cliquez sur **entrée** pour lancer la recherche.

    Le fichier est affiché dans les résultats de recherche.

  1. Pour afficher le contenu d’un fichier, ouvrez le menu contextuel pour le fichier et choisissez **Ouvrir**.

## <a name="collect-cloud-service-diagnostics"></a>Collecter les tests de diagnostic de service cloud

Vous pouvez collecter des données de diagnostic pour votre service cloud Azure. Ces données sont ajoutées à un compte de stockage. Vous pouvez définir les différentes chaînes de connexion pour configurations de services différentes. Par exemple, vous souhaiterez peut-être un compte de stockage local pour une configuration de service local qui a une valeur de UseDevelopmentStorage = true. Vous pourriez également configurer une configuration de service cloud qui utilise un compte de stockage dans Azure. Pour plus d’informations sur les diagnostics de Windows Azure, reportez-vous à la section collecter des données de journalisation à l’aide des diagnostics Azure.

>[AZURE.NOTE] La configuration du service local est déjà configurée pour utiliser les ressources locales. Si vous utilisez la configuration de service cloud pour publier votre service cloud Azure, la chaîne de connexion que vous spécifiez lors de la publication est également utilisée pour la chaîne de connexion de diagnostic sauf si vous avez spécifié une chaîne de connexion. Si vous empaquetez votre service cloud à l’aide de Visual Studio, la chaîne de connexion dans la configuration du service n’est pas modifiée.

### <a name="to-collect-cloud-service-diagnostics"></a>Pour collecter des tests de diagnostic de service cloud

1. Cliquez sur l’onglet **Configuration** .

1. Dans la liste de la **Configuration du Service** , choisissez la configuration du service que vous souhaitez mettre à jour ou sélectionnez **Toutes les Configurations**.

    >[AZURE.NOTE] Vous pouvez mettre à jour le compte de stockage pour une configuration de service spécifiques, mais si vous souhaitez activer ou désactiver les diagnostics, vous devez choisir toutes les Configurations.

1. Pour activer les diagnostics, activez la case à cocher **Activer les tests de diagnostic** .

1. Pour modifier la valeur pour le compte de stockage, cliquez sur le bouton de sélection (...).

    La boîte de dialogue **Chaîne de connexion de stockage de créer** s’affiche.

1. Pour utiliser une chaîne de connexion locale, choisissez option d’émulateur de stockage Azure et cliquez sur le bouton **OK** .

1. Pour utiliser un compte de stockage associé à votre abonnement Azure, choisissez l’option de **votre abonnement** .

1. Pour utiliser un compte de stockage pour la chaîne de connexion locale, choisissez l’option **Saisie manuelle des informations d’identification** .

    Pour plus d’informations sur la création d’un compte de stockage et comment entrer les détails pour le compte de stockage dans la boîte de dialogue **Chaîne de connexion de stockage de créer** , voir la rubrique [Prepare à publier ou à déployer une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Cliquez sur le compte de stockage que vous souhaitez utiliser dans la zone **nom de compte**.

    Si vous êtes manuellement entrer vos informations d’identification de compte de stockage, copiez ou tapez votre clé primaire dans la **clé de compte**. Cette clé peut être copiée à partir du [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). Pour copier cette clé, procédez comme suit à partir de l’affichage des **Comptes de stockage** [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Sélectionnez le compte de stockage que vous souhaitez utiliser pour votre service cloud.

  1. Cliquez sur le bouton **Gérer les touches d’accès rapide** situé au bas de l’écran. La boîte de dialogue **Gérer les touches d’accès rapide** s’affiche.

  1. Pour copier la touche d’accès rapide, cliquez sur le bouton **Copier dans le Presse-papiers** . Vous pouvez maintenant coller cette clé dans le champ **clé de compte** .

1. Pour utiliser le compte de stockage que vous fournissez, comme la chaîne de connexion pour les diagnostics (et la mise en cache) lorsque vous publiez votre service cloud sur Azure, activez la case à cocher **mise à jour développement stockage les chaînes de connexion pour les Diagnostics et la mise en cache avec le stockage Azure compte les informations d’identification lors de la publication vers Azure** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Modifier la taille de la machine virtuelle utilisée pour chaque rôle.

Vous pouvez définir la taille de la machine virtuelle pour chaque rôle. Vous pouvez uniquement définir cette taille pour toutes les configurations de service. Si vous sélectionnez une taille plus petite de l’ordinateur, puis moins de cœurs de processeur, mémoire et disque local stockage est alloué. La bande passante allouée est également plus petite. Pour plus d’informations sur les tailles et les ressources allouées, reportez-vous à la section [tailles pour les Services en nuage](cloud-services/cloud-services-sizes-specs.md).

Les ressources requises pour chaque ordinateur virtuel dans Azure affecte le coût de l’exécution de votre service cloud dans Azure. Pour plus d’informations sur la facturation d’Azure, voir [comprendre votre facture Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-size-of-the-virtual-machine"></a>Pour modifier la taille de la machine virtuelle

1. Cliquez sur l’onglet **Configuration** .

1. Dans la liste de la **Configuration du Service** , sélectionnez **Toutes les Configurations**.

1. Pour sélectionner la taille de l’ordinateur virtuel pour ce rôle, sélectionnez la taille appropriée dans la liste **taille de la mémoire virtuelle** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans le fichier de configuration de service.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Gérer les points de terminaison et les certificats à vos rôles

Vous configurez des points de terminaison réseau en spécifiant le protocole, le numéro de port et, pour le protocole HTTPS, les informations de certificat SSL. Versions antérieures à juin 2012 prend en charge TCP, HTTP et HTTPS. La version de juin 2012 prend en charge les protocoles UDP. Vous ne pouvez pas utiliser UDP pour les points de terminaison d’entrée dans l’émulateur de calcul. Vous pouvez utiliser ce protocole uniquement pour les points de terminaison internes.

Pour améliorer la sécurité de votre service cloud Azure, vous pouvez créer des points de terminaison qui utilisent le protocole HTTPS. Par exemple, si vous disposez d’un service en nuage qui est utilisé par les clients pour les commandes fournisseur, vous souhaitez vous assurer que leurs informations sont protégées à l’aide de SSL.

Vous pouvez également ajouter des points de terminaison qui peut être utilisé en interne ou en externe. Les points de terminaison externes sont appelés des points de terminaison d’entrée. Un point de terminaison d’entrée permet à un autre point d’accès pour les utilisateurs de votre service cloud. Si vous disposez d’un service WCF, vous pouvez souhaiter exposer un point de terminaison interne pour un rôle web à utiliser pour accéder à ce service.

>[AZURE.IMPORTANT] Vous pouvez uniquement mettre à jour les points de terminaison pour toutes les configurations de service.

Si vous ajoutez des points de terminaison HTTPS, vous devez utiliser un certificat SSL. Pour ce faire, vous pouvez associer des certificats à votre rôle pour toutes les configurations de service et les utiliser pour vos points de terminaison.

>[AZURE.IMPORTANT] Ces certificats ne sont pas fournies avec votre service. Vous devez télécharger vos certificats séparément sur Azure via [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

Les certificats de gestion que vous associez à vos configurations de service s’appliquent uniquement lorsque votre service cloud s’exécute dans Azure. Lorsque votre service cloud s’exécute dans l’environnement de développement local, un certificat qui est géré par l’émulateur de calcul Azure standard est utilisé.

### <a name="to-add-a-certificate-to-a-role"></a>Pour ajouter un certificat à un rôle

1. Cliquez sur l’onglet **certificats** .

1. Dans la liste de la **Configuration du Service** , sélectionnez **Toutes les Configurations**.

    >[AZURE.NOTE] Pour ajouter ou supprimer des certificats, vous devez sélectionner toutes les Configurations. Vous pouvez mettre à jour le nom et l’empreinte numérique pour une configuration de service spécifiques si nécessaire.

1. Pour ajouter un certificat pour ce rôle, cliquez sur le bouton **Ajouter un certificat** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , entrez le nom du certificat.

1. Dans la liste **Emplacement de la banque** , choisissez l’emplacement du certificat que vous souhaitez ajouter.

1. Dans la liste **Nom de magasin** , sélectionnez le magasin que vous souhaitez utiliser pour sélectionner le certificat.

1. Pour ajouter le certificat, cliquez sur le bouton de sélection (...). La boîte de dialogue **Sécurité de Windows** s’affiche.

1. Sélectionnez le certificat que vous souhaitez utiliser dans la liste et cliquez sur le bouton **OK** .

    >[AZURE.NOTE] Lorsque vous ajoutez un certificat du magasin de certificats, les certificats intermédiaires sont ajoutés automatiquement aux paramètres de configuration pour vous. Ces certificats intermédiaires doivent également être téléchargés vers Azure pour configurer correctement votre service pour SSL.

1. Pour supprimer un certificat, choisissez le certificat et cliquez sur le bouton **Supprimer le certificat** .

1. Cliquez sur l’icône **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans les fichiers de configuration de service.

### <a name="to-manage-endpoints-for-a-role"></a>Pour gérer les points de terminaison pour un rôle

1. Cliquez sur l’onglet **points de terminaison** .

1. Dans la liste de la **Configuration du Service** , sélectionnez **Toutes les Configurations**.

1. Pour ajouter un point de terminaison, cliquez sur le bouton **Ajouter un point de terminaison** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour ce point de terminaison.

1. Dans la liste **Type** , choisissez le type de point de terminaison dont vous avez besoin.

1. Choisissez le protocole pour le point de terminaison dont vous avez besoin à partir de la liste des **protocoles** .

1. S’il s’agit d’un point de terminaison d’entrée, dans la zone de texte **Port Public** , entrez le port public à utiliser.

1. Dans la zone de texte **Port de privé** , tapez le port privé à utiliser.

1. Si le point de terminaison nécessite le protocole https, procédez comme suit : dans la liste **Nom de certificat SSL** choisir un certificat à utiliser.

    >[AZURE.NOTE] Cette liste répertorie les certificats que vous avez ajoutés à ce rôle dans l’onglet **certificats** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications dans les fichiers de configuration de service.

## <a name="next-steps"></a>Étapes suivantes
Plus d’informations sur les projets Azure dans Visual Studio en lisant la [configuration d’un projet d’Azure](vs-azure-tools-configuring-an-azure-project.md). Plus d’informations sur le schéma de service cloud en lisant le [Schéma de référence](https://msdn.microsoft.com/library/azure/dd179398).
