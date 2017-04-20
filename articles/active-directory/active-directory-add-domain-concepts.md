<properties
    pageTitle="Vue d’ensemble conceptuelle des noms de domaine personnalisé dans Azure Active Directory | Microsoft Azure"
    description="Explique le cadre conceptuel pour l’utilisation de noms de domaine personnalisé dans Azure Active directory, y compris la fédération pour l’ouverture de session unique"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Vue d’ensemble conceptuelle des noms de domaine personnalisé dans Azure Active Directory

Un nom de domaine est une partie importante de l’identificateur de nombreuses ressources de répertoire : il fait partie d’un utilisateur nom ou adresse e-mail d’un utilisateur, une partie de l’adresse d’un groupe et peut faire partie de l’identificateur URI d’application pour une application. Une ressource dans Azure Active Directory (AD Azure) peut inclure un nom de domaine qui est déjà vérifié appartenant au répertoire qui contient la ressource. Seul un administrateur global peut effectuer les tâches de gestion de domaine dans Active Directory Azure.

Les noms de domaine dans Active Directory Azure sont globalement uniques. Un nom de domaine peut être utilisé par une publicité Azure. Si un répertoire AD Azure a vérifié un nom de domaine, aucun autre annuaire AD Azure peut vérifier ou utiliser ce même nom de domaine.

## <a name="initial-and-custom-domain-names"></a>Noms de domaine initial et personnalisé

Chaque nom de domaine dans Active Directory Azure est un nom de domaine d’origine, ou un nom de domaine personnalisé.

Chaque annonce Azure est fourni avec un nom de domaine initial dans le formulaire contoso.onmicrosoft.com. Ce nom de domaine de niveau troisième, dans cet exemple, « contoso.onmicrosoft.com », a été établi lorsque le répertoire a été créé, en général par l’administrateur qui a créé le répertoire. Le nom de domaine d’origine d’un répertoire ne peut pas être modifié ni supprimé. Le nom de domaine d’origine, bien qu’entièrement fonctionnelles, est conçu principalement pour être utilisé comme un mécanisme d’amorçage jusqu'à ce qu’un nom de domaine personnalisé est vérifié.

Dans la plupart des environnements de production, un répertoire a au moins un domaine personnalisé vérifié, par exemple, « contoso.com », et il est ce domaine personnalisé qui est visible pour les utilisateurs finaux. Un nom de domaine personnalisé est un nom de domaine appartenant à et utilisé par cette organisation, par exemple, « contoso.com », pour des utilisations telles que l’hébergement de son site web. Ce nom de domaine est familière aux employés car elle fait partie du nom d’utilisateur qu’ils utilisent pour se connecter au réseau de l’entreprise, ou pour envoyer et récupérer du courrier électronique.

Avant de pouvoir utiliser par AD Azure, le nom de domaine personnalisé doit être ajouté à votre répertoire et vérifié.

## <a name="verified-and-unverified-domain-names"></a>Noms de domaine vérifié et non vérifiées

Le nom de domaine d’origine d’un répertoire est implicitement évalué comme vérifié par AD Azure. Lorsqu’un administrateur ajoute un nom de domaine personnalisé à une annonce d’Azure, il est initialement dans un état non vérifié. AD Azure n’autorise pas les ressources du répertoire à utiliser un nom de domaine non vérifié. Cela garantit qu’un seul répertoire peut utiliser un nom de domaine, et que l’organisation utilise le nom de domaine est en réalité propriétaire de ce nom de domaine.

Annonce Azure vérifie la propriété de nom de domaine en recherchant une entrée particulière dans le fichier de zone de domaine nom service (DNS) pour le nom de domaine. Pour vérifier la propriété d’un nom de domaine, un administrateur Obtient l’entrée DNS dans Active Directory Azure que AD Azure recherchera et ajoute cette entrée dans le fichier de zone DNS pour le nom de domaine. Le fichier de zone DNS est géré par le Registre des noms de domaine pour ce domaine. Les étapes pour vérifier un domaine sont affichées dans l’article pour [Ajouter un domaine personnalisé pour votre annuaire AD Azure](active-directory-add-domain.md).

Ajout d’une entrée DNS pour le fichier de zone pour le nom de domaine n’affecte pas les autres services de domaine tels qu’email ou l’hébergement web.

## <a name="federated-and-managed-domain-names"></a>Noms de domaines fédérés et gérés

Un nom de domaine personnalisé dans Azure annonce peut être configuré pour permettre aux utilisateurs d’un signe fédéré dans des expériences entre votre sur site Active Directory et l’annonce Azure. Configuration d’un domaine pour la fédération requiert des mises à jour des ressources privilégiées dans Azure AD et également à Windows Server Active Directory. Configuration de qu'un domaine fédéré doit être complété sur Azure Connect de publicité ou à l’aide de PowerShell. Fédération d’un domaine personnalisé ne peut pas être lancée à partir du portail classique Azure. [Regardez cette vidéo pour en savoir plus sur la configuration d’ADFS pour la connexion d’un utilisateur avec Azure Connect d’Active Directory](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domaines qui ne sont pas fédérées sont parfois appelés des domaines gérés. Le domaine initial pour un annuaire AD Azure est implicitement considéré comme un domaine géré.

## <a name="primary-domain-names"></a>Noms de domaine principal

Le nom de domaine principal pour un répertoire est le nom de domaine qui est déjà sélectionné comme la valeur par défaut pour la partie 'domaine' du nom d’utilisateur, lorsqu’un administrateur crée un nouvel utilisateur dans [Azure portal classique](https://manage.windowsazure.com/) ou un autre portail tels que le portail d’administration d’Office 365. Un répertoire peut avoir qu’un seul nom de domaine principal. Un administrateur peut modifier le nom de domaine principal à un domaine personnalisé qui n’est pas fédéré, de vérifier ou du domaine initial.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Les noms de domaine dans Active Directory Azure et autres Microsoft Online Services

Un nom de domaine doit être vérifié dans Azure annonce avant de pouvoir être utilisé par un autre Service en ligne Microsoft Exchange Online, SharePoint Online et Intune. Ces autres services ont généralement besoin d’un administrateur ajouter une ou plusieurs entrées DNS qui sont spécifiques au service.

Une application web Azure utilise son propre mécanisme pour vérifier la propriété d’un domaine. Un domaine doit être vérifié pour une utilisation avec Active Directory Azure même si elle a été précédemment vérifié pour une utilisation par une application web Azure dans un abonnement qui s’appuie sur cette publicité Azure. Une application Azure web peut utiliser un nom de domaine qui a été vérifié dans un répertoire différent du répertoire qui sécurise l’application web.

## <a name="managing-domain-names"></a>Gestion des noms de domaine

Les tâches de gestion de domaine peuvent être effectuées à partir du portail classique Azure et à partir de PowerShell. De nombreuses tâches peuvent être effectuées à l’aide de l’API Azure AD graphique (dans la version d’évaluation).

-   [Ajout et vérification d’un nom de domaine personnalisé](active-directory-add-domain.md)

-   [Gestion des domaines dans Azure portal classique](active-directory-add-manage-domain-names.md)

-   [Utilisation de PowerShell pour gérer des noms de domaine dans Active Directory Azure](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [À l’aide de l’API Azure AD graphique pour gérer les noms de domaine dans Active Directory Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
