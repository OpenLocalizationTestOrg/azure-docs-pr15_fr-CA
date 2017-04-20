<properties 
  pageTitle="Déploiement de votre propre registre privé Docker sur Azure | Microsoft Azure"
  description="Décrit comment vous pouvez utiliser Docker Registre pour héberger vos images de conteneur dans le service de stockage des objets Blob Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Déploiement de votre propre registre privé Docker sur Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Ce document décrit le Docker Registre privé est et montre comment vous pouvez déployer une image du conteneur Docker Registre 2.0 à un Registre privé de Docker sur Microsoft Azure avec le stockage Blob Azure.

Ce document suppose que :

1. Vous savez comment utiliser Docker et ont des images Docker à stocker. (Vous n’est pas ? [En savoir plus sur Docker](https://www.docker.com))
2. Vous disposez d’un serveur qui a moteur Docker installé. (Vous n’est pas ? [Faire rapidement sur Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>En quoi consiste un Registre Docker privé ?

Pour expédier vos applications en conteneur vers le nuage, vous construisez une image du conteneur Docker et stockez quelque part afin qu’il peut être utilisé par vous-même et par d’autres utilisateurs. 

Lors de la création d’une image de conteneur et l’expédition vers le nuage sont simple, il s’agit d’un défi pour stocker l’image générée de manière fiable. Pour cette raison, Docker propose un service centralisé, appelé [Concentrateur de Docker] [ docker-hub] pour stocker des images sur le nuage de conteneur et vous permet de créer des conteneurs à tout moment à l’aide de ces images.

Bien que le [Concentrateur de Docker] [ docker-hub] est un service payant pour le stockage de votre conteneur d’application privé des images, des besoins des développeurs Docker leurs aspects et fournit un ensemble d’outils open source pour stocker vos images dans votre propre registre Docker privé derrière un pare-feu ou sur le site sans appuyer sur l’Internet public.
Stockage des objets Blob Azure étant facile à sécuriser, vous pouvez les utiliser rapidement pour créer et utiliser un Registre de Docker privé dans Azure que vous contrôlez vous-même.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>Pourquoi doit héberger un Registre Docker sur Azure ?

En hébergeant votre instance de Registre de Docker sur Microsoft Azure et stocker vos images sur le stockage Blob Azure, vous pouvez avoir plusieurs avantages :

**Sécurité :** Vos images Docker ne laissent pas les centres de données Azure, afin qu’ils ne traversent pas le réseau Internet public comme si vous utilisiez le concentrateur de Docker.
  
**Performances :** Comme vos applications, vos images Docker sont stockés dans le même centre de données ou la même région. Cela signifie que les images se fera plus rapidement et de manière plus fiable par rapport au concentrateur de Docker.

**La fiabilité :** En utilisant le stockage Blob Microsoft Azure, vous pouvez rendre l’utilisation de nombreuses propriétés de stockage à haute disponibilité, redondance, stockage de prime (SSD) et ainsi de suite.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configuration de Registre de Docker d’utiliser le stockage Blob Azure

(Il est recommandé de lire la [documentation Docker du Registre 2.0][-docs du Registre] avant de continuer.)

Vous pouvez [configurer] [ registry-config] votre Registre Docker de deux façons différentes.
Vous pouvez :

1. Utilisez un `config.yml` fichier. Dans ce cas, vous devrez créer une image distincte de Docker sur des `registry` image.
2. Remplacer le fichier de configuration par défaut les variables d’environnement : Obtient les choses sans la création et la maintenance d’une image Docker distincte.

Par souci de simplicité, cette rubrique suit l’option 2, en utilisant les variables d’environnement.

Pour exécuter un Registre Docker instance qui :

* utilise votre compte de stockage Azure pour stocker les images
* écoute sur le port de la Machine virtuelle 5000
* aucune méthode d’authentification configuré (non recommandé, de voir la Remarque ci-dessous)

Vous devez exécuter la commande Docker suivante dans votre terminal bash (remplacer `<storage-account>` et `<storage-key>` avec vos informations d’identification) :

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Une fois que la commande s’arrête, vous pouvez voir le conteneur qui héberge votre instance privée de Registre de Docker en exécutant la `docker ps` commande sur votre hôte :

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Configuration de la sécurité du Registre Docker n’est pas couverte dans ce document et votre Registre n’est accessibles à toute personne sans authentification par défaut si vous ouvrez le port pour le port du Registre sur le point de terminaison de Machine virtuelle ou l’équilibreur de charge si vous utilisez la commande de déploiement ci-dessus.
>
> Veuillez lire la [Configuration du Registre de Docker] [ registry-config] documentation pour savoir comment sécuriser vos images et l’instance du Registre.

## <a name="next-steps"></a>Étapes suivantes

Une fois le Registre configuré, il est temps de plus l’utiliser. Démarrer avec le docker [- docs du Registre]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[Registre-documents]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
