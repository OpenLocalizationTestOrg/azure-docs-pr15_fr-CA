
<properties
    pageTitle="Référence technique des accès conditionnel au répertoire actif Azure | Microsoft Azure"
    description="Avec contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Lorsque ces conditions sont réunies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Référence technique des accès conditionnel au répertoire actif Azure

## <a name="services-enabled-with-conditional-access"></a>Services activés avec accès conditionnel
Les règles d’accès conditionnels sont pris en charge sur les différents types d’applications Azure AD. Cette liste inclut :

- Applications fédérées à partir de la galerie d’applications Azure AD
- Applications de l’authentification unique mot de passe à partir de la galerie d’applications Azure AD
- Applications qui utilisent le Proxy d’Application Azure
- Ligne de développé des applications professionnelles et mutualisées enregistrées avec Azure AD
- En ligne de Visual Studio
- Application à distance Azure
-   Dynamics CRM
- Microsoft Office 365 Yammer
- En ligne Exchange de Microsoft Office 365
- Microsoft Office 365 SharePoint Online (inclut OneDrive pour l’entreprise)


## <a name="enable-access-rules"></a>Activer les règles d’accès

Chaque règle peut être activée ou désactivée sur un par les bases de l’application. Lorsque les règles sont **sur** elles seront activés et appliquées pour les utilisateurs qui accèdent à l’application. Lorsqu’ils sont **hors** qu’ils n’utilisent pas et n’aura aucun impact sur le processus de connexion aux utilisateurs.

## <a name="applying-rules-to-specific-users"></a>Application de règles à des utilisateurs spécifiques
Les règles peuvent être appliquées à des groupes spécifiques d’utilisateurs basés sur le groupe de sécurité en attribuant à **Appliquer**. **Appliquer à** la peut être définie à **Tous les utilisateurs** ou **groupes**. Lorsque la valeur à **Tous les utilisateurs** les règles seront appliquent à tous les utilisateurs d’accéder à l’application. L’option de **groupes** permet de sécurité spécifiques et des groupes de distribution à sélectionner, les règles seront appliquées uniquement pour ces groupes.

Lors du déploiement d’une règle, il est courant d’appliquer tout d’abord un jeu limité d’utilisateurs, qui sont membres d’un groupe de pilotage. Une fois terminé, la règle peut être appliquée à **Tous les utilisateurs**. Ceci entraînera la règle à appliquer pour tous les utilisateurs de l’organisation.

Sélectionner des groupes peuvent également être exemptées de stratégie à l’aide de l’option **à l’exception** . Tous les membres de ces groupes seront exonérées même si elles apparaissent dans un groupe inclus.

## <a name="at-work-networks"></a>« Au bureau » des réseaux


Les règles d’accès conditionnel qui utilisent un réseau « au bureau », s’appuient sur les plages d’adresses IP approuvées qui ont été configurés dans Azure AD, ou l’utilisation de la demande « à l’intérieur du réseau d’entreprise » à partir d’AD FS. Ces règles sont les suivantes :

- Exiger une authentification multifactorielle lorsque pas au travail
- Bloquer l’accès lorsque pas au travail

Options pour spécifier les réseaux « au bureau »

1. Dans la [page de configuration de l’authentification multicritères](../multi-factor-authentication/multi-factor-authentication-whats-next.md), configurer les plages d’adresses IP de confiance. Stratégie d’accès conditionnelle utilise les plages configurées sur chaque demande d’authentification et d’une émission de jeton d’évaluer les règles. 
2. Configurer l’utilisation de l’intérieur de revendication corpnet, cette option peut être utilisée avec les répertoires fédérés, à l’aide d’ADFS. [En savoir plus sur l’intérieur des déclarations de coronet](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configurer les plages d’adresses IP publiques. Dans l’onglet Configurer, pour votre annuaire, vous pouvez définir des adresses IP publiques. Accès conditionnel utilisera ces comme 'at work' adresses IP, ceci permet à des plages supplémentaires à configurer, au-dessus de la limite d’adresse IP 50 qui est appliquée par la page de paramètre AMF.



## <a name="rules-based-on-application-sensitivity"></a>En fonction de la sensibilité de l’application des règles

Les règles sont configurées par application autorisant les services à forte valeur sécurisé sans perturber l’accès aux autres services. Les règles d’accès conditionnel peuvent être configurés dans l’onglet **configuration** de l’application. 

Règles actuellement proposées :

- **Exiger une authentification multifactorielle**
 - Cette stratégie est appliquée à tous les utilisateurs devront s’authentifier via une authentification multifacteur au moins une fois.
 
- **Exiger une authentification multifactorielle lorsque pas au travail**
 - Si cette stratégie est appliquée, tous les utilisateurs devront effectuées au moins une fois l’authentification multifactorielle si elles accèdent le service à partir d’un emplacement distant non liés au travail. S’ils se déplacent d’un travail à un emplacement distant, ils devront effectuer l’authentification multifacteur lors de l’accès au service.
 
- **Bloquer l’accès lorsque pas au travail** 
 - Lorsque les utilisateurs déplacent de travail vers un emplacement distant, ils seront bloquées si la stratégie « Bloquer l’accès lorsque pas au travail » leur est appliquée.  Ils ré-accès seront autorisés depuis un lieu de travail.


## <a name="related-topics"></a>Rubriques connexes

- [Sécurisation de l’accès à Office 365 et autres applications connecté à Azure Active Directory](active-directory-conditional-access.md)
- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
