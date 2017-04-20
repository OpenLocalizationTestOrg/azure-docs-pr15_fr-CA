<properties
    pageTitle="Gestion d’un service Web en utilisant le portail Azure Machine formation Web Serivces | Microsoft Azure"
    description="Gérer l’accès aux espaces de travail de formation de Machine d’Azure et de déployer et de gérer des services web API de ML"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gestion d’un service Web en utilisant le portail Azure Machine Learning Web Services

Vous pouvez gérer votre Machine Learning nouvelle et services Web classique en utilisant le portail Microsoft Azure Machine Learning Web Services. Dans la mesure où les services Web classique et services Web nouvelle sont basées sur différentes technologies sous-jacentes, vous avez légèrement différentes capacités de gestion pour chacun d’eux.

Dans le portail de Services Web de formation Machine, vous pouvez :

- Surveiller l’utilisation du service web.
- Configurer la description, de mettre à jour les clés pour le service web (New uniquement), de mise à jour de votre stockage compte clé (nouveau uniquement), activer l’enregistrement et d’activer ou de désactiver des exemples de données.
- Supprimer le service web.
- Créer, supprimer, ou la facturation de la mise à jour des plans (New uniquement).
- Ajouter et supprimer des points de terminaison (classique uniquement)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Gérer les services d’un site Web

Pour gérer vos services de nouveau site Web :

1.  Se connecter au portail [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure - utiliser le compte qui est associé à l’abonnement Azure.
2.  Dans le menu, cliquez sur **Les Services Web**.

Cela affiche la liste des services Web déployés pour votre abonnement. 

Pour gérer un service Web, cliquez sur les Services Web. À partir de la page Web Services, vous pouvez :

- Cliquez sur le service web pour le gérer.
- Cliquez sur la facturation Plan pour le service web à mettre à jour.
- Supprimer un service web.
- Copie d’un service web et le déployer dans une autre région.

Lorsque vous cliquez sur un service web, la page de démarrage rapide de service web s’ouvre. La page de démarrage rapide de service web propose deux options de menu qui vous permettent de gérer votre service web :

- **Tableau de bord** - vous permet d’afficher l’utilisation du service Web.
- **Configurer** - vous permet d’ajouter un texte descriptif, de mettre à jour la clé pour le compte de stockage associé au service Web et d’activer ou de désactiver des exemples de données.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Contrôle comment le service web est utilisé ###

Cliquez sur l’onglet **tableau de bord** .

Du tableau de bord, vous pouvez afficher l’utilisation globale de votre service Web sur une période donnée. Vous pouvez sélectionner la période à afficher dans la liste déroulante période dans le coin supérieur droit des graphiques d’utilisation. Le tableau de bord affiche les informations suivantes :

- **Demandes dans le temps** affiche un graphique d’étape du nombre de demandes sur la période sélectionnée. Il peut aider à identifier si vous rencontrez des pics d’utilisation.
- **Demandes de requête-réponse** affiche le nombre total d’appels de demande-réponse que le service a reçu sur la période sélectionnée et n’a pas pu combien d'entre eux.
- **Calculer temps de réponse à la demande** affiche la moyenne du temps nécessaire à l’exécution des demandes reçues.
- **Requêtes de lots** affiche le nombre total de requêtes de lots que le service a reçu sur la période sélectionnée et n’a pas pu combien d'entre eux.
- **Latence moyenne de travail** affiche la moyenne du temps nécessaire à l’exécution des demandes reçues.
- **Erreurs** affiche le nombre cumulé d’erreurs qui se sont produites sur les appels au service web.
- **Les frais des services** affiche les frais pour le plan de facturation associé au service.

### <a name="configuring-the-web-service"></a>Configuration du service web ###

Cliquez sur l’option de menu de **configuration** .

Vous pouvez mettre à jour les propriétés suivantes :

* **Description** vous permet d’entrer une description pour le service Web.
* **Titre** vous permet d’entrer un titre pour le service Web
* **Clés** vous permet de faire pivoter vos clés API primaires et secondaires.
* **Clé de compte de stockage** vous permet de mettre à jour la clé pour le compte de stockage associé avec les modifications de service Web. 
* **Activer les exemples de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service web dans le Studio d’apprentissage Machine, les exemples de données provient des données votre utilisé pour exercer votre modèle. Si vous avez créé le service par programme, les données provient les données d’exemple fournies dans le cadre du package JSON.

### <a name="managing-billing-plans"></a>Gestion des plans de facturation ###

Cliquez sur l’option de menu de **Plans** à partir de la page de démarrage rapide de services web. Vous pouvez également cliquer sur le plan associé au service Web spécifique pour gérer ce plan.

* **Nouveau** vous permet de créer un nouveau plan.
* **Instance de Plan d’ajout/suppression** vous permet « d’élargissement » un plan existant à ajouter de la capacité.
* **Reclassement/déclassement** vous permet de « évoluer » un plan existant à ajouter de la capacité.
* **Supprimer** permet de supprimer un plan.

Cliquez sur un plan pour afficher son tableau de bord. Le tableau de bord vous offre un instantané ou le plan de l’utilisation sur une période de temps. Pour sélectionner la période à afficher, cliquez sur la liste déroulante de **période** dans l’angle supérieur droit du tableau de bord. 

Le tableau de bord plan fournit les informations suivantes :

* **Description du plan** affiche des informations sur les coûts et les associés au plan de capacité.
* **Plan d’utilisation** affiche le nombre de transactions et les heures de calcul qui ont été imputées sur le plan.
* **Services Web** affiche le nombre de services Web qui utilisent ce plan.
* **Haut en appels de Service Web** affiche les services Web de quatre premières qui effectuent des appels qui sont facturés par rapport au plan.
* **Services Web de haut par calculer les heures** affiche les services Web de quatre premières utilise des ressources de calcul qui sont facturés par rapport au plan.

## <a name="manage-classic-web-services"></a>Gérer des Services Web classique

> [AZURE.NOTE] Les procédures décrites dans cette section sont relatifs à la gestion des services web standard via le portail Azure Machine Learning Web Services. Pour plus d’informations sur la gestion des services Web classique par le biais du Studio de formation de Machine et le portail classique Azure, consultez [Gestion d’un espace de travail de formation de Machine d’Azure](machine-learning-manage-workspace.md).

Pour gérer vos services Web classique :

1.  Se connecter au portail [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure - utiliser le compte qui est associé à l’abonnement Azure.
2.  Dans le menu, cliquez sur **Les Services Web classique**.

Pour gérer un Service Web classique, cliquez sur **Les Services Web classique**. À partir de la page des Services Web classique, vous pouvez :

- Cliquez sur le service web pour afficher les points de terminaison associés.
- Supprimer un service web.

Lorsque vous gérez un service Web classique, vous gérez chacun des points de terminaison séparément. Lorsque vous cliquez sur un service web dans la page Web Services, la liste des points de terminaison associés au service s’ouvre. 

Sur la page de point de terminaison des services Web classique, vous pouvez ajouter et supprimer des points de terminaison sur le service. Pour plus d’informations sur l’ajout de points de terminaison, consultez [Création de points de terminaison de](machine-learning-create-endpoint.md).

Cliquez sur un points de terminaison pour ouvrir la page de démarrage rapide de service web. Sur la page de démarrage rapide, il existe deux options de menu qui vous permettent de gérer votre service web :

- **Tableau de bord** - vous permet d’afficher l’utilisation du service Web.
- **Configurer** - vous permet d’ajouter un texte descriptif, d’activer l’enregistrement des erreurs et désactiver la mise à jour de la clé pour le stockage du compte associé au service Web, activer et désactiver des exemples de données.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Contrôle comment le service web est utilisé ###

Cliquez sur l’onglet **tableau de bord** .

Du tableau de bord, vous pouvez afficher l’utilisation globale de votre service Web sur une période donnée. Vous pouvez sélectionner la période à afficher dans la liste déroulante période dans le coin supérieur droit des graphiques d’utilisation. Le tableau de bord affiche les informations suivantes :

- **Demandes dans le temps** affiche un graphique d’étape du nombre de demandes sur la période sélectionnée. Il peut aider à identifier si vous rencontrez des pics d’utilisation.
- **Demandes de requête-réponse** affiche le nombre total d’appels de demande-réponse que le service a reçu sur la période sélectionnée et n’a pas pu combien d'entre eux.
- **Calculer temps de réponse à la demande** affiche la moyenne du temps nécessaire à l’exécution des demandes reçues.
- **Requêtes de lots** affiche le nombre total de requêtes de lots que le service a reçu sur la période sélectionnée et n’a pas pu combien d'entre eux.
- **Latence moyenne de travail** affiche la moyenne du temps nécessaire à l’exécution des demandes reçues.
- **Erreurs** affiche le nombre cumulé d’erreurs qui se sont produites sur les appels au service web.
- **Les frais des services** affiche les frais pour le plan de facturation associé au service.

### <a name="configuring-the-web-service"></a>Configuration du service web ###

Cliquez sur l’option de menu de **configuration** .

Vous pouvez mettre à jour les propriétés suivantes :

* **Description** vous permet d’entrer une description pour le service Web. Description est un champ obligatoire.
* **Enregistrement** permet d’activer ou de désactiver la journalisation sur le point de terminaison des erreurs. Pour plus d’informations sur la journalisation, reportez-vous à la section Activer l' [enregistrement pour l’apprentissage de l’ordinateur des services web](machine-learning-web-services-logging.md).
* **Activer les exemples de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service web dans le Studio d’apprentissage Machine, les exemples de données provient des données votre utilisé pour exercer votre modèle. Si vous avez créé le service par programme, les données provient les données d’exemple fournies dans le cadre du package JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Accorder ou suspendre l’accès aux services Web pour les utilisateurs du portail

À l’aide du portail classique Azure, vous pouvez autoriser ou refuser l’accès à des utilisateurs spécifiques.

### <a name="access-for-users-of-new-web-services"></a>Accès pour les utilisateurs des services d’un site Web

Pour permettre aux autres utilisateurs de travailler avec vos services Web dans le portail Azure Machine formation Web Services, vous devez les ajouter en tant qu’administrateurs-co sur votre abonnement Azure.

Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure - utiliser le compte qui est associé à l’abonnement Azure.

1. Dans le volet de navigation, cliquez sur **paramètres**, puis cliquez sur **administrateurs**.
2. En bas de la fenêtre, cliquez sur **Ajouter**. 
3. Dans la boîte de dialogue ADD A CO-ADMINISTRATOR, tapez l’adresse de messagerie de la personne que vous souhaitez ajouter en tant qu’administrateur de collaboration, puis sélectionnez l’abonnement que vous souhaitez co-l’administrateur pour accéder à.
4. Cliquez sur **Enregistrer**.

### <a name="access-for-users-of-classic-web-services"></a>Accès pour les utilisateurs de services Web classique

Pour gérer un espace de travail :

Connectez-vous à l' [Azure portal classique](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure - utiliser le compte qui est associé à l’abonnement Azure.

1. Dans le panneau de services Microsoft Azure, cliquez sur **Apprentissage automatique**.
1. Cliquez sur l’espace de travail que vous souhaitez gérer.
1. Cliquez sur l’onglet **configurer** .

À partir de l’onglet configuration, vous pouvez suspendre l’accès à l’espace de travail d’apprentissage automatique en cliquant sur **Refuser**. Les utilisateurs ne seront plus en mesure d’ouvrir l’espace de travail dans un Studio de formation de Machine. Pour restaurer l’accès, cliquez sur **Autoriser**.

À des utilisateurs spécifiques :

Pour gérer les comptes supplémentaires qui ont accès à l’espace de travail dans un Studio de formation de Machine, cliquez sur **connexion à un Studio de ML** dans l’onglet **tableau de bord** . L’espace de travail s’ouvre dans un Studio de formation de Machine. À partir d’ici, cliquez sur l’onglet **paramètres** , puis sur **utilisateurs**. Vous pouvez cliquer sur **Inviter plus d’utilisateurs** pour permettre aux utilisateurs d’accéder à l’espace de travail, ou sélectionnez un utilisateur et cliquez sur **Supprimer**.

> [AZURE.NOTE] Le **signe de ML Studio** ouvre Studio de formation de Machine à l’aide de Microsoft Account vous êtes actuellement connecté à. Le Account de Microsoft que vous permet de vous connecter au portail classique Azure pour créer un espace de travail n’est pas automatiquement autorisé à ouvrir cet espace de travail. Pour ouvrir un espace de travail, vous devez être connecté à la Account de Microsoft qui a été défini comme propriétaire de l’espace de travail, ou vous devez recevoir une invitation au propriétaire à rejoindre l’espace de travail.
