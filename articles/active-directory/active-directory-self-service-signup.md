<properties
    pageTitle="Nouveautés d’inscription libre-service pour Azure ? | Microsoft Azure"
    description="Une inscription libre-service de présentation pour Azure, comment gérer le processus d’inscription et de prendre en charge un nom de domaine DNS."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>Nouveautés d’inscription libre-service pour Azure ?

Cette rubrique explique la procédure d’inscription de libre-service et prendre en charge un nom de domaine DNS.  

## <a name="why-use-self-service-signup"></a>Pourquoi utiliser libre-service l’inscription ?

- Obtenir des clients aux services qu’ils veulent plus rapidement.
- Créer par messagerie électronique des offres pour un service.
- Créer un flux d’abonnement à un par messagerie électronique qui permettent aux utilisateurs de créer des identités à l’aide de leurs alias de messagerie facile à mémoriser le travail de rapidement.
- Les répertoires Azure non managés peuvent être prises dans les annuaires gérés et être réutilisées pour d’autres services.

## <a name="terms-and-definitions"></a>Termes et définitions

+ **Inscription libre-service**: c’est la méthode par laquelle un utilisateur s’inscrit pour un service en nuage et a une identité créée automatiquement dans Azure Active Directory (AD Azure) en fonction de leur domaine de messagerie.
+ **Répertoire d’Azure non managé**: il s’agit du répertoire où cette identité est créée. Un répertoire non managé est un répertoire qui n’a aucun administrateur global.
+ **E-mail-vérifié l’utilisateur**: il s’agit d’un type de compte d’utilisateur dans Active Directory Azure. Un utilisateur qui possède une identité créée automatiquement après l’inscription d’une offre de libre-service est appelé un utilisateur vérifié sur l’e-mail. Un utilisateur vérifié sur le courrier électronique est un membre normal d’un répertoire balisé avec creationmethod = EmailVerified.

## <a name="user-experience"></a>Expérience de l’utilisateur

Par exemple, un utilisateur dont le message est Dan@BellowsCollege.com reçoit les fichiers sensibles par courrier électronique. Les fichiers ont été protégés par la gestion des droits (RMS Azure) Azure. Mais l’organisation de Daniel, Université de soufflets, n’a pas signé pour Azure RMS, ni qu’il a déployé Active Directory RMS. Dans ce cas, Dan pouvez vous inscrire pour un abonnement gratuit à RMS pour les personnes pour pouvoir lire les fichiers protégés.

Si Dan est le premier utilisateur avec une adresse de messagerie à partir de BellowsCollege.com pour vous inscrire pour ce libre-service offre, un répertoire non managé est créées pour les BellowsCollege.com dans Azure AD. Si d’autres utilisateurs dans le domaine de la BellowsCollege.com s’inscrire pour cette offre ou d’une offre de libre-service similaire, ils auront également vérifié de messagerie comptes créés dans le même répertoire non managé dans Azure.

## <a name="admin-experience"></a>Expérience de l’administration

Un administrateur qui possède le nom de domaine DNS d’un répertoire Azure non managé peut prendre ou fusionner le répertoire après le logement. Les sections qui suivent expliquent l’expérience d’administration plus en détail, mais voici un résumé :

- Lorsque vous prenez sur un répertoire Azure non managé, vous simplement Devenez l’administrateur global du répertoire non managé. Il s’agit parfois d’une prise en charge interne.
- Lorsque vous fusionnez un répertoire Azure non managé, vous ajoutez le nom de domaine DNS de l’annuaire non managée à votre répertoire Azure géré et un mappage des utilisateurs-pour-ressources est créé, donc les utilisateurs peuvent continuer à accéder aux services sans interruption. On parle parfois d’une prise en charge externe.

## <a name="what-gets-created-in-azure-active-directory"></a>Ce qui est créé dans Azure Active Directory ?

#### <a name="directory"></a>répertoire

- Un répertoire Azure Active Directory pour le domaine est créé, un répertoire par domaine.
- Le répertoire AD Azure n’a aucun administrateur global

#### <a name="users"></a>Utilisateurs

- Pour chaque utilisateur qui s’inscrit, un objet utilisateur est créé dans le répertoire AD Azure.
- Chaque objet utilisateur est marqué comme externe.
- Accès est accordé à chaque utilisateur à qui ils inscrit sur le service.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Comment puis-je faire valoir une publicité Azure libre service répertoire pour un domaine dont vous êtes propriétaire ?

Vous pouvez demander une annonce Azure libre service annuaire en effectuant la validation de domaine. Validation de domaine prouve que vous êtes le propriétaire du domaine en créant des enregistrements DNS.

Il existe deux façons de faire une prise en charge DNS d’un annuaire AD Azure :

- prise en charge interne (Admin découvre un répertoire Azure non managé et souhaite activer dans un répertoire)
- prise en charge externe (administrateur essaie d’ajouter un nouveau domaine dans leur répertoire Azure managé)

Vous intéresser dans la validation que vous possédez un domaine parce que vous prenez sur un répertoire non managé après l’exécution de libre service d’abonnement à un, ou ajouter un domaine à un annuaire managé existant. Par exemple, vous avez un domaine nommé contoso.com et vous souhaitez ajouter un nouveau domaine appelé contoso.co.uk ou contoso.uk.

## <a name="what-is-domain-takeover"></a>Quelle est la prise en charge du domaine ?  

Cette section explique comment valider que vous possédez un domaine

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Quelle est la validation de domaine et pourquoi l’utiliser ?

Pour effectuer des opérations sur un répertoire, AD Azure nécessite que vous validiez la possession du domaine DNS.  Validation du domaine vous permet de demander l’annuaire et soit promouvoir le répertoire libre service à un répertoire, ou fusionner le répertoire libre-service dans un répertoire géré existant.

## <a name="examples-of-domain-validation"></a>Exemples de validation de domaine

Il existe deux façons de faire une prise en charge DNS d’un répertoire :

+ prise en charge interne (par exemple, un administrateur découvre un répertoire autonome, non managé et souhaite activer dans le répertoire géré)
+ prise en charge externe (par exemple, un administrateur essaie d’ajouter un nouveau domaine dans un répertoire géré)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Prise en charge interne - promouvoir un répertoire autonome, non managé d’un répertoire

Lorsque vous effectuez une prise en charge interne, le répertoire converti à partir d’un répertoire non managé en un répertoire. Vous devez effectuer la validation de nom de domaine DNS, où vous créez un enregistrement MX ou TXT dans la zone DNS. Cette action :

+ Vérifie que vous possédez le domaine
+ Rend le répertoire géré
+ Vous rend l’administrateur global du répertoire

Supposons qu’un administrateur du College de soufflets découvre que les utilisateurs de l’école ont inscrit pour les offres de libre-service. BellowsCollege.com de nom de propriétaire inscrit pour le serveur DNS, l’administrateur peut valider la possession du nom DNS dans Azure et se chargent ensuite du répertoire non managé. Le répertoire devient alors un répertoire, et l’administrateur du rôle d’administrateur global pour le répertoire BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Prise en charge externe - fusionner un répertoire libre service un répertoire géré existant

Dans une prise de contrôle externe, vous disposez déjà d’un répertoire et vous souhaitez que tous les utilisateurs et les groupes à partir d’un répertoire non managé pour joindre ce répertoire managé plutôt que deux propre séparez les répertoires.

En tant qu’administrateur d’un répertoire, vous ajoutez un domaine et que ce domaine pour avoir un répertoire non managé associé.

Par exemple, supposons que vous êtes un administrateur et que vous disposez déjà d’un répertoire pour Contoso.com, un nom de domaine qui est inscrit dans votre organisation. Vous découvrez que les utilisateurs de votre organisation ont effectué inscription au libre-service pour une offre à l’aide du nom de domaine de messagerie user@contoso.co.uk, qui est un autre nom de domaine que votre organisation est propriétaire. Ces utilisateurs ont actuellement des comptes dans un répertoire non managé pour contoso.co.uk.

Vous ne souhaitez pas gérer deux répertoires distincts, vous fusionnez le répertoire non managé pour contoso.co.uk dans votre répertoire informatique géré existant pour contoso.com.

Prise en charge externe suit le même processus de validation de DNS en tant que prise en charge interne.  Différence étant : les utilisateurs et services qui sont remappés au répertoire informatiques gérés.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Quel est l’impact de l’exécution d’une prise en charge externe ?

Avec une prise en charge externe, un mappage des utilisateurs-pour-ressources est créé afin que les utilisateurs peuvent continuer à accéder aux services sans interruption. De nombreuses applications, y compris de RMS pour les personnes, gérer le mappage des utilisateurs-pour-ressources bien, et les utilisateurs peuvent continuer à accéder à ces services sans modification. Si une application ne gère pas le mappage des utilisateurs-pour-ressources efficacement, prise en charge externe peut être explicitement bloqué pour empêcher les utilisateurs d’une mauvaise expérience.

#### <a name="directory-takeover-support-by-service"></a>prise en charge par le service de prise en charge répertoire

Actuellement, les services suivants prennent en charge leur prise en charge :

- RMS


Les services suivants seront bientôt être prise en charge de leur prise en charge :

- PowerBI

Les éléments suivants ne garantissent pas et nécessitent une action d’administration supplémentaires pour migrer des données de l’utilisateur après une prise de contrôle externe.

- SharePoint/OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Comment faire pour effectuer une prise en charge du nom de domaine DNS

Vous disposez de quelques options pour savoir comment effectuer une validation de domaine (et faire une prise en charge si vous le souhaitez) :

1.  Portail de gestion Azure

    Une prise en charge est déclenchée en effectuant un ajout de domaine.  Si un répertoire existe déjà pour le domaine, vous aurez la possibilité de réaliser une prise en charge externe.

    Ouvrez une session sur le portail Azure à l’aide de vos informations d’identification.  Accédez à votre répertoire existant, puis à **Ajouter domaine**.

2.  Office 365

    Vous pouvez utiliser les options de la page [Gérer les domaines](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) dans Office 365 pour travailler avec vos domaines et les enregistrements DNS. Reportez-vous à la section [vérifier votre domaine dans Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    Les étapes suivantes sont nécessaires pour effectuer une validation à l’aide de Windows PowerShell.

    Étape    |   Applet de commande à utiliser
    ------- | -------------
    Créer un objet d’informations d’identification | Get-Credential
    Connexion à Active Directory Azure | MsolService de connexion
    obtenir la liste des domaines   | Get-MsolDomain
    Créer un défi  | Get-MsolDomainVerificationDns
    Créer l’enregistrement DNS   | Cela sur votre serveur DNS
    Vérifiez le défi    | Confirmer-MsolEmailVerifiedDomain

Par exemple :

1. Se connecter à Azure AD en utilisant les informations d’identification qui ont été utilisées pour répondre à l’offre de service automatique : import-module MSOnline $msolcred = get-credential connecter-msolservice-$msolcred des informations d’identification

2. Obtenir une liste des domaines :

    Get-MsolDomain

3. Ensuite, exécutez l’applet de commande Get-MsolDomainVerificationDns pour créer un défi :

    Get-MsolDomainVerificationDns-DomainName *Votre_nom_de_domaine* – Mode DnsTxtRecord

    Par exemple :

    Get-MsolDomainVerificationDns-DomainName contoso.com – Mode DnsTxtRecord

4. Copier la valeur qui est retournée à partir de cette commande (stimulation).

    Par exemple :

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. Dans votre espace de noms DNS public, créez un enregistrement txt DNS qui contient la valeur que vous avez copié à l’étape précédente.

    Le nom de cet enregistrement est le nom du domaine parent, si vous créez cet enregistrement de ressource à l’aide du rôle DNS dans Windows Server, laissez l’enregistrement nom vide simplement Coller la valeur dans la zone de texte

6. Exécutez l’applet de commande confirmer-MsolDomain pour vérifier le challenge :

    Confirmer-MsolEmailVerifiedDomain - DomainName *Votre_nom_de_domaine*

    par exemple :

    Confirmer-MsolEmailVerifiedDomain - DomainName contoso.com

Un défi réussi pour revenir à l’invite de commandes sans erreur.

## <a name="how-do-i-control-self-service-settings"></a>Comment contrôler les paramètres du libre services ?

Administrateurs ont deux contrôles libre services aujourd'hui. Ils peuvent contrôler :

- Si les utilisateurs peuvent rejoindre le répertoire par courrier électronique.
- Si les utilisateurs peuvent octroyer des licences eux-mêmes pour les applications et les services.


### <a name="how-can-i-control-these-capabilities"></a>Comment puis-je contrôler ces fonctionnalités ?

Un administrateur peut configurer ces fonctionnalités à l’aide de ces paramètres d’applet de commande Set-MsolCompanySettings AD Azure :

+ **AllowEmailVerifiedUsers** contrôle si un utilisateur peut créer ou rejoindre un répertoire non managé. Si vous définissez ce paramètre sur $false, aucun utilisateur vérifié sur le courrier électronique ne peut joindre l’annuaire.
+ **AllowAdHocSubscriptions** contrôle la possibilité pour les utilisateurs à effectuer l’inscription au libre-service. Si vous définissez ce paramètre sur $false, aucun utilisateur ne peut effectuer libre service d’abonnement à un.


### <a name="how-do-the-controls-work-together"></a>Comment les contrôles fonctionnent ensemble ?

Ces deux paramètres peuvent être conjointement pour définir un contrôle plus précis sur l’inscription au libre-service des. Par exemple, la commande suivante permettra aux utilisateurs d’effectuer des inscription au libre-service, mais uniquement si ces utilisateurs ont déjà un compte dans Active Directory Azure (en d’autres termes, les utilisateurs qui avait besoin d’un compte e-mail vérifié doit être créé ne peut pas effectuer inscription au libre-service) :

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

L’organigramme suivant décrit les différentes combinaisons de ces paramètres et les conditions qui en résulte pour le répertoire et l’inscription au libre-service des.

![][1]

Pour plus d’informations et d’exemples d’utilisation de ces paramètres, voir [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Voir aussi

-  [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md)

-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Référence d’applet de commande Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Ensemble-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
