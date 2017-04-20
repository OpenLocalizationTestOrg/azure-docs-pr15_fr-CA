
<properties
    pageTitle="À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS | Microsoft Azure"
    description="Il s’agit de la page de publicité Azure se connecter la santé qui va expliquer comment analyser les services AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>À l’aide d’Active Directory Azure se connecter la santé avec les services AD DS
La documentation suivante est spécifique à la surveillance des Services de domaine Active Directory avec l’état connexion Azure AD. Les versions prises en charge des services AD DS sont : Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

Pour plus d’informations sur la surveillance AD FS avec état de connexion AD Azure, consultez [AD à l’aide de Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md). En outre, pour plus d’informations sur la surveillance de connexion AD Azure (synchronisation) avec état de connexion AD Azure consultez [AD à l’aide de Azure se connecter la santé pour la synchronisation](active-directory-aadconnect-health-sync.md).

![AD Azure se connecter la santé pour les services AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Alertes de publicité Azure connectent la santé pour les services AD DS
La section alertes dans Azure santé AD se connecter pour AD DS, vous fournit une liste d’alertes actifs et résolus, liés à vos contrôleurs de domaine. Sélection d’une alerte de l’active ou résolue ouvre une nouvelle lame avec des informations supplémentaires, ainsi que les étapes de résolution et des liens vers la documentation de prise en charge. Chaque type d’alerte peut avoir une ou plusieurs instances, qui correspondent à chacun des contrôleurs de domaine affectés par cette alerte. Dans la partie inférieure de la lame d’alerte, vous pouvez double-cliquer sur un contrôleur de domaine affecté pour ouvrir une lame supplémentaire avec plus de détails sur cette instance d’alerte.

Dans cette blade, vous pouvez activer les notifications par courrier électronique pour les alertes et modifier l’intervalle de temps dans l’affichage. En développant la plage horaire vous permet de voir les alertes résolues préalables.

![Erreur de synchronisation AD Connect Azure](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Bord de contrôleurs de domaine
Ce tableau de bord fournit une vue topologique de votre environnement, ainsi que des mesures opérationnelles clés et état de chacun de vos contrôleurs de domaine géré. Les mesures présentées aider à identifier rapidement, les contrôleurs de domaine qui peuvent nécessiter un examen supplémentaire. Par défaut, seul un sous-ensemble des colonnes s’affiche. Toutefois, vous pouvez trouver l’ensemble de colonnes disponibles, en double-cliquant sur la commande de colonnes. Sélectionner les colonnes qui vous plus intéressent, désactive ce tableau de bord simple et en une seule place pour afficher l’état de santé de votre environnement AD DS.

![Contrôleurs de domaine](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Les contrôleurs de domaine peuvent être regroupés par leur domaine respectif ou le site, ce qui est utile pour la compréhension de la topologie de l’environnement. Enfin, si vous double-cliquez sur l’en-tête de la lame, le tableau de bord s’agrandit pour utiliser l’immobilier disponible à l’écran. Ce mode d’affichage plus grand est utile lorsque plusieurs colonnes sont affichées.

## <a name="replication-status-dashboard"></a>Bord d’état de réplication
Ce tableau de bord fournit une vue de l’état de la réplication et la topologie de réplication de vos contrôleurs de domaine géré. L’état de la dernière tentative de réplication est répertoriée, ainsi que la documentation utile pour toute erreur trouvée. Vous pouvez double-cliquer sur un contrôleur de domaine avec une erreur, pour ouvrir une nouvelle lame avec des informations, telles que : plus d’informations sur l’erreur, recommandé des étapes de résolution et des liens vers la documentation sur le dépannage.

![État de la réplication](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Surveillance
Cette fonctionnalité fournit des tendances graphiques de différents compteurs de performance, qui sont collectées de manière continue à partir de chacun des contrôleurs de domaine géré. Les performances d’un contrôleur de domaine peuvent être facilement comparées sur tous les autres contrôleurs de domaine géré dans votre forêt. En outre, vous pouvez voir les différents compteurs de performance côte à côte, qui est utile lors du dépannage de problèmes dans votre environnement.

![Surveillance](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Par défaut, nous avons présélectionné quatre compteurs de performance ; Toutefois, vous pouvez inclure d’autres personnes en cliquant sur la commande de filtre et en sélectionnant ou en désélectionnant les compteurs de performance souhaité. En outre, vous pouvez double-cliquer sur un graphique de compteur de performance pour ouvrir une nouvelle lame, qui inclut des points de données pour chacun des contrôleurs de domaine géré.

## <a name="related-links"></a>Liens connexes

* [Annonce Azure se connecter à la santé](active-directory-aadconnect-health.md)
* [AD Azure se connecter l’Installation de l’Agent de la santé](active-directory-aadconnect-health-agent-install.md)
* [AD Azure se connecter à des opérations de santé](active-directory-aadconnect-health-operations.md)
* [À l’aide d’Active Directory Azure se connecter la santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [À l’aide de la santé se connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [Annonce Azure se connecter le Forum aux questions sur la santé](active-directory-aadconnect-health-faq.md)
* [Annonce Azure se connecter à l’historique de Version de la santé](active-directory-aadconnect-health-version-history.md)
