<properties 
    pageTitle="Gérer les points de terminaison en continu avec le portail Azure | Microsoft Azure" 
    description="Cette rubrique indique comment gérer les points de terminaison en continu avec le portail Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gérer les points de terminaison en continu avec le portail Azure

## <a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour terminer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, reportez-vous à la section [D’essai Azure](https://azure.microsoft.com/pricing/free-trial/). 

Dans les Services de support Microsoft Azure, un **Point de terminaison en continu** représente un service de diffusion en continu qui peut diffuser du contenu directement dans une application de lecteur client ou à un réseau de livraison de contenu (CDN) distribution se poursuive. Media Services fournit également une intégration transparente Azure CDN. Le flux de données sortant d’un service de StreamingEndpoint peut être un flux en direct, ou une vidéo à la demande actifs sur votre compte de Services de support.

En outre, vous pouvez contrôler la capacité du service de diffusion en continu le point de terminaison pour gérer les besoins croissants de la bande passante en réglant les unités de transmission en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Unités de transmission en continu vous fournissent avec capacité de sortie dédié qui peut être achetée par incréments de 200 Mbits/s et des fonctionnalités supplémentaires, notamment : [emballage dynamique](media-services-dynamic-packaging-overview.md), l’intégration du CDN et configuration avancée.

>[AZURE.NOTE]Facturation uniquement lorsque votre point de terminaison pour la diffusion en continu est en état de marche.

Cette rubrique fournit une vue d’ensemble des fonctionnalités principales qui sont fournis par les points de terminaison de diffusion en continu. La rubrique indique également comment utiliser le portail Azure pour gérer les points de terminaison en continu. Pour plus d’informations sur la façon de mettre à l’échelle le point de terminaison en continu, consultez la rubrique [suivante](media-services-portal-scale-streaming-endpoints.md) .

## <a name="start-managing-streaming-endpoints"></a>Démarrer la gestion des points de terminaison en continu

Pour commencer à gérer les points de terminaison en continu de votre compte, effectuez les opérations suivantes.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , sélectionnez les **points de terminaison de diffusion en continu**.

    ![Point de terminaison de la diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Ajout/Suppression d’un point de terminaison en continu

Pour ajouter/supprimer un point de terminaison en continu via le portail d’Azure, effectuez les opérations suivantes :

1. Pour ajouter un point de terminaison en continu, cliquez sur le **+ de point de terminaison** en haut de la page. 
2. Pour supprimer un point de terminaison en continu, appuyez sur le bouton **Supprimer** . 

    Diffusion en continu de point de terminaison par défaut ne peut pas être supprimé.
2. Cliquez sur le bouton **Démarrer** pour démarrer le point de terminaison en continu.

    ![Point de terminaison de la diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Par défaut, vous pouvez avoir jusqu'à deux points de terminaison en continu. Si vous demandez plus, reportez-vous à la section [Quotas et des limitations](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>Configurer le point de terminaison en continu

Diffusion en continu de point de terminaison vous permet de configurer les propriétés suivantes lorsque vous disposez d’au moins 1 unité de l’échelle permet de : 

- Contrôle d’accès
- Contrôle de cache
- Entre les stratégies d’accès de site

Pour plus d’informations sur ces propriétés, consultez [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Vous pouvez configurer le point de terminaison en continu en procédant comme suit :

1. Sélectionnez le point de terminaison en continu que vous souhaitez configurer.
1. Cliquez sur **paramètres**.
  
Une description brève des champs ci-dessous.

![Point de terminaison de la diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Stratégie de cache maximale : permet de configurer la durée de vie du cache pour les actifs pris en charge par ce point de terminaison en continu. Si aucune valeur n’est définie, la valeur par défaut est utilisé. Les valeurs par défaut peuvent également être définies directement dans le stockage Azure. Si Azure CDN est activé pour le point de terminaison en continu, vous ne devez pas définir la valeur de stratégie de cache à moins de 600 secondes.  

2. Autorisé des adresses IP : permet de spécifier les adresses IP qui seraient autorisés à se connecter au point de terminaison en continu publiée. Si aucune adresse IP n’est spécifiée, toutes les adresses IP serait en mesure de se connecter. Adresses IP peuvent être spécifiées sous la forme d’une seule adresse IP (par exemple, « 10.0.0.1 »), une plage IP à l’aide d’une adresse IP et un masque de sous-réseau CIDR (par exemple, ' 10.0.0.1/22') ou une plage IP à l’aide d’adresse IP et un masque de sous-réseau décimal séparé (par exemple, ' 10.0.0.1(255.255.255.0)').

3. Configuration de l’authentification de Akamai signature en-tête : permet de spécifier la configuration de la demande de signature en-tête d’authentification de serveurs Akamai. Expiration est l’heure UTC.



##<a id="enable_cdn"></a>Activer l’intégration d’Azure CDN

Vous pouvez spécifier pour activer l’intégration d’Azure CDN pour un point de terminaison de diffusion en continu (elle est désactivée par défaut).

Pour configurer l’intégration Azure CDN sur true :

- Le point de terminaison de flux doit avoir au moins une unité de transmission en continu. Si vous souhaitez ultérieurement unités de l’échelle de la valeur 0, vous devez d’abord désactiver l’intégration du CDN. Par défaut, lorsque vous créez une nouvelle diffusion en continu une unité de transmission en continu de point de terminaison est définie automatiquement.

- Le point de terminaison en flux continu doit être dans un état arrêté. Une fois le CDN obtient activé, vous pouvez démarrer le point de terminaison en continu. 

Il peut prendre jusqu'à 90 minutes pour l’intégration d’Azure CDN pour activé.  Il prend deux heures pour que les modifications soit active tous les POP de CDN.

Intégration du CDN est activée dans tous les centres de données Azure : nous ouest, nous est, Europe du Nord, Europe de l’ouest, ouest du Japon, Japon Moyen-Orient, Asie du Sud-est et Asie de l’est.

Une fois qu’elle est activée, la configuration du **Contrôle d’accès** obtient désactivée.

![Point de terminaison de la diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Intégration de Media Services Azure avec Azure CDN est implémentée sur **Azure CDN auprès de Verizon**.  Si vous souhaitez utiliser **Azure CDN à partir d’Akamai** pour Azure Media Services, vous devez [configurer manuellement le point de terminaison](../cdn/cdn-create-new-endpoint.md).  Pour plus d’informations sur les fonctionnalités d’Azure CDN, consultez la [vue d’ensemble CDN](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Considérations supplémentaires

- Lorsque le CDN est activé pour un point de terminaison en continu, les clients ne peuvent pas demander du contenu directement à partir de l’origine. Si vous avez besoin de la possibilité de tester votre contenu avec ou sans CDN, vous pouvez créer un autre point de terminaison en continu n’est pas les CDN activé.
- Votre nom d’hôte du point de terminaison en continu reste le même après l’activation du CDN. Vous ne devez apporter des modifications à votre flux de travail de services média après que CDN est activé. Par exemple, si votre nom d’hôte du point de terminaison en continu est strasbourg.streaming.mediaservices.windows.net, après avoir activé le CDN, le même nom d’hôte exact est utilisé.
- De nouveaux points de terminaison en continu, vous pouvez activer le CDN simplement par la création d’un nouveau point de terminaison ; pour les points de terminaison existants en continu, vous devez tout d’abord arrêter le point de terminaison et activer le CDN.
 

Pour plus d’informations, consultez [intégration d’annonce Azure Media Services avec Azure CDN (Content Delivery Network)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services, voies de formation.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
