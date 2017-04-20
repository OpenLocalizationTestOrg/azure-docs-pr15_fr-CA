<properties
    pageTitle=" Créer un compte Azure Media Services avec le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des étapes de création d’un compte Azure Media Services avec le portail Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Créer un compte Azure Media Services via le portail Azure

> [AZURE.SELECTOR]
- [Portail](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTE](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

Le portail Azure fournit un moyen de créer rapidement un compte Azure Media Services (AMS). Vous pouvez utiliser votre compte pour accéder aux Services de support qui vous permettent de stocker les chiffrer, coder, gérer et diffuser du contenu multimédia dans Azure. Au moment de la création d’un compte de Services de support, vous aussi créer un compte de stockage associé (ou utilisez un) dans la même région géographique, comme le compte de Services de support.

Cet article explique certains concepts communs et montre comment créer un compte de Services de support avec le portail Azure.

## <a name="concepts"></a>Concepts

L’accès aux Services de support requiert deux comptes associés :

- Un compte de Services de support. Votre compte vous donne accès à un ensemble de Services de support en nuage disponibles dans Azure. Un compte de Services de support ne stocke pas le contenu réel. Au lieu de cela, il stocke les métadonnées sur les contenus multimédias et les travaux de traitement de support dans votre compte. Au moment de la que création du compte, vous sélectionnez une zone de Services de support disponible. La région que vous sélectionnez est un centre de données qui stocke les enregistrements de métadonnées pour votre compte.

    Régions de Media Services (AMS) disponibles sont les suivants : Europe du Nord, Europe de l’ouest, États-Unis Ouest, américains Extrême-Orient, Asie du Sud-est, Asie de l’est, du Japon ouest, Nord-est du Japon. Media Services n’utilise pas d’affinités.
    
    AMS est désormais également disponibles dans les centres de données suivant : Brésil sud ouest de l’Inde, du Sud de l’Inde et Central de l’Inde. Vous pouvez désormais utiliser le portail Azure pour créer des comptes de Service de support et d’effectuer diverses tâches décrites ici. Toutefois, l ' encodage Live n’est pas activé dans ces centres de données. En outre, pas tous les types d’unités réservées de codage sont disponibles dans ces centres de données.
    
    - Sud du Brésil : Seuls Standard et codage réservé des unités de base sont disponibles.
    - Inde ouest, sud de l’Inde : 

- Un compte de stockage Azure. Comptes de stockage doivent se trouver dans la même région géographique, comme le compte de Services de support. Lorsque vous créez un compte de Services de support, vous pouvez choisir un compte de stockage existant dans la même région, ou vous pouvez créer un nouveau compte de stockage dans la même région. Si vous supprimez un compte de Services de support, les blobs dans votre compte de stockage associés ne sont pas supprimés.

## <a name="create-an-ams-account"></a>Créer un compte AMS

Les étapes de cette section montrent comment créer un compte de l’AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Web + Mobile** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** , entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte de Services de support est de tous les nombres en minuscules ou lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure auxquelles vous avez accès à.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. Pour en savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans **emplacement**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements des supports et des métadonnées pour votre compte de Services de support. Cette région permettra de traiter et de diffuser vos médias. Seules les régions de Media Services disponibles s’affichent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte de Services de support. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique, comme votre compte de Services de support, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles pour les noms de compte de stockage sont les mêmes que pour les comptes de Services de support.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour voir la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, coder actifs, surveiller la progression de la tâche) utilisez la fenêtre **paramètres** .

## <a name="manage-keys"></a>La gestion des clés

Vous devez le nom de compte et les informations de clé primaire pour accéder par programme le compte des Services de support.

1. Dans le portail Azure, sélectionnez votre compte. 

    La fenêtre **paramètres** s’affiche sur la droite. 

2. Dans la fenêtre **paramètres** , sélectionnez **clés**. 

    **Gérer les clés** windows affiche le nom de compte et les clés primaires et secondaires s’affiche. 
3. Appuyez sur le bouton Copier pour copier les valeurs.
    
    ![Les clés des Services multimédia](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant télécharger des fichiers dans votre compte AMS. Pour plus d’informations, consultez [télécharger des fichiers](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


