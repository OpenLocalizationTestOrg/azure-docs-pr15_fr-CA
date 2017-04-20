<properties
    pageTitle="Vue d’ensemble de l’enregistrement de Active Directory périphérique Azure | Microsoft Azure"
    description="est la base pour les scénarios basés sur un dispositif d’accès conditionnel. Lorsqu’un périphérique est enregistré, enregistrement de dispositif Azure Active Directory met le périphérique avec une identité qui sert à authentifier le périphérique lorsque l’utilisateur se connecte."
    services="active-directory"
    keywords="enregistrement de dispositif, activer, dispositif d’enregistrement, l’enregistrement de dispositifs et MDM"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="Markvi"/>

# <a name="get-started-with-azure-active-directory-device-registration"></a>Mise en route de l’enregistrement de dispositif Azure Active Directory

Azure inscription de périphérique Active Directory est la base pour les scénarios basés sur un dispositif d’accès conditionnel. Lorsqu’un périphérique est inscrit, inscription de périphérique Azure Active Directory fournit le périphérique avec une identité qui sert à authentifier le périphérique lorsque l’utilisateur se connecte. Le périphérique authentifié et les attributs du périphérique, puis utilisable pour appliquer des stratégies d’accès conditionnel pour les applications qui sont hébergées dans le cloud et sur site.

Lorsqu’elle est combinée avec une solution de management(MDM) de périphérique mobile comme Microsoft Intune, les attributs du périphérique dans Azure Active Directory sont mis à jour avec des informations supplémentaires sur le périphérique. Cela vous permet de créer des règles d’accès conditionnel qui permettent d’accéder à partir de périphériques pour répondre à vos normes de sécurité et de conformité. Pour plus d’informations sur l’inscription de périphériques dans Microsoft Intune, consultez [inscrire les périphériques pour la gestion de Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Scénarios permis par enregistrement de dispositif Azure Active Directory

Azure inscription de périphérique Active Directory inclut la prise en charge pour iOS, Android et Windows périphériques. Les scénarios individuels qui utilisent l’enregistrement de dispositifs de publicité Azure peuvent avoir des exigences et plate-forme prise en charge plus spécifiques. Ces scénarios sont les suivants :

- **Accès conditionnel aux applications qui sont hébergées sur site**: vous pouvez utiliser des périphériques avec les stratégies d’accès pour les applications qui sont configurées pour utiliser ADFS avec Windows Server 2012 R2. Pour plus d’informations sur la configuration d’un accès conditionnel pour sur site, consultez [Configuration des accès conditionnel en local à l’aide d’Azure Active Directory périphérique d’enregistrement](active-directory-conditional-access-on-premises-setup.md).

- **Accès conditionnel pour les applications d’Office 365 avec Microsoft Intune** : IT admins peut configurer des stratégies de périphérique d’accès conditionnel pour sécuriser les ressources de l’entreprise, tout en permettant des travailleurs de l’information sur les périphériques compatibles pour accéder aux services. Pour plus d’informations, reportez-vous à la section [Conditionnelle stratégies de périphérique d’accès pour les services d’Office 365](active-directory-conditional-access-device-policies.md).

##<a name="setting-up-azure-active-directory-device-registration"></a>Configuration d’enregistrement de dispositif Azure Active Directory

Vous devez activer l’enregistrement de dispositif de publicité d’Azure dans le portail Azure afin que les périphériques mobiles peuvent découvrir le service en recherchant des enregistrements DNS connus. Vous devez configurer votre société DNS afin que les périphériques Windows 10, 8.1 de Windows, Windows 7, Android et iOS peuvent découvrir et utiliser le service.
Vous pouvez afficher et activer ou désactiver des périphériques à l’aide du portail d’administrateur dans Azure Active Directory.

>[AZURE.NOTE]
 Pour des instructions plus récentes sur la configuration d’enregistrement de dispositif automatique, consultez [comment configurer l’enregistrement automatique du domaine Windows joint périphériques avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

### <a name="enable-azure-active-directory-device-registration-service"></a>Activer le Service d’enregistrement de dispositif Azure Active Directory

1. Ouvrez une session sur le portail Microsoft Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet **répertoire** , sélectionnez le répertoire.
4. Sélectionnez l’onglet **configurer** .
5. Faites défiler jusqu'à la section **périphériques**.
6. Sélectionnez **tous les** **utilisateurs peuvent les périphériques de jointure poste de travail**.
7. Sélectionnez le nombre maximal de périphériques que vous souhaitez autoriser par utilisateur.

>[AZURE.NOTE]
>Inscription avec Microsoft Intune ou de gestion des périphériques mobiles pour Office 365 nécessite la jointure de l’espace de travail. Si vous avez configuré une ou l’autre de ces services, tout est sélectionné et le bouton NONE est désactivé.

Par défaut, l’authentification à deux facteurs n’est pas activée pour le service. Toutefois, l’authentification à deux facteurs est recommandée lors de l’enregistrement d’un périphérique.

- Avant de nécessiter une authentification à deux facteurs pour ce service, vous devez configurer un fournisseur d’authentification à deux facteurs dans Azure Active Directory et configurer vos comptes d’utilisateur pour une authentification à facteurs multiples, reportez-vous à la section [Ajout d’authentification à plusieurs facteurs pour Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Si vous utilisez AD FS avec Windows Server 2012 R2, vous devez configurer un module d’authentification à deux facteurs dans AD FS, voir [L’aide de l’authentification multicritères avec Active Directory Federation Services](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurer la recherche de l’enregistrement de dispositif Azure Active Directory
Périphériques de Windows 7 et Windows 8.1 découvre le service d’enregistrement de dispositif en combinant le nom de compte d’utilisateur avec un nom de serveur d’inscription de périphérique connu.

Vous devez créer un enregistrement DNS CNAME qui pointe vers l’enregistrement A associé à votre service d’enregistrement de dispositif Azure Active Directory. L’enregistrement CNAME doit utiliser l’enterpriseregistration de préfixe bien connu suivie du suffixe UPN utilisé par les comptes d’utilisateurs dans votre organisation. Si votre organisation utilise plusieurs suffixes de noms UPN, plusieurs enregistrements CNAME doivent être créés dans le système DNS.

Par exemple, si vous utilisez deux suffixes UPN à votre organisation nommée @contoso.com et @region.contoso.com, vous allez créer les enregistrements DNS ci-après.

| Entrée                                     | Type de  | Adresse                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | CNAME | enterpriseregistration.Windows.NET |
| enterpriseregistration.Region.contoso.com | CNAME | enterpriseregistration.Windows.NET |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Permet d’afficher et de gérer des objets de périphérique dans Azure Active Directory
1. À partir du portail Azure administrateur, vous pouvez afficher, bloquer et débloquer des périphériques. Un périphérique bloqué n’auront plus accès aux applications qui sont configurés pour autoriser uniquement les appareils inscrits.
2. Ouvrez une session sur le portail Microsoft Azure en tant qu’administrateur.
3. Dans le volet gauche, sélectionnez **Active Directory**.
4. Sélectionnez le répertoire.
5. Sélectionnez l’onglet **utilisateurs** . Puis sélectionnez un utilisateur d’afficher leurs périphériques
6. Sélectionnez l’onglet **périphériques** .
7. Sélectionnez **Les périphériques inscrits** dans le menu déroulant.
8. Vous pouvez afficher ici, bloquer ou débloquer les périphériques des utilisateurs.

## <a name="additional-topics"></a>Rubriques supplémentaires

Vous pouvez enregistrer vos périphériques Windows 7 et le domaine Windows 8.1 avec enregistrement de dispositif de publicité Azure. Les rubriques suivantes fournit plus d’informations sur les conditions requises et les étapes nécessaires pour configurer l’enregistrement de dispositifs sur les périphériques Windows 7 et Windows 8.1.

- [Enregistrement de dispositif automatique avec Azure Active Directory pour les périphériques à un domaine Windows](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique pour appareils de joint au domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurer l’inscription automatique de périphérique pour les périphériques de joint au domaine Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Enregistrement de dispositif automatique avec les périphériques à un domaine d’Azure Active Directory pour Windows 10](active-directory-azureadjoin-devices-group-policy.md)
