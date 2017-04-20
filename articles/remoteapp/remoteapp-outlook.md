<properties
    pageTitle="À l’aide d’Outlook dans Azure RemoteApp | Microsoft Azure" 
    description="Apprenez à configurer et à utiliser Outlook dans Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>À l’aide de Microsoft Outlook dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure prend en charge Microsoft Outlook O365. En savoir plus sur la façon [d’Office fonctionne dans Azure RemoteApp](remoteapp-officesubscription.md). Il existe quelques paramètres recommandés pour Outlook lorsqu’il est utilisé dans Azure RemoteApp.

## <a name="cached-mode"></a>Mode mis en cache
Le mode mis en cache est une configuration recommandée lors de l’utilisation d’Outlook dans Azure RemoteApp. Lorsque vous configurez un compte Outlook 2013 pour utiliser le Mode Exchange mis en cache, Outlook 2013 fonctionne à partir d’une copie locale de la boîte aux lettres de Microsoft Exchange de l’utilisateur qui est stocké dans un fichier de données hors connexion (fichier OST) sur l’ordinateur de l’utilisateur, ainsi que le carnet d’adresses en mode hors connexion (OAB). La boîte aux lettres de mise en cache et l’OAB sont régulièrement mis à jour à partir du service O365. Apprenez-en plus sur [les différences entre le mode mis en cache et en ligne](https://technet.microsoft.com/library/jj683103.aspx).

L’utilisateur peut sélectionner **Le Mode Exchange mis en cache** ou **En Mode en ligne** au cours de la configuration du compte, ou en modifiant les paramètres du compte. Vous pouvez également déployer un mode ou à l’autre à l’aide de l’outil de personnalisation Office (OCT) ou la stratégie de groupe.  

Lire les [instructions étape par étape sur l’activation du mode mis en cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Recherche
Dans Azure RemoteApp, à l’aide de la recherche dans Outlook comporte des limitations. RemoteApp Azure utilise un pool de machines virtuelles pour prendre en charge les sessions utilisateur. L’indexation de recherche dépend de l’ID d’ordinateur, ce qui est différent pour différents ordinateurs virtuels. Il est possible que chaque fois qu’un utilisateur se connecte à Azure RemoteApp, ils sont dirigés vers une nouvelle machine virtuelle. Qui signifie que, si nous activer recherche locale, l’indexeur s’exécute chaque fois que le code d’appareil change (lorsque l’utilisateur est sur un ordinateur virtuel différent). Selon la taille de la. Fichier OST, l’indexeur peut prendre beaucoup de temps à effectuer et à utiliser des ressources nécessaires pour les autres applications. Recherche seulement serait pas lente mais peut ne pas produire les résultats. À l’aide d’un profil de compte en Mode en ligne peut contourner ce problème, mais les performances risquent d’être affectées en raison du manque d’un cache local (voir le lien ci-dessus pour plus d’informations sur la différence entre le mode mis en cache et en ligne). Malheureusement, recherche d’indexation/local ne peut pas être désactivé et la recherche en ligne ne peut pas être activé par défaut dans Outlook 2013.
