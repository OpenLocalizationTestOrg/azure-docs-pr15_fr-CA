<properties
   pageTitle="Créer une règle basée sur le chemin d’accès d’une passerelle d’application via le portail | Microsoft Azure"
   description="Apprenez à créer une règle basée sur le chemin d’accès d’une passerelle d’application à l’aide du portail"
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

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Créer une règle basée sur le chemin d’accès d’une passerelle d’application à l’aide du portail

> [AZURE.SELECTOR]
- [Azure portal](application-gateway-create-url-route-portal.md)
- [Azure PowerShell de gestionnaire de ressources](application-gateway-create-url-route-arm-ps.md)

Routage basé sur le chemin d’accès d’URL vous permet d’associer des itinéraires basés sur le chemin d’accès de l’URL de requête Http. Il vérifie s’il existe un itinéraire vers un pool de back-end configuré pour les listes d’URL dans une Application passerelle et envoyer le trafic réseau vers le pool principal défini. Une utilisation courante de routage basé sur l’URL est à la charge des demandes de solde pour les types de contenu différents pour les pools des serveurs principaux différents.

Passerelle d’application basée sur l’URL de routage présente un nouveau type de règle. Passerelle d’application possède deux types de règles : les règles de base et basée sur le chemin d’accès. Type de règle de base fournit un service de répétition alternée pour les pools de back-end en règles de chemin d’accès en plus de la distribution de la répétition alternée, prend également le modèle de chemin d’accès de l’URL de la demande en considération lors du choix du pool back-end.

## <a name="scenario"></a>Scénario

Le scénario suivant parcourt la création d’une règle basée sur le chemin d’accès dans une passerelle d’application existant.
Le scénario suppose que vous avez déjà suivi les étapes pour [créer une passerelle d’Application](application-gateway-create-gateway-portal.md).

![itinéraire de l’URL][scenario]

## <a name="createrule"></a>Créer la règle de chemin d’accès

Une règle de chemin d’accès requiert son propre port d’écoute, avant de créer la règle vérifier que vous avez un écouteur disponible à utiliser.

### <a name="step-1"></a>Étape 1

Accédez à http://portal.azure.com et sélectionnez une passerelle d’application existant. Cliquez sur **les règles**

![Vue d’ensemble de la passerelle application][1]

### <a name="step-2"></a>Étape 2

Cliquez sur **basée sur le chemin d’accès** pour ajouter une nouvelle règle basée sur le chemin d’accès.

### <a name="step-3"></a>Étape 3

La lame de **Ajouter une règle basée sur le chemin d’accès** comporte deux sections. La première section est où vous avez défini l’écouteur, le nom de la règle et les paramètres de chemin d’accès par défaut. Les paramètres de chemin d’accès par défaut sont pour les itinéraires qui ne relèvent pas de la gamme basée sur le chemin d’accès personnalisée. La deuxième section de la lame **d’Ajouter une règle basée sur le chemin d’accès** est l’où vous définissez les règles basées sur le chemin d’accès lui-même.

**Paramètres de base**

- **Nom** - il s’agit d’un nom convivial pour la règle qui est accessible dans le portail.
- **Écouteur** - il s’agit de l’écouteur utilisé pour la règle.
- **Pool de serveur principal par défaut** : ce paramètre est le paramètre qui définit le back-end à utiliser pour la règle par défaut
- **Les paramètres HTTP par défaut** - ce paramètre est le paramètre qui définit les paramètres HTTP à utiliser pour la règle par défaut.

**Règles basées sur le chemin d’accès**

- **Nom** - il s’agit d’un nom convivial pour une règle basée sur le chemin d’accès.
- Les **chemins d’accès** - ce paramètre définit le chemin d’accès que la règle va rechercher pour transférer le trafic
- **Pool de serveur principal** - ce paramètre est le paramètre qui définit le back-end à utiliser pour la règle
- Le **paramètre HTTP** - ce paramètre est le paramètre qui définit les paramètres HTTP à utiliser pour la règle.

>[AZURE.IMPORTANT] Chemins d’accès : La liste des modèles de chemin d’accès pour faire correspondre. Chacun doit commencer par / et le seul endroit où une «\*» est autorisé est à la fin. Exemples valides sont /xyz, /xyz* ou /xyz/*.  

![Ajouter des lames de règle basée sur le chemin d’accès avec les informations de remplissage][2]

Ajout d’une règle basée sur le chemin d’accès à une passerelle d’application existante est un processus simple via le portail. Après avoir créé une règle basée sur le chemin d’accès, il peut être modifié pour ajouter facilement des règles supplémentaires. 

![Ajout des règles basées sur le chemin d’accès supplémentaires][3]

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer le déchargement SSL avec la passerelle d’Application Azure consultez [Configurer le déchargement SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png