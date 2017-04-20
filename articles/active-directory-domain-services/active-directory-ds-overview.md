<properties
    pageTitle="Vue d’ensemble des Services de domaine ActiveDirectory Azure | Microsoft Azure"
    description="Vue d’ensemble des Services de domaine ActiveDirectory Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Services de domaine d’Active Directory Azure

## <a name="overview"></a>Vue d’ensemble
Services d’Infrastructure Azure vous permettent de vous permet de déployer une large gamme de solutions informatiques d’une manière agile. Avec les Machines virtuelles Azure, vous pouvez déployer presque instantanément et vous payez uniquement à la minute. À l’aide de la prise en charge pour Windows, Linux, SQL Server, Oracle, IBM, SAP et BizTalk, vous pouvez déployer les charges de travail, n’importe quel langage, sur presque n’importe quel système d’exploitation. Ces avantages permettent de migrer des applications héritées déployées sur site vers Azure, pour économiser sur les frais d’exploitation.

Un aspect clé de la migration des applications sur site vers Azure gère les besoins de l’identité de ces applications. Applications orientées annuaire peuvent s’appuient sur le protocole LDAP pour l’accès en lecture ou écriture à l’annuaire d’entreprise ou reposent sur l’authentification intégrée de Windows (authentification Kerberos ou NTLM) pour authentifier les utilisateurs finaux. Les applications de métier (LOB) s’exécutant sur Windows Server sont généralement déployées sur les machines joint au domaine, afin qu’ils peuvent être gérés en toute sécurité à l’aide de la stratégie de groupe. Pour les applications de 'ascenseur et MAJ' locaux vers le nuage, ces dépendances sur l’infrastructure d’identité d’entreprise doivent être résolus.

Les administrateurs sont souvent activer à l’une des solutions suivantes pour satisfaire les besoins de l’identité de leurs applications déployées dans Azure :

- Déploiement d’une connexion VPN de site à site entre les charges de travail en cours d’exécution dans les Services d’Infrastructure Azure et l’annuaire de l’entreprise locale.
- Étendre l’infrastructure de domaine/forêt Active Directory d’entreprise en configurant des contrôleurs de domaine de réplica à l’aide des machines virtuelles Azure.
- Déployer un domaine autonome dans Azure à l’aide de contrôleurs de domaine déployés dans des machines virtuelles Azure.

Toutes ces approches en souffrir du coût élevé et la charge administrative. Les administrateurs doivent déployer des contrôleurs de domaine à l’aide de machines virtuelles dans Azure. En outre, ils ont besoin gérer, sécuriser, correctif, surveiller, sauvegarder et résoudre les problèmes liés à ces ordinateurs virtuels. La dépendance vis-à-vis des connexions VPN dans le répertoire local entraîne des charges de travail déployés dans Azure pour être vulnérable aux problèmes de réseau temporaires ou de panne. Ces pannes réseau à son tour entraînent la disponibilité inférieure et de la baisse de fiabilité de ces applications.

Nous avons conçu des Services de domaine Active Directory de Azure pour fournir une alternative plus simple.


## <a name="introducing-azure-ad-domain-services"></a>Présentation des Services de domaine d’Active Directory Azure
Les Services de domaine Active Directory Azure fournit des services de domaine géré par exemple de jonction de domaine, l’authentification Kerberos/NTLM groupe stratégie, LDAP, qui sont entièrement compatibles avec Windows Server Active Directory. Vous pouvez utiliser ces services de domaine sans avoir besoin de vous permettant de déployer, de gérer et de corriger les contrôleurs de domaine dans le nuage. Les Services de domaine Active Directory Azure s’intègre à vos clients AD Azure existant, permettant ainsi aux utilisateurs de se connecter à l’aide de leurs informations d’identification d’entreprise. En outre, vous pouvez utiliser des comptes d’utilisateurs et des groupes existants pour sécuriser l’accès aux ressources, garantissant ainsi une plus lisse 'ascenseur-et-MAJ » des ressources sur site pour les Services d’Infrastructure Azure.

Fonctionnalités des Services de domaine Active Directory Azure fonctionnent en toute transparence que vos clients AD Azure cloud uniquement ou synchronisée avec Active Directory sur site.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Services de domaine Active Directory Azure pour les organisations de nuage uniquement
Un nuage-uniquement les clients AD Azure (souvent appelés « locataires managés ») n’ont pas aucun encombrement sur l’identité sur site. En d’autres termes, les comptes d’utilisateurs, leurs mots de passe et les appartenances aux groupes sont tout cela étant natifs vers le nuage - c'est-à-dire, créés et gérés dans Azure AD. Penchez-vous un moment que Contoso est un nuage-uniquement les clients AD Azure. Comme indiqué dans l’illustration suivante, l’administrateur de Contoso a configuré un réseau virtuel dans les Services d’Infrastructure Azure. Les charges de travail de serveur et les applications sont déployées dans ce réseau virtuel dans des machines virtuelles Azure. Dans la mesure où Contoso est un client de nuage uniquement, toutes les identités des utilisateurs, leurs informations d’identification et les appartenances aux groupes sont créés et gérés dans Active Directory Azure.

![Vue d’ensemble des Services Azure domaine d’Active Directory](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso administrateur peut activer des Services de domaine Active Directory de Azure pour leurs clients AD Azure et choisir de rendre les services de domaine disponibles dans ce réseau virtuel. Par la suite, des Services de domaine Active Directory Azure dispositions un domaine géré et le rend disponible dans le réseau virtuel. Tous les comptes d’utilisateur, les appartenances de groupe et informations d’identification disponibles dans clients de Azure AD de Contoso sont également disponibles dans ce domaine qui vient d’être créé. Cette fonctionnalité permet aux utilisateurs de l’organisation pour vous connecter au domaine à l’aide de leurs références d’entreprise - par exemple, lors de la connexion à distance à un domaine d’ordinateurs via le Bureau à distance. Les administrateurs peuvent provisionner l’accès aux ressources du domaine à l’aide des appartenances aux groupes existants. Les applications déployées dans des machines virtuelles sur le réseau virtuel peuvent utiliser des fonctionnalités telles que la jonction de domaine LDAP en lecture, LDAP bind, l’authentification NTLM et Kerberos et stratégie de groupe.

Quelques aspects saillantes du domaine géré qui est mis en service par les Services de domaine Active Directory de Azure sont les suivants :

- Contoso administrateur ne pas doit-elle gérer, des correctifs ou contrôler ce domaine ou des contrôleurs de domaine pour ce domaine géré.
- Il n’est pas nécessaire de gérer la réplication Active Directory pour ce domaine. Comptes d’utilisateur, les appartenances au groupe et les informations d’identification à partir de clients d’Azure AD de Contoso sont automatiquement disponibles dans ce domaine géré.
- Étant donné que le domaine est géré par Azure domaine Services AD, Contoso administrateur ne dispose pas de privilèges d’administrateur de domaine ou administrateur d’entreprise sur ce domaine.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Services de domaine Active Directory Azure pour les organisations de hybride
Les organisations avec une infrastructure informatique de hybride consomment une combinaison de ressources de cloud et sur site. Ces organisations synchroniser les informations d’identité à partir de leur répertoire local pour leurs clients AD Azure. Comme les organisations hybride Rechercher pour effectuer une migration plus de leurs applications sur site vers le nuage, en particulier les applications orientées annuaire hérités, les Services de domaine Active Directory de Azure peuvent être utiles de les.

Litware Corporation a déployé [Azure Connect de publicité](../active-directory/active-directory-aadconnect.md), pour synchroniser les informations d’identité à partir de leur répertoire local pour leurs clients AD Azure. Les informations d’identité qui sont synchronisées comprennent les comptes d’utilisateurs, leurs hachages d’informations d’identification pour l’authentification (synchronisation de mot de passe) et les appartenances aux groupes.

> [AZURE.NOTE] La **synchronisation de mot de passe est obligatoire pour les organisations hybride utiliser les Services de domaine Active Directory de Azure**. Cette exigence est parce que les informations d’identification des utilisateurs sont nécessaires dans le domaine géré fourni par les Services de domaine Active Directory de Azure, pour authentifier les utilisateurs via les méthodes d’authentification NTLM ou Kerberos.

![Les domaines Active Directory Azure Services pour Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

L’illustration ci-dessus montre comment les organisations avec un hybride de l’infrastructure informatique, tel que Litware Corporation, peuvent utiliser les Services de domaine Active Directory de Azure. Les applications et les charges de travail nécessitant des services de domaine de Litware sont déployés dans un réseau virtuel dans les Services d’Infrastructure Azure. De Litware administrateur système peut activer les Services de domaine Active Directory de Azure pour leurs clients AD Azure et choisir à disposition un domaine géré de ce réseau virtuel. Litware étant une organisation avec une infrastructure informatique de hybride, informations d’identification, des groupes et des comptes d’utilisateurs sont synchronisées avec leurs clients AD Azure à partir de leur répertoire local. Cette fonctionnalité permet aux utilisateurs de se connecter au domaine à l’aide de leurs références d’entreprise - par exemple, lors de la connexion à distance à des ordinateurs joints au domaine via le Bureau à distance. Les administrateurs peuvent provisionner l’accès aux ressources du domaine à l’aide des appartenances aux groupes existants. Les applications déployées dans des machines virtuelles sur le réseau virtuel peuvent utiliser des fonctionnalités telles que la jonction de domaine LDAP en lecture, LDAP bind, l’authentification NTLM et Kerberos et stratégie de groupe.

Quelques aspects saillantes du domaine géré qui est mis en service par les Services de domaine Active Directory de Azure sont les suivants :

- Le domaine géré est un domaine autonome. Il n’est pas une extension de domaine des locaux de Litware.
- De Litware administrateur ne doit pas gérer, correctif, ou de surveiller les contrôleurs de domaine pour ce domaine géré.
- Il n’est pas nécessaire de gérer la réplication Active Directory pour ce domaine. Comptes d’utilisateur, les appartenances au groupe et les informations d’identification du répertoire des locaux de Litware sont synchronisées avec Azure AD via Azure Connect d’Active Directory. Ces comptes utilisateur, les appartenances aux groupes et les informations d’identification sont automatiquement disponibles dans le domaine géré.
- Étant donné que le domaine est géré par Azure domaine Services AD, Litware administrateur ne dispose pas de privilèges d’administrateur de domaine ou administrateur d’entreprise sur ce domaine.


## <a name="benefits"></a>Avantages
Avec les Services de domaine Active Directory de Azure, vous pouvez bénéficier des avantages suivants :

-   **Simple** – vous pouvez satisfaire les besoins de l’identité des ordinateurs virtuels déployés à des services d’Infrastructure d’Azure avec un simple clic. Vous n’avez pas besoin déployer et gérer l’infrastructure d’identité de connectivité Azure ou le programme d’installation à votre infrastructure d’identité sur site.

-   **Intégré** – les Services de domaine Active Directory de Azure est profondément intégré à vos clients AD Azure. Vous pouvez désormais utiliser AD Azure comme un annuaire d’entreprise intégré de nuage qui convient aux besoins de vos applications modernes et des applications orientées annuaire traditionnelles.

-   **Compatible** , les Services de domaine Active Directory de Azure est construit sur l’infrastructure de qualité entreprise éprouvée de Windows Server Active Directory. Par conséquent, vos applications peuvent s’appuyer sur une plus grande compatibilité avec les fonctionnalités de Windows Server Active Directory. Pas toutes les fonctionnalités disponibles dans Windows Server Active Directory sont actuellement disponibles dans les Services de domaine Active Directory de Azure. Toutefois, les fonctionnalités disponibles sont compatibles avec les fonctionnalités de Windows Server, Active Directory correspondantes que dépendent de votre infrastructure sur site. Les fonctions de join LDAP, Kerberos, NTLM, stratégie de groupe et domaine constituent une offre mature qui a été testée et affinée au fil des différentes versions de Windows Server.

-   **Rentables** – avec les Services de domaine Active Directory Azure, vous pouvez éviter la charge de gestion et d’infrastructure qui est associée à la gestion de l’infrastructure d’identité pour prendre en charge les applications orientées annuaire traditionnelles. Vous pouvez déplacer ces applications aux Services d’Infrastructure Azure et bénéficier d’économies substantielles sur les frais d’exploitation.
