<properties
   pageTitle="Rapport Azure d’Active Directory - aperçu | Microsoft Azure"
   description="Répertorie les différents rapports disponibles pour l’aperçu d’Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Rapport Azure d’Active Directory - aperçu

> [AZURE.SELECTOR]
- [Azure portal](active-directory-reporting-azure-portal.md)
- [Azure portal classique](active-directory-reporting-guide.md)

*Cette documentation fait partie de l' [Azure Reporting Guide de Active Directory](active-directory-reporting-guide.md).*

Les rapports dans l’aperçu Azure Active Directory, vous obtenez toutes les informations dont vous avez besoin pour déterminer le faire de votre environnement. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

Il existe deux zones principales de la création de rapports :

- **Reconnectez-vous activités** – informations sur l’utilisation des applications managées et les activités d’ouverture de session utilisateur

- **Journaux d’audit** - informations sur l’activité système sur les utilisateurs et gestion de groupe, vos applications gérées et les opérations d’annuaire

En fonction de la portée des données que vous recherchez, vous pouvez consulter ces rapports en cliquant sur **les utilisateurs et les groupes** ou **les applications d’entreprise** dans la liste des services dans le [portail Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Connexion des activités

### <a name="user-sign-in-activities"></a>Activités d’ouverture de session utilisateur

Avec les informations fournies par l’état de connexion utilisateur, vous trouverez les réponses aux questions telles que :

- Quel est le modèle d’ouverture de session d’un utilisateur ?
- Combien d’utilisateurs ont utilisateurs connecté plus d’une semaine ?
- Quel est le statut de ces connexions ?

Le point d’entrée pour ces données est le graphique d’ouverture de session utilisateur dans la section **vue d’ensemble** , sous **utilisateurs et groupes**.

 ![Création de rapports] (./media/active-directory-reporting-azure-portal/05.png "Création de rapports")

Le graphique d’ouverture de session utilisateur affiche les agrégations hebdomadaires du signe complémentaires pour tous les utilisateurs dans une période donnée. La valeur par défaut pour la période est de 30 jours.

![Création de rapports] (./media/active-directory-reporting-azure-portal/02.png "Création de rapports")

Lorsque vous cliquez sur un jour dans le graphique d’ouverture de session, vous obtenez une liste détaillée des activités d’ouverture de session.

![Création de rapports] (./media/active-directory-reporting-azure-portal/03.png "Création de rapports")

Chaque ligne de la liste des activités de connexion vous donne des informations détaillées sur la connexion à sélectionné tels que :

- Qui a inscrit ?

- Quel était le nom UPN lié ?

- Quelle application a été la cible de la connexion à ?

- Quelle est l’adresse IP de la connexion à ?

- Quel était l’état de la connexion à ?

### <a name="usage-of-managed-applications"></a>Utilisation des applications gérées

Avec une vue orientée applications de vos données de connexion, vous pouvez répondre aux questions telles que :

- Qui est à l’aide de mes applications ?

- Quels sont les 3 principales applications dans votre organisation ?

- J’ai récemment déployé une application. Comment il fait ?


Le point d’entrée pour ces données est les 3 principales applications de votre organisation dans le dernier rapport de 30 jours dans la section **vue d’ensemble** dans **les applications d’entreprise**.

 ![Création de rapports] (./media/active-directory-reporting-azure-portal/06.png "Création de rapports")


Les agrégations app utilisation graphique hebdomadaire de signer ins pour vos applications haut 3 dans un laps de temps donné. La valeur par défaut pour la période est de 30 jours.

![Création de rapports] (./media/active-directory-reporting-azure-portal/78.png "Création de rapports")

Si vous le souhaitez, vous pouvez définir le focus dans une application spécifique.

![Création de rapports] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Création de rapports")


Lorsque vous cliquez sur un jour dans le graphique d’utilisation app, vous obtenez une liste détaillée des activités d’ouverture de session.


![Création de rapports] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Création de rapports")



L’option **connexions** vous donne un aperçu complet de tous les événements de connexion à vos applications.

![Création de rapports] (./media/active-directory-reporting-azure-portal/85.png "Création de rapports")

À l’aide du sélecteur de colonne, vous pouvez sélectionner les champs de données que vous souhaitez afficher.

![Création de rapports] (./media/active-directory-reporting-azure-portal/column_chooser.png "Création de rapports")



### <a name="filtering-sign-ins"></a>Filtrage des connexions

Vous pouvez filtrer les connexions par un intervalle de temps pour limiter la quantité de données affichées.

![Création de rapports] (./media/active-directory-reporting-azure-portal/927.png "Création de rapports")


Une autre méthode pour filtrer les entrées des activités d’ouverture de session est de rechercher des entrées spécifiques.
La méthode de recherche vous permet de l’étendue de votre signe-ins autour des **utilisateurs**, des **groupes** ou des **applications**.


![Création de rapports] (./media/active-directory-reporting-azure-portal/84.png "Création de rapports")

## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit dans Azure Active Directory fournissent des enregistrements des activités du système de mise en conformité.

Il existe trois catégories principales pour l’audit des activités connexes dans le portail Azure :

- Les utilisateurs et les groupes   

- Applications

- Répertoire   


Pour obtenir une liste complète des activités de rapport d’audit, consultez la [liste des événements du rapport d’audit](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Le point d’entrée à toutes les données d’audit est de **journaux d’Audit** dans la section de **l’activité** **d’Azure Active Directory**.


![L’audit] (./media/active-directory-reporting-azure-portal/61.png "L’audit")


Un journal d’audit a un affichage de liste qui montre les acteurs (qui), les activités (quoi) et les cibles.


![L’audit] (./media/active-directory-reporting-azure-portal/345.png "L’audit")


En cliquant sur un élément dans la liste, vous pouvez obtenir plus de détails à ce sujet.

![L’audit] (./media/active-directory-reporting-azure-portal/873.png "L’audit")




### <a name="users-and-groups-audit-logs"></a>Les utilisateurs et les groupes des journaux d’audit


Avec l’utilisateur et les rapports d’audit au niveau de groupe, vous pouvez obtenir des réponses à des questions telles que :

- Les types de mises à jour ont été appliquées les utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Le nombre de mots de passe ont été modifié ?

- Qu’a fait un administrateur dans un répertoire ?

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des modifications d’appartenance aux groupes ?

- Les propriétaires de groupe ont été modifiés ?

- Les licences ont été affectés à un groupe ou un utilisateur ?


Si vous souhaitez simplement consulter des données d’audit relatives aux utilisateurs et aux groupes, vous trouverez une vue filtrée dans les **journaux d’Audit** dans la section de **l’activité** des **utilisateurs et des groupes**.


![L’audit] (./media/active-directory-reporting-azure-portal/93.png "L’audit")


### <a name="application-audit-logs"></a>Journaux d’audit des applications

Avec application des rapports d’audit, vous pouvez obtenir des réponses à des questions telles que :

- Quelles sont les applications qui ont été ajoutées ou mis à jour ?

- Quelles sont les applications qui ont été supprimées ?

- Un principe de service pour une application qui a changé ?

- Les noms des applications ont été modifiées ?

- Qui a donné de consentement à une application ?


Si vous souhaitez simplement consulter des données d’audit relatives aux applications, vous trouverez une vue filtrée dans les **journaux d’Audit** dans la section de **l’activité** des **applications**d’entreprise.


![L’audit] (./media/active-directory-reporting-azure-portal/134.png "L’audit")


### <a name="filtering-audit-logs"></a>Journaux d’audit de filtrage

Vous pouvez filtrer un rapport d’audit par un intervalle de temps pour limiter la quantité de données affichées.

![L’audit] (./media/active-directory-reporting-azure-portal/324.png "L’audit")

Une autre méthode pour filtrer les entrées d’un journal d’audit est de rechercher des entrées spécifiques.

![L’audit] (./media/active-directory-reporting-azure-portal/237.png "L’audit")

## <a name="next-steps"></a>Étapes suivantes

Consultez [Azure Active Directory Guide de création de rapports](active-directory-reporting-guide.md).
