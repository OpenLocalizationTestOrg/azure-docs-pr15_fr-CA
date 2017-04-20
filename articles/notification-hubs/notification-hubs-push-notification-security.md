<properties
    pageTitle="Sécurité pour les concentrateurs de Notification"
    description="Cette rubrique explique la sécurité pour les concentrateurs de notification Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Sécurité

##<a name="overview"></a>Vue d’ensemble

Cette rubrique décrit le modèle de sécurité de concentrateurs de Notification Azure. Concentrateurs de Notification étant une entité de Bus des services, qu’ils implémentent le même modèle de sécurité que le Bus de Service. Pour plus d’informations, consultez les rubriques de [l’Authentification du Service de Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

##<a name="shared-access-signature-security-sas"></a>Sécurité par Signature accès partagé (SAS) 

Notification de concentrateurs implémente un modèle de sécurité au niveau de l’entité appelée SAS (Signature de l’accès partagé). Ce schéma permet de messagerie d’entités déclarer jusqu'à 12 règles d’autorisation dans leur description des droits sur cette entité.

Chaque règle contient un nom, une valeur de clé (secret partagé) et un ensemble de droits, comme expliqué dans la section « Revendications de sécurité ». Lors de la création d’un concentrateur de la Notification, les deux règles sont automatiquement créées : une avec des droits d’écoute (qui utilise de l’application client) et l’autre avec tous les droits (par le back-end de l’application).

Lors de la gestion de l’inscription à partir d’applications clientes, si les informations envoyées notifications ne sont pas sensibles (par exemple, les mises à jour de la météo), une méthode commune pour accéder à un concentrateur de Notification pour donner la valeur de la clé de la règle d’accès écoute uniquement à l’application cliente et donner la valeur de clé de l’accès complet à la règle pour le back-end de l’application.

Il n’est pas recommandé que vous incorporez la valeur de clé dans les applications du Windows Store client. Une façon d’éviter l’incorporation de la valeur de clé est d’avoir l’application client de récupérer à partir du serveur principal chargé de l’application au démarrage.

Il est important de comprendre que la clé avec accès d’écoute permet à une application cliente pour vous inscrire à n’importe quelle balise. Si votre application doit limiter les enregistrements à des balises spécifiques à des clients spécifiques (par exemple, lorsque les balises représentent l’ID utilisateur), la principale de l’application doit effectuer les enregistrements. Pour plus d’informations, consultez Gestion de l’inscription. Notez que de cette façon, l’application client auront pas un accès direct à des concentrateurs de Notification.

##<a name="security-claims"></a>Demandes de sécurité

Similaire à d’autres entités, opérations de concentrateur de Notification sont autorisées pour les trois demandes de sécurité : écouter, envoyer et gérer.

| Demande d’indemnisation | Description | Opérations autorisées |
|-------|-------------|--------------------|
| Écouter | Créer/mettre à jour, lire et supprimer les enregistrements unique | Créer/mettre à jour l’enregistrement<br><br>Lire l’enregistrement<br><br>Lire tous les enregistrements d’une poignée<br><br>Supprimer l’enregistrement |
| Envoyer | Envoyer des messages au hub notification | Envoyer le message |
| Gérer les | CRUDs sur les concentrateurs de Notification (y compris la mise à jour des informations d’identification de la solution et des clés de sécurité) et lecture des enregistrements en fonction de marqueurs | Concentrateurs de notifications de création/mise à jour/Read/Delete<br><br>Lire des enregistrements par balise |


Concentrateurs de notification acceptent les affirmations accordées par les jetons de contrôle d’accès Microsoft Azure et par les jetons signature générées avec des clés pré-partagées configurés directement sur le concentrateur de Notification.