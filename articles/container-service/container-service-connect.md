<properties
   pageTitle="Se connecter à un cluster Service de conteneur Azure | Microsoft Azure"
   description="Se connecter à un cluster Service de conteneur Azure à l’aide d’un tunnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Services-docker, conteneurs, Micro, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# <a name="connect-to-an-azure-container-service-cluster"></a>Se connecter à un cluster Service de conteneur Azure

Le DC/système d’exploitation et les clusters Docker par essaim qui sont déployées par le Service de conteneur Azure exposent des points de terminaison REST. Toutefois, ces points de terminaison ne sont pas ouverts au monde extérieur. Pour gérer ces points de terminaison, vous devez créer un tunnel de SSH (Secure Shell). Après un SSH tunnel a été établi, vous pouvez exécuter des commandes sur les points de terminaison du cluster et afficher l’interface utilisateur de cluster via un navigateur sur votre propre système. Ce document vous guide dans le processus de création d’un tunnel SSH à partir de Linux, OS X et Windows.

>[AZURE.NOTE] Vous pouvez créer une session SSH avec un système de gestion de cluster. Toutefois, nous ne le recommandons. Travailler directement sur un système de gestion expose le risque de modifications de configuration involontaire.   

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Créer un tunnel SSH sous Linux ou OS X

La première chose que vous effectuez lorsque vous créez un tunnel SSH sous Linux ou OS X est de localiser le nom DNS public de masques d’équilibrage de charge. Pour ce faire, développez le groupe de ressources pour chaque ressource est affiché. Recherchez et sélectionnez l’adresse IP publique du maître. Une lame qui contient des informations sur l’adresse IP publique, qui inclut le nom DNS s’ouvre. Enregistrer ce nom pour une utilisation ultérieure. <br />


![Nom DNS publique](media/pubdns.png)

Ouvrez un shell et exécutez la commande suivante où :

**Le PORT** est le port du point de terminaison que vous souhaitez exposer. Pour l’essaim, il s’agit de 2375. Pour un contrôleur de domaine/système d’exploitation, utilisez le port 80.  
**Nom d’utilisateur** est le nom d’utilisateur fourni lorsque vous avez déployé le cluster.  
**DNSPREFIX** est le préfixe DNS que vous avez fournies lorsque vous avez déployé le cluster.  
**La région** est la région dans laquelle se trouve le groupe de ressources.  
**PATH_TO_PRIVATE_KEY** [Facultatif] est le chemin d’accès à la clé privée qui correspond à la clé publique que vous avez fourni lors de la création du cluster Service de conteneur. Utilisez cette option avec le i - indicateur.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> Le port de connexion SSH est 2200--pas le port standard 22.

## <a name="dcos-tunnel"></a>Tunnel de contrôleur de domaine/OS

Pour ouvrir un tunnel vers les points de terminaison DC/système d’exploitation-connexes, exécutez une commande semblable à la suivante :

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez désormais accéder les points de terminaison DC/système d’exploitation-liées à :

- CONTRÔLEUR DE DOMAINE/OS :`http://localhost/`
- Marathon :`http://localhost/marathon`
- Mesos :`http://localhost/mesos`

De même, vous pouvez atteindre les autres API pour chaque application par le biais de ce tunnel.

## <a name="swarm-tunnel"></a>Tunnel de l’essaim

Pour ouvrir un tunnel vers le point de terminaison par essaims particulaires, exécutez une commande semblable à la suivante :

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Maintenant, vous pouvez définir votre variable d’environnement DOCKER_HOST comme suit. Vous pouvez continuer à utiliser l’interface de ligne de commande (CLI) Docker comme à l’accoutumée.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Créer un tunnel SSH sur Windows

Il existe plusieurs options pour la création de tunnels SSH sur Windows. Ce document décrit comment utiliser PuTTY pour cela.

Télécharger PuTTY sur votre système Windows et exécutez l’application.

Entrez un nom d’hôte qui comprend le nom d’utilisateur admin cluster et le nom DNS public du premier maître dans le cluster. Le **Nom de l’hôte** se présente comme suit : `adminuser@PublicDNS`. Entrez le **Port**2200.

![Configuration de mastic 1](media/putty1.png)

Sélectionnez **SSH** et **l’authentification**. Ajoutez votre fichier de clé privée pour l’authentification.

![Configuration de mastic 2](media/putty2.png)

Sélectionnez des **Tunnels** et configurer les éléments suivants transmis des ports :
- **Port source :** Votre préférence--utiliser 80 pour le contrôleur de domaine/OS ou 2375 de l’essaim.
- **Destination :** Utilisez localhost:80 pour DC/OS ou localhost:2375 de l’essaim.

L’exemple suivant est configuré pour le contrôleur de domaine/système d’exploitation, mais le ressemblera pour Docker par essaim.

>[AZURE.NOTE] Port 80 ne doit pas être en cours d’utilisation lorsque vous créez ce tunnel.

![Configuration de mastic 3](media/putty3.png)

Lorsque vous avez terminé, enregistrez la configuration de la connexion et connecter la session mastic. Lorsque vous vous connectez, vous pouvez voir la configuration du port dans le journal des événements mastic.

![Journal des événements de mastic](media/putty4.png)

Lorsque vous avez configuré le tunnel pour le contrôleur de domaine/système d’exploitation, vous pouvez accéder au point de terminaison associé à :

- CONTRÔLEUR DE DOMAINE/OS :`http://localhost/`
- Marathon :`http://localhost/marathon`
- Mesos :`http://localhost/mesos`

Lorsque vous avez configuré le tunnel Docker par essaims particulaires, vous pouvez accéder au cluster de l’essaim via la CLI de Docker. Vous devez d’abord configurer une variable d’environnement Windows nommée `DOCKER_HOST` avec une valeur de ` :2375`.

## <a name="next-steps"></a>Étapes suivantes

Déployer et gérer des récipients munis d’un contrôleur de domaine/OS ou essaim :

- [Fonctionne avec le Service de conteneur Azure et DC/OS](container-service-mesos-marathon-rest.md)
- [Travailler avec le Service de conteneur Azure et Docker essaim](container-service-docker-swarm.md)
