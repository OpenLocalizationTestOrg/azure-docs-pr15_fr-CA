<properties
   pageTitle="Azure Active Directory création de rapports : Mise en route | Microsoft Azure"
   description="Répertorie les différents rapports disponibles dans le rapport d’Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Mise en route avec Azure Active Directory Reporting

## <a name="what-it-is"></a>Qu’il est

Azure Active Directory (AD Azure) inclut la sécurité, l’activité et les rapports d’audit de votre annuaire. Voici une liste des États inclus :

### <a name="security-reports"></a>Rapports de sécurité

- Connexions provenant de sources inconnues
- Après plusieurs échecs de connexions
- Connexions de plusieurs régions
- Connexions provenant d’adresses IP avec une activité suspecte
- Activité de connexion irrégulière
- Connexions à partir de périphériques probablement infectés
- Utilisateurs avec l’activité de connexion anormale

### <a name="activity-reports"></a>Rapports d’activité

- Utilisation de l’application : résumé
- Utilisation de l’application : détaillées
- Tableau de bord application
- Erreurs de mise en service de compte
- Périphériques utilisateur individuel
- Activité de l’utilisateur individuelle
- Rapport d’activité de groupes
- Rapport sur les activités d’enregistrement réinitialisation mot de passe
- Activité de réinitialisation de mot de passe

### <a name="audit-reports"></a>Rapports d’audit

- Rapport d’audit de répertoire

> [AZURE.TIP] Pour plus d’informations sur Azure Reporting de publicité, consultez [Afficher vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).



## <a name="how-it-works"></a>Mode de fonctionnement


### <a name="reporting-pipeline"></a>Pipeline de génération de rapports

Le pipeline de génération de rapports se compose de trois étapes principales. Chaque fois qu’un utilisateur se connecte, ou si une authentification est effectuée, les événements suivants se produisent :

- Tout d’abord, l’utilisateur est authentifié (correctement ou non), et le résultat est stocké dans les bases de données du service Active Directory de Azure.
- À intervalles réguliers, signe de récentes tous les modules sont traités. À ce stade, notre sécurité et les algorithmes d’activité anormale Rechercher tout signe récente ins toute activité suspecte.
- Après le traitement, les rapports sont écrits, mis en cache et pris en charge dans Azure portal classique.

### <a name="report-generation-times"></a>Temps de génération de rapports

En raison du volume important d’authentifications et signer des modules traités par la plateforme Azure AD, les signe plus récentes-ins traités sont, en moyenne, datant d’une heures. Dans de rares cas, il peut prendre jusqu'à 8 heures pour traiter les connexions plus récentes.

Vous pouvez trouver la plus récente traitée reconnectez-vous en examinant le texte d’aide en haut de chaque rapport.

![Texte d’aide en haut de chaque rapport.](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Pour plus d’informations sur Azure Reporting de publicité, consultez [Afficher vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).



## <a name="getting-started"></a>Mise en route


### <a name="sign-into-the-azure-classic-portal"></a>Connectez-vous au portail classique Azure

Tout d’abord, vous devez vous connecter à l' [Azure portal classique](https://manage.windowsazure.com) comme global ou l’administrateur de la conformité. Vous doit également être un administrateur du service d’abonnement Azure ou un administrateur de collaboration, ou être à l’aide de le « accès à AD Azure » abonnement Azure.

### <a name="navigate-to-reports"></a>Accédez aux rapports

Pour afficher les rapports, accédez à l’onglet Rapports dans la partie supérieure de votre répertoire.

S’il s’agit d’affichage des rapports pour la première fois, vous devez accepter avant de pouvoir afficher les rapports d’une boîte de dialogue. C’est pour vous assurer qu’il est acceptable pour les administrateurs de votre organisation pour afficher ces données, qui peuvent être considérées comme des informations confidentielles dans certains pays.

![Boîte de dialogue](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Explorez chaque état

Naviguer dans chaque rapport pour voir les données collectées et les connexions de traitement. Vous trouverez une [liste de tous les rapports ici](active-directory-reporting-guide.md).

![Tous les rapports](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Télécharger les rapports au format CSV

Chaque rapport peut être téléchargé sous la forme d’un fichier CSV (valeurs séparées par des virgules). Vous pouvez utiliser ces fichiers dans Excel, de PowerBI ou d’analyse tiers programmes complémentaire analyser vos données.

Pour télécharger un rapport sous la forme d’un fichier CSV, accédez à l’état et cliquez sur « Télécharger » en bas.

![Bouton de téléchargement](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Pour plus d’informations sur Azure Reporting de publicité, consultez [Afficher vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).





## <a name="next-steps"></a>Étapes suivantes

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personnaliser les alertes pour signe d’anomalie dans l’activité

Accédez à l’onglet « Configuration » de votre répertoire.

Faites défiler jusqu'à la section « Notifications ».

Activer ou désactiver la section « Courrier électronique des Notifications de connexions anormale ».

![La section de Notifications](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Intégrer la publicité Azure Reporting API

Reportez-vous à la section [mise en route avec l’API de création de rapports](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Engager une authentification multifactorielle sur les utilisateurs

Sélectionnez un utilisateur dans un rapport.

Cliquez sur le bouton « Activer AMF » en bas de l’écran.

![Le bouton d’authentification à plusieurs facteurs au bas de l’écran](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Pour plus d’informations sur Azure Reporting de publicité, consultez [Afficher vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).




## <a name="learn-more"></a>Pour en savoir plus


### <a name="audit-events"></a>Événements d’audit

Obtenir des informations sur les événements qui sont audités dans le répertoire dans [Azure Active Directory rapport d’événements d’Audit](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Intégration de l’API

Consultez la [documentation de référence API](https://msdn.microsoft.com/library/azure/mt126081.aspx)et [mise en route avec l’API de création de rapports](active-directory-reporting-api-getting-started.md) .

### <a name="get-in-touch"></a>Obtenir en contact

E-mail [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) pour l’évaluation, l’aide ou pour toute question, vous pouvez avoir.

> [AZURE.TIP] Pour plus d’informations sur Azure Reporting de publicité, consultez [Afficher vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md).
