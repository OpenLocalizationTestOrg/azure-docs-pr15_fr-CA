<properties
    pageTitle="Intégrer un compte de stockage CDN | Microsoft Azure"
    description="Apprenez à utiliser le réseau de livraison de contenu (CDN) Azure pour diffuser du contenu de la bande passante élevée par la mise en cache des objets BLOB de stockage Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Intégrer un compte de stockage CDN

Canada peut être activée pour le contenu du cache de votre stockage Azure. Il offre aux développeurs une solution globale pour fournir un contenu de large bande passante en mettant en cache des objets BLOB et le contenu statique des instances de calcul au niveau des nœuds physiques dans les États-Unis, Europe, Asie, Australie et Amérique du Sud.


## <a name="step-1-create-a-storage-account"></a>Étape 1 : Créer un compte de stockage

Utilisez la procédure suivante pour créer un nouveau compte de stockage pour un abonnement Azure. Un compte de stockage permet d’accéder aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour accéder à chacun des composants du service de stockage Azure : Blob de services, les services de file d’attente et les services de la Table. Pour plus d’informations, reportez-vous à l' [Introduction au stockage Azure de Microsoft](../storage/storage-introduction.md).

Pour créer un compte de stockage, vous devez être l’administrateur du service ou administrateur de collaboration pour l’abonnement associé.

> [AZURE.NOTE] Il existe plusieurs méthodes que vous pouvez utiliser pour créer un compte de stockage, y compris le portail Azure et Powershell.  Pour ce didacticiel, nous utilisons le portail Azure.  

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le coin supérieur gauche, sélectionnez **Nouveau**. Dans la boîte de dialogue **Nouveau** , sélectionnez **données + de stockage**, puis cliquez sur **compte de stockage**.

    La lame de **créer de compte de stockage** s’affiche.

    ![Créer le compte de stockage][create-new-storage-account]

4. Dans le champ **nom** , tapez un nom de sous-domaine. Cette entrée peut contenir des lettres minuscules et les chiffres de 3-24.

    Cette valeur devient le nom d’hôte dans l’URI qui est utilisée pour adresser les Blob, file d’attente ou un tableau des ressources pour l’abonnement. Pour répondre à une ressource de conteneur dans le service d’objet Blob, vous utiliseriez un URI au format suivant, où * &lt;StorageAccountLabel&gt; * fait référence à la valeur que vous avez tapé dans la zone **Entrez une URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Important :** Le libellé de l’URL du sous-domaine du compte de stockage URI de formulaires et doit être unique parmi tous les services hébergés dans Azure.

    Cette valeur est également utilisée comme le nom de ce compte de stockage dans le portail, ou lors de l’accès par programme de ce compte.

5. Laissez les valeurs par défaut pour le **modèle de déploiement**, de **type de compte**, de **performances**et de **réplication**. 

6. Sélectionnez l' **abonnement** qui sera utilisé avec le compte de stockage.

7. Sélectionnez ou créez un **Groupe de ressources**.  Pour plus d’informations sur les groupes de ressources, consultez [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Sélectionnez un emplacement pour votre compte de stockage.

8. Cliquez sur **créer**. Le processus de création du compte de stockage peut prendre plusieurs minutes.


## <a name="step-2-create-a-new-cdn-profile"></a>Étape 2 : Créer un nouveau profil CDN

Un profil CDN est une collection de points de terminaison CDN.  Chaque profil contient un ou plusieurs points de terminaison CDN.  Vous souhaiterez peut-être plusieurs profils permettent d’organiser vos points de terminaison CDN par domaine internet, application web ou d’autres critères.

> [AZURE.TIP] Si vous avez déjà un profil CDN que vous souhaitez utiliser pour ce didacticiel, passez à [l’étape 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Étape 3 : Créer un nouveau point de terminaison CDN

**Pour créer un nouveau point de terminaison CDN pour votre compte de stockage**

1. Dans le [Portail de gestion Azure](https://portal.azure.com), accédez à votre profil CDN.  Vous pouvez avoir épinglé il au tableau de bord à l’étape précédente.  Si vous ne vous le trouverez en cliquant sur **Parcourir**, puis **les profils CDN**, sur le profil que vous souhaitez ajouter votre point de terminaison.

    La lame de profil CDN s’affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison** .

    ![Ajouter bouton de point de terminaison][cdn-new-endpoint-button]

    La blade **d’Ajouter un point de terminaison** s’affiche.

    ![Ajouter des lames de point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN.  Ce nom servira à accéder à vos ressources mises en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Dans la liste déroulante **type d’origine** , sélectionnez *stockage*.  

5. Dans la liste déroulante **nom d’hôte d’origine** , sélectionnez votre compte de stockage.

6. Laissez les valeurs par défaut pour le **port de protocole/origine**, **en-tête hôte d’origine**et **chemin d’accès d’origine**.  Vous devez spécifier au moins un protocole (HTTP ou HTTPS).

    > [AZURE.NOTE] Cette configuration permet à tous vos conteneurs visibles publiquement dans votre compte de stockage pour la mise en cache dans le CDN.  Si vous souhaitez limiter la portée à un seul conteneur, utilisez le **chemin d’accès d’origine**.  Notez que le conteneur doit avoir sa visibilité la valeur publique.

7. Cliquez sur le bouton **Ajouter** pour créer le nouveau point de terminaison.

8. Une fois le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour le profil. La vue liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.NOTE] Le point de terminaison pas seront immédiatement disponible pour utilisation.  Il peut prendre jusqu'à 90 minutes pour l’enregistrement de se propager à travers le réseau CDN. Les utilisateurs qui tentent d’utiliser le nom de domaine CDN immédiatement receviez code d’état 404, jusqu'à ce que le contenu est disponible via le CDN.


## <a name="step-4-access-cdn-content"></a>Étape 4 : Accès CDN contenu

Pour accéder au contenu mis en cache sur le CDN, utiliser l’URL de CDN fourni dans le portail. L’adresse d’un objet blob mis en cache sera semblable au suivant :

http://< ;*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Une fois que vous activez l’accès à un compte de stockage CDN ou service hébergé, tous les objets accessibles sont éligibles pour la mise en cache de bord CDN. Si vous modifiez un objet qui est actuellement mis en cache dans le CDN, le nouveau contenu ne sera pas disponible via le CDN jusqu'à ce que le CDN actualise son contenu lors de l’expiration de la période de durée de vie contenue mis en cache.

## <a name="step-5-remove-content-from-the-cdn"></a>Étape 5 : Suppression du contenu du CDN

Si vous ne souhaitez plus mettre en cache un objet dans l’Azure réseau CDN (Content Delivery), vous pouvez prendre une des étapes suivantes :

-   Vous pouvez rendre le conteneur privé et non public. Pour plus d’informations, reportez-vous à la section [accès anonyme en lecture de gestion pour les conteneurs et les objets BLOB](../storage/storage-manage-access-to-resources.md) .
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN via le portail de gestion.
-   Vous pouvez modifier votre service hébergé pour ne plus répondre aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN restera en mémoire cache jusqu'à l’expiration de la période de durée de vie de l’objet ou jusqu'à ce que le point de terminaison est purgée. Lorsque la période de durée de vie expire, le CDN vérifie si le point de terminaison CDN est toujours valide et l’objet toujours accessible de manière anonyme. S’il n’est pas le cas, l’objet ne sera pas plus caché.


## <a name="additional-resources"></a>Ressources supplémentaires

-   [Comment mapper le contenu CDN pour un domaine personnalisé](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
