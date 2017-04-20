<properties
   pageTitle="Créer une passerelle d’application avec le pare-feu pour applications web via le portail | Microsoft Azure"
   description="Apprenez à créer une passerelle d’Application avec le pare-feu pour applications web à l’aide du portail"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
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

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Créer une passerelle d’application avec le pare-feu pour applications web à l’aide du portail

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-web-application-firewall-powershell.md)

Le pare-feu d’application web (WAF) dans Azure Application Gateway protège les applications web contre les attaques courantes sur le web comme SQL injection, les attaques de script entre sites et détournements de session. Application Web protège contre un grand nombre de l’OWASP avoir top 10 web les vulnérabilités courantes.

Passerelle d’Application Azure est un équilibreur de charge de couche 7. Il fournit le basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site.
L’application fournit de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC), notamment l’équilibrage de charge HTTP, l’affinité de cookie de session, déchargement de Secure Sockets Layer (SSL), les sondes d’état personnalisé, prise en charge de plusieurs sites et bien d’autres.
Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble de la passerelle Application](application-gateway-introduction.md)

## <a name="scenarios"></a>Scénarios

Dans cet article, il existe deux scénarios :

Dans le premier scénario, vous apprenez à [Ajouter des pare-feu d’application web vers une passerelle d’application existant](#add-web-application-firewall-to-an-existing-application-gateway).

Dans le second scénario, vous apprendrez à [créer une passerelle d’application avec le pare-feu pour applications web](#create-an-application-gateway-with-web-application-firewall)

![Exemple de scénario][scenario]

>[AZURE.NOTE] Sondes de configuration supplémentaire de la passerelle d’application, y compris leur santé personnalisée, les adresses de pool back-end et des règles supplémentaires sont configurées après avoir configuré la passerelle d’application et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

Passerelle d’Application Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace adresse pour plusieurs sous-réseaux. Dès que vous déployez une passerelle d’application à un sous-réseau, passerelles d’application supplémentaire seulement peuvent être ajoutés au sous-réseau.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajouter des pare-feu d’application web à une passerelle d’application existante

Ce scénario met à jour une passerelle d’application existant pour prendre en charge les pare-feu pour applications web en mode de prévention.

### <a name="step-1"></a>Étape 1

Accédez au portail Azure, sélectionnez une passerelle d’Application existant.

![Création d’Application passerelle][1]

### <a name="step-2"></a>Étape 2

Cliquez sur **Configuration** et mettre à jour les paramètres de passerelle de l’application. Lorsque vous avez terminé cliquez sur **Enregistrer**

Mettre à jour une passerelle d’application existant pour prendre en charge les pare-feu pour applications web sont les suivantes :

- **Niveau** - le niveau sélectionné doit être **WAF** pour prendre en charge les pare-feu pour applications web
- **Taille de point de stock** : ce paramètre est la taille de la passerelle d’application avec le pare-feu pour applications web, les options disponibles sont (**moyenne** et **grande**).
- **État du pare-feu** - ce paramètre désactive ou Active le pare-feu d’application web.
- **Mode pare-feu** - ce paramètre est la façon dont pare-feu d’application web traite le trafic malveillant. Mode de **détection** enregistre uniquement les événements, où le mode de **prévention** consigne les événements et arrête le trafic malveillant.

![paramètres de base d’Affichage de lame][2]

>[AZURE.NOTE] Pour afficher les journaux de pare-feu d’application web, les tests de diagnostic doivent être activés et ApplicationGatewayFirewallLog est sélectionnée. Vous pouvez choisir un nombre d’instances de 1 aux fins de test. Il est important de savoir que n’importe quel nombre d’instances sous deux instances n’est pas couvert par le contrat SLA et ne sont donc pas recommandées. Passerelles de petite taille ne sont pas disponibles lors de l’utilisation de pare-feu pour applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle d’application avec le pare-feu pour applications web

Ce scénario est :

- Créer une passerelle d’application support web application pare-feu avec deux instances.
- Créer un réseau virtuel appelé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16.
- Créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme son bloc CIDR.
- Configurer un certificat pour le déchargement SSL.

### <a name="step-1"></a>Étape 1

Accédez au portail Azure, cliquez sur **Nouveau** > **réseau** > **Application Gateway**

![Création d’Application passerelle][1-1]

### <a name="step-2"></a>Étape 2

Remplissez ensuite les informations de base sur la passerelle d’application. Veillez à choisir **WAF** que la couche. Lorsque vous avez terminé, cliquez sur **OK**

Les informations requises pour les paramètres de base sont la suivante :

- **Nom** : le nom de la passerelle d’application.
- **Niveau** - le niveau de la passerelle d’application, les options disponibles sont (**Standard** et **WAF**). Pare-feu pour applications Web est disponible uniquement dans la couche WAF.
- **Taille de point de stock** : ce paramètre est la taille de la passerelle d’application, les options disponibles sont (**moyenne** et **grande**).
- **Nombre d’instances** : le nombre d’instances, cette valeur doit être un nombre compris entre **2** et **10**.
- **Groupe de ressources** - le groupe de ressources pour maintenir la passerelle d’application, elle peut être un groupe de ressources existant ou un nouveau.
- **Emplacement** - la région pour la passerelle d’application, il s’agit du même emplacement que le groupe de ressources. *L’emplacement est important que le réseau virtuel et l’adresse IP publique doit être dans le même emplacement que la passerelle*.

![paramètres de base d’Affichage de lame][2-2]

>[AZURE.NOTE] Vous pouvez choisir un nombre d’instances de 1 aux fins de test. Il est important de savoir que n’importe quel nombre d’instances sous deux instances n’est pas couvert par le contrat SLA et ne sont donc pas recommandées. Passerelles de petite taille ne sont pas pris en charge pour les scénarios de pare-feu d’application web.

### <a name="step-3"></a>Étape 3

Une fois les paramètres de base sont définis, l’étape suivante consiste à définir le réseau virtuel à utiliser. Le réseau virtuel héberge l’application que la passerelle d’application équilibrage de la charge.

Cliquez sur **Choisir un réseau virtuel** pour configurer le réseau virtuel.

![affichage des paramètres de la passerelle d’application de lame][3]

### <a name="step-4"></a>Étape 4

De la lame de **Choisir le réseau virtuel** , cliquez sur **Créer un nouveau**

Tandis que ne pas expliquée dans ce scénario, un réseau virtuel peut être sélectionné à ce stade.  Si un réseau virtuel est utilisé, il est important de savoir que le réseau virtuel doit être un sous-réseau vide ou un sous-réseau uniquement passerelle des ressources d’application à utiliser.

![Choisissez la lame de réseau virtuel][4]

### <a name="step-5"></a>Étape 5

Remplissez les informations de réseau de la lame de **Créer un réseau virtuel** , comme décrit dans la description du [scénario](#scenario) précédente.

![Créer des lames de réseau virtuel avec les informations saisies][5]

### <a name="step-6"></a>Étape 6

Une fois le réseau virtuel est créé, l’étape suivante consiste à définir le front-end IP pour la passerelle d’application. À ce stade, le choix est entre un public ou une adresse IP privée pour le serveur frontal. Le choix dépend de l’application internet en vis-à-vis ou internes uniquement. Ce scénario suppose l’utilisation d’une adresse IP publique. Pour choisir une adresse IP privée, le bouton **privé** peut être sélectionné. Une adresse IP automatiquement affectée est choisie, ou vous pouvez cliquer sur la case à cocher **Choisir une adresse IP privée spécifique** pour entrer une manuellement.

Cliquez sur **Choisir une adresse IP publique**. Si une adresse IP publique d’existante n’est disponible, il peut être choisi à ce stade, dans ce scénario, vous créez une nouvelle adresse IP publique. Cliquez sur **Créer nouveau**

![Choisissez la lame d’adresse IP publique][6]

### <a name="step-7"></a>Étape 7

Ensuite donner un nom convivial à l’adresse IP publique et cliquez sur **OK**

![Créer des lames d’adresse IP publique][7]

### <a name="step-8"></a>Étape 8

Ensuite, de l’installation de la configuration de l’écouteur.  Si **le protocole http** est utilisé, rien n’est laissé pour configurer et vous pouvez cliquer sur **OK** . Pour utiliser le **protocole https** configuration supplémentaires sont nécessaires.

Pour utiliser le **protocole https**, un certificat est requis. La clé privée du certificat est nécessaire pour un fichier .pfx exporter le certificat doit être fourni et le mot de passe pour le fichier.

Cliquez sur **HTTPS**, cliquez sur l’icône de **dossier** située en regard de la zone de texte **les certificats PFX de télécharger** .
Naviguez jusqu’au fichier .pfx certificat sur votre système de fichiers. Une fois qu’il est sélectionné, donner au certificat un nom convivial et taper le mot de passe pour le fichier .pfx.

Une fois terminé, cliquez sur **OK** pour passer en revue les paramètres de la passerelle d’Application.

![Section de Configuration de récepteur sur la lame de paramètres][8]

### <a name="step-9"></a>Étape 9

Configurer les paramètres spécifiques de **WAF** .

- **État du pare-feu** - ce paramètre Active ou désactive la WAF.
- **Mode pare-feu** - ce paramètre détermine que le actions WAF prend en charge le trafic malveillant. Si **la détection** est choisi, le trafic est enregistré uniquement.  Si la **prévention** est choisi, le trafic est enregistré et s’est arrêté avec un non autorisé 403.

![paramètres de pare-feu d’application Web][9]

### <a name="step-10"></a>Étape 10

Examinez la page Résumé, puis cliquez sur **OK**.  Désormais, la passerelle d’application est la file d’attente et créée.

### <a name="step-12"></a>Étape 12

Une fois que la passerelle d’application a été créée, y accéder dans le portail pour poursuivre la configuration de la passerelle d’application.

![Affichage des ressources Application Gateway][10]

Ces étapes créent une passerelle d’application de base avec les paramètres par défaut pour l’écouteur, pool de back-end, back-end les paramètres http et les règles. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois la mise en service est réussie

## <a name="next-steps"></a>Étapes suivantes

Apprenez à configurer la journalisation des diagnostics, pour consigner les événements détectés ou empêchés avec pare-feu pour applications Web en visitant les [Diagnostics de passerelle d’Application](application-gateway-diagnostics.md)

Apprenez à créer des sondes de santé personnalisées en visitant [créer une sonde d’état personnalisé](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et prendre le décryptage SSL coûteux sur vos serveurs web en visitant [Configurer le déchargement SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png