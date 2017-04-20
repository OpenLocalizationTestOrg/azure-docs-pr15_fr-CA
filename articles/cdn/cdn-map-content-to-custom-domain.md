<properties
     pageTitle="Comment mapper Azure Content Delivery Network (CDN) contenu dans un domaine personnalisé | Microsoft Azure"
     description="Cette rubrique montrent comment mapper un contenu CDN pour un domaine personnalisé."
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
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Le mappage personnalisé domaine au point de terminaison réseau CDN (Content Delivery)
Vous pouvez mapper un domaine personnalisé à un point de terminaison CDN pour utiliser votre propre nom de domaine dans les URL pour le contenu mis en cache, plutôt que d’utiliser un sous-domaine de azureedge.net.

Il existe deux moyens de mapper votre domaine personnalisé à un point de terminaison du Canada :

1. [Créer un enregistrement CNAME avec votre registraire de domaine et mapper votre domaine personnalisé et le sous-domaine au point de terminaison CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    Un enregistrement CNAME est une fonction DNS qui mappe le domaine source, comme `www.contosocdn.com` ou `cdn.contoso.com`, à un domaine de destination. Dans ce cas, le domaine source est votre domaine personnalisé et un sous-domaine (un sous-domaine, tels que **www** ou **cdn** est toujours requis). Le domaine de destination est votre point de terminaison CDN.  

    Le processus de mappage de votre domaine personnalisé pour votre point de terminaison CDN, toutefois, vous risquez dans une courte période de temps d’arrêt pour le domaine pendant que vous enregistrez le domaine dans le portail Azure.

2. [Ajouter une étape d’enregistrement intermédiaire avec **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Si votre domaine personnalisé est actuellement en charge d’une application avec un accord de niveau de service (SLA) qui requiert sans interruption de service, vous pouvez utiliser le sous-domaine Azure **cdnverify** pour fournir une étape intermédiaire d’enregistrement afin que les utilisateurs pourront accéder à votre domaine DNS mappage a lieu lors de le.  

Après avoir enregistré votre domaine personnalisé à l’aide de l’une des procédures ci-dessus, vous voulez [vérifier que le sous-domaine personnalisé fait référence à votre point de terminaison CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Vous devez créer un enregistrement CNAME avec votre registraire de domaine pour mapper votre domaine au point de terminaison CDN. Les enregistrements CNAME mappent des sous-domaines spécifiques telles que `www.contoso.com` ou `cdn.contoso.com`. Il n’est pas possible de mapper un enregistrement CNAME pour un domaine racine, de telles que `contoso.com`.
>    
> Un sous-domaine ne peut être associé à un point de terminaison CDN. L’enregistrement CNAME que vous créez achemine tout le trafic adressé au sous-domaine au point de terminaison spécifié.  Par exemple, si vous associez `www.contoso.com` avec votre point de terminaison CDN, vous ne pouvez pas associer ensuite à autres points de terminaison Azure, tel qu’un point de terminaison de compte de stockage ou un point de terminaison de service cloud. Toutefois, vous pouvez utiliser des sous-domaines différents provenant du même domaine pour les points de terminaison de service différent. Vous pouvez également mapper des noms de sous-domaines différents pour le même point de terminaison CDN.
>
> Pour les points de terminaison **Azure CDN de Verizon** (Standard et Premium), notez que ce délai peut atteindre **90 minutes** pour que les modifications de domaine personnalisé de se propager à noeuds CDN.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Enregistrer un domaine personnalisé pour un point de terminaison Azure CDN

1.  Journal du [portail Azure](https://portal.azure.com/).
2.  Cliquez sur **Parcourir**, puis **Les profils CDN**, puis le profil CDN avec le point de terminaison que vous souhaitez mapper à un domaine personnalisé.  
3.  Dans la lame **CDN profil** , cliquez sur le point de terminaison CDN avec lequel vous voulez associer le sous-domaine.
4.  En haut de la lame de point de terminaison, cliquez sur le bouton **Ajouter un domaine personnalisé** .  De la lame **d’Ajouter un domaine personnalisé** , vous verrez le nom d’hôte de point de terminaison, dérivé à partir de votre point de terminaison CDN, à utiliser lors de la création d’un nouvel enregistrement CNAME. Le format de l’adresse de nom d’hôte s’affiche en tant que ** &lt;EndpointName >. azureedge.net**.  Vous pouvez copier ce nom d’hôte à utiliser lors de la création de l’enregistrement CNAME.  
5.  Accédez au site web de votre registraire de domaine et recherchez la section de la création d’enregistrements DNS. Vous pouvez le trouver dans une section comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.
6.  Recherchez la section de gestion des enregistrements CNAME. Il se peut que vous deviez accéder à une page de paramètres avancés et recherchez les mots CNAME, Alias ou sous-domaines.
7.  Créer un enregistrement CNAME qui mappe votre sous-domaine choisie (par exemple, **www** ou **cdn**) au nom d’hôte fourni dans la blade **d’Ajouter un domaine personnalisé** .
8.  Revenez à la lame de **Ajouter un domaine personnalisé** et entrez votre domaine personnalisé, y compris le sous-domaine, dans la boîte de dialogue. Par exemple, entrez le nom de domaine dans le format `www.contoso.com` ou `cdn.contoso.com`.   

    Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine que vous avez entré. Si l’enregistrement CNAME est correct, votre domaine personnalisé est validé.  Pour les points de terminaison **Azure CDN de Verizon** (Standard et Premium), il peut prendre jusqu'à 90 minutes pour se propager à tous les nœuds de bord CDN, toutefois, les paramètres de domaine personnalisé.  

    Notez que dans certains cas peut prendre du temps pour l’enregistrement CNAME propager aux serveurs sur Internet. Si votre domaine n’est pas validée immédiatement, et vous pensez que l’enregistrement CNAME est correct, patientez quelques minutes, puis essayez à nouveau.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Enregistrer un domaine personnalisé pour un point de terminaison Azure CDN utilisant le sous-domaine cdnverify intermédiaire  

1. Journal du [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir**, puis **Les profils CDN**, puis le profil CDN avec le point de terminaison que vous souhaitez mapper à un domaine personnalisé.  
3. Dans la lame **CDN profil** , cliquez sur le point de terminaison CDN avec lequel vous voulez associer le sous-domaine.
4. En haut de la lame de point de terminaison, cliquez sur le bouton **Ajouter un domaine personnalisé** .  De la lame **d’Ajouter un domaine personnalisé** , vous verrez le nom d’hôte de point de terminaison, dérivé à partir de votre point de terminaison CDN, à utiliser lors de la création d’un nouvel enregistrement CNAME. Le format de l’adresse de nom d’hôte s’affiche en tant que ** &lt;EndpointName >. azureedge.net**.  Vous pouvez copier ce nom d’hôte à utiliser lors de la création de l’enregistrement CNAME.
5. Accédez au site web de votre registraire de domaine et recherchez la section de la création d’enregistrements DNS. Vous pouvez le trouver dans une section comme **Nom de domaine**, de **DNS**ou de **Gestion de serveur de nom**.
6. Recherchez la section de gestion des enregistrements CNAME. Il se peut que vous deviez accéder à une page de paramètres avancés et recherchez les mots **CNAME**, **Alias**ou **sous-domaines**.
7. Créer un nouvel enregistrement CNAME et fournir un alias de sous-domaine qui inclut le sous-domaine **cdnverify** . Par exemple, le sous-domaine que vous spécifiez sera dans le format **cdnverify.www** ou **cdnverify.cdn**. Puis entrez le nom d’hôte, qui est votre point de terminaison CDN, au format **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Revenez à la lame de **Ajouter un domaine personnalisé** et entrez votre domaine personnalisé, y compris le sous-domaine, dans la boîte de dialogue. Par exemple, entrez le nom de domaine dans le format `www.contoso.com` ou `cdn.contoso.com`. Notez que dans cette étape, vous n’avez pas besoin de faire précéder le sous-domaine avec **cdnverify**.  

    Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine cdnverify que vous avez entré.
9. À ce stade, votre domaine personnalisé a été vérifiée par Azure, mais le trafic vers votre domaine n’est pas encore être acheminé vers votre point de terminaison CDN. Après attente suffisamment longtemps pour permettre les paramètres de domaine personnalisé pour se propager sur les nœuds de bord CDN (90 minutes pour **Azure CDN de Verizon**, 1 – 2 minutes pour **Azure CDN à partir d’Akamai**), revenir au site web de votre registraire DNS et créer un autre enregistrement CNAME qui mappe votre sous-domaine à votre point de terminaison CDN. Par exemple, spécifier le sous-domaine que **www** ou **cdn**et le nom d’hôte en tant que ** &lt;EndpointName >. azureedge.net**. À cette étape, l’enregistrement de votre domaine personnalisé est terminée.
10. Enfin, vous pouvez supprimer l’enregistrement CNAME, vous avez créé à l’aide de **cdnverify**, tel qu’il était nécessaire que comme une étape intermédiaire.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Vérifiez que le sous-domaine personnalisé fait référence à votre point de terminaison CDN

- Après avoir terminé l’enregistrement de votre domaine personnalisé, vous pouvez accéder contenu mis en cache à votre point de terminaison du Canada à l’aide du domaine personnalisé.
Tout d’abord, assurez-vous qu’un contenu public qui est mis en cache sur le point de terminaison. Par exemple, si votre point de terminaison CDN est associé à un compte de stockage, le CDN met en cache des contenus dans des récipients de blob publique. Pour tester le domaine personnalisé, assurez-vous que votre conteneur est défini pour permettre un accès public, et qu’il contient au moins un blob.
- Dans votre navigateur, accédez à l’adresse de l’objet blob le nom de domaine personnalisé. Par exemple, si votre domaine personnalisé est `cdn.contoso.com`, l’URL à un objet blob mis en cache sera similaire à l’URL suivante : http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Voir aussi

[Comment faire pour activer le réseau de diffusion de contenu (CDN) pour Azure](./cdn-create-new-endpoint.md)  
