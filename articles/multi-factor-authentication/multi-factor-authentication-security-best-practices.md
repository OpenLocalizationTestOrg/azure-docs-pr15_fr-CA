<properties 
    pageTitle="Les méthodes conseillées pour l’utilisation d’Azure AMF"
    description="Ce document fournit les meilleures pratiques concernant l’utilisation d’Azure AMF avec comptes Azure"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Les méthodes conseillées pour l’utilisation de l’authentification multifactorielle Azure avec les comptes Active Directory Azure

Lorsqu’il s’agit à l’amélioration de votre processus d’authentification, l’authentification selon plusieurs facteur est le choix idéal pour la plupart des organisations. Authentification de facteurs multiples Azure (AMF) permet aux entreprises de répondre à leurs exigences en matière de sécurité et de conformité tout en fournissant une expérience de connexion simple pour leurs utilisateurs. Cet article traite des quelques méthodes conseillées que vous devez considérer lorsque vous planifiez l’adoption de l’AMF d’Azure.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Méthodes conseillées pour l’authentification à plusieurs facteurs Azure dans le nuage
Tous les utilisateurs avec une authentification à facteurs multiples et tirer parti des fonctionnalités étendues qui offre d’Azure, plusieurs facteurs d’authentification, vous devez activer l’authentification selon plusieurs facteurs Azure sur tous vos utilisateurs.  Cela s’effectue à l’aide d’une des opérations suivantes :

- Azure AD Premium ou à la Suite de mobilité d’entreprise
- Fournisseur d’authentification selon plusieurs facteurs

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Suite de mobilité Azure AD Premium/entreprise

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

La première étape recommandée pour adopter AMF Azure dans le cloud à l’aide d’Azure AD Premium ou à la Suite de mobilité d’entreprise doit attribuer les licences à vos utilisateurs.  Authentification à plusieurs facteurs Azure fait partie de ces suites et en tant que tel votre organisation n’a pas besoin tout élément supplémentaire pour étendre la fonctionnalité d’authentification à plusieurs facteurs pour tous les utilisateurs.

Lorsque la configuration de l’authentification multicritères examine suivantes :

- Vous n’avez pas besoin de créer un fournisseur d’authentification selon plusieurs facteurs.  Azure Premium de publicité et de la Suite de mobilité d’entreprise est livré avec authentification à plusieurs facteurs Azure.  Si vous créez un fournisseur d’authentification pourrait avoir double facturé.
- Azure AD Connect n’est obligatoire que si vous synchronisez votre environnement d’Active Directory sur site avec un annuaire AD d’Azure. Si vous utilisez uniquement un annuaire AD Azure qui n’est pas synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin d’Azure Connect d’Active Directory.


### <a name="multi-factor-auth-provider"></a>Fournisseur d’authentification selon plusieurs facteurs

![Fournisseur d’authentification selon plusieurs facteurs](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si vous n’avez pas Azure AD Premium ou à la Suite de mobilité d’entreprise la première étape recommandée pour adopter AMF Azure dans le nuage est de créer un fournisseur d’authentification AMF. Bien qu’AMF est disponible par défaut pour les administrateurs globaux qui ont Azure Active Directory, lorsque vous déployez AMF pour votre organisation, que vous devez étendre la capacité d’authentification à plusieurs facteurs à tous les utilisateurs et que vous avez besoin d’un fournisseur d’authentification selon plusieurs facteurs.
Lorsque vous sélectionnez le fournisseur d’authentification, vous devez sélectionner un répertoire et de prendre en compte les éléments suivants :

- Vous n’avez pas besoin d’un annuaire AD Azure pour créer un fournisseur d’authentification selon plusieurs facteurs.
- Vous devez associer le fournisseur d’authentification selon plusieurs facteurs avec un annuaire AD Azure si vous souhaitez étendre l’authentification multifactorielle pour tous vos utilisateurs ou souhaitez votre administrateur global pour pouvoir tirer parti de fonctionnalités telles que le portail de gestion, des salutations personnalisées et rapports.
- DAS synchronisation ou synchronisation d’annuaire sont uniquement nécessaires si vous synchronisez votre environnement d’Active Directory sur site avec un annuaire AD d’Azure. Si vous utilisez uniquement un annuaire AD Azure qui n’est pas synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin DAS synchronisation ou synchronisation d’annuaire.
- Si vous avez Azure AD Premium ou à la Suite de mobilité d’entreprise, vous n’avez pas besoin créer un fournisseur d’authentification de plusieurs facteurs. Vous ne devez affecter une licence à un utilisateur et commencer à tension AMF pour les utilisateurs.
- Veillez à choisir le modèle d’utilisation de droite pour votre entreprise (par auth ou par utilisateur activé), une fois que vous sélectionnez le modèle d’utilisation vous ne pouvez pas le modifier.

### <a name="user-account"></a>Compte d’utilisateur
Lors de l’activation AMF pour vos utilisateurs, les comptes d’utilisateur peuvent être dans un des trois états de base : désactivé, activé ou appliquées.
Suivez les instructions ci-dessous pour assurer à que l’aide de l’option la plus appropriée pour votre déploiement :

- Lorsque l’état est défini sur désactivé, cet utilisateur n’utilise pas l’authentification multifactorielle. Il s’agit de l’état par défaut.
- Lorsque l’état est défini sur activé, cela signifie que l’utilisateur est activé, mais n’a pas terminé le processus d’inscription. Ils devront effectuer le processus à la connexion suivante. Ce paramètre n’affecte pas les applications de navigateur non. Toutes les applications continueront à fonctionner tant que le processus d’inscription est terminé.
- Lorsque l’état de l’utilisateur a forcé, cela signifie que l’utilisateur peut ou peut ne pas avoir terminer l’inscription. Si elles ont terminé le processus d’inscription ils utilisent une authentification multifactorielle. Dans le cas contraire, l’utilisateur sera invité à terminer le processus d’inscription à la connexion suivante. Ce paramètre n’affecte pas les applications de navigateur non. Ces applications ne fonctionneront pas tant que les mots de passe app sont créées et utilisées.
- Le modèle de Notification utilisateur disponibles dans l’article [mise en route avec authentification à plusieurs facteurs Azure dans le nuage](multi-factor-authentication-get-started-cloud.md) permet d’envoyer un courrier électronique à vos utilisateurs concernant l’adoption de l’AMF.

### <a name="supportability"></a>Prise en charge

Dans la mesure où la grande majorité des utilisateurs sont habitués à l’utilisation de mots de passe uniquement pour authentifier, il est important que votre société mettre sensibilisation à tous les utilisateurs en ce qui concerne ce processus. Cette reconnaissance peut réduire la probabilité que les utilisateurs appelle votre service de support technique les problèmes mineurs de l’AMF.
Toutefois, il y a certains scénarios où il est nécessaire de désactiver temporairement AMF. Suivez les instructions ci-dessous pour savoir comment gérer ces scénarios :

- Assurez-vous que votre personnel de support technique est formés à poignée des scénarios où l’application mobile ou un téléphone ne reçoit une notification ou un téléphone et pour cette raison, qu'il est impossible de se connecter. Ils peuvent activer une option Autoriser un utilisateur à s’authentifier une seule fois par « contournement » plusieurs facteurs d’authentification unique. Le contournement est temporaire et expire après le nombre de secondes spécifié.
- Si nécessaire, vous pouvez exploiter les fonctionnalités de confiance des IPs dans Azure AMF. Cette fonctionnalité permet aux administrateurs d’un client géré ou fédéré ignorer plusieurs facteurs d’authentification pour les utilisateurs qui sont la signature à partir de l’intranet local de la société. Les fonctionnalités sont disponibles pour les locataires AD Azure qui ont des licences Azure AD Premium, Suite de mobilité d’entreprise ou authentification à plusieurs facteurs Azure.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Méthodes conseillées pour Azure une authentification multifactorielle sur site
Si votre société a décidé de tirer parti de sa propre infrastructure pour activer AMF, il sera nécessaire de déployer un serveur de l’authentification multifactorielle Azure sur site. Les composants serveur de l’AMF sont affichés dans le diagramme ci-dessous :

![Fournisseur d’authentification selon plusieurs facteurs](./media/multi-factor-authentication-security-best-practices/server.png)
*pas installé par défaut ** installé mais pas activé par défaut


Serveur d’authentification selon plusieurs facteurs Azure peut servir à sécuriser des ressources de cloud et locaux qui sont accessibles par les comptes Active Directory Azure.  Toutefois, il peut uniquement effectuée à l’aide de fédération.  Vous devez avoir AD FS et il fédéré avec vos clients AD Azure.
Lorsque la configuration de serveur de l’authentification multifactorielle examine suivantes :

- Si vous êtes sécurisation des ressources Azure AD à l’aide des Services de fédération Active Directory, puis le 1er facteur d’authentification est effectué sur site à l’aide d’ADFS et du 2e facteur est effectuée sur place par honorer la demande.
- Il n’est pas obligatoire que le serveur de l’authentification multifactorielle Azure être installé sur votre serveur de fédération AD FS mais l’adaptateur de l’authentification multifactorielle pour AD FS doit être installé sur un serveur Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur, tant qu’elle est une version prise en charge et installation de la carte AD FS séparément sur votre serveur de fédération AD FS. Consultez la procédure ci-dessous pour obtenir des instructions sur l’installation de la carte séparément.
- L’Assistant d’installation de plusieurs facteurs d’authentification AD FS carte crée un groupe de sécurité appelé PhoneFactor Admins dans Active Directory, puis ajoute le compte de service AD FS de votre service de fédération à ce groupe. Il est conseillé de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins est effectivement créé et que le compte de service l’AD FS est un membre de ce groupe. Si nécessaire, ajoutez le compte de service AD FS manuellement au groupe PhoneFactor Admins sur votre contrôleur de domaine.

### <a name="user-portal"></a>Portail de l’utilisateur
Ce portail s’exécute dans un site web de Internet Information Server (IIS), qui autorise les fonctionnalités de libre-service et fournit un ensemble complet de fonctionnalités d’administration utilisateur. Suivez les instructions ci-dessous pour configurer ce composant :

- IIS version 6 ou supérieure est requise
- V2.0.507207 d’ASP.NET doivent être installés et inscrits
- Ce serveur peut être déployé dans un réseau de périmètre.



### <a name="app-passwords"></a>Application de mots de passe
Si votre organisation est fédérée à l’aide de l’authentification unique avec Active Directory Azure et que vous vous apprêtez à Azure AMF, puis être conscient des éléments suivants lors de l’utilisation de mots de passe app (n’oubliez pas que ceci s’applique uniquement aux fédéré (SSO) est utilisée) :

- Le mot de passe d’application est vérifiée par AD Azure et ignore donc la fédération. Fédération n’est utilisée que lorsque vous configurez l’application de mot de passe.
- Les mots de passe utilisateurs seront stockées dans l’id d’organisation pour fédéré (SSO). Si l’utilisateur quitte la société, cette information est à l’id d’organisation à l’aide de la synchronisation d’annuaire en temps réel. Désactivation/suppression du compte peut prendre jusqu'à 3 heures pour synchroniser, de retarder la désactivation/suppression du mot de passe de l’application dans Azure AD.
- Paramètres de contrôle d’accès au Client local ne sont pas respectés par application de mot de passe
- Aucune authentification locales consignation / audit de fonctionnalité n’est disponible pour l’application de mot de passe
- Éducation de l’utilisateur final plus est requise pour le client Microsoft Lync 2013.
- Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de nom d’utilisateur d’organisation et les mots de passe et les mots de passe app lors de l’utilisation de plusieurs facteurs d’authentification avec les clients, en fonction de l’endroit où ils s’authentifient. Pour les clients qui s’authentifient par rapport à une infrastructure sur site, vous utilisez un nom d’utilisateur d’organisation et d’un mot de passe. Pour les clients qui s’authentifient AD Azure, vous devez utiliser le mot de passe d’application.
- Par défaut, les utilisateurs ne peut pas créer des mots de passe app, si votre entreprise requiert qu’ou si vous avez besoin permettre aux utilisateurs de créer un mot de passe app dans certains scénarios, assurez-vous que l’option Autoriser les utilisateurs à créer des mots de passe app pour signer dans des applications autres que des navigateurs est sélectionné.

## <a name="additional-considerations"></a>Considérations supplémentaires
La liste ci-dessous permet de comprendre certaines considérations supplémentaires et les meilleures pratiques pour chaque composant qui sera déploiement sur site :

Méthode|Description
:------------- | :------------- |
[Service de fédération Active Directory](multi-factor-authentication-get-started-adfs.md)|Informations sur la configuration d’une authentification à facteurs multiples Azure avec AD FS.
[Authentification RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec RADIUS.
[Authentification IIS](multi-factor-authentication-get-started-server-iis.md)|Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec IIS.
[Authentification de Windows](multi-factor-authentication-get-started-server-windows.md)|  Informations sur le programme d’installation et de la configuration du serveur d’AMF Azure avec l’authentification Windows.
[Authentification LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec l’authentification LDAP.
[Passerelle des services Bureau à distance et Azure plusieurs facteurs d’authentification serveur, à l’aide de RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informations sur le programme d’installation et de la configuration du serveur AMF Azure avec la passerelle des services Bureau à distance à l’aide de RADIUS.
[Synchronisation avec Active Directory de Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informations sur le programme d’installation et de configuration de la synchronisation entre Active Directory et le serveur AMF Azure.
[Déploiement du Service de Mobile application Web Server Azure plusieurs facteurs d’authentification](multi-factor-authentication-get-started-server-webservice.md)|Informations sur le programme d’installation et de configuration du service web du serveur Azure AMF.
[Configuration avancée de VPN avec authentification à plusieurs facteurs Azure](multi-factor-authentication-advanced-vpn-configurations.md)|Informations sur la configuration des appliances ASA de Cisco, Citrix Netscaler et VPN de Juniper/Pulse sécurisé à l’aide de LDAP ou RADIUS.


## <a name="additional-resources"></a>Ressources supplémentaires
Bien que cet article met en évidence certaines des meilleures pratiques pour Azure AMF, il existe des autres ressources que vous pouvez également utiliser lors de la planification de votre déploiement de l’AMF. La liste ci-dessous présente certains articles clés qui peuvent vous aider au cours de ce processus :

- [Rapports dans Azure plusieurs facteurs d’authentification](multi-factor-authentication-manage-reports.md)
- [Mise en route pour Azure, plusieurs facteurs d’authentification](multi-factor-authentication-end-user-first-time.md)
- [Forum aux questions sur Azure plusieurs facteurs d’authentification](multi-factor-authentication-faq.md)
