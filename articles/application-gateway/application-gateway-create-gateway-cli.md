<properties
   pageTitle="Créer une passerelle d’application dans le Gestionnaire de ressources à l’aide de la CLI Azure | Microsoft Azure"
   description="Apprenez à créer une passerelle d’Application à l’aide de la CLI d’Azure dans le Gestionnaire de ressources"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Créer une passerelle d’application à l’aide de la CLI d’Azure

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classique](application-gateway-create-gateway.md)
- [Modèle de gestionnaire de ressources Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Passerelle d’Application Azure est un équilibreur de charge de couche 7. Il fournit le basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Passerelle d’application a les fonctionnalités de livraison d’application suivantes : HTTP charge équilibrage affinité de session basée sur les cookies et déchargement de Secure Sockets Layer (SSL), les sondes d’état personnalisé et prise en charge de plusieurs sites.

## <a name="prerequisite-install-the-azure-cli"></a>Au préalable : Installez l’interface CLI Azure

Pour effectuer les étapes de cet article, vous devez [installer l’Interface de ligne de commande Azure pour Mac, Linux et Windows (CLI Azure)](../xplat-cli-install.md) , et vous devez ouvrir une session [sur Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Si vous n’avez pas un compte Azure, vous avez besoin d’un. Remonter le signe pour un [essai gratuit ici](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle d’application via le portail Azure.

Ce scénario est :

- Création d’une passerelle d’application moyenne avec deux instances.
- Créer un réseau virtuel appelé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16.
- Créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme son bloc CIDR.
- Configurer un certificat pour le déchargement SSL.

![Exemple de scénario][scenario]

>[AZURE.NOTE] Sondes de configuration supplémentaire de la passerelle d’application, y compris leur santé personnalisée, les adresses de pool back-end et des règles supplémentaires sont configurées après avoir configuré la passerelle d’application et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

Passerelle d’Application Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace adresse pour plusieurs sous-réseaux. Dès que vous déployez une passerelle d’application à un sous-réseau, passerelles d’application supplémentaire seulement peuvent être ajoutés au sous-réseau.

## <a name="log-in-to-azure"></a>Ouvrez une session Azure

Ouvrez l' **invite de commande Microsoft Azure**et connectez-vous. 

    azure login

Une fois que vous tapez l’exemple précédent, c’est un code qui est fourni. Accédez à https://aka.ms/devicelogin dans un navigateur pour continuer le processus de connexion.

![connexion de périphérique d’affichage cmd][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![navigateur pour entrer le code][2]

Une fois que le code a été saisi vous êtes connecté, fermez le navigateur pour poursuivre sur le scénario.

![connecté][3]

## <a name="switch-to-resource-manager-mode"></a>Passer en Mode de gestionnaire de ressources

    azure config mode arm

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la passerelle d’application, un groupe de ressources est créé pour contenir la passerelle d’application. Voici la commande.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Une fois le groupe de ressources est créé, un réseau virtuel est créé pour la passerelle d’application.  Dans l’exemple suivant, l’espace d’adressage a été sous la forme 10.0.0.0/16 telle que définie dans les notes du scénario précédent.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Créer un sous-réseau

Une fois le réseau virtuel est créé, un sous-réseau est ajouté pour la passerelle d’application.  Si vous envisagez d’utiliser la passerelle d’application avec une application web hébergée sur le même réseau virtuel comme la passerelle d’application, veillez à laisser suffisamment de place pour un autre sous-réseau.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Créer la passerelle d’application

Une fois le réseau virtuel et le sous-réseau sont créés, les composants requis pour la passerelle d’application sont terminées. En outre un certificat précédemment exporté .pfx et le mot de passe pour le certificat sont requis pour l’étape suivante : adresses IP utilisées pour le back-end sont les adresses IP de votre serveur principal. Ces valeurs peuvent être privée IPs sur le réseau virtuel, ips publics ou des noms de domaine pleinement qualifié pour vos serveurs principaux.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] Pour obtenir une liste de paramètres qui peuvent être fournis lors de la création, exécutée la commande suivante : **créer de passerelle d’application azure réseau--aide**.

Cet exemple crée une passerelle d’application de base avec les paramètres par défaut pour l’écouteur, pool de back-end, back-end les paramètres http et les règles. Il configure également le déchargement SSL. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois la mise en service a réussi.
Si vous avez déjà votre application web avec le pool de back-end dans les étapes précédentes, une fois créé, l’équilibrage de charge commence.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à créer des sondes de santé personnalisées en visitant [créer une sonde d’état personnalisé](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et prendre le décryptage SSL coûteux sur vos serveurs web en visitant [Configurer le déchargement SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png