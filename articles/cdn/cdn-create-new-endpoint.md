<properties
     pageTitle="À l’aide d’Azure CDN | Microsoft Azure"
     description="Cette rubrique indique comment activer le réseau CDN (Content Delivery) pour Azure. Le didacticiel vous guide dans la création d’un nouveau profil CDN et un point de terminaison."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>À l’aide d’Azure CDN  

Cette rubrique explique l’activation Azure CDN en créant un nouveau profil CDN et un point de terminaison.

>[AZURE.IMPORTANT] Pour une introduction au fonctionne CDN, ainsi qu’une liste des fonctionnalités, consultez la [Vue d’ensemble du CDN](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Créer un nouveau profil CDN

Un profil CDN est une collection de points de terminaison CDN.  Chaque profil contient un ou plusieurs points de terminaison CDN.  Vous souhaiterez peut-être plusieurs profils permettent d’organiser vos points de terminaison CDN par domaine internet, application web ou d’autres critères.

> [AZURE.NOTE] Par défaut, un seul abonnement Azure est limité à huit profils CDN. Chaque profil CDN est limitée aux points de terminaison de dix CDN.
>
> Prix du CDN sont appliqué au niveau du profil CDN. Si vous souhaitez utiliser un mélange de niveaux de tarification Azure CDN, vous devrez plusieurs profils CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Créer un nouveau point de terminaison CDN

**Pour créer un nouveau point de terminaison CDN**

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre profil CDN.  Vous pouvez avoir épinglé il au tableau de bord à l’étape précédente.  Si vous ne vous le trouverez en cliquant sur **Parcourir**, puis **les profils CDN**, sur le profil que vous souhaitez ajouter votre point de terminaison.

    La lame de profil CDN s’affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison** .

    ![Ajouter bouton de point de terminaison][cdn-new-endpoint-button]

    La blade **d’Ajouter un point de terminaison** s’affiche.

    ![Ajouter des lames de point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN.  Ce nom servira à accéder à vos ressources mises en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Dans la liste déroulante **type d’origine** , sélectionnez votre type d’origine.  Sélectionnez le **stockage** pour un compte de stockage Azure, **service de Cloud** pour un Service de Cloud Azure, **Web App** pour un Azure Web App ou **personnalisé origine** de toute autre origine de serveur web accessible publiquement (hébergé dans Azure ou ailleurs).

    ![Type d’origine CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Dans la liste déroulante **nom d’hôte d’origine** , sélectionnez ou tapez votre domaine d’origine.  La liste déroulante répertorie toutes les origines disponibles du type spécifié à l’étape 4.  Si vous avez sélectionné *origine de personnaliser* votre **type d’origine**, vous saisirez dans le domaine d’origine du lot personnalisé.

6. Dans la zone de texte **chemin d’accès d’origine** , entrez le chemin d’accès aux ressources que vous souhaitez mettre en cache, ou laissez vide pour permettre de cache toutes les ressources au niveau du domaine que vous avez spécifié à l’étape 5.

7. Dans l' **en-tête de l’hôte origine**, entrez l’en-tête d’hôte que vous souhaitez que le CDN pour envoyer à chaque demande ou laissez la valeur par défaut.

    > [AZURE.WARNING] Certains types d’origines, telles que le stockage Azure et les applications Web, nécessitent l’en-tête d’hôte pour le correspond au domaine de l’origine. À moins d’avoir une origine nécessitant un en-tête d’hôte différent de son domaine, vous devez laisser la valeur par défaut.

8. Pour le **protocole** et le **port d’origine**, spécifier les protocoles et les ports utilisés pour accéder à vos ressources à l’origine.  Au moins un protocole (HTTP ou HTTPS) doit être sélectionné.
    
    > [AZURE.NOTE] Le **port d’origine** n’affecte que le port du point de terminaison utilise pour récupérer des informations à partir de l’origine.  Le point de terminaison sera uniquement disponible pour les clients finaux sur la valeur par défaut HTTP et HTTPS ports (80 et 443), quel que soit le **port d’origine**.  
    >
    > Les points de terminaison **Azure CDN à partir d’Akamai** n’autorisent pas la plage de ports TCP complet pour les origines.  Pour obtenir une liste des ports d’origine qui ne sont pas autorisés, consultez [Azure CDN à partir des Ports d’origine Akamai autorisés](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > L’accès CDN contenu à l’aide de HTTPS a les contraintes suivantes :
    > 
    > - Vous devez utiliser le certificat SSL fourni par le CDN. Les certificats de tierce partie ne sont pas pris en charge.
    > - Vous devez utiliser le domaine fournis par les CDN (`<endpointname>.azureedge.net`) pour accéder au contenu HTTPS. La prise en charge HTTPS n’est pas disponible pour les noms de domaine personnalisé (enregistrements CNAME) le CDN ne gérant pas les certificats personnalisés pour l’instant.

9. Cliquez sur le bouton **Ajouter** pour créer le nouveau point de terminaison.

10. Une fois le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour le profil. La vue liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] Le point de terminaison pas seront immédiatement disponible pour une utilisation, comme le temps requis pour l’enregistrement de se propager par l’intermédiaire du CDN.  Pour les profils <b>Azure CDN à partir d’Akamai</b> , propagation se terminera en une minute.  Pour les profils <b>Azure CDN de Verizon</b> , propagation termine généralement l’opération dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.
    >    
    > Les utilisateurs qui tentent d’utiliser le nom de domaine CDN avant la configuration de point de terminaison a propagé aux POP recevront les codes de réponse HTTP 404.  Si elle a été plusieurs heures étant donné que vous avez créé votre point de terminaison et vous recevez toujours 404 réponses, consultez [CDN de résolution des problèmes de points de terminaison 404 statuts de retour](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Voir aussi
- [Contrôle du comportement de mise en cache de requêtes avec des chaînes de requête](cdn-query-string.md)
- [Comment mapper le contenu CDN pour un domaine personnalisé](cdn-map-content-to-custom-domain.md)
- [Précharger des actifs sur un point de terminaison Azure CDN](cdn-preload-endpoint.md)
- [Purge d’un point de terminaison Azure CDN](cdn-purge-endpoint.md)
- [Dépannage des points de terminaison CDN 404 statuts de retour](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
