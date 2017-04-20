<Properties
    pageTitle="Accès conditionnel à Active Directory Azure | Microsoft Azure"  
    description="Utilisez le contrôle d’accès conditionnel dans Azure Active Directory pour vérifier des conditions spécifiques lors de l’authentification pour l’accès aux applications."  
    services="active-directory"
    keywords="accès conditionnel à des applications, l’accès conditionnel avec AD Azure, un accès sécurisé aux ressources de l’entreprise, les stratégies d’accès conditionnel"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Accès conditionnel dans Azure Active Directory   

Les fonctionnalités de contrôle de l’accès conditionnel d’Azure Active Directory (AD Azure) offrent des méthodes simples pour aider à sécuriser les ressources dans le nuage et sur site. Les stratégies d’accès conditionnel telles que l’authentification à plusieurs facteurs peut vous protéger contre le risque de vol et les informations d’identification de récoltées. Autres stratégies d’accès conditionnel peuvent aider à protéger les données de votre organisation. Par exemple, en plus des informations d’identification, peuvent avoir une stratégie que seuls périphériques qui sont inscrits dans un système de gestion de dispositif mobile comme Microsoft Intune peut accéder aux services sensibles de votre entreprise.


## <a name="prerequisites"></a>Conditions préalables

Azure accès conditionnel d’Active Directory est une fonctionnalité de [Azure Active Directory prime](http://www.microsoft.com/identity). Chaque utilisateur qui accède à une application qui a des stratégies d’accès conditionnel appliqués doit avoir une licence Azure AD Premium. Pour plus d’informations sur les conditions de licence dans le [rapport sur les utilisateurs sans licence](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>Comment le contrôle d’accès conditionnel est appliqué ?  

Avec contrôle d’accès conditionnel en place, AD Azure vérifie des conditions spécifiques que vous définissez pour un utilisateur d’accéder à une application. Une fois que les exigences d’accès sont remplies, l’utilisateur est authentifié et peut accéder à l’application.  

![Vue d’ensemble de l’accès conditionnel](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Conditions

Voici les conditions que vous pouvez inclure dans une stratégie d’accès conditionnel :

- **L’appartenance au groupe**. Contrôler l’accès d’un utilisateur selon l’appartenance à un groupe.

- **Emplacement**. Utilisez l’emplacement de l’utilisateur d’authentification à plusieurs facteurs déclencheur et contrôles de bloc lorsqu’un utilisateur n’est pas sur un réseau approuvé.

- **Plate-forme de périphérique**. Utiliser la plate-forme de périphérique, par exemple l’iOS, Android, Windows Mobile ou Windows, en tant que condition pour appliquer la stratégie.

- **Périphérique activé**. État du périphérique, activé ou désactivé, est validé lors de l’évaluation de stratégie de périphérique. Si vous désactivez un périphérique dans le répertoire perdu ou volé, il peut ne répondent plus aux exigences de la stratégie.

- **Risque d’ouverture de session et de l’utilisateur**. Vous pouvez utiliser la [Protection d’identité AD Azure](active-directory-identityprotection.md) pour les stratégies de risques d’accès conditionnel. Stratégies de risque d’accès conditionnel conférer à protection d’avance de votre organisation basée sur les événements de risque et reconnectez-vous activités inhabituelles.


## <a name="controls"></a>Contrôles

Il s’agit de contrôles que vous pouvez utiliser pour appliquer une stratégie d’accès conditionnel :

- **Authentification à plusieurs facteurs**. Vous pouvez exiger une authentification renforcée par l’authentification multi-facteurs. Vous pouvez utiliser plusieurs facteurs d’authentification avec une authentification à facteurs multiples Azure, ou à l’aide d’un fournisseur d’authentification à plusieurs facteurs sur site, combiné avec Active Directory Federation Services (ADFS). À l’aide de l’authentification multicritères, vous aide à protéger les ressources contre l’accès par un utilisateur non autorisé qui peut ont obtenu l’accès pour les informations d’identification d’un utilisateur valide.

- **Bloc**. Vous pouvez appliquer des conditions comme emplacement de l’utilisateur pour bloquer l’accès de l’utilisateur. Par exemple, vous pouvez bloquer l’accès lorsqu’un utilisateur n’est pas sur un réseau approuvé.

- **Périphériques compatibles**. Vous pouvez définir des stratégies d’accès conditionnel au niveau du périphérique. Vous pouvez définir une stratégie afin qu’uniquement les ordinateurs qui sont joints au domaine, ou des périphériques mobiles qui sont inscrits dans une application de gestion des appareils mobiles, peuvent accéder aux ressources de votre organisation. Par exemple, vous pouvez Intune permet de vérifier la conformité du dispositif et signaler à Azure AD pour l’application lorsque l’utilisateur tente d’accéder à une application. Pour obtenir des instructions détaillées sur l’utilisation des Intune pour protéger les données et les applications, reportez-vous à la section [protéger les applications et les données avec Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Vous pouvez également utiliser Intune pour appliquer la protection des données pour les appareils perdus ou volés. Pour plus d’informations, consultez [l’aide de protéger vos données avec balayage complet ou sélectif à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Applications

Vous pouvez appliquer une stratégie d’accès conditionnel au niveau de l’application. Définir des niveaux d’accès pour les applications et les services dans le nuage ou sur site. La stratégie est appliquée directement sur le site Web ou un service. La stratégie est appliquée pour l’accès au navigateur et à des applications qui accèdent au service.


## <a name="device-based-conditional-access"></a>Basé sur un dispositif d’accès conditionnel

Vous pouvez limiter l’accès aux applications à partir de périphériques qui sont inscrites avec AD Azure, et qui répondent à certaines conditions. Basé sur un dispositif d’accès conditionnel protège les ressources d’une organisation des utilisateurs qui tentent d’accéder aux ressources à partir de :

- Périphériques inconnus ou non managés.
- Les périphériques qui ne respectent pas les stratégies de sécurité votre organisation configurée.

Vous pouvez définir des stratégies en fonction de ces exigences :

- **Les périphériques associés à un domaine**. Définir une stratégie pour restreindre l’accès à des périphériques qui sont joints à un domaine d’Active Directory sur site, et qui sont également inscrits avec AD Azure. Cette stratégie s’applique aux ordinateurs de bureau Windows, les ordinateurs portables et les tablettes de l’entreprise.
Pour plus d’informations sur la configuration de l’enregistrement automatique de périphériques à un domaine AD Azure, consultez [définir l’enregistrement automatique de Windows à un domaine de périphériques avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Périphériques compatibles**. Définir une stratégie pour restreindre l’accès à des périphériques qui sont marqués comme **conformes** dans le répertoire du système de gestion. Cette stratégie garantit que seuls les périphériques répondant aux stratégies de sécurité comme la mise en œuvre du cryptage de fichiers sur un périphérique sont autorisés à accéder. Vous pouvez utiliser cette stratégie pour restreindre l’accès à partir des périphériques suivants :

    - **Les périphériques associés à un domaine Windows**. Gérés par System Center Configuration Manager (dans la branche actuels) déployé dans une configuration hybride.
    - **Travail de Windows 10 Mobile ou des périphériques personnels**. Géré par Intune ou par un système de gestion de périphérique mobile de tiers pris en charge.
    - les **périphériques iOS ou Android**. Géré par Intune.


Les utilisateurs qui accèdent aux applications qui sont protégées par des périphériques, stratégie d’autorité de certification doit accéder à l’application à partir d’un périphérique qui répond aux exigences de cette stratégie. L’accès est refusé si tentée sur un périphérique qui ne répond pas aux exigences de la stratégie.

Pour plus d’informations sur la configuration d’une stratégie d’autorité de certification basée sur le périphérique, dans Active Directory Azure, consultez [définir une stratégie basée sur un dispositif d’accès conditionnel pour applications connectées Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Ressources

Consultez les catégories de ressources et les articles pour en savoir plus sur la configuration d’un accès conditionnel pour votre organisation ci-dessous.


### <a name="multi-factor-authentication-and-location-policies"></a>Stratégies d’authentification et l’emplacement selon plusieurs facteurs

- [Mise en route avec accès conditionnel à Azure applications AD-connecté basée sur le groupe, l’emplacement et les stratégies d’authentification à plusieurs facteurs](active-directory-conditional-access-azuread-connected-apps.md)
- [Applications qui sont prises en charge](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Basé sur un dispositif d’accès conditionnel

- [Définir une stratégie de contrôle d’accès aux applications connectées Azure Active Directory basé sur un dispositif d’accès conditionnel](active-directory-conditional-access-policy-connected-applications.md)
- [Définir l’enregistrement automatique de Windows à un domaine de périphériques avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Dépannage des problèmes d’accès Azure Active Directory](active-directory-conditional-access-device-remediation.md)
- [Protéger les données sur les appareils perdus ou volés à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Protéger les ressources en fonction de l’ouverture de session risque

-   [Protection de l’identité Active Directory Azure](active-directory-identityprotection.md)

### <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel Foire aux questions](active-directory-conditional-faqs.md)
- [Guide de référence technique](active-directory-conditional-access-technical-reference.md)
