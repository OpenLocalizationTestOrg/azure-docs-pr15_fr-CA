<properties
    pageTitle="AD Azure se connecter en Allemagne de nuage Microsoft"
    description="Azure Connect de publicité s’intégrera les répertoires locaux avec Azure Active Directory. Cela vous permet de fournir une identité commune pour les applications SaaS, Azure et Office 365 intégrées à AD Azure."
    keywords="Introduction à Azure Connect d’Active Directory, vue d’ensemble de Azure Connect d’Active Directory, nouveautés Azure Connect d’Active Directory, installation d’active directory, Allemagne, forêt-noire"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Annonce Azure se connecter dans le nuage de Microsoft, Allemagne - version d’évaluation

## <a name="introduction"></a>Introduction
Azure AD connexion fournit la synchronisation entre votre sur site Active Directory et l’Azure Active Directory.
Actuellement, la plupart des scénarios de [Cloud Microsoft Allemagne](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) doivent être effectuées par l’opérateur. Lorsque vous utilisez Microsoft Cloud Allemagne, vous devez tenir compte des éléments suivants :


- Les URL suivantes doivent être ouvert sur un serveur proxy pour la synchronisation se déroule correctement :
    - *. microsoftonline.de
    - *. windows.net
    - + Listes de révocation de certificats

- Pour vous connecter à votre répertoire AD Azure, vous devez utiliser un compte dans le domaine onmicrosoft.de.
- Les fonctionnalités suivantes ne sont pas disponibles :
    - Annonce Azure se connecter à la santé
    - Mises à jour automatiques
    - Écriture différée de mot de passe

## <a name="download"></a>Télécharger
Vous pouvez télécharger Azure Connect d’annonce du serveur lame Azure Connect d’Active Directory au sein du portail.  Utilisez les instructions ci-dessous pour localiser la lame Azure Connect d’Active Directory.

### <a name="the-azure-ad-connect-blade"></a>La publicité Azure se connecter lame

Une fois que vous êtes connecté au portail Azure, effectuez les opérations suivantes :

1. Accédez à parcourir
2.  Sélectionnez Azure Active Directory
3.  Puis sélectionnez Azure Connect de publicité

Vous devez voir les éléments suivants :

![Annonce Azure se connecter lame](media\active-directory-aadconnect-germany\germany1.png)

 
Le tableau suivant décrit les fonctions illustrées dans la lame.


Titre|Description|
----- | ----- |
ÉTAT DE LA SYNCHRONISATION|Nous allons vous savez si la synchronisation est activée ou désactivée.|
DERNIÈRE SYNCHRONISATION|La dernière fois réussi une synchronisation terminée.|
DOMAINES FÉDÉRÉS|Affiche le nombre de domaines fédérés actuellement configuré.|


## <a name="installation"></a>Installation
Pour installer Azure Connect d’Active Directory, vous pouvez utiliser la documentation [ici](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Fonctionnalités avancées et des informations supplémentaires
Pour plus d’informations et de conseils sur les paramètres personnalisés ou des configurations avancées, démarrez avec [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).  Cette page fournit des informations et des liens vers des conseils supplémentaires.
