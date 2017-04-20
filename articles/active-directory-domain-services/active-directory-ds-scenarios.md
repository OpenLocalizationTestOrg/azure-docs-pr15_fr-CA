<properties
    pageTitle="Azure Active Directory Domain Services : Scénarios de déploiement | Microsoft Azure"
    description="Scénarios de déploiement pour les Services de domaine Active Directory de Azure"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>Scénarios de déploiement et de cas d’usage
Dans cette section, nous examinons quelques scénarios et cas d’utilisation qui bénéficient des Services de domaine Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Sécuriser, facilite l’administration des machines virtuelles Azure
Vous pouvez utiliser Azure des Services de domaine Active Directory pour gérer vos ordinateurs virtuels Azure de manière simplifiée. Les machines virtuelles Azure peuvent être joints au domaine géré, vous permettant ainsi d’utiliser vos informations d’identification Active Directory d’entreprise pour vous connecter. Cette approche permet d’éviter les soucis de gestion d’informations d’identification, tels que la gestion des comptes d’administrateur local sur chacun de vos machines virtuelles Azure.

Serveurs virtuels qui sont joints au domaine géré peut également être gérés et sécurisés à l’aide de la stratégie de groupe. Vous pouvez appliquer des bases de sécurité requises à vos machines virtuelles Azure et verrouiller les directives de sécurité de l’entreprise. Par exemple, vous pouvez utiliser les fonctionnalités de gestion de stratégie de groupe pour restreindre les types d’applications qui peuvent être lancées sur ces machines virtuelles.

![Rationaliser l’administration des machines virtuelles Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Comme les serveurs et une autre infrastructure atteint la fin de vie, Contoso est déplacer plusieurs applications actuellement hébergées dans les locaux vers le nuage. Leur standard informatique actuel impose que les serveurs qui hébergent des applications d’entreprise doivent être joint au domaine et gérés à l’aide de la stratégie de groupe. Contoso administrateur informatique souhaite domaine la jointure des machines virtuelles déployées dans Azure, faciliter l’administration. Par conséquent, les administrateurs et les utilisateurs peuvent se connecter à l’aide de leurs informations d’identification d’entreprise. Dans le même temps, les ordinateurs peuvent être configurés pour se conformer à des bases de sécurité requis à l’aide de la stratégie de groupe. Vous préférez ne pas devoir déployer, de surveiller et de gérer les contrôleurs de domaine dans Azure pour sécuriser les ordinateurs virtuels Azure contoso. Par conséquent, les Services de domaine Active Directory de Azure est parfait pour ce cas d’usage.

**Remarques sur le déploiement**

Prenez en compte les points suivants pour ce scénario de déploiement :

- Les domaines gérés fournies par les Services de domaine Active Directory de Azure fournissent une structure d’unité d’organisation (unité d’organisation) plat unique par défaut. Tous les ordinateurs à un domaine se trouvent dans une seule unité d’organisation plate. Vous pouvez toutefois choisir de créer des unités d’organisation personnalisées.

- Des Services de domaine Active Directory Azure prend en charge la stratégie de groupe simple sous la forme d’un objet GPO intégré chaque pour les utilisateurs et les ordinateurs conteneurs. Vous ne peut pas cibler GP par unité d’organisation/service, effectuer le filtrage WMI ou créer des objets stratégie de groupe personnalisé.

- Les Services de domaine Active Directory Azure prend en charge le schéma d’objet ordinateur Active Directory base. Vous ne pouvez pas étendre le schéma de l’objet ordinateur.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Ascenseur et MAJ une application sur site qui utilise l’authentification de liaison LDAP aux Services d’Infrastructure Azure

![Liaison LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso dispose d’une application locale qui a été achetée à partir d’un éditeur de logiciels, il y a de nombreuses années. L’application est actuellement en mode de maintenance par le ISV et les modifications apportées à l’application de la demande sont prohibitif pour Contoso. Cette application est un site Web frontal web qui collecte les informations d’identification de l’utilisateur à l’aide d’un formulaire web et puis authentifie les utilisateurs en effectuant une liaison LDAP à l’annuaire Active Directory d’entreprise. Contoso souhaite migrer cette application aux Services d’Infrastructure Azure. Il est souhaitable que l’application fonctionne en l’état, sans nécessiter la modification. En outre, les utilisateurs doivent être capables de s’authentifier à l’aide de leurs informations d’identification d’entreprise existantes, sans avoir à former les utilisateurs à faire les choses différemment. En d’autres termes, les utilisateurs finaux doivent être classait d’où l’application est en cours d’exécution et la migration doit être transparente pour les.

**Remarques sur le déploiement**

Prenez en compte les points suivants pour ce scénario de déploiement :

- Assurez-vous que l’application n’a pas besoin modifier/écriture au répertoire. Accès en écriture à domaines gérés fournie par les Services de domaine Active Directory de Azure LDAP n’est pas pris en charge.

- Vous ne pouvez pas modifier les mots de passe directement sur le domaine géré. Les utilisateurs finaux peuvent modifier leur mot de passe soit à l’aide du mécanisme de modification de mot de passe libre-service Azure annonce ou par rapport au répertoire local. Ces modifications sont automatiquement synchronisées et disponibles dans le domaine géré.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Ascenseur et MAJ de lire une application sur site qui utilise le protocole LDAP pour accéder au répertoire aux Services d’Infrastructure Azure
Contoso dispose d’une application locale de métier (LOB) qui a été développée presque dix ans auparavant. Cette application est compatibles avec directory et a été conçue pour fonctionner avec Windows Server Active Directory. L’application utilise LDAP (Lightweight Directory Access Protocol) pour lire les attributs/informations sur les utilisateurs dans Active Directory. L’application ne pas modifier les attributs ou dans le cas contraire écrire dans l’annuaire. Contoso souhaite migrer cette application aux Services d’Infrastructure Azure et retirer le matériel sur site de vieillissement héberge actuellement cette application. L’application ne peut pas être réécrit pour utiliser des API telles que l’API Azure AD graphique basée sur REST de répertoire moderne. Par conséquent, une option de décalage ascenseur et est souhaitée selon laquelle l’application peut être migrée pour s’exécuter dans le nuage, sans modification de code ou de réécriture de l’application.

**Remarques sur le déploiement**

Prenez en compte les points suivants pour ce scénario de déploiement :

- Assurez-vous que l’application n’a pas besoin modifier/écriture au répertoire. Accès en écriture à domaines gérés fournie par les Services de domaine Active Directory de Azure LDAP n’est pas pris en charge.

- Assurez-vous que l’application n’a pas besoin un schéma Active Directory étendu/personnalisé. Extensions de schéma ne sont pas pris en charge dans les Services de domaine Active Directory de Azure.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migration d’une application de service ou le démon sur site pour les Services d’Infrastructure Azure
Certaines applications se composent de plusieurs niveaux, où un des niveaux doit effectuer des appels authentifiés vers un niveau back-end, par exemple un niveau de base de données. Les comptes de service Active Directory sont couramment utilisés pour ces cas d’usage. Vous pouvez d’ascenseur et MAJ ces applications à des Services d’Infrastructure Azure et l’utilisation des Services de domaine Active Directory Azure pour les besoins de l’identité de ces applications. Vous pouvez choisir d’utiliser le même compte de service qui est synchronisé à partir de votre répertoire local pour Azure AD. Alternativement, vous pouvez tout d’abord créer une unité d’organisation personnalisée et puis créez un compte de service distinct dans cette unité d’organisation, de déployer des applications.

![Compte de service à l’aide de WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso possède une application de coffre-fort de logiciels personnalisés qui comprend un serveur web frontal, un serveur SQL et un serveur principal FTP. Intégrée de Windows pour l’authentification des comptes de service est utilisée pour authentifier le site web frontal pour le serveur FTP. Le serveur web frontal est configuré pour s’exécuter comme compte de service. Le serveur principal est configuré pour autoriser l’accès à partir du compte de service pour le serveur web frontal. Contoso préfère ne pas nécessaire de déployer un ordinateur virtuel de contrôleur de domaine dans le nuage pour déplacer cette application vers les Services d’Infrastructure Azure. Contoso administrateur peut déployer les serveurs hébergeant le serveur web frontal, SQL server et le serveur FTP pour les machines virtuelles Azure. Ces ordinateurs sont ensuite joints à un domaine géré de Services de domaine Active Directory de Azure. Puis, ils peuvent utiliser le même compte de service dans leur répertoire local à des fins d’authentification de l’application. Ce compte de service est synchronisé avec le domaine géré les Services de domaine Active Directory de Azure et est disponible pour utilisation.

**Remarques sur le déploiement**

Prenez en compte les points suivants pour ce scénario de déploiement :

- Assurez-vous que l’application utilise le nom d’utilisateur/mot de passe pour l’authentification. L’authentification de certificat/carte à puce en fonction n’est pas pris en charge par les Services de domaine Active Directory de Azure.

- Vous ne pouvez pas modifier les mots de passe directement sur le domaine géré. Les utilisateurs finaux peuvent modifier leur mot de passe soit à l’aide du mécanisme de modification de mot de passe libre-service Azure annonce ou par rapport au répertoire local. Ces modifications sont automatiquement synchronisées et disponibles dans le domaine géré.


## <a name="azure-remoteapp"></a>RemoteApp Azure
RemoteApp Azure permet à l’administrateur de Contoso créer une collection à un domaine. Cette fonctionnalité permet à distance applications pris en charge par Azure RemoteApp à exécuter sur les ordinateurs joints au domaine et accéder aux autres ressources à l’aide de l’authentification intégrée Windows. Contoso peut utiliser les Services de domaine Active Directory de Azure afin de fournir un domaine géré utilisé par les collections Azure RemoteApp à un domaine.

![RemoteApp Azure](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Pour plus d’informations sur ce scénario de déploiement, consultez l’article de Blog de Services Bureau à distance intitulé [décalage ascenseur et vos charges de travail avec Azure RemoteApp et aux Services de domaine Active Directory Azure](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
