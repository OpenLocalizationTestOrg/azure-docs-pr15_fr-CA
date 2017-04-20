<properties
    pageTitle="Dans votre espace de travail à l’aide de périphériques de Windows 10 | Microsoft Azure"
    description="Fournit un instantané des fonctionnalités pour les utilisateurs et informatiques, contraste de différentes façons un périphérique peut être mis en service et utilisé dans une entreprise avec Windows 10."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>À l’aide de périphériques de Windows 10 dans votre espace de travail

S’applique à : Windows 10 PC

Windows 10 offre trois modèles pour les organisations qui permettent aux utilisateurs d’accéder aux ressources de travail de manière sécurisée et pratique.

- **Joindre d’Azure Active Directory** (Jointure de publicité azure), pour les travailleurs qui accèdent à des ressources telles que Office 365 principalement dans le nuage. Azure AD jointure est libre service est nouvelle dans Windows 10 expérience en provisionnement.
- **Jonction de domaine**, pour les organisations qui ont des investissements dans les ressources et les applications sur site. Jonction de domaine offre une meilleure expérience Windows 10 lors de la connexion à Active Directory Azure.
- **Une nouvelle expérience BYOD simplifiée**, pour les utilisateurs qui souhaitent ajouter un compte ou à l’école à Windows et facilement accéder aux ressources de périphériques personnels.

Le tableau suivant présente un instantané des fonctionnalités pour les utilisateurs et les administrateurs informatiques, de comparer les différentes façons un périphérique peut être mis en service et utilisé dans une entreprise avec Windows 10 :

|                                                                                                                                                                 | Jonction de domaine     | Jointure de publicité Azure | DISPOSITIF personnel |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows périphérique reconnectez-vous pour les comptes de travail ou l’école.                                                                                                                      | Oui             | Oui           | N°              |
| Utilisateur unique single-sign-on (SSO) pour les applications Office 365 et Azure AD. SSO est la possibilité de se connecter une seule fois accéder aux ressources de l’organisation. | Oui             | Oui           | Oui             |
| Utilisateur SSO pour les applications Kerberos/NTLM.                                                                                                                                  | Oui             | Limitée       | Oui, via le VPN         |
| Mécanisme d’autorisation sûr et pratique à la connexion pour les comptes de travail ou à l’école avec Microsoft Passport et Windows Hello.                                                                   | Oui             | Oui           | Oui             |
| Accès à Windows Store d’entreprise avec un compte de travail ou de l’école (et non un compte Microsoft).                                                                                    | Oui             | Oui           | Oui             |
| Compatible entreprise itinérant des paramètres utilisateur sur les périphériques avec des comptes de travail ou l’école.                                                                                 | Oui             | Oui           | Oui             |
| La possibilité de restreindre l’accès aux applications d’organisation pour les périphériques qui sont compatibles avec les stratégies d’unité d’organisation.                                                      | Oui             | Oui           | Oui             |
| Utilisateur libre-service de mise en service de dispositifs permettant de « travailler n’importe où ».                                                                                                | N°              | Oui           | Oui             |
| Possibilité de gérer les périphériques.                                                                                                                                       | Oui, par l’intermédiaire de GP/SCCM. | Oui           | Oui             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Utilisation des périphériques appartenant à un travail avec Azure AD jointure et domaine de jointure dans Windows 10

Windows 10 offre les périphériques appartenant à un travail d’accéder aux ressources de travail de deux façons :

- Jointure de publicité Azure
- Jonction de domaine

 Les deux peuvent être des options valides selon les besoins et les exigences d’une organisation. Dans certains cas, les organisations peuvent bénéficier de l’activation de ces deux méthodes de déploiement.

## <a name="when-to-use-azure-active-directory-join"></a>Quand utiliser Active Directory Participez à Azure

Jointure de publicité Azure est un nouveau travail de libre-service mise en service de l’expérience dans Windows 10.  Il est destiné aux travailleurs qui accèdent à des ressources de travail Office 365 principalement dans le nuage. Il s’agit d’une solution légère pour configurer les ordinateurs, les tablettes et les téléphones portables de l’entreprise. Les périphériques sont gérés via la gestion des périphériques mobiles, à l’aide de contrôles de cohérence sur les plates-formes Windows.

**Joindre des annonces d’Azure pour une des raisons suivantes**:

- Vous souhaitez activer l’attribution en libre-service des périphériques pour les travailleurs lors de vos déplacements.
- Vous fournissez aux utilisateurs appartenant à un travail de périphériques mobiles comme les Tablet PC et de téléphones portables.
- Vous souhaitez gérer un ensemble d’utilisateurs dans AD Azure et non dans Active Directory, tels que les travailleurs saisonniers, des fournisseurs et des stagiaires.
- Vous souhaitez que fournir des capacités de joindre aux travailleurs à distance avec infrastructure limitée sur site.
- Vous n’avez pas de site Active Directory.

Certaines organisations utiliseront joindre des annonces d’Azure comme le moyen principal de déployer des périphériques appartenant à un travail, particulièrement lors de la migration tout ou partie de leurs ressources vers le nuage. Les organisations hybride avec Active Directory et AD Azure peuvent également choisir de déployer une méthode ou une autre en fonction de l’utilisateur ou le service.

Circonscriptions scolaires et les universités, par exemple, peuvent gérer le personnel dans Active Directory et les stagiaires dans Azure AD. Peut-être que certaines sociétés souhaitent gérer des bureaux distants ou les départements des ventes dans Azure AD. Jointure de publicité Azure et de méthodes de jointure de domaine peuvent servir au sein d’une organisation hybride. Jointure de publicité Azure peut être un complément idéal à la jonction de domaine pour le déploiement de périphériques dans un environnement de travail.

**Si la plus usuelle l’accès aux ressources d’entreprise est via le nuage, votre organisation peut tirer parti des avantages supplémentaires si**:

- Vous pouvez supprimer les dépendances sur l’infrastructure d’identité sur site.
- Vous pouvez simplifier votre modèle de déploiement de périphériques, l’obtention à partir de solutions d’imagerie en permettant la configuration Self-service.
- Vous pouvez utiliser la gestion des périphériques mobiles pour gérer tous vos périphériques sur différentes plates-formes.

Pour plus d’informations sur la jointure de publicité Azure, consultez [extension des fonctionnalités de nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Quand utiliser une jointure du domaine (ou conserver l’utiliser)

Au cours des 15 dernières années, de nombreuses entreprises ont utilisé la jointure de domaines pour connecter des périphériques de travail. Il permet aux utilisateurs de se connecter à leurs périphériques avec leurs travaux de Active Directory ou d’établissement des comptes. Jonction de domaine autorise également pleinement et de manière centralisée GÈRENT ces périphériques. Organisations s’appuient généralement sur les méthodes d’imagerie pour dispositifs de fourniture et souvent utilisent System Center Configuration Manager (SCCM) ou stratégie de groupe (GP) pour les gérer.

**Votre entreprise doit utiliser jointure du domaine (ou conserver l’utiliser), pour les raisons suivantes**:

- Vous avez des applications Win32 déployées pour ces dispositifs qui utilisent NTLM/Kerberos.
- Vous avez besoin de GP ou SCCM/DCM gérer les périphériques.
- Vous souhaitez continuer à utiliser des solutions d’imagerie pour configurer les appareils pour vos employés.

**Jonction de domaine dans Windows 10 vous offre également les avantages suivants lors de la connexion à Active Directory Azure**:

- Une authentification forte liés aux périphériques et pratique à la connexion pour les comptes de travail ou à l’école avec Microsoft Passport et Windows Hello.
- Accès à l’entreprise de Windows Store pour les périphériques qui utilisent de travail ou de l’école de comptes (aucun compte Microsoft requis).
- Compatible entreprise itinérant des paramètres utilisateur sur les périphériques qui utilisent des comptes de travail ou de l’école (aucun compte Microsoft requis).
- La possibilité de restreindre l’accès aux applications d’organisation pour les périphériques qui sont compatibles avec les stratégies d’unité d’organisation.

Pour plus d’informations sur la jointure de publicité Azure, consultez [extension des fonctionnalités de nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Activer la jointure des personnels des équipements de travail ou à l’école

Pour prendre en charge BYOD dans l’entreprise, Windows 10 donne à l’utilisateur la possibilité d’ajouter un compte de travail ou à l’école de leur ordinateur, un Tablet PC ou un téléphone. Une fois que l’utilisateur ajoute un compte de travail ou à l’école, le périphérique est enregistré avec la publicité Azure et éventuellement inscrits dans le système de gestion de périphérique mobile que l’organisation a configuré. Le répertoire affiche ces périphériques 'Enregistré' et « Joint AD Azure ». INFORMATIQUE administraors pouvez appliquer différentes stratégies en fonction de ces informations, en fournissant une touche plus claire sur des périphériques personnels que sur les périphériques appartenant à un travail si vous le souhaitez.

Les utilisateurs peuvent ajouter un travail ou école compte à leur appareil de communication personnel très facilement. Ils peuvent le faire lorsque vous accédez à une application de travail pour la première fois, ou ils peuvent le faire manuellement via le menu Paramètres. Ce compte fournit l’authentification unique pour les ressources de l’organisation.

Pour plus d’informations sur la jointure de publicité Azure, voir [se connecter à un domaine périphériques AD Azure pour Windows 10 rencontre](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Activer la jointure de domaine ou Azure AD

Toutes les méthodes décrites plus haut (ajouter, Azure AD jointure et la jointure de domaines de travail ou l’école compte) ont des points d’entrée dans l’expérience utilisateur de Windows 10. Toutefois, tous requièrent un administrateur activer la fonctionnalité de l’infrastructure avant d’utiliser l’expérience.

## <a name="requirements-for-deploying-azure-ad-join"></a>Exigences pour le déploiement de joindre des annonces d’Azure

Pour déployer le joindre de publicité Azure pour n’importe quel ensemble d’utilisateurs, vous devez les éléments suivants :

- Un abonnement Azure AD.
- Un abonnement Azure AD Premium, par exemple d’appareil mobile gestion d’inscription automatique, si vous avez besoin de davantage de fonctions.
- Gestion des périphériques mobiles--par exemple, un abonnement Microsoft Intune, gestion des périphériques mobiles pour Office 365 ou un des fournisseurs de gestion de périphérique mobile partenaire qui s’intègrent à AD Azure. (Voir la [section Forum aux questions](#frequently-asked-questions) à la fin de cet article pour plus d’informations).

Si vos sites sont hybride, que nous recommandons vivement que vous déployez Azure Connect d’Active Directory pour étendre le répertoire local pour Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Conditions d’utilisation de jonction de domaine avec Active Directory Azure

Jonction de domaine continue à fonctionner comme il a toujours. Toutefois, pour obtenir les avantages de la publicité Azure, vous devrez les éléments suivants :

- Un abonnement Azure AD.
- Un déploiement d’Azure Connect AD pour étendre le répertoire local à Active Directory Azure.
- Une stratégie qui permet aux périphériques de joint au domaine d’avoir un accès conditionnel à AD Azure.
- Une stratégie qui autorise l’accès aux périphériques de « domaine » si vous souhaitez être en mesure de restreindre l’accès de certains périphériques.
- System Center Configuration Manager version 1509 pour l’aperçu technique, pour activer les règles d’exigence de périphériques compatibles. (Voir la documentation de TechNet et valider de blog).

Pour plus d’informations sur la jointure de domaines Windows 10, voir [se connecter à un domaine périphériques AD Azure pour Windows 10 rencontre](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Configuration requise pour utiliser BYOD et « Ajouter un compte de travail ou de l’école »

Pour activer « apportez votre propre appareil » (BYOD) avec les comptes de l’école ou de travail, vous devez les éléments suivants :

- Un abonnement Azure AD.
- Un abonnement Azure AD Premium, par exemple d’appareil mobile gestion d’inscription automatique, si vous avez besoin de davantage de fonctions.

## <a name="requirements-for-using-microsoft-passport"></a>Configuration requise pour utiliser Microsoft Passport

Pour permettre à Microsoft Passport, vous devrez les éléments suivants :

- Une infrastructure à clé publique (PKI) pour la prise en charge de l’authentification basée sur certificat qui utilise Microsoft Passport.
- Un abonnement Intune pour la prise en charge de l’authentification basée sur certificat qui utilise Microsoft Passport pour joindre des annonces d’Azure et comptes de travail ou l’école.
- Version 1509 pour la version bêta de System Center Configuration Manager (voir la documentation de TechNet et blog valider) pour la prise en charge de l’authentification basée sur certificat qui utilise Microsoft Passport pour la jonction de domaine.
- Une stratégie d’activation de Microsoft Passport dans l’organisation.

Comme alternative à l’utilisation d’une infrastructure à clé publique, vous pouvez activer la clé Microsoft Passport en procédant comme suit :

- Déployer des contrôleurs de domaine Windows Server 2016 « Aperçu de Production 1 » (pas nécessaire pour les niveaux fonctionnels de domaine ou une forêt, plusieurs contrôleurs de domaine pour le service de redondance que chaque site Active Directory suffira).
- Définir la stratégie pour activer Microsoft Passport dans l’organisation.

Pour plus d’informations sur Microsoft Passport et Windows Hello dans Windows 10, reportez-vous à la section < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Forum aux questions
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Les produits de gestion de périphérique mobile partenaire intégrant avec Active Directory Azure ?

Les produits des fournisseurs suivants s’intègrent avec AD Azure pour inscription unifiée et à accès conditionnel dans Windows 10 :

- AirWatch par VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Gestion des périphériques mobiles sur site SOTI
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Qu’en est-il de travail participer dans Windows 10 ?
Jointure de l’espace de travail a été utilisé dans Windows 8.1 pour activer BYOD. Dans Windows, 10, BYOD est activée via les « Ajouter un travail ou l’école compte » comme expliqué plus haut dans ce document. Pour les organisations qui ne s’intègrent leur gestion des périphériques mobiles AD Azure, les utilisateurs peuvent s’inscrire le périphérique dans manuellement via les **paramètres de**gestion des > **comptes** > **accès de travail**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>Les utilisateurs peuvent se connecter leur compte Microsoft pour leur compte de domaine dans Windows 10 ?
Pas dans Windows 10. Point 8.1 de Windows, les utilisateurs d’appareils de joints au domaine peuvent « se connecter » leur compte Microsoft (par exemple, Hotmail, Live, Outlook, Xbox, etc.) à leur compte de domaine pour activer certaines expériences telles que l’authentification unique pour les services Windows Live, utilisation du Windows Store et itinérance des paramètres utilisateur sur les périphériques. Dans Windows, 10, le compte de Microsoft « se connecter » fonctionnalités a été supprimé. L’utilisateur peut ajouter un ou plusieurs comptes de Microsoft en tant que comptes supplémentaires pour permettre l’authentification unique de services aux consommateurs tels que le Windows Store. Cela s’effectue dans les **paramètres** > **comptes** > **votre compte**.

Les utilisateurs qui sont mise à niveau à partir de périphériques de Windows 8.1 à un domaine, et qui avait son compte Microsoft connectée, auront automatiquement leur compte connecté à Microsoft ajouté à la liste des comptes supplémentaires, qu’ils utilisent.


## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : méthodes d’utilisation de périphériques pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du nuage pour périphériques Windows 10 par le biais de joindre de Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour joindre des annonces d’Azure](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des périphériques d’à un domaine à AD Azure pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Paramétrage de joindre des annonces d’Azure](active-directory-azureadjoin-setup.md)
