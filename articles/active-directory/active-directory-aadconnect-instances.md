<properties
    pageTitle="Azure AD Connect : Synchronisation des instances de service | Microsoft Azure"
    description="Cette page décrit des considérations spéciales pour les instances de AD Azure."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect : Des remarques spéciales concernant les instances
Azure AD Connect est couramment utilisé avec l’instance à l’échelle mondiale de publicité Azure et Office 365. Mais il existe également des autres instances et ceux-ci ont des exigences différentes pour les URL et les autres considérations spéciales.

## <a name="microsoft-cloud-germany"></a>Allemagne de nuage Microsoft
L' [Allemagne de nuage de Microsoft](http://www.microsoft.de/cloud-deutschland) est un nuage souverain exploité par un tiers de confiance de données allemande.

Ce nuage est actuellement en mode Aperçu. La plupart des scénarios que normalement faire vous-même, telles que vérifier les domaines, doit être effectuée par l’opérateur. Veuillez contacter votre représentant Microsoft local pour plus d’informations sur la manière de participer à l’aperçu.

URL à ouvrir dans le serveur proxy |
--- |
\*. microsoftonline.de |
\*. windows.net |
+ Des listes de révocation des certificats |

Pour vous connecter à votre répertoire AD Azure, vous devez utiliser un compte dans le domaine onmicrosoft.de.

Fonctionnalités qui ne sont pas présentes dans l’Allemagne de Cloud Microsoft :

- Santé de connexion AD Azure n’est pas disponible.
- Mises à jour automatiques n’est pas disponible.
- L’écriture différée de mot de passe n’est pas disponible.

## <a name="microsoft-azure-government-cloud"></a>Nuage de Microsoft Azure gouvernement
Les [pouvoirs publics de Microsoft Azure cloud](https://azure.microsoft.com/features/gov/) est un nuage pour le gouvernement des États-Unis.

Ce nuage a été pris en charge par les versions antérieures de synchronisation d’annuaire. À partir de la version 1.1.180 d’Azure AD connexion la prochaine génération du nuage est prise en charge. Cette génération à l’aide de points de terminaison en fonction uniquement aux États-Unis et avez une autre liste d’URL à ouvrir dans votre serveur proxy.

URL à ouvrir dans le serveur proxy |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Des listes de révocation des certificats |

Azure Connect d’annonce ne sera pas en mesure de détecter automatiquement que votre annuaire AD Azure se trouve dans le nuage de gouvernement. Au lieu de cela, vous devez effectuer les actions suivantes lorsque vous installez Azure Connect d’Active Directory.

1. Démarrez l’installation Azure Connect d’Active Directory.
2. Dès que vous voyez la première page où vous êtes supposé accepter le CLUF, ne continuez pas, mais laissez l’Assistant d’installation en cours d’exécution.
3. Démarrez regedit et modifier la clé de Registre `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` la valeur `2`.
4. Accédez à l’Assistant d’installation Azure Connect de publicité, accepter l’accord de licence et continuer. Lors de l’installation, veillez à utiliser le chemin d’installation de **configuration personnalisée** (et pas les installation Express). Poursuivez ensuite l’installation comme d’habitude.

Fonctionnalités qui ne sont pas présentes dans le nuage Microsoft Azure gouvernement :

- Santé de connexion AD Azure n’est pas disponible.
- Mises à jour automatiques n’est pas disponible.
- L’écriture différée de mot de passe n’est pas disponible.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
