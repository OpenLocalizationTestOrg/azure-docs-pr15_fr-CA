
<properties
    pageTitle="L’utilisation d’Office avec RemoteApp Azure | Microsoft Azure" 
    description="Découvrez comment Office et Azure RemoteApp collaborer"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-office-with-azure-remoteapp"></a>L’utilisation d’Office avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Vous avez deux possibilités pour l’hébergement des applications Office dans Azure RemoteApp : Office 365 ProPlus ou version d’évaluation de Office 2013 Professionnel Plus.

**Bonjour, saviez-vous que nous avons un nouveau meilleur article qui remplacera dès ce ? Découvrez [comment utiliser votre abonnement à Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md). Il couvre toutes les informations que vous avez besoin pour l’utilisation d’Office 365 + RemoteApp Azure.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
Vous pouvez créer une collection de RemoteApp à l’aide de l’image du modèle Office 365 ProPlus. Cette option vous permet d’étendre votre service d’Office 365 à RemoteApp. Vous devez disposer d’un abonnement existant et vos utilisateurs doivent posséder une licence pour le service Office 365 ProPlus, individuellement ou par l’intermédiaire de l’Office 365 les plans de service.

RemoteApp prend en charge l’activation de l’ordinateur Office 365 partagés. Lorsque vous activez l’activation de l’ordinateur partagé et utilisez l' [outil de déploiement](http://www.microsoft.com/download/details.aspx?id=36778) pour l’installation, Office 365 ProPlus installe sans être activé. Lorsqu’un utilisateur signe dans une collection qui contient Office 365, Office vérifie si l’utilisateur a été mis en service pour Office 365 ProPlus. Si Oui, Office active temporairement Office 365 ProPlus - cette activation persiste jusqu'à ce que ce signe les utilisateurs hors du service.

Pour utiliser l’activation de l’ordinateur Office 365 partagé, vous devez créer un [modèle personnalisé](remoteapp-create-custom-image.md) et installer Office 365 ProPlus, à la suite de [ces instructions](https://technet.microsoft.com/library/dn782858.aspx).

Vous pouvez gérer les licences d’Office 365 de vos utilisateurs sur le [Portail d’administration d’Office 365](https://portal.office365.com/). En savoir plus sur les [plans de service d’Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Version d’évaluation de Office 2013 Professionnel Plus
Au cours d’une version d’évaluation de 30 jours de RemoteApp, vous pouvez utiliser l’image du modèle Professionnel Plus (version d’évaluation) Office 2013 pour créer une collection de RemoteApp. Vous pouvez affecter des utilisateurs à cette collection d’évaluation à l’aide de leurs comptes de travail Azure Active Directory ou Microsoft. Aucun abonnement supplémentaire n’est requise.

Il s’agit d’une option intéressante pour lancer les rouleaux et obtenir une bonne idée Office de RemoteApp. Toutefois, cette option est prévue pour l’évaluation et de test uniquement. Collections de RemoteApp créées à l’aide de l’image du modèle Professionnel Plus (version d’évaluation) Office 2013 ne peut pas être transférées vers le mode de production et il seront désactivées à la fin de la période d’évaluation.

## <a name="switching-from-trial-to-production"></a>Commutation à partir de la version d’évaluation de la production
Lorsque vous démarrez votre version d’évaluation gratuite de 30 jours, une note dans la section RemoteApp du portail vous indique combien de temps il vous reste dans la version d’évaluation que vous devez passer à un compte payant. Vous pouvez activer votre compte et passer au mode de production à l’aide de la liaison dans cette note.

Lorsque vous activez votre compte, cela affectera toutes les collections de RemoteApp dans votre compte.

- Les collections qui sont exécutent avec le Windows Server 2012 R2 ou les images de modèle Office 365 ProPlus passera à la production en toute transparence. Toutes les données et paramètres utilisateur, y compris les sessions en cours, restent intactes.
- Si vous avez téléchargé des images du modèle personnalisé, collections à l’aide de ces images seront également une transition transparente.
- L’image du modèle Professionnel Plus (version d’évaluation) Office 2013 est destiné d’évaluation uniquement. Collections en cours d’exécution avec cette image de modèle ne peut pas être transférées vers production. Ils figureront dans l’état de « désactivé ».


Si vous ne pas passer au mode de production à l’expiration de votre période d’essai, vos collections RemoteApp seront désactivées. Ne vous inquiétez pas, vos paramètres et les données des utilisateurs sont enregistrées pour un autre 90 jours, vous pouvez activer votre service et passer au mode de production sans perte de données.
