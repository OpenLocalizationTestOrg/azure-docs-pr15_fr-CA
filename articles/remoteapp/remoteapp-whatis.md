<properties 
    pageTitle="Nouveautés d’Azure RemoteApp ? | Microsoft Azure" 
    description="Découvrez comment partager des applications et des ressources pour n’importe quel périphérique par le biais de RemoteApp d’Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>Nouveautés d’Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure offre les fonctionnalités du programme Microsoft RemoteApp locaux, soutenue par les Services Bureau à distance, sur Azure. Azure RemoteApp vous permet de fournir un accès à distance sécurisé aux applications à partir de nombreux périphériques de l’autre utilisateur. RemoteApp Azure héberge essentiellement des sessions Terminal Server non persistant dans le nuage, et vous devez les utiliser et les partager avec vos utilisateurs.

Avec Azure RemoteApp vous pouvez partager des applications et des ressources avec des utilisateurs sur pratiquement n’importe quel périphérique. Nous héberger vos applications dans le nuage, c'est-à-dire que nous occuper du matériel et mise à l’échelle pour répondre aux besoins de l’utilisateur. Il vous suffit est télécharger les applications que vous souhaitez partager, puis obtenir vos utilisateurs d’utiliser ces applications. [Les utilisateurs obtiennent de conserver leurs propres périphériques](remoteapp-clients.md), lors de la gestion tout via le portail Azure. Vous avez même la possibilité d’utiliser vos informations d’identification d’entreprise, ce qui vous permet de garantir la sécurité des applications et des données.

Continuez la lecture pour plus d’informations sur RemoteApp d’Azure, ou, si nous avons déjà convaincus vous, [Essayez-le maintenant](https://azure.microsoft.com/services/remoteapp/).

Pour toute question sur Azure RemoteApp ? Consultez notre [Forum aux questions](remoteapp-faq.md).

RemoteApp Azure fait partie de l' [Infrastructure de bureau virtuel de Microsoft](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nouveau !** Vous souhaitez en savoir plus sur Azure RemoteApp ? Ou prêt à valider Azure RemoteApp à grande échelle ? Participez à notre hebdomadaire [Demandez aux experts conférence Web](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Collections de RemoteApp Azure
Il existe deux types de [collections de RemoteApp d’Azure](remoteapp-collections.md):


- Une **collection de nuage** est hébergé dans et stocke les données pour les programmes dans le nuage. Les utilisateurs peuvent accéder des applications en vous connectant avec leur compte Microsoft ou les informations d’identification d’entreprise synchronisé ou fédéré avec Azure Active Directory.

    Choisir une collection de nuage lorsque l’application que vous souhaitez partager ne nécessite pas une connexion à n’importe quelle ressource réseau privé de votre société (par exemple, par l’intermédiaire d’un périphérique VPN). Si l’application utilise des ressources sur Internet, OneDrive ou Azure, une collection de nuage fonctionne pour vous. Il est également le plus rapide à créer.

- Une **collection de hybride** est hébergé dans et stocke les données dans le nuage Azure mais également des données d’access permet aux utilisateurs et des ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder des applications en vous connectant avec leurs informations d’identification d’entreprise synchronisé ou fédéré avec Azure Active Directory.

    Choisir une collection hybride si vous avez besoin d’une connexion à des ressources sur le réseau privé de votre société. Par exemple, si l’application a besoin d’accéder à l’une des opérations suivantes :

    - Serveurs de fichiers situés sur votre intranet
    - Quicken
    - Bases de données derrière un pare-feu

    Il est généralement plus utile pour les grandes entreprises avec beaucoup de ressources sur leurs réseaux privés ne peuvent pas être déplacées vers le nuage.

Différentes collections ont plusieurs options, y compris les réseaux, pour le calcul de la [collection qui](remoteapp-collections.md) vous convient le mieux. 


### <a name="updating-your-collection"></a>Mise à jour de votre collection.
Une des principales différences entre les collections hybride et nuage est la gestion des mises à jour logicielles. Avec une collection de nuage qui utilise l’image d’Office 365 ProPlus ou Office 2013 préinstallé, vous n’avez pas à vous soucier des mises à jour. Le service gère lui-même et annule les mises à jour en continu, à la fois les applications et le système d’exploitation.

Pour les collections hybride, ainsi que les collections de nuage qui utilisent une image de modèle personnalisé, vous êtes responsable de la maintenance de l’image et les applications. Pour les images à un domaine, vous pouvez contrôler les mises à jour à l’aide d’outils tels que Windows Update, la stratégie de groupe ou System Center.

Une fois que vous mettez à jour l’image de votre modèle personnalisé, vous téléchargez la nouvelle image vers le nuage Azure et mettre à jour la collection pour utiliser la nouvelle image. (Vous pouvez faire cela à partir de la page Azure RemoteApp **Quick Start** ou le tableau de bord.)

Pour plus d’informations, consultez [mise à jour de votre collection](remoteapp-update.md) .

## <a name="supported-remoteapp-clients"></a>Clients pris en charge de RemoteApp
RemoteApp Azure prend en charge les applications client de RemoteApp pour Windows et Windows RT, ainsi que les applications de bureau à distance de Microsoft pour Mac, iOS et Android. Les utilisateurs peuvent utiliser ces applications sur leur mobile ou le calcul des périphériques pour accéder aux nouveaux programmes RemoteApp d’Azure.

Pour plus d’informations sur les clients, consultez [l’accès à vos applications dans Azure RemoteApp](remoteapp-clients.md) .

## <a name="next-steps"></a>Étapes suivantes
Go ! Essayez-le ! Ces articles vous aident avec Azure RemoteApp :

- [Quel type de collection avez-vous besoin pour Azure RemoteApp ?](remoteapp-collections.md)
- [Créer une image d’Azure RemoteApp](remoteapp-imageoptions.md)
- [Comment créer une collection de nuage d’Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Comment créer une collection hybride d’Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Comment le Gestionnaire de licences fonctionne dans Azure RemoteApp ?](remoteapp-licensing.md)
- [Méthodes conseillées pour utiliser RemoteApp d’Azure](remoteapp-bestpractices.md)
- [Forum aux questions sur Azure RemoteApp](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Aidez-nous à vous aider 
Saviez-vous qu’en plus de cet article d’évaluation et faire des commentaires vers le bas ci-dessous, vous pouvez modifier l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose de déroutant juste ? Défiler vers le haut, puis cliquez sur **Modifier sur GitHub** ou sur **Modifier** pour apporter des modifications - ceux arriveront à nous pour révision, et puis, une fois que nous avons approuver les, vous verrez vos modifications et les améliorations ici.