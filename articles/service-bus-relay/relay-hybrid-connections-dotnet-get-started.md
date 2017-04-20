<properties
    pageTitle="Mise en route de relais hybride connexions | Microsoft Azure"
    description="Comment écrire une application de console C# pour les connexions hybride"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Mise en route de connexions hybride de relais

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Ce qui va être accompli

Dans la mesure où les connexions hybride nécessite à la fois un client et un composant de serveur, nous allons créer deux applications de console dans ce didacticiel. Voici la procédure :

1. Créer un espace de noms de relais, l’utilisation du portail Azure.

2. Créer une connexion hybride, l’utilisation du portail Azure.

3. Écrire un serveur application console pour recevoir des messages.

4. Écrire un client application console pour envoyer des messages.

## <a name="prerequisites"></a>Conditions préalables

1. [2013 de Visual Studio ou Visual Studio 2015](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2015.

2. Un abonnement Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. création d’un espace de noms à l’aide du portail Azure

Si vous disposez déjà d’un espace de noms relais créé, passer à la section [Création d’une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. création d’une connexion hybride utilisant le portail Azure

Si vous avez déjà une connexion hybride créée, passer à la section [Création d’une application serveur](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. création d’une application serveur (récepteur)

Pour écouter et recevoir des messages de relais, nous avons écrit une application de console C# à l’aide de Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. création d’une application cliente (expéditeur)

Pour envoyer des messages au relais, nous avons écrit une application de console C# à l’aide de Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Exécutez les applications

1. Exécutez l’application serveur.

2. Exécutez l’application client et entrez du texte.

3. Vérifiez que la console serveur d’application renvoie le texte qui a été saisi dans l’application cliente.

![applications en cours d’exécution](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Félicitations, vous avez créé une application hybride connexions de bout en bout.

## <a name="next-steps"></a>Étapes suivantes :

- [Forum aux questions de relais](relay-faq.md)
- [Créer un espace de noms](relay-create-namespace-portal.md)
- [Mise en route de nœud](relay-hybrid-connections-node-get-started.md)