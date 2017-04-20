<properties
   pageTitle="Créer une sonde personnalisée pour une passerelle d’application via le portail | Microsoft Azure"
   description="Apprenez à créer une sonde personnalisée pour la passerelle d’Application à l’aide du portail"
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

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Créer une sonde personnalisée pour la passerelle d’Application à l’aide du portail

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-create-probe-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-probe-ps.md)
- [Azure PowerShell classique](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Scénario

Le scénario suivant va créer une sonde d’état personnalisé dans une passerelle d’application existant.
Le scénario suppose que vous avez déjà suivi les étapes pour [créer une passerelle d’Application](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Créer la sonde

Les sondes sont configurés dans un processus en deux étapes via le portail. La première étape consiste à créer la sonde, vous ajoutez ensuite la sonde aux paramètres de la passerelle d’application http back-end.

### <a name="step-1"></a>Étape 1

Accédez à http://portal.azure.com et sélectionnez une passerelle d’application existant.

![Vue d’ensemble de la passerelle application][1]

### <a name="step-2"></a>Étape 2

**Sondes** puis cliquez sur le bouton **Ajouter** pour ajouter une nouvelle sonde.

![Ajouter des lames de sonde avec rempli d’informations][2]

### <a name="step-3"></a>Étape 3

Remplissez les informations requises pour la sonde et lorsque vous avez terminé, cliquez sur **OK**.

- **Nom** - il s’agit d’un nom convivial pour la sonde qui est accessible dans le portail.
- **Hôte** : il s’agit du nom d’hôte qui est utilisé pour la sonde. Applicable uniquement lorsque plusieurs sites est configuré sur Application Gateway, sinon utilisez '127.0.0.1'. Cela est différent du nom d’hôte de machine virtuelle.
- **Chemin d’accès** - le reste de l’url complète de la sonde personnalisée. Un chemin d’accès valide commence par '/'
- **Intervalle (secondes)** - de la fréquence d’exécution de la sonde pour vérifier la santé. Il est déconseillé de définir le coin inférieur à 30 secondes.
- **Délai d’attente (en secondes)** : la durée de que la sonde d’attend avant d’expirer. L’intervalle de délai d’attente doit être suffisamment élevé pour qu’un appel http est possible Vérifiez que la page d’état de santé de back-end est disponible.
- **Seuil de mauvais état** - nombre de tentatives infructueuses pour être considérée comme incorrecte. Un seuil de 0 signifie que si une vérification échoue au back-end est déterminé défectueux immédiatement.

> [AZURE.IMPORTANT] le nom d’hôte n’est pas le nom du serveur. C’est le nom de l’hôte virtuel en cours d’exécution sur le serveur d’application. La sonde est envoyée à http://(host name) :(port from httpsetting)/urlPath

![paramètres de configuration de sonde][3]

## <a name="add-probe-to-the-gateway"></a>Ajouter la sonde à la passerelle

Maintenant que la sonde a été créée, il est temps pour l’ajouter à la passerelle. Paramètres de sonde sont définis dans les paramètres http de back-end de la passerelle d’application.

### <a name="step-1"></a>Étape 1

Cliquez sur les **Paramètres HTTP** de la passerelle d’application, puis cliquez sur paramètres http du back-end en cours dans la fenêtre pour faire apparaître la lame de configuration.

![fenêtre Paramètres de HTTPS][4]

### <a name="step-2"></a>Étape 2

Sur la lame de paramètres **appGatewayBackEndHttp** , cliquez sur **sonde personnalisé d’utilisation** et choisissez la sonde créée dans la section [créer la sonde](#createprobe) .
Lorsque vous avez terminé, cliquez sur **OK** et les paramètres sont appliqués.

![lame de paramètres appgatewaybackend][5]

La sonde par défaut vérifie l’accès par défaut à l’application web. Maintenant qu’une sonde personnalisée a été créée, la passerelle d’application utilise le chemin d’accès personnalisé défini pour surveiller la santé pour le serveur principal sélectionné. Selon les critères qui ont été définies, la passerelle d’application vérifie le fichier spécifié dans la sonde. Si l’appel à l’hôte : Port / chemin d’accès ne renvoie pas une réponse Http 200 OK de l’état, le serveur est mis hors rotation, une fois atteint le seuil en mauvais état. Détection continue sur l’instance incorrecte pour déterminer dès qu’elle est saine. Une fois que l’instance est à nouveau ajoutée au pool de serveur sain le trafic commence circulant à nouveau lui et détection à l’instance continue à intervalle régulier d’utilisateur comme d’habitude.


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer le déchargement SSL avec la passerelle d’Application Azure consultez [Configurer le déchargement SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png