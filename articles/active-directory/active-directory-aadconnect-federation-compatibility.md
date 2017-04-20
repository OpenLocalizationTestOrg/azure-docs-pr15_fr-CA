<properties
    pageTitle="Liste de compatibilité de fédération AD Azure"
    description="Cette page contient des fournisseurs d’identité de non Microsoft qui peuvent être utilisés pour mettre en œuvre une ouverture de session unique."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Liste de compatibilité de fédération AD Azure
Azure Active Directory fournit l’authentification unique sur et amélioré la sécurité des accès aux applications pour Office 365 et d’autres services Microsoft Online pour hybride et d’implémentations de cloud uniquement sans nécessiter de n’importe quelle solution non Microsoft. Office 365, comme la plupart des services en ligne de Microsoft est intégré avec Azure Active Directory pour les services d’annuaire, d’authentification et d’autorisation. Azure Active Directory fournit également de l’authentification unique à des milliers d’applications de SaaS et les applications web en local. Veuillez consulter la galerie d’applications Azure Active Directory pour les applications SaaS prises en charge.

Pour les entreprises qui ont investi dans des solutions de fédération de non Microsoft, cette rubrique contient des instructions pour la configuration de l’authentification unique pour les utilisateurs de Windows Server Active Directory avec Microsoft Online services à l’aide des fournisseurs d’identité de non Microsoft dans la « Azure Active Directory federation compatibilité liste » ci-dessous. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Groupe d’ordinateurs Oxford](http://oxfordcomputergroup.com/), une tierce partie, pour le compte de Microsoft, testé ces seule session expériences à l’aide des fournisseurs d’identité de non Microsoft par rapport à un ensemble de cas d’usage communs avec Azure Active Directory.

Pour plus d’informations sur comment vous pouvez obtenir de votre fournisseur d’identité tiers répertorié ici, contactez le groupe d’ordinateurs Oxford à [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Groupe d’ordinateurs Oxford testé uniquement les fonctionnalités de fédération de ces scénarios d’ouverture de session unique. Groupe d’ordinateurs Oxford n’a pas effectué les tests de la synchronisation, l’authentification à deux facteurs, composants etc. de ces scénarios d’ouverture de session unique.

>Utilisation du signe dans par un autre ID à l’UPN n’est également pas testée dans ce programme.



- [Azure Active Directory](#azure-active-directory)
- [Services de fédération serveur optimale IDM identité virtuelle](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11](#pingfederate-611) 
- [7.2 de PingFederate](#pingfederate-72) 
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify) 
- [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [IdP de SecureAuth 7.2.0](#secureauth-idp-720) 
- [Autorité de certification SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [Gestionnaire d’accès NetIQ 4.0.1](#netiq-access-manager-401) 
- [Avec le Gestionnaire de stratégie d’accès BIG-IP version 11.3 x – 11,6 BIG-IP](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [Portail d’espace de travail de VMware version 2.1](#vmware-workspace-portal-version-21) 
- [Signer & aller 5.3](#signampgo-53) 
- [Fédération de IceWall Version 3.0](#icewall-federation-version-30) 
- [Nuage sécurisé d’autorité de certification](#ca-secure-cloud) 
- [Dell une identité Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
- [Ouverture de session unique de AuthAnvil 4.5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Dans la mesure où il s’agit de produits tiers, Microsoft ne fournit pas de prise en charge pour le déploiement, configuration, dépannage, meilleures pratiques, etc. les problèmes et questions relatives à ces fournisseurs d’identité. Pour les questions relatives à ces fournisseurs d’identité et de prise en charge, contactez directement le tiers pris en charge.

>Ces fournisseurs d’identité tiers ont été testées pour assurer l’interopérabilité avec les services de cloud de Microsoft à l’aide de WS-Federation et uniquement les protocoles WS-Trust. Test ne comportait pas l’utilisation du protocole SAML.

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory peut authentifier des utilisateurs à se fédérer avec votre Active Directory local ou sans un serveur de fédération local grâce à l’utilisation de la synchronisation de mot de passe. 

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session : 


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|
|Applications modernes à l’aide de ADAL, tels que Office 2016| Prise en charge|Aucun|

Pour plus d’informations sur l’utilisation d’Azure Active Directory avec AD FS, voir [Active Directory Federation Services (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Pour plus d’informations sur l’utilisation d’Azure Active Directory avec la synchronisation de mot de passe voir [Azure Connect d’Active Directory](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Services de fédération serveur optimale IDM identité virtuelle 
Optimale IDM Virtual Server Federation Services d’identité peuvent authentifier les utilisateurs qui résident dans des répertoires Active de clients locaux.

Voici la matrice de support le scénario de cette expérience d’ouverture de session unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Authentification Windows intégrée|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Pour plus d’informations sur l’accès client voir des stratégies [limiter l’accès à Office 365 Services en fonction de l’emplacement du Client.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 

PingFederate 6.11 implémente la norme d’identité de fédération de WS largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun (les versions antérieures doivent mettre à niveau vers 6.11|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour les instructions de PingFederate sur la façon de configurer SharePoint Team Services pour fournir l’expérience d’ouverture de session unique à vos utilisateurs d’Active Directory, téléchargez le pdf [ici.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>7.2 de PingFederate 
PingFederate 7.2 implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour les instructions de PingFederate sur la façon de configurer cette STS fournir l’expérience d’ouverture de session unique à vos utilisateurs d’Active Directory, voir [ici.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x 
PingFederate 8.x implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour les instructions de PingFederate sur la façon de configurer cette STS fournir l’expérience d’ouverture de session unique à vos utilisateurs d’Active Directory, voir [ici.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify fournit fédérés une seule expérience de session à Office 365 sans le besoin d’un serveur de fédération local d’hébergement.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Contrôle d’accès au client n’est pas pris en charge. 

Pour plus d’informations sur Centrify, consultez [ici.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2 
IBM Tivoli Federated Identity Manager 6.2.2 avec IBM Security Access Manager pour 1.4 des Applications Microsoft implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur IBM Tivoli Federated Identity Manager, consultez [IBM Security Access Manager pour les Applications Microsoft.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>IdP de SecureAuth 7.2.0 
SecureAuth IdP 7.2.0 implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et de la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur SecureAuth, reportez-vous à la section [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>Autorité de certification SiteMinder 12.52 SP1 version Cumulative 4
Autorité de certification SiteMinder fédération 12.52 implémente la norme d’identité de fédération de WS/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur l’autorité de certification SiteMinder, consultez [CA SiteMinder fédération.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
Service de fédération de nuage de RadiantOne (CFS) 3.0 implémente la norme d’identité de fédération de WS/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Authentification Windows intégrée|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur RadiantOne CFS, consultez [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |L’authentification intégrée Windows requiert le programme d’installation de serveur web supplémentaires et l’application de Okta.|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Authentification Windows intégrée|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur Okta, consultez [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin testé en mai 2014 implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Authentification Windows intégrée|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Authentification Windows intégrée|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur OneLogin, consultez [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>Gestionnaire d’accès NetIQ 4.0.1 
Le Gestionnaire d’accès NetIQ 4.0.1 implémente la norme d’identité de fédération de WS/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |* Kerberos contrats pris en charge|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

* NetIQ prend en charge l’authentification Kerberos via la configuration d’un contrat de Kerberos.  Pour obtenir une assistance avec cette configuration, veuillez contacter NetIQ ou consulter le guide d’installation. Pour plus d’informations sur NetIQ Access Manager, consultez [le Gestionnaire d’accès NetIQ.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP avec la version du Gestionnaire de stratégie d’accès BIG-IP 11.3 x – 11,6 
BIG-IP avec accès Gestionnaire de stratégies, (l’alimentation APM) ver de BIG-IP. 11.3 x – x 11,6 implémente la norme d’identité SAML largement utilisée pour fournir une expérience d’authentification unique et de la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Non pris en charge |Non pris en charge|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur le Gestionnaire de stratégie accès BIG-IP, reportez-vous à la section [Gestionnaire de stratégie d’accès BIG-IP.](https://f5.com/products/modules/access-policy-manager) 

Pour les instructions configurer ce gestionnaire de stratégie d’accès BIG-IP STS pour fournir l’expérience d’ouverture de session unique aux utilisateurs Active Directory, téléchargez le pdf [ici.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Portail d’espace de travail de VMware version 2.1 
Portail d’espace de travail de VMware version 2.1 implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Applications clientes telles que Lync, abonnement Office, CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur le portail d’espace de travail de VMware version 2.1, télécharger le fichier pdf [ici.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Signer & aller 5.3 
Signer & Continuer l’identité de fédération de WS/WS-Trust répandu 5.3 implémente standard pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Contrats de Kerberos pris en charge |
| Applications clientes telles que Lync, abonnement Office, CRM | Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|


Signe & atteindre 5.3 prend en charge l’authentification Kerberos via la configuration d’un contrat de Kerberos.  Pour obtenir une assistance avec cette configuration, veuillez contacter Ilex ou consulter le guide d’installation [ici.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>Fédération de IceWall Version 3.0 
IceWall fédération Version 3.0 implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Applications clientes telles que Lync, abonnement Office, CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur la fédération des IceWall, consultez [ici](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) et [ici.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>Nuage sécurisé d’autorité de certification 

Nuage de sécuriser d’autorité de certification implémente la norme d’identité de fédération de WS/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Applications clientes telles que Lync, abonnement Office, CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur le nuage sécurisé d’autorité de certification, consultez [Cloud sécurisé d’autorité de certification.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell une identité Cloud Access Manager v7.1 
Gestionnaire d’accès de nuage de Dell une identité implémente la norme d’identité de fédération de WS/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office, CRM |  Prise en charge |Aucun|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur Dell un nuage accès Gestionnaire d’identité, consultez [Gestionnaire d’accès Dell une identité nuage.](http://software.dell.com/products/cloud-access-manager)

 Pour obtenir des instructions sur la façon de configurer cette STS fournir l’expérience d’ouverture de session unique à vos utilisateurs d’Office 365, reportez-vous à la section [configurer les utilisateurs d’Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>Ouverture de session unique de AuthAnvil 4.5 
AuthAnvil seul signe sur 4.5 implémente la norme d’identité WS fédération/WS-Trust largement utilisée pour fournir un unique et la structure d’attribut d’exchange.

Voici la matrice de prise en charge de scénario de cette expérience d’ouverture de session unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que Microsoft Exchange Web Access et SharePoint Online | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Applications clientes telles que Lync, abonnement Office, CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge.|
| Clients riches en messagerie électronique comme Outlook et ActiveSync |  Prise en charge |Aucun|


Pour plus d’informations, consultez [AuthAnvil Single Sign On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
