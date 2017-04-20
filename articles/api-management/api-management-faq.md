<properties
    pageTitle="FAQ sur la gestion des API Azure | Microsoft Azure"
    description="Découvrez les réponses aux questions courantes, de motifs et de meilleures pratiques dans la gestion des API Azure."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Gestion des questions fréquentes sur les API Azure

Obtenez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour la gestion des API Azure.

## <a name="frequently-asked-questions"></a>Forum aux questions

-   [Comment puis-je demander l’équipe de gestion de Microsoft Azure API en question ?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [Ce que cela signifie lorsqu’une fonctionnalité est en mode Aperçu ?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [Comment puis-je pour sécuriser la connexion entre la passerelle de gestion des API et Mes services back-end ?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [Comment copier des mon instance du service de gestion de l’API à une nouvelle instance ?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [Puis-je gérer mon instance API gestion par programmation ?](#can-i-manage-my-api-management-instance-programmatically)
-   [Comment pour ajouter un utilisateur au groupe Administrateurs ?](#how-do-i-add-a-user-to-the-administrators-group)
-   [Pourquoi est la stratégie que vous voulez ajouter indisponible dans l’éditeur de stratégie ?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [Comment utiliser le versioning d’API dans les API de gestion ?](#how-do-i-use-api-versioning-in-api-management)
-   [Comment configurer des environnements multiples dans une seule API ?](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [Puis-je utiliser SOAP avec l’API de gestion ?](#can-i-use-soap-with-api-management)
-   [Est la constante de gestion de l’API passerelle IP adresse ? Puis-je utiliser dans les règles de pare-feu ?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [Puis-je configurer un serveur d’autorisation OAuth 2.0 avec une sécurité AD FS ?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [Quelle méthode de routage utiliser par gestion des API dans les déploiements sur plusieurs emplacements géographiques ?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [Puis-je utiliser un modèle de gestionnaire de ressources Azure pour créer une instance de service de gestion de l’API ?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [Puis-je utiliser un certificat SSL auto-signé pour un back-end ?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [Je reçois un échec d’authentification lors de cloner un référentiel GIT ?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [API de gestion fonctionne avec Azure ExpressRoute ?](#does-api-management-work-with-azure-expressroute)
-   [Puis-je déplacer un service de gestion de l’API d’un abonnement à un autre ?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Comment puis-je demander l’équipe de gestion de Microsoft Azure API en question ?

Vous pouvez nous contacter à l’aide d’une des options suivantes :

-   Publiez vos questions dans notre [forum MSDN de gestion API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Envoyez un e-mail à <apimgmt@microsoft.com>.
-   Nous envoyer une demande de fonctionnalité dans les [Evaluations Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Ce que cela signifie lorsqu’une fonctionnalité est en mode Aperçu ?

Lorsqu’une fonctionnalité est en mode Aperçu, cela signifie que nous sommes activement à la recherche commentaires sur le fonctionne de la fonctionnalité pour vous. Une fonctionnalité d’aperçu est fonctionnellement complet, mais il est possible que nous allons modifier une rupture en réponse aux commentaires des clients. Il est recommandé que vous ne dépendez pas une fonctionnalité qui est dans l’aperçu dans votre environnement de production. Si vous avez des commentaires sur les fonctionnalités de visualisation, faites-le nous savoir via l’une des options de contact [Comment puis-je demander l’équipe de gestion des API Microsoft Azure une question ?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Comment puis-je pour sécuriser la connexion entre la passerelle de gestion des API et Mes services back-end ?

Vous disposez de plusieurs options pour sécuriser la connexion entre la passerelle de gestion des API et de vos services back-end. Vous pouvez :

-   Utiliser l’authentification de base HTTP. Pour plus d’informations, reportez-vous à la section [paramètres de l’API de configuration](api-management-howto-create-apis.md#configure-api-settings).
- Utiliser l’authentification mutuelle SSL comme décrit dans [la sécurisation des services back-end en utilisant l’authentification de certificat client dans la gestion des API Azure](api-management-howto-mutual-certificates.md).
- Utiliser la création de listes autorisées IP sur votre service back-end. Si vous avez une instance de gestion de l’API de couche Standard ou Premium, l’adresse IP de la passerelle reste constante. Vous pouvez définir votre liste d’autorisation pour autoriser cette adresse IP. Vous pouvez obtenir l’adresse IP de votre instance de gestion de l’API sur le tableau de bord dans le portail Azure.
- Se connecter à votre instance de gestion de l’API à un réseau virtuel d’Azure. Pour plus d’informations, consultez [comment configurer des connexions VPN dans Gestion de l’API Azure](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Comment copier des mon instance du service de gestion de l’API à une nouvelle instance ?

Si vous souhaitez copier une instance de gestion de l’API dans une nouvelle instance, vous disposez de plusieurs options. Vous pouvez :

-   Utiliser la sauvegarde et de restauration de la fonction dans l’API de gestion. Pour plus d’informations, consultez [comment mettre en œuvre la reprise après sinistre à l’aide de sauvegarde et de restauration dans la gestion des API Azure](api-management-howto-disaster-recovery-backup-restore.md).
-   Créer votre propre sauvegarde et restaurer la fonctionnalité à l’aide de l' [API REST de gestion API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilisez l’API REST pour enregistrer et restaurer les entités à partir de l’instance du service que vous souhaitez.
-   Télécharger la configuration du service à l’aide de Git et ensuite de le télécharger vers une nouvelle instance. Pour plus d’informations, consultez [comment enregistrer et configurer la configuration de votre service de gestion de l’API à l’aide de Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Puis-je gérer mon instance API gestion par programmation ?

Oui, vous pouvez gérer les API de gestion par programme à l’aide de :

-   La [gestion de l’API API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   La [bibliothèque de gestion de Service de ApiManagement Microsoft Azure SDK](http://aka.ms/apimsdk).
-   Les applets de commande PowerShell de [Gestion des services](https://msdn.microsoft.com/library/mt613507.aspx) et de [déploiement du Service](https://msdn.microsoft.com/library/mt619282.aspx) .

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Comment pour ajouter un utilisateur au groupe Administrateurs ?

Voici comment vous pouvez ajouter un utilisateur au groupe Administrateurs :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Atteindre le groupe de ressources qui a l’instance de gestion de l’API que vous souhaitez mettre à jour.
3. Dans Gestion de l’API, attribuer le rôle de **Collaborateur de gestion Api** à l’utilisateur.

Le collaborateur nouvellement ajouté peut à présent utiliser Azure PowerShell [applets de commande](https://msdn.microsoft.com/library/mt613507.aspx). Voici comment procéder pour vous connecter en tant qu’administrateur :

1. Utilisez le `Login-AzureRmAccount` applet de commande pour vous connecter.
2. Définir le contexte à l’abonnement avec le service à l’aide de `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obtenir une URL à l’aide de `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utiliser l’URL pour accéder au portail d’administration.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Pourquoi est la stratégie que vous voulez ajouter indisponible dans l’éditeur de stratégie ?

Si la stratégie que vous souhaitez ajouter apparaît estompé ou ombré dans l’éditeur de stratégie, n’oubliez pas que vous êtes dans l’étendue appropriée pour la stratégie. Chaque instruction de stratégie est conçue pour vous permet d’utiliser des étendues spécifiques et des sections de la stratégie. Pour passer en revue les sections de la stratégie et les étendues pour une stratégie, voir la section de l’utilisation de la stratégie dans les [stratégies de gestion de l’API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>Comment utiliser le versioning d’API dans les API de gestion ?

Vous disposez de quelques options pour utiliser le versionnage de l’API dans les API de gestion :

-   Dans Gestion de l’API, vous pouvez configurer l’API pour représenter les différentes versions. Par exemple, peut avoir deux différentes API, MyAPIv1 et MyAPIv2. Un développeur peut choisir la version que le développeur souhaite utiliser.
-   Vous pouvez également configurer votre API avec une URL de service qui n’inclut pas un version du segment, par exemple, https://my.api. Ensuite, configurez un segment de version sur le modèle de [Réécrire les URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) de chaque opération. Par exemple, vous avez une opération avec un [modèle d’URL](api-management-howto-add-operations.md#url-template) appelé /resource et un modèle de [Réécrire les URL](api-management-howto-add-operations.md#rewrite-url-template) appelée /v1/Resource. Vous pouvez modifier la valeur du segment de version séparément pour chaque opération.
-   Si vous souhaitez conserver un segment de la version « par défaut » dans l’URL du service de l’API, sur les opérations sélectionnées, définir une stratégie qui utilise la stratégie de [service en aval de la valeur](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) pour modifier le chemin d’accès de la demande de back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Comment configurer des environnements multiples dans une seule API ?

Pour configurer plusieurs environnements, par exemple, un environnement de test et un environnement de production dans une seule API, vous disposez de deux options. Vous pouvez :

-   Hôte de différentes API sur le même client.
-   Héberger les mêmes API de locataires différents.

### <a name="can-i-use-soap-with-api-management"></a>Puis-je utiliser SOAP avec l’API de gestion ?

La prise en charge [directe SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) est désormais disponible. Les administrateurs peuvent importer le WSDL du service SOAP, et gestion des API Azure va créer un front-end SOAP. Documentation du portail développeur, console de test, les stratégies et analytique est disponibles pour les services SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Est la constante de gestion de l’API passerelle IP adresse ? Puis-je utiliser dans les règles de pare-feu ?

Sur les couches Standard et Premium, l’adresse IP publique (VIP) du locataire API gestion est statique pour la durée de vie de la cliente, à quelques exceptions près. Les modifications d’adresse IP dans ces circonstances :

-   Le service est supprimé et puis créé à nouveau.
-   L’abonnement au service est suspendue (par exemple, nonpayment) et puis relancé.
-   Vous ajoutez ou que vous supprimez Azure de réseau virtuel (vous pouvez utiliser réseau virtuel uniquement au niveau Premium).

Pour les déploiements de plusieurs régions, modification de l’adresse régionale si la région est libérée et puis relancée (vous pouvez utiliser le déploiement de plusieurs région uniquement au niveau Premium).

Locataires de couche de prime qui sont configurés pour le déploiement de plusieurs région sont affectés d’une adresse IP publique par région.

Vous pouvez obtenir votre adresse IP (ou adresse, dans un déploiement de plusieurs région) sur la page de clients dans le portail Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Puis-je configurer un serveur d’autorisation OAuth 2.0 avec une sécurité AD FS ?

Pour savoir comment configurer un serveur d’autorisation OAuth 2.0 avec la sécurité d’Active Directory Federation Services (ADFS), reportez-vous [à l’aide d’ADFS dans Gestion de l’API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Quelle méthode de routage utiliser par gestion des API dans les déploiements sur plusieurs emplacements géographiques ?

API de gestion utilise la [méthode de routage de trafic de performances](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) dans les déploiements sur plusieurs emplacements géographiques. Le trafic entrant est routé vers la passerelle API le plus proche. Si une région est déconnectée, le trafic entrant est automatiquement acheminé vers la passerelle plus proche suivante. En savoir plus sur les méthodes de routage dans le [Gestionnaire de trafic de routage des méthodes](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Puis-je utiliser un modèle de gestionnaire de ressources Azure pour créer une instance de service de gestion de l’API ?

Oui. Consultez les modèles de QuickStart [Azure API de gestion de Service](http://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Puis-je utiliser un certificat SSL auto-signé pour un back-end ?

Oui. Voici comment utiliser un certificat auto-signé de Secure Sockets Layer (SSL) pour un back-end :

1. Créer une entité [principale](https://msdn.microsoft.com/library/azure/dn935030.aspx) à l’aide des API de gestion.
2. La propriété **skipCertificateChainValidation** la valeur **true**.
3. Si vous ne souhaitez plus autoriser les certificats autosignés, supprimez l’entité principale ou la propriété **skipCertificateChainValidation** la valeur **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Je reçois un échec d’authentification lors de cloner un référentiel Git ?

Si vous utilisez le Gestionnaire d’informations d’identification Git, ou si vous essayez de dupliquer un référentiel Git à l’aide de Visual Studio, vous pourriez rencontrer un problème connu avec la boîte de dialogue informations d’identification Windows. La boîte de dialogue limites de longueur de mot de passe de 127 caractères et il tronque le mot de passe généré par Microsoft. Nous travaillons sur raccourcir le mot de passe. Pour l’instant, utilisez Git Bash pour dupliquer votre référentiel Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>API de gestion fonctionne avec Azure ExpressRoute ?

Oui. API de gestion fonctionne avec ExpressRoute d’Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Puis-je déplacer un service de gestion de l’API d’un abonnement à un autre ?

Oui. Pour savoir comment procéder, voir [déplacer les ressources vers un nouveau groupe de ressources ou un abonnement](../resource-group-move-resources.md).
