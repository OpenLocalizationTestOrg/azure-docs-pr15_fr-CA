<properties
    pageTitle="Déployer une application sur les jeux d’ordinateur virtuel échelle | Microsoft Azure"
    description="Déployer une application sur les jeux d’échelle de Machine virtuelle"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Déployer une application sur les jeux d’échelle de Machine virtuelle

Une application s’exécutant sur une échelle de machine virtuelle définie est généralement déployée dans un de trois manières différentes :

- L’installation de nouveaux logiciels sur une image de la plate-forme au moment du déploiement. Une image de plate-forme dans ce contexte est une image de système d’exploitation du marché d’Azure, Ubuntu 16.04, Windows Server 2012 R2, etc..

Vous pouvez installer le nouveau logiciel sur l’image d’une plate-forme à l’aide d’une [Extension de la machine virtuelle](../virtual-machines/virtual-machines-windows-extensions-features.md). Une extension de la machine virtuelle est un logiciel qui s’exécute lorsqu’un ordinateur virtuel est déployé. Vous pouvez exécuter tout code que vous le souhaitez au moment du déploiement à l’aide d’une extension de script personnalisé. [Ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) est un exemple de modèle de gestionnaire de ressources Azure avec deux extensions de machine virtuelle : Extension de Script Linux personnalisée pour installer Apache et PHP et au Diagnostic d’Extension pour émettre des données de performances utilisées par Azure Autoscale.

L’avantage de cette approche est avez un niveau de séparation entre votre code d’application et le système d’exploitation et vous pouvez mettre à jour votre application séparément. Bien sûr que signifie qu’est également plus déplacement des pièces et le temps de déploiement VM peut être plus long si il y a beaucoup de script à télécharger et à configurer.

**Si vous transmettez des informations sensibles dans votre commande d’Extension de Script personnalisée (par exemple, un mot de passe), veillez à spécifier le `commandToExecute` dans les `protectedSettings` les attributs de l’Extension de Script personnalisé à la place de la `settings` attribut.**

- Créer une image de machine virtuelle personnalisée qui inclut à la fois le système d’exploitation et les applications dans un VHD unique. Ici, l’ensemble de l’échelle se compose d’un ensemble de machines virtuelles copié à partir d’une image créée par vous, vous devez mettre à jour. Cette approche ne requiert aucune configuration supplémentaire au moment du déploiement VM. Toutefois, dans le `2016-03-30` version de VM échelle jeux (et versions antérieures), les disques du système d’exploitation pour les ordinateurs virtuels dans l’ensemble de l’échelle sont limités à un compte de stockage unique. Ainsi, vous pouvez avoir un maximum de 40 VMs dans un jeu d’échelle, et non de la VM 100 par l’échelle limite avec des images de la plate-forme. Pour plus d’informations, reportez-vous à la section [Présentation de conception la valeur échelle](./virtual-machine-scale-sets-design-overview.md) .

- Déployer une plate-forme ou une image personnalisée qui est essentiellement un conteneur hôte et installez votre application sous la forme d’un ou plusieurs conteneurs que vous gérez avec un outil de gestion orchestrator ou de configuration. Un autre intérêt de cette approche est que vous avez extrait votre infrastructure de cloud à partir de la couche d’application et que vous pouvez conserver séparément.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>Que se passe-t-il lorsqu’un ensemble d’échelle de machine virtuelle peut évoluer ?

Lorsque vous ajoutez des ordinateurs virtuels d’un ou plusieurs à une échelle définie par l’augmentation de la capacité : manuellement ou à l’échelle automatique – l’application est automatiquement installée. Pour exemple, si la valeur de l’échelle a extensions définies, ils s’exécutent sur une machine virtuelle chaque fois qu’il est créé. Si l’échelle est basé sur une image personnalisée de tout nouvel ordinateur virtuel est une copie de l’image personnalisée de source. Si la valeur de l’échelle VMs sont des hôtes de conteneur, vous pouvez avoir le code de démarrage pour charger les conteneurs dans une Extension de Script personnalisé, ou une extension peut installer un agent qui enregistre avec un orchestrator cluster (par exemple, le Service de conteneur Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Comment gérer les mises à jour de l’application dans les jeux d’échelle VM ?

Pour les mises à jour de l’application dans les jeux d’échelle VM, trois approches principales suivent à partir des trois méthodes de déploiement d’application précédent :

* Mise à jour avec les extensions de la machine virtuelle. La reconfiguration de toutes les extensions VM sont définies pour un ensemble d’échelle de VM sont exécutées chaque fois qu’un nouvel ordinateur virtuel est déployé, un ordinateur virtuel existant, ou une extension de la machine virtuelle est mis à jour. Si vous devez mettre à jour votre application, directement mise à jour d’une application via des extensions est une approche viable, vous mettez simplement à jour la définition de l’extension. Une méthode simple pour cela est en modifiant le fileUris pour pointer vers le nouveau logiciel.

* L’approche immuable image personnalisée. Vous pouvez vous concentrer sur la création d’un pipeline fiable pour automatiser la génération, de test et de déploiement des images lorsque vous gâteaux de l’application (ou des composants de l’application) dans une image de machine virtuelle. Vous pouvez concevoir votre architecture afin de faciliter le remplacement rapide d’un ensemble d’échelle intermédiaire dans la production. Un bon exemple de cette approche est le [pilote d’Azure Spinnaker fonctionne](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

GARNITURE d’étanchéité et Terraform prennent également en charge le Gestionnaire de ressources Azure, donc vous pouvez également définir vos images « en tant que code » et à les intégrer dans Azure, puis utiliser le disque dur virtuel dans votre jeu d’échelle. Toutefois, cela donc devient problématique pour les images de marché, où les scripts d’extensions/personnalisée deviennent plus importantes dans la mesure où vous ne pas manipuler directement les bits de marché.

* Mettre à jour les conteneurs. Abstraite la gestion du cycle de vie des applications à un niveau au-dessus de l’infrastructure de nuage, par exemple par encapsulation des applications et des composants de l’application, dans des conteneurs et de gérer ces conteneurs, par l’intermédiaire d’orchestrators du conteneur et les gestionnaires de configuration comme Chef/Marionnette.

L’échelle définie ordinateurs virtuels deviennent un substrat stable pour les conteneurs et requièrent uniquement les mises à jour du système d’exploitation et sécurité exceptionnel. Comme mentionné, le Service de conteneur Azure est un bon exemple de cette approche et la création d’un service autour de lui.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Comment vous restaurer une mise à jour du système d’exploitation sur des domaines de la mise à jour ?

Supposons que vous souhaitez mettre à jour votre image du système d’exploitation tout en conservant l’exécution VM échelle définie. Pour ce faire consiste à mettre à jour de la machine virtuelle une VM d’images à la fois. Vous pouvez le faire avec PowerShell ou Azure CLI. Il existe des commandes distinctes pour mettre à jour le modèle VM échelle définie (comment sa configuration est définie) et émettre des appels de « mise à niveau manuelle » sur des ordinateurs virtuels individuels.

[Ici](https://github.com/gbowerman/vmsstools) est un exemple de script de Python qui automatise le processus de mise à jour d’un domaine d’une mise à jour machine virtuelle échelle définie à la fois. (Avertissement : il s’agit plus d’une preuve de concept qu’une solution renforcée de prêt pour la production, vous souhaiterez peut-être ajouter certains etc. de vérification d’erreur.).
