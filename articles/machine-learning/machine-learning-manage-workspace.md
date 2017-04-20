<properties
    pageTitle="Gérer un espace de travail d’apprentissage automatique | Microsoft Azure"
    description="Gérer l’accès aux espaces de travail de formation de Machine d’Azure et de déployer et de gérer des services web API de ML"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Gérer un espace de travail formation de Machine Azure

>[AZURE.NOTE] Les procédures décrites dans cet article concernent les services Web classique de formation de Machine Azure. Pour plus d’informations sur la gestion des services Web sur le portail de Services Web de formation Machine, consultez [Gestion d’un service Web en utilisant le portail Azure Machine Learning Web Services](machine-learning-manage-new-webservice.md).

À l’aide du portail classique Azure, vous pouvez gérer vos espaces de travail d’apprentissage automatique pour :

- Surveiller l’utilisation de l’espace de travail
- Configurer l’espace de travail pour autoriser ou refuser l’accès
- Gérer les services Web créés dans l’espace de travail
- Supprimer l’espace de travail

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

En outre, l’onglet tableau de bord fournit une vue d’ensemble de l’utilisation de votre espace de travail et un aperçu rapide de vos informations d’espace de travail.  

> [AZURE.TIP] Dans un Studio de formation de Machine Azure, sous l’onglet **SERVICES du WEB** , vous pouvez ajouter, mettre à jour ou supprimer un service Web de formation de Machine.

Pour gérer un espace de travail :

1.  Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure - utiliser le compte qui est associé à l’abonnement Azure.
2.  Dans le panneau de services Microsoft Azure, cliquez sur **Apprentissage automatique**.
3.  Cliquez sur l’espace de travail que vous souhaitez gérer.

La page espace de travail comporte trois onglets :

- **Tableau de bord** - permet d’afficher l’utilisation d’espace de travail et des informations
- **Configurer** - vous permet de gérer l’accès à l’espace de travail
- **SERVICES WEB** - vous permet de gérer les services Web qui ont été publiés à partir de cet espace de travail

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Pour surveiller l’utilisation de l’espace de travail

Cliquez sur l’onglet **tableau de bord** .

Du tableau de bord, vous pouvez afficher l’utilisation globale de votre espace de travail et obtenir un aperçu rapide des informations d’espace de travail.

- Le graphique **de calcul** affiche les ressources de calcul utilisés par l’espace de travail. Vous pouvez modifier l’affichage pour afficher le rapport ou les valeurs absolues, et vous pouvez modifier la période affichée dans le graphique.
- **Vue d’ensemble de l’utilisation** affiche le stockage Azure utilisé par l’espace de travail.
- **Coup de œil rapide** fournit un résumé des informations d’espace de travail et liens utiles.

> [AZURE.NOTE] Le **signe de ML Studio** ouvre Studio de formation de Machine à l’aide de Microsoft Account vous êtes actuellement connecté à. Le Account de Microsoft que vous permet de vous connecter au portail classique Azure pour créer un espace de travail n’est pas automatiquement autorisé à ouvrir cet espace de travail. Pour ouvrir un espace de travail, vous devez être connecté à la Account de Microsoft qui a été défini comme propriétaire de l’espace de travail, ou vous devez recevoir une invitation au propriétaire à rejoindre l’espace de travail.


## <a name="to-grant-or-suspend-access-for-users"></a>D’accorder ou de suspendre l’accès pour les utilisateurs ##

Cliquez sur l’onglet **configurer** .

Dans l’onglet configuration, vous pouvez :

- Suspendre l’accès à l’espace de travail d’apprentissage automatique en cliquant sur Refuser. Les utilisateurs ne seront plus en mesure d’ouvrir l’espace de travail dans un Studio de formation de Machine. Pour restaurer l’accès, cliquez sur Autoriser.

Pour gérer les comptes supplémentaires qui ont accès à l’espace de travail dans un Studio de formation de Machine, cliquez sur **connexion à un Studio de ML** dans l’onglet **tableau de bord** (voir la remarque précédente concernant les **connecter en Studio de ML**). L’espace de travail s’ouvre dans un Studio de formation de Machine. À partir d’ici, cliquez sur l’onglet **paramètres** , puis sur **utilisateurs**. Vous pouvez cliquer sur **Inviter plus d’utilisateurs** pour permettre aux utilisateurs d’accéder à l’espace de travail, ou sélectionnez un utilisateur et cliquez sur **Supprimer**.


## <a name="to-manage-web-services-in-this-workspace"></a>Gestion des services web dans cet espace de travail

Cliquez sur l’onglet de **SERVICES WEB** .

Cette option affiche une liste de services web publiés à partir de cet espace de travail.
Pour gérer un service web, cliquez sur le nom dans la liste pour ouvrir la page du service Web.

Un service Web peut avoir un ou plusieurs points de terminaison définis.

- Vous pouvez définir des points de terminaison de plus en plus le point de terminaison « Par défaut ». Pour ajouter le point de terminaison, cliquez sur **Gérer les points de terminaison** en bas du tableau de bord pour ouvrir le portail Azure Machine Learning Web Services.

- Pour supprimer un point de terminaison (vous ne pouvez pas supprimer le point de terminaison « Par défaut »), cliquez sur la case à cocher au début de la ligne de point de terminaison, puis cliquez sur **Supprimer**. Cela supprime le point de terminaison du service Web.

    > [AZURE.NOTE] Si une application utilise le point de terminaison de service web lorsque le point de terminaison est supprimé, l’application reçoit une erreur la prochaine fois qu’il essaie d’accéder au service.

Cliquez sur le nom d’un point de terminaison de service Web pour l’ouvrir. 

Du tableau de bord, vous pouvez afficher l’utilisation globale de votre service Web sur une période donnée. Vous pouvez sélectionner la période à afficher dans la liste déroulante période dans le coin supérieur droit des graphiques d’utilisation. Le tableau de bord affiche les informations suivantes :

- **Demandes dans le temps** affiche un graphique d’étape du nombre de demandes sur la période sélectionnée. Il peut aider à identifier si vous rencontrez des pics d’utilisation.
- **Demandes de requête-réponse** affiche le nombre total d’appels de demande-réponse que le service a reçu sur la période sélectionnée et n’a pas pu combien d'entre eux.
- **Calculer temps de réponse à la demande** affiche la moyenne du temps nécessaire à l’exécution des demandes reçues.
- **Requêtes de lots** affiche le nombre total de requêtes de lots que le service a reçu sur la période sélectionnée et n’a pas pu combien d'entre eux.
- **Latence moyenne de travail** affiche la moyenne du temps nécessaire à l’exécution des demandes reçues.
- **Erreurs** affiche le nombre cumulé d’erreurs qui se sont produites sur les appels au service web.
- **Les frais des services** affiche les frais pour le plan de facturation associé au service.

À partir de la page de configuration, vous pouvez mettre à jour les propriétés suivantes :

* **Description** vous permet d’entrer une description pour le service Web. Description est un champ obligatoire.
* **Enregistrement** permet d’activer ou de désactiver la journalisation sur le point de terminaison des erreurs. Pour plus d’informations sur la journalisation, reportez-vous à la section Activer l' [enregistrement pour l’apprentissage de l’ordinateur des services web](machine-learning-web-services-logging.md).
* **Activer les exemples de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service web dans le Studio d’apprentissage Machine, les exemples de données provient des données votre utilisé pour exercer votre modèle. Si vous avez créé le service par programme, les données provient les données d’exemple fournies dans le cadre du package JSON.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
