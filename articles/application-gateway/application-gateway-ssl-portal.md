<properties
   pageTitle="Configuration d’une passerelle d’application pour le déchargement SSL à l’aide du portail | Microsoft Azure"
   description="Cette page fournit des instructions pour créer une passerelle d’application avec SSL de déchargement à l’aide du portail"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configuration d’une passerelle d’application pour le déchargement SSL à l’aide du portail

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[PowerShell classique d’Azure](application-gateway-ssl.md)

Passerelle d’Application Azure peut être configuré pour mettre fin à la session de Secure Sockets Layer (SSL) au niveau de la passerelle afin d’éviter les coûteuses tâches de décryptage SSL pour se produire au niveau de la batterie de serveurs web. Déchargement SSL simplifie également la configuration du serveur frontal et la gestion de l’application web.

## <a name="scenario"></a>Scénario

Le scénario suivant parcourt de configuration SSL de déchargement sur une passerelle d’application existant. Le scénario suppose que vous avez déjà suivi les étapes pour [créer une passerelle d’Application](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer une passerelle d’application déchargement SSL, un certificat est requis. Ce certificat est chargé sur la passerelle d’application et utilisé pour crypter et décrypter le trafic envoyé via SSL. Le certificat doit être au format d’échange d’informations personnelles (pfx). Ce format de fichier permet d’exporter la clé privée qui est requis par la passerelle d’application pour effectuer le cryptage et le décryptage du trafic.

## <a name="add-an-https-listener"></a>Ajouter un port d’écoute HTTPS

Le port d’écoute HTTPS recherche le trafic en fonction de sa configuration et vous aide à acheminer le trafic vers des pools de back-end.

### <a name="step-1"></a>Étape 1

Accédez au portail Azure et sélectionnez une passerelle d’application existante

### <a name="step-2"></a>Étape 2

Cliquez sur ports d’écoute et cliquez sur le bouton Ajouter pour ajouter un écouteur.

![lame de vue d’ensemble de passerelle App][1]

### <a name="step-3"></a>Étape 3

Remplissez les informations requises pour le port d’écoute et télécharger le certificat .pfx, lorsque vous avez terminé cliquez sur OK.

**Nom** - cette valeur est un nom convivial de l’écouteur.

**Configuration IP du site Web frontal** - cette valeur est frontend IP qui est utilisé pour le port d’écoute.

**Le port frontal (nom/Port)** : un nom convivial pour le port utilisé sur le front-end de la passerelle d’application et le port utilisé.

**Protocole** - un commutateur pour déterminer si le protocole https ou http est utilisé pour le front-end.

**Certificat (nom/mot de passe)** - déchargement de si le cryptage SSL est utilisé, un certificat .pfx est requis pour ce paramètre et un nom convivial et un mot de passe sont requis.

![ajouter des lames de port d’écoute][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Créer une règle et l’associer à l’écouteur

L’écouteur a été créé. Il est temps de créer une règle pour gérer le trafic de l’écouteur.

### <a name="step-1"></a>Étape 1

Cliquez sur les **règles** de la passerelle d’application, puis cliquez sur Ajouter.

![lame de règles application gateway][3]

### <a name="step-2"></a>Étape 2

Sur la lame de la **règle de base ajouter** , tapez le nom convivial de la règle et cliquez sur le port d’écoute créé à l’étape précédente. Choisissez le paramètre http et le pool de serveur principal approprié, puis cliquez sur **OK**

![fenêtre Paramètres de HTTPS][4]

Les paramètres sont maintenant enregistrés à la passerelle d’application. L’opération de ces paramètres peuvent prendre un certain temps avant d’être disponibles pour l’affichage par le biais du portail ou PowerShell. Une fois enregistrée la passerelle d’application gère le cryptage et le décryptage du trafic. Tout le trafic entre la passerelle d’application et les serveurs web principaux est traité via http. Toute communication au client si initié via https sera retournée au client crypté.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une sonde d’état personnalisé avec la passerelle d’Application Azure, consultez [créer une sonde d’état personnalisé](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png