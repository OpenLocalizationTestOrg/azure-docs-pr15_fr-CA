<properties
    pageTitle="Serveur de vs Azure AMF nuage | Microsoft Azure"
    description="Choisissez la solution de sécurité d’authentification à plusieurs facteurs droite pour vous en demandant quelle am i tente de sécuriser et où sont situés les utilisateurs.  Puis choisissez cloud, le serveur de l’AMF ou AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Choisissez la solution d’authentification à plusieurs facteurs Azure pour vous

Car il existe plusieurs types d’Azure plusieurs facteurs d’authentification (AMF), nous devons répondre à quelques questions pour déterminer quelle version est l’outil à utiliser.  Ces questions sont les suivantes :

-   [Que suis-je en train de le sécuriser](#what-am-i-trying-to-secure)
-   [Où se trouvent les utilisateurs](#where-are-the-users-located)
- [Quelles sont les fonctions ont-ils besoin ?](#what-featured-do-i-need)

Les sections suivantes fournissent des conseils sur la détermination de chacun de ces réponses.

## <a name="what-am-i-trying-to-secure"></a>Que suis-je en train de le sécuriser ?

Pour déterminer la solution de vérification en deux étapes correct, tout d’abord nous devons répondre à la question de quels sont vous essayez secure avec une deuxième méthode d’authentification.  S’agit-il d’une application dans Azure ?  Ou un système d’accès distant ?  En déterminant ce que nous essayons de sécuriser, nous pouvons répondre à la question d’où plusieurs facteurs d’authentification doit être activée.  


Ce que vous essayez de sécuriser| Plusieurs facteurs d’authentification dans le nuage|Serveur d’authentification à plusieurs facteurs
------------- | :-------------: | :-------------: |
Applications internes Microsoft|● |● |
Applications SaaS dans la galerie d’application|● |● |
Applications IIS publiées via un Proxy d’application Azure AD|● |● |
Applications IIS ne pas publiées par l’intermédiaire de Proxy d’application Azure AD | |● |
Accès distant VPN, distance| |● |



## <a name="where-are-the-users-located"></a>Où se trouvent les utilisateurs

Ensuite, examinez où se trouvent les utilisateurs permet de déterminer la solution correcte à utiliser, que ce soit dans le nuage ou sur site à l’aide du serveur de l’AMF.



Emplacement de l’utilisateur| Plusieurs facteurs d’authentification dans le nuage|Serveur d’authentification à plusieurs facteurs
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Annonce Azure et annonce à l’aide de la fédération avec AD FS sur site|● |● |
Annonce Azure et annonce à l’aide de la synchronisation d’annuaire, Azure AD Sync, Azure Connect AD - aucune synchronisation de mot de passe local|● |● |
Annonce Azure et annonce à l’aide de la synchronisation d’annuaire, Azure AD Sync, Azure Connect de AD - avec la synchronisation de mot de passe local|● | |
Sur site Active Directory| |● |

## <a name="what-features-do-i-need"></a>Quelles sont les fonctions ont-ils besoin ?

Le tableau suivant présente une comparaison des fonctionnalités qui sont disponibles avec une authentification multifactorielle dans le nuage et le serveur d’authentification selon plusieurs facteurs.

 | Plusieurs facteurs d’authentification dans le nuage | Serveur d’authentification à plusieurs facteurs
------------- | :-------------: | :-------------: |
Notification d’application mobile en tant que second facteur | ● | ● |
Code de vérification d’application mobile en tant que second facteur | ● | ●
Appel téléphonique comme second facteur | ● | ●
SMS à sens unique en tant que second facteur | ● | ●
SMS bidirectionnel comme second facteur |  | ●
Jetons matériels comme second facteur |  | ●
Mots de passe App pour les clients qui ne prennent pas en charge les AMF | ● |  
Contrôle d’administration sur les méthodes d’authentification | ● | ●
Mode de broche |  | ●
Alerte à la fraude | ● | ●
Rapports de l’AMF | ● | ●
Contournement unique |  | ●
Voeux personnalisés pour les appels téléphoniques | ● | ●
ID de l’appelant personnalisables pour les appels téléphoniques | ● | ●
Adresses IP autorisées | ● | ●
N’oubliez pas d’AMF pour périphériques approuvés  | ● |  
Accès conditionnel | ● | ●
Mémoire cache |  | ●

Maintenant que nous avons déterminé s’il faut utiliser le nuage plusieurs facteurs d’authentification ou de l’AMF serveur sur site, nous pouvons commencer paramétrage et l’utilisation de l’authentification multifactorielle Azure. **Sélectionnez l’icône qui représente votre scénario !**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
