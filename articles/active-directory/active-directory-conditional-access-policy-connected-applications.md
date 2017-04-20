<properties
    pageTitle="Définir une stratégie basée sur un dispositif d’accès conditionnel pour applications connectées Azure Active Directory | Microsoft Azure"
    description="Définir des stratégies basées sur un dispositif d’accès conditionnel pour des applications Azure connecté à Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Définir une stratégie basée sur un dispositif d’accès conditionnel pour applications connectées Azure Active Directory


Accès Azure Active Directory (AD Azure) basé sur un dispositif conditionnelle peut vous aider à protéger les ressources d’entreprise à partir de :

- Les tentatives d’accès à partir de périphériques inconnus ou non managés.
- Périphériques qui ne respectent pas les stratégies de sécurité de votre organisation.

Pour une vue d’ensemble de l’accès conditionnel, consultez [accès conditionnel d’Azure Active Directory](active-directory-conditional-access.md).

Vous pouvez définir des stratégies basées sur un dispositif d’accès conditionnel afin de protéger ces applications :

- Office 365 SharePoint Online, pour protéger les documents et les sites de votre organisation
- Office 365 Exchange Online, pour protéger les e-mails de votre entreprise
- Logiciel comme une application de service (SaaS) qui sont connectés à Azure AD pour l’authentification
- Les applications qui sont publiées à l’aide des services de Proxy d’Application Azure AD local

Pour définir une stratégie basée sur un dispositif d’accès conditionnel dans le portail Azure, accédez à l’application spécifique dans le répertoire.


  ![Liste des applications dans le répertoire de portail Azure] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")


Sélectionnez l’application, puis cliquez sur l’onglet **configuration** pour définir la stratégie d’accès conditionnel.  


  ![Configurer l’application] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Les règles d’accès basés sur des périphériques")




Pour définir une stratégie basée sur un dispositif d’accès conditionnel, dans la section **périphérique en fonction des règles d’accès** , **Activez les règles d’accès**, sélectionnez **sur**.

Une stratégie basée sur un dispositif d’accès conditionnel comporte trois composants :

- **S’applique à**. L’étendue de la stratégie s’applique à des utilisateurs.

- **Règles de périphériques**. Les conditions avant d’accéder à l’application, un périphérique doit respecter.

- **Mise en œuvre de l’application**. Les applications clientes (natif ou navigateur) la stratégie s’applique à.

  ![Les trois composants d’une stratégie d’accès basé sur le périphérique] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Les règles d’accès basés sur des périphériques")


## <a name="select-the-users-the-policy-applies-to"></a>Sélectionnez les utilisateurs que la stratégie s’applique à

Dans la section **Appliquer à** , vous pouvez sélectionner la portée de cette stratégie s’applique à des utilisateurs.

Vous avez deux options lorsque vous créez une étendue de stratégie d’accès pour les utilisateurs :

- **Tous les utilisateurs**. Appliquer la stratégie à tous les utilisateurs qui accèdent à l’application.

- **Groupes**. Limiter la stratégie pour les utilisateurs qui sont un membre d’un groupe spécifique.

![Appliquer la stratégie à tous les utilisateurs ou à un groupe] (./media/active-directory-conditional-access-policy-connected-applications/11.png "S’applique à")


 Pour exclure un utilisateur d’une stratégie, activez la case à cocher **à l’exception** . Ceci est utile lorsque vous devez accorder des autorisations à un utilisateur spécifique à accéder temporairement à l’application. Sélectionnez cette option, par exemple, si certains de vos utilisateurs ont des périphériques qui ne sont pas prêtes pour l’accès conditionnel. Les périphériques ne soit pas encore enregistrés, ou qu’ils sont sur le point d’être non conformes.


## <a name="select-the-conditions-that-devices-must-meet"></a>Sélectionnez les dispositifs doivent satisfaire aux conditions

Utiliser les **Règles de périphériques** pour définir les conditions à un périphérique doit respecter pour accéder à l’application.

Vous pouvez définir des périphériques d’accès conditionnel à ces types de périphériques :

- Mise à jour de Windows 10 anniversaire, Windows 8.1 et Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2
- périphériques d’e/s (iPad, iPhone)
- Appareils Android

Prise en charge pour Mac est bientôt disponible.

  ![Appliquer la stratégie aux périphériques] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")

 >[AZURE.NOTE] Pour plus d’informations sur les différences entre les périphériques AD-joint à un domaine et Azure, voir [les périphériques à l’aide de Windows 10 dans votre espace de travail](active-directory-azureadjoin-windows10-devices.md).

Vous avez deux options pour les règles de périphériques :

- **Tous les périphériques doivent être conformes**. Toutes les plates-formes de périphérique qui accèdent à l’application doivent être conformes. Les périphériques qui s’exécutent sur des plates-formes qui ne prennent pas en charge des périphériques d’accès conditionnel sont refusés.

- **Seuls les périphériques sélectionnés doivent être conformes**. Uniquement les plates-formes de périphérique spécifique doivent être conformes. Autres plates-formes ou autres plates-formes qui peuvent accéder à l’application, ont accès.

  ![Définir l’étendue pour les règles de périphériques] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")

Périphériques de joints AD Azure sont compatibles s’ils sont marqués comme étant **conformes** dans le répertoire par Intune ou par un système de gestion de périphérique mobile de tiers qui s’intègre à Active Directory Azure.

Un périphérique à un domaine est conforme si :

- Elle est inscrite avec AD Azure. De nombreuses organisations traitent les périphériques associés à un domaine en tant que périphériques approuvés.
- Il est marqué comme **étant conformes** dans Azure AD par 2016 de System Center Configuration Manager.

 ![Qui sont compatibles avec les périphériques à un domaine] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Règles de périphériques")

Dispositifs personnels de Windows sont compatibles s’ils sont marqués comme étant **conformes** dans le répertoire par Intune ou par un système de gestion de périphérique mobile de tiers qui s’intègre à Active Directory Azure.

Périphériques non-Windows sont compatibles s’ils sont marqués comme étant **conformes** dans le répertoire par Intune.

 >[AZURE.NOTE] Pour plus d’informations sur comment configurer AD Azure de mise en conformité de l’équipement dans les systèmes de gestion, consultez [Azure Active Directory l’accès conditionnel](active-directory-conditional-access.md).


Vous pouvez sélectionner une ou plusieurs plates-formes de périphérique pour une stratégie d’accès basé sur le périphérique. Cela inclut les Android, iOS, Windows Mobile (Windows 8.1 téléphones et des tablettes) et Windows (tous les autres périphériques Windows, y compris tous les périphériques Windows 10).
Évaluation de la stratégie se produit uniquement sur les plates-formes sélectionnées. Si un périphérique tente d’accéder n’exécute pas une des plateformes sélectionnées, le périphérique peut accéder à l’application si l’utilisateur a accès. Aucune stratégie de périphérique n’est évalué.

![Sélectionnez les plates-formes pour les règles de périphériques] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Règles de périphériques")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Définir l’évaluation d’une stratégie pour un type d’application

Dans la section de **Mise en œuvre de l’Application** , définissez le type d’applications évalue la stratégie d’accès utilisateur ou périphérique.

Vous avez deux options pour le type d’application à inclure :

- Navigateur et les applications natives
- Applications natives

![Choisissez navigateur ou des applications natives] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")

Pour appliquer la stratégie d’accès pour les applications, sélectionnez le **navigateur et les applications natives**. Ensuite, vous pouvez inclure :

- Navigateurs (par exemple, Microsoft Edge Windows 10) ou Safari dans iOS.
- Applications qui utilisent la bibliothèque de l’authentification Active Directory (ADAL) sur n’importe quelle plate-forme, ou qui utilisent la WebAccountManager (WAM) API dans Windows 10.

>[AZURE.NOTE] Pour plus d’informations sur la prise en charge de navigateur et d’autres considérations pour un utilisateur qui accède à un périphérique basé sur, applications protégé par l’autorité de certificat, consultez [accès conditionnel d’Azure Active Directory](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Protéger l’accès de courrier électronique à partir d’applications Exchange ActiveSync

Dans les applications Office 365 Exchange Online, vous pouvez utiliser Exchange ActiveSync pour bloquer l’accès de messagerie aux applications de messagerie basés sur Exchange ActiveSync.

![Options de conformité d’Exchange ActiveSync] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")

![Nécessite un périphérique compatible pour accéder aux e-mails] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")


## <a name="next-steps"></a>Étapes suivantes

- [Azure accès conditionnel de Active Directory](active-directory-conditional-access.md)
