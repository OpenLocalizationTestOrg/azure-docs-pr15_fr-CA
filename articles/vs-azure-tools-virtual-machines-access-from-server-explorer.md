<properties
   pageTitle="L’accès à des ordinateurs virtuels Azure à partir de l’Explorateur de serveurs | Microsoft Azure"
   description="Obtenir une vue d’ensemble de la façon d’afficher, créer et gérer des machines virtuelles Azure (VM) dans l’Explorateur de serveurs dans Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>L’accès à des ordinateurs virtuels Azure à partir de l’Explorateur de serveurs

En utilisant l’Explorateur de serveurs dans Visual Studio, vous pouvez afficher des informations sur vos ordinateurs virtuels hébergés par Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>L’accès à des ordinateurs virtuels dans l’Explorateur de serveurs

Si vous avez des ordinateurs virtuels hébergés par Azure, vous pouvez y accéder dans l’Explorateur de serveurs. Vous devez vous connecter à votre abonnement Azure pour afficher vos services mobiles. Pour vous connecter, ouvrez le menu contextuel pour le nœud Azure dans l’Explorateur de serveurs et choisissez **se connecter à Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Pour obtenir des informations sur vos machines virtuelles

1. Dans l’Explorateur de serveurs, choisissez une machine virtuelle et cliquez ensuite sur la touche F4 pour afficher sa fenêtre de propriétés.

    Le tableau suivant indique quelles propriétés sont disponibles, mais elles sont en lecture seule. Pour les modifier, utilisez le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Propriété|Description|
  	|---|---|
  	|Nom DNS|L’URL et l’adresse Internet de l’ordinateur virtuel.|
  	|Environnement|Pour une machine virtuelle, la valeur de cette propriété est toujours Production.|
  	|Nom|Le nom de l’ordinateur virtuel.|
  	|Taille|La taille de la machine virtuelle, ce qui reflète la quantité de mémoire et espace disque disponible. Pour plus d’informations, consultez Comment : configurer les tailles de Machine virtuelle.|
  	|État|Les valeurs incluent le démarrage, démarré, arrêt, arrêté et récupération de l’état. Si le statut de récupération s’affiche, l’état actuel est inconnu. Les valeurs de cette propriété diffèrent des valeurs qui sont utilisées sur [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|L’ID d’abonnement pour votre compte Azure. Vous pouvez afficher ces informations sur [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885) en affichant les propriétés d’un abonnement.|

1. Cliquez sur un nœud de point de terminaison, puis affichez la fenêtre **Propriétés** .

1. Le tableau suivant décrit les propriétés disponibles pour des points de terminaison, mais ils sont en lecture seule. Pour ajouter ou modifier les points de terminaison pour un ordinateur virtuel, utilisez le [Azure portal classique](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|Propriété|Description|
  	|---|---|
  	|Nom|Identificateur du point de terminaison.|
  	|Port de privé|Le port pour l’accès réseau interne à votre application.|
  	|Protocole|Le protocole utilisé par la couche de transport pour ce point de terminaison, le protocole TCP ou UDP.|
  	|Port public|Le port qui est utilisé pour l’accès public à votre application.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation des rôles d’Azure dans Visual Studio, reportez-vous [à l’aide de bureau à distance avec les rôles d’Azure](vs-azure-tools-remote-desktop-roles.md).
