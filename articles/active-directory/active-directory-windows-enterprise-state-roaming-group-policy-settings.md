<properties
    pageTitle="Les paramètres de stratégie et MDM de groupe | Microsoft Azure"
    description="Fournit des informations sur l’appareil mobile et de la stratégie de groupe des paramètres de gestion (MDM) qui doivent être utilisés sur des périphériques appartenant à l’entreprise. Ces stratégies sont appliquées à l’ensemble de l’unité de l’utilisateur."
    services="active-directory"
    keywords="Quels sont les paramètres de groupe stratégie et MDM itinérants d’état entreprise, entreprise état d’itinérance, nuage de windows"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Paramètres de stratégie de groupe et MDM

Utilisez ces paramètres (MDM) de gestion des périphériques mobiles et stratégie de groupe uniquement sur les périphériques appartenant à l’entreprise, étant donné que ces stratégies sont appliquées à l’ensemble de l’unité de l’utilisateur. Application d’une stratégie Mobile Device Manager pour désactiver la synchronisation des paramètres pour un personnel, appartenant à l’utilisateur de périphérique sera un impact négatif sur l’utilisation de ce périphérique. En outre, autres comptes d’utilisateur sur le périphérique seront également affectées par la stratégie.

Entreprises qui souhaitent gérer l’itinérance de vos périphériques personnels (non managés) peuvent utiliser le portail Azure pour activer ou désactiver l’itinérance, et non à l’aide de la stratégie de groupe ou MDM.
Les tableaux suivants décrivent les paramètres de stratégie disponibles.

## <a name="mdm-settings"></a>Paramètres de MDM
Les paramètres de stratégie MDM s’appliquent à la fois Windows 10 et 10 de Windows Mobile.  Prise en charge de Windows 10 Mobile existe uniquement pour Microsoft compte itinérant via le compte de l’utilisateur OneDrive.  Reportez-vous à la section « Périphériques et points de terminaison » pour plus d’informations sur les périphériques qui sont pris en charge pour la synchronisation de Azure annonce en fonction.

| Nom                               | Description                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Autoriser la connexion du compte Microsoft | Permet aux utilisateurs de s’authentifier à l’aide d’un compte de Microsoft sur le périphérique |
| Permettre à mes paramètres de synchronisation             | Permet aux utilisateurs de passer des paramètres de Windows et les données de l’application ; La désactivation de cette stratégie désactive la synchronisation, ainsi que des sauvegardes sur des périphériques mobiles                  |

## <a name="group-policy-settings"></a>Paramètres de stratégie de groupe
Les paramètres de stratégie de groupe s’appliquent aux périphériques Windows 10 qui sont rattachés à un domaine Active Directory. Le tableau inclut également les paramètres hérités qui apparaîtrait pour gérer les paramètres de synchronisation, mais qui ne fonctionnent pas pour l’entreprise état itinérant pour Windows 10, qui sont marqués avec « n’utilisez pas' dans la description.

| Nom                                | Description |
|-------------------------------------|-------------|
| Comptes : Comptes de Microsoft de bloc  |Ce paramètre de stratégie empêche les utilisateurs d’ajouter de nouveaux comptes Microsoft sur cet ordinateur|
| Ne pas synchroniser                         |Empêche les utilisateurs pour passer des paramètres de Windows et les données d’application|
| Ne pas synchroniser personnaliser             |Désactive la synchronisation du groupe Thèmes|
| Ne pas synchroniser les paramètres de votre navigateur        |Désactive la synchronisation du groupe d’Internet Explorer|
| Ne pas synchroniser les mots de passe               |Désactive la synchronisation de groupe de mots de passe|
| Ne pas synchroniser les autres paramètres Windows  |Désactive la synchronisation du groupe de paramètres des autres fenêtres|
| Ne pas synchroniser la personnalisation du bureau |N’utilisez pas ; n’a aucun effet|
| Ne pas synchroniser les connexions contrôlé  |Désactive l’itinérance sur mesuré de connexions, par exemple des 3G cellulaire|
| Ne pas synchroniser les applications                    |N’utilisez pas ; n’a aucun effet|
|Ne pas synchroniser les paramètres de l’application             |Désactive l’itinérance de données d’application|
|Ne pas synchroniser les paramètres de démarrage           |N’utilisez pas ; n’a aucun effet|


## <a name="related-topics"></a>Rubriques connexes
- [Vue d’ensemble de l’entreprise d’état itinérance](active-directory-windows-enterprise-state-roaming-overview.md)
- [Activer l’état de l’entreprise d’itinérance dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Forum aux questions sur l’itinérance des données et les paramètres](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Référence des paramètres itinérant Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
