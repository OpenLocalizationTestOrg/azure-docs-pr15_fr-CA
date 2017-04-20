<properties 
    pageTitle="Comment faire pour configurer des notifications et des modèles d’e-mail dans Azure API de gestion" 
    description="Apprenez à configurer les notifications et les modèles dans Azure API de gestion des e-mails." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Comment faire pour configurer des notifications et des modèles d’e-mail dans Azure API de gestion

API de gestion permet de configurer les notifications d’événements spécifiques et pour configurer les modèles de courriel utilisés pour communiquer avec les administrateurs et les développeurs d’une instance de gestion de l’API. Cette rubrique indique comment configurer les notifications d’événements disponibles et fournit une vue d’ensemble de la configuration des modèles de courrier électronique utilisés pour ces événements.

## <a name="publisher-notifications"> </a>Configurer des notifications de publisher

Pour configurer les notifications, cliquez sur **Gérer** dans le portail classique d’Azure pour votre service de gestion de l’API. Cela vous amène sur le portail d’éditeur de gestion de l’API.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion de l’API, voir [Création d’une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Dans le menu de **Gestion de l’API** à gauche pour consulter les notifications disponibles, cliquez sur **Notifications** .

![Notifications de Publisher][api-management-publisher-notifications]

La liste suivante des événements peut être configurée pour les notifications.

-   **Demandes d’abonnement (nécessitant une approbation)** - les destinataires de l’email spécifié et les utilisateurs recevront des notifications par courrier électronique à propos des demandes d’abonnement pour les produits API nécessitant une approbation.
-   **Nouveaux abonnements** - les destinataires de l’email spécifié et les utilisateurs recevront des notifications par courrier électronique à propos des abonnements de produit nouvelles API.
-   **Demandes de galerie d’application** - les destinataires de l’email spécifié et les utilisateurs recevront des notifications par courrier électronique lorsque de nouvelles applications sont soumises à la bibliothèque de l’application.
-   **Cci** - les destinataires de l’email spécifié et les utilisateurs recevront des copies carbone cachées de messagerie de tous les e-mails envoyés aux développeurs.
-   **Nouveau problème ou commentaire** - les destinataires de l’email spécifié et les utilisateurs recevront des notifications par courrier électronique lorsqu’un nouveau problème ou commentaire est présentée sur le portail des développeurs.
-   **Message de clôturer le compte** - les destinataires de l’email spécifié et les utilisateurs recevront des notifications par courrier électronique lorsqu’un compte est fermé.
-   **Limite de quota d’abonnement Approaching** - les destinataires du courrier électronique et les utilisateurs suivants recevront des notifications par courrier électronique lors de l’utilisation de l’abonnement est proche de quota d’utilisation.

Pour chaque événement, vous pouvez spécifier les destinataires de courrier électronique à l’aide de la zone de texte adresse e-mail, ou vous pouvez sélectionner des utilisateurs dans une liste.

Pour spécifier les adresses e-mail pour être informé, entrez-les dans la zone de texte adresse e-mail. Si vous avez plusieurs adresses e-mail, séparez-les par des virgules.

![Destinataires de la notification][api-management-email-addresses]

Pour spécifier les utilisateurs à notifier, cliquez sur **Ajouter un destinataire**, cochez la case en regard de l’utilisateur doit être informé et cliquez sur **OK**.

>Notez que seuls les administrateurs sont affichés dans la liste.

Après avoir configuré les destinataires de la notification, cliquez sur **Enregistrer** pour appliquer les destinataires de la notification de mise à jour.

>Si vous quittez l’onglet **Notifications de Publisher** le portail publisher vous avertit s’il y a des modifications non enregistrées.

## <a name="email-templates"> </a>Configurer les modèles de courriel

API de gestion fournit des modèles d’e-mail pour les messages électroniques qui sont envoyés dans l’administration et l’utilisation du service. Les modèles de courrier électronique suivants sont fournis.

-   Présentation de galerie d’application approuvée
-   Lettre d’adieu de développeur
-   Limite de quota de développeur approche de notification
-   Inviter un utilisateur
-   Nouveau commentaire ajouté à un problème
-   Nouveau problème reçu
-   Nouvel abonnement activé
-   Confirmation d’abonnement renouvelé
-   Refus de la demande d’abonnement
-   Demande d’abonnement reçue

Ces modèles peuvent être modifiés selon les besoins.

Pour afficher et configurer les modèles d’e-mail pour votre instance de gestion de l’API, cliquez sur **Notifications** dans le menu de **Gestion de l’API** sur la gauche et sélectionnez l’onglet **Modèles d’E-mail** .

![Modèles de courriel][api-management-email-templates]

Pour afficher ou modifier un modèle spécifique, sélectionnez-le dans la liste déroulante des **modèles** .

![Liste des modèles de courrier électronique][api-management-email-templates-list]

Chaque modèle d’e-mail est un objet au format texte brut et une définition de corps au format HTML. Chaque élément peut être personnalisé selon les besoins.

![Éditeur de modèle de courrier électronique][api-management-email-template]

La liste de **paramètres** contient une liste de paramètres, où sera inséré dans l’objet ou le corps, remplacé la valeur désignée lorsque le message est envoyé. Pour insérer un paramètre, placez le curseur où vous souhaitez le paramètre à passer et cliquez sur la flèche à gauche du nom du paramètre.

Cliquez sur **Aperçu** ou **Envoyer un test** pour voir comment l’e-mail va rechercher ou envoyer un e-mail de test.

>Notez que les paramètres ne sont pas remplacées par des valeurs réelles lors de l’aperçu ou l’envoi d’un test.
>
>Pour enregistrer les modifications apportées au modèle d’e-mail, cliquez sur **Enregistrer**, ou pour annuler les modifications cliquez sur **Annuler**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Mise en route de la gestion des API Azure]: api-management-get-started.md
[Créez une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance