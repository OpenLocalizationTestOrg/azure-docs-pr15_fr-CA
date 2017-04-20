<properties
    pageTitle="Annonce Azure se connecter et fédération | Microsoft Azure"
    description="Cette page est l’emplacement central pour toute la documentation concernant les opérations AD FS à l’aide d’Azure Connect d’AD"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Azure Connect de publicité et de fédération

Azure Connect d’Active Directory vous permet de configurer la fédération avec les locaux AD FS et Azure AD. Avec la fédération de session, vous pouvez activer les utilisateurs à signer aux services Azure AD sur leurs mots de passe locaux et, alors que sur le réseau d’entreprise, sans avoir à entrer leur mot de passe à nouveau. La fédération avec AD FS vous permet de déployer une nouvelle ou spécifier une Federation Services existant dans la batterie de serveurs Windows Server 2012 R2.

Cette rubrique est la page d’accueil pour plus d’informations sur la fédération liés des fonctionnalités pour Azure Connect et listes contient un lien vers toutes les autres rubriques qui lui sont associés. Les liens vers Azure Connect de publicité, consultez intégrant vos identités sur site Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure se connecter AD - sujets de la fédération

| Rubrique | Ce qu’il couvre et le moment de lire |
|:------|:-----------|
| **Azure AD connexion utilisateur options de connexion** ||
| [Présentation des options d’ouverture de session utilisateur](active-directory-aadconnect-user-signin.md) | Description des options de connexion utilisateur différents et comment elles affectent l’expérience utilisateur signe dans Azure |
| **Installation de FS d’Active Directory à l’aide d’Azure Connect de publicité**||
| [Conditions préalables](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Conditions préalables pour une installation réussie de AD FS via les connexion AD Azure|
| [Configurer la batterie de serveurs ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Comment faire pour installer une nouvelle batterie AD FS à l’aide d’Azure Connect de publicité |
| **Modification de la configuration d’ADFS** | |
| [Réparation de l’approbation](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Comment réparer une approbation en cours entre sur site AD FS et Office 365 / Azure |
| [Ajout d’un nouveau serveur ADFS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Extension de batterie AD FS avec AD FS server post initiale installation supplémentaires |
| [Ajout d’un nouveau serveur AD FS WAP](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Extension de batterie AD FS avec WAP server post initiale installation supplémentaires |
| [Ajouter un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Ajouter un autre domaine pour être fédéré avec Azure AD |
|**Valider les tâches d’installation**||
| [Ajouter le logo de la société personnalisé / illustration](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modifier l’expérience de connexion en spécifiant le logo personnalisé à afficher sur la page de connexion AD FS |
| [Ajouter la description de l’authentification](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Modification de la description de l’authentification sur la page de connexion AD FS | 
| [Règles de revendication de modification AD FS](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modifier / ajouter des règles de revendications dans ADFS correspondant à la configuration de la synchronisation Azure Connect d’AD |


## <a name="additional-resources"></a>Ressources supplémentaires

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
* [Déploiement AD FS Azure](active-directory-aadconnect-azure-adfs.md)
* [Déploiement de FS haute disponibilité AD cross-géographique dans Azure avec Azure Traffic Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


