<properties
   pageTitle="Application ou un service de Marathon spécifiques à l’utilisateur | Microsoft Azure"
   description="Créer une application ou un service de Marathon spécifiques à l’utilisateur"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, Marathon, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Créer une application ou un service de Marathon spécifiques à l’utilisateur

Service de conteneur Azure fournit un ensemble de serveurs maîtres sur lequel nous préconfigurer Apache Mesos et Marathon. Ceux-ci peuvent être utilisés pour orchestrer vos applications sur le cluster, mais il est préférable de ne pas utiliser les serveurs maîtres à cet effet. Par exemple, réglage de la configuration de Marathon nécessite Connectez les serveurs maîtres et apporter des modifications, cela encourage les serveurs maîtres uniques qui sont un peu différentes de la norme et doivent être soignés et géré de manière indépendante. En outre, la configuration requise par une équipe peut-être pas la configuration optimale pour une autre équipe.

Dans cet article, nous expliquerons comment ajouter une application ou un service de Marathon spécifiques à l’utilisateur.

Parce que ce service appartient à un seul utilisateur ou une équipe, ils sont libres de le configurer en aucune manière qu’ils souhaitent. En outre, Azure conteneur garantit que le service continue à s’exécuter. Si le service échoue, Azure conteneur Service redémarre pour vous. La plupart du temps vous même conscience qu’il avait des interruptions de service.

## <a name="prerequisites"></a>Conditions préalables

[Déployer une instance de Service de conteneur Azure](container-service-deployment.md) avec type d’orchestrator DC/système d’exploitation et [vous assurer que votre client peut se connecter à votre cluster](container-service-connect.md). En outre, procédez comme suit.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Créer une application ou un service de Marathon spécifiques à l’utilisateur

Commencez par créer un fichier de configuration de JSON qui définit le nom du service d’application que vous souhaitez créer. Ici, nous utilisons `marathon-alice` comme nom du framework. Enregistrez le fichier sous quelque chose comme `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Ensuite, utilisez la CLI de contrôleur de domaine/système d’exploitation pour installer l’instance Marathon avec les options qui sont définies dans le fichier de configuration :

```bash
dcos package install --options=marathon-alice.json marathon
```

Vous devez maintenant voir votre `marathon-alice` service s’exécutant dans l’onglet Services de l’interface utilisateur du contrôleur de domaine/système d’exploitation. L’interface utilisateur sera `http://<hostname>/service/marathon-alice/` si vous souhaitez y accéder directement.

## <a name="set-the-dcos-cli-to-access-the-service"></a>La valeur de l’interface CLI de contrôleur de domaine/système d’exploitation pour accéder au service

Vous pouvez éventuellement configurer votre CLI DC/système d’exploitation pour accéder à ce nouveau service en définissant le `marathon.url` propriété pour pointer vers le `marathon-alice` d’instance comme suit :

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Vous pouvez vérifier quelle instance de Marathon par votre CLI contre le `dcos config show` commande. Vous pouvez revenir à l’utilisation de votre service de Marathon maître avec la commande `dcos config unset marathon.url`.
