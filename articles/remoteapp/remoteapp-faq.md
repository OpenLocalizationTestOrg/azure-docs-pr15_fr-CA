<properties 
    pageTitle="Forum aux questions de RemoteApp Azure | Microsoft Azure" 
    description="Découvrez les réponses aux questions les plus fréquemment posées sur Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Forum aux questions sur Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Nous avons entendu les questions suivantes sur Azure RemoteApp. Que d’autres personnes ? Visitez les [forums de RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) et dites-nous ce que vous devez savoir, ou un commentaire liste déroulante ci-dessous.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Impossible de trouver ce que vous cherchez ? Vous avez une question que nous n’a pas répondu ?
Si vous ne trouvez pas les informations dont vous avez besoin, ou que vous avez une question supplémentaire que nous n’allons pas ici couvrant, allez sur le [forum d’Azure RemoteApp](http://aka.ms/araforum) et posez votre question. Nous pouvons toujours ajouter des réponses plus ici.

## <a name="what-is-azure-remoteapp"></a>Nouveautés d’Azure RemoteApp ? ##


- **Nouveautés d’Azure RemoteApp ?** RemoteApp est qu'un service Azure permet de fournir un accès à distance sécurisé aux applications à partir de nombreux périphériques de l’autre utilisateur. Apprenez-en plus sur [Azure RemoteApp](remoteapp-whatis.md).
- **Quelles sont les options de déploiement ?** Il existe deux types de collections de RemoteApp : cloud et hybride. Celui que vous avez besoin dépend d’un certain nombre de facteurs, tels que de la nécessité de la jonction de domaine. Nous parlons de toutes ces décisions [ici](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Astuces sur l’utilisation d’Azure RemoteApp ##
- **Combien de temps jusqu'à ce que je suis déconnecté ? Combien de temps puis-je être inactif avant de me donner l’initialisation ?** 4 heures. Si vous ou un de vos utilisateurs est inactif pendant 4 heures, vous allez être automatiquement déconnecté de RemoteApp d’Azure. Consultez les autres paramètres par défaut dans un [abonnement Azure et les limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md).
- **Puis-je essayer ce service gratuitement ?** Oui. Il existe une version d’évaluation gratuite pendant 30 jours. Après la fin d’évaluation, vous pouvez passer à un compte payant (que vous pouvez utiliser dans la production) ou d’arrêter l’utilisation du service. Démarrer votre version d’évaluation gratuite en accédant à [portal.azure.com](http://portal.azure.com) - permet de créer une nouvelle instance de RemoteApp. Avec la version d’évaluation gratuite, vous pouvez créer 2 instances de RemoteApp avec 10 utilisateurs par instance. N’oubliez pas que cette version d’évaluation n’existe que pendant 30 jours.
## <a name="azure-remoteapp-subscription-details"></a>Détails de l’abonnement Azure RemoteApp ##

- **Quelles sont les limites de service ?** Vous pouvez obtenir des informations sur les paramètres par défaut et les limites du service de RemoteApp Azure dans un [abonnement Azure et les limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md). Faites-nous savoir si vous avez d’autres questions.
- **Le nombre d’utilisateurs que je dois avoir ?** Il existe un minimum de 20 utilisateurs. Me permettre de répéter que pour être clair super - la valeur minimale est 20. Vous serez facturé pour 20. 
- **Combien coût de RemoteApp ?** Consultez les [Détails de tarification Azure RemoteApp ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Est un type de collection le coût supérieur à un autre ?** Oui, il peut, en fonction des besoins de votre collection. Une collection hybride nécessite une connexion à partir d’Azure RemoteApp à votre réseau local. Si vous utilisez un itinéraire VNET/Express, il n’existe aucun coût supplémentaire. Mais si vous utilisez un nouveau VNET d’Azure et une passerelle ou un itinéraire de Express, vous devrez payer pour la [passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ou de la [Gamme d’Express](https://azure.microsoft.com/pricing/details/expressroute/). Ce coût (détaillé dans les liens) est sur votre RemoteApp Azure mensuelle des coûts.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Collections - ce qui est pris en charge, qui doit utiliser, etc.
- **Les applications personnalisées de métier (LOB) sont pris en charge ?** Oui. Pour utiliser une application personnalisée dans Azure RemoteApp, créer une [image de modèle personnalisé](remoteapp-create-custom-image.md), puis chargez-le sur la collection de RemoteApp.
- **Mon application métier personnalisée ne fonctionnera dans Azure RemoteApp ?** La meilleure solution qu’out est à tester. Visitez le [Centre de compatibilité de bureau à distance](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quelle méthode de déploiement (cloud ou hybride) est plus adapté à mon entreprise ?** Les collections hybride fournissent l’expérience plus complète si vous souhaitez que l’intégration totale avec authentification unique (SSO) et de connectivité de réseau local sécurisé. Collections de nuage fournissent un moyen souple et facile à isoler votre déploiement à l’aide de plusieurs méthodes d’authentification. Apprenez-en plus sur les [options de déploiement](remoteapp-whatis.md).
- **Nous avons SQL ou un autre de la base de données sur site ou dans Azure. Type de déploiement devons-nous utiliser ?** Cela dépend à partir de votre base de données SQL ou back-end. Si la base de données est dans un réseau privé, utilisez la collection hybride. Si la base de données est exposé à Internet et permet des connexions à s’y connecter client, vous pouvez utiliser la collection de nuage.
- **Qu’en est-il de mappage de lecteur USB et port série, le partage du Presse-papiers et la redirection d’imprimante ?** Toutes ces fonctionnalités sont prises en charge dans Azure RemoteApp. La redirection du Presse-papiers de partage et d’imprimantes est activée par défaut. Plus d’informations sur la redirection [ici](remoteapp-redirection.md). 


## <a name="template-images"></a>Images de modèle
- **Puis-je utiliser un nuage ou un ordinateur virtuel existant comme modèle pour ma collection RemoteApp ?** Oui ! Vous pouvez créer une image basée sur une machine virtuelle d’Azure, utilisez une des images inclus dans votre abonnement ou créer une image personnalisée. Vérifiez les [options d’image RemoteApp](remoteapp-imageoptions.md).


## <a name="network-options"></a>Options de réseau
- **La collection hybride nécessite un VNET. Si nous pouvons utiliser notre VNET existant ?** Vous pouvez si vous le VNET existant est un VNET d’Azure. Reportez-vous à la section « Étape 1 : configurer votre réseau virtuel » dans les [instructions de collection hybride](remoteapp-create-hybrid-deployment.md) pour plus d’informations.
- **Puis-je utiliser un VNET avec une collection de nuage ?** Vous pouvez en effet. Consultez [créer une collection de nuage](remoteapp-create-cloud-deployment.md), notamment étape 1, pour plus d’informations.

## <a name="authentication-options"></a>Options d’authentification



- **Qu’en est-il de l’authentification ? Les méthodes qui sont pris en charge ?** La collection de nuage prend en charge les comptes Microsoft Azure comptes et Active Directory, qui sont également des comptes Office 365. La collection hybride prend en charge uniquement les comptes Azure Active Directory qui ont été synchronisées (à l’aide d’un outil tel que [Azure la synchronisation Active Directory](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) d’un déploiement Active Directory de Windows Server ; plus précisément, soit synchronisé avec l’option de synchronisation de mot de passe soit synchronisé avec la fédération Active Directory Federation Services (ADFS) configurée. Vous pouvez également configurer [Plusieurs facteurs d’authentification (AMF)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Les utilisateurs Azure Active Directory doivent être entre le locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l’onglet **paramètres** dans le portail. Voir [Modifier le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d’informations.)

- **Pourquoi ne puis-je pas pour donner mon accès de compte Azure Active Directory ?** Les utilisateurs d’Azure Active Directory doivent être dans le répertoire associé à votre abonnement. Vous pouvez afficher ou modifier ce répertoire dans l’onglet Paramètres dans le portail. Pour plus d’informations, consultez [modification le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) .

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Clients - quel périphérique peut utiliser pour accéder à Azure RemoteApp ?
Vous pouvez trouver des informations de bon client, y compris les étapes de l’installation des différents clients à [accéder à vos applications dans Azure RemoteApp](remoteapp-clients.md).

- **Les périphériques et les systèmes d’exploitation les applications clientes prennent en charge ?**
Premier, les ordinateurs et les tablettes : 
    - Windows 10 (aperçu du client)
    - Windows 8.1 et Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Tablettes Android
    - iPads et les téléphones portables :
    - iPhone
    - Téléphone Android
    - Windows Phone
 
    [Télécharger](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) maintenant un client de RemoteApp.
- **Azure RemoteApp prend en charge les Clients légers ?** Oui, les clients légers Windows Embedded suivants sont pris en charge :
    - Windows Embedded Standard 7
    - Windows Embedded Standard 8
    - Windows Embedded industrie 8.1 Pro
    - Windows 10 IoT Enterprise

- **Quelle version de Windows Server est pris en charge pour l’hôte de Session bureau (RDSH) l’à distance ?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Commentaires et support


- **Quel est le plan de la prise en charge de RemoteApp ?** Prise en charge de la gestion de la facturation et des abonnements est proposé gratuitement. Le support technique est disponible via les [programmes de services Azure](https://azure.microsoft.com/support/plans/). Vous pouvez également obtenir la prise en charge de la Communauté gratuite via notre [forum de discussion Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Comment envoyer des commentaires ?** Visitez le [forum de commentaires](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Qui puis-je contacter pour en savoir plus sur Azure RemoteApp ?** En plus de notre [forum de discussion](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), qui est l’endroit idéal pour poser des questions, vous pouvez joindre le hebdomadaire [poser au webinaire Experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), où nous parlons de tous les éléments de RemoteApp.
- **Qu’en est-il de documentation de RemoteApp ?** Nous sommes heureux que vous avez demandé. En plus du contenu de l’aide dans le tiroir du portail d’aide (cliquez simplement sur le **?** sur la page du portail), les articles suivants sont disponibles pour vous apprendre tout sur RemoteApp :
    - **Mise en route :**
        - [Nouveautés de RemoteApp ?](remoteapp-whatis.md)
        - [Nouveautés dans les images du modèle RemoteApp ?](remoteapp-images.md)
        - [Comment fonctionne la licence ?](remoteapp-licensing.md)
        - [Fonctionnement commun de RemoteApp et bureau](remoteapp-o365.md)
        - [Comment la redirection fonctionne dans RemoteApp](remoteapp-redirection.md)?
    - **Déploiement :**
        - [Créer une image de modèle personnalisé](remoteapp-create-custom-image.md)
        - [Créer une collection hybride](remoteapp-create-hybrid-deployment.md)
        - [Créer une collection de nuage](remoteapp-create-cloud-deployment.md)
        - [Configurer Azure Active Directory de RemoteApp](remoteapp-ad.md)
        - [Publier une application dans RemoteApp](remoteapp-publish.md)
    - **Gérer :**
        - [Ajouter des utilisateurs](remoteapp-user.md)
        - [Méthodes conseillées pour la configuration et à l’aide de RemoteApp](remoteapp-bestpractices.md)  

    Vidéos ! Nous avons également un certain nombre de vidéos sur RemoteApp. Certains proposent une introduction ([Introduction à Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) tandis que d’autres vous guideront de déploiement ([déploiement de Cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) et [Déploiement hybride](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Les extraire !

 
### <a name="help-us-help-you"></a>Aidez-nous à vous aider 
Saviez-vous qu’en plus de cet article d’évaluation et faire des commentaires vers le bas ci-dessous, vous pouvez modifier l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose de déroutant juste ? Faites défiler et cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux arriveront à nous pour révision, et puis, une fois que nous avons approuver les, vous verrez vos modifications et les améliorations ici.
