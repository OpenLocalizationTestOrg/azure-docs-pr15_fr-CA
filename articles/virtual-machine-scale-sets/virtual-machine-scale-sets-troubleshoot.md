<properties
    pageTitle="Résoudre les problèmes d’échelle automatique avec des jeux d’échelle de Machine virtuelle | Microsoft Azure"
    description="Résoudre les problèmes d’échelle automatique avec des jeux d’échelle de Machine virtuelle. Comprendre les problèmes généralement rencontrés et comment les résoudre."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Échelle de résolution des problèmes avec des jeux d’échelle de Machine virtuelle

**Problème** : vous avez créé une infrastructure autoscaling dans Azure le Gestionnaire de ressources à l’aide de jeux d’échelle VM – par exemple par le déploiement d’un modèle comme suit : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – vous avez vos règles d’échelle définies et il fonctionne très bien, sauf que, quelle que soit la quantité de charge vous placer sur les ordinateurs virtuels, il ne l’échelle automatique.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

Quelques éléments à prendre en compte sont les suivants :

- Nombre de noyaux chaque machine virtuelle a, et que vous chargez chaque cœur ?
 L’exemple de modèle Azure Quickstart ci-dessus a un script do_work.php, qui charge un seul cœur. Si vous utilisez une machine virtuelle supérieure à un seul cœur, taille de la mémoire virtuelle comme Standard_A1 ou D1, vous devez exécuter cette charge plusieurs fois. Vérifiez le nombre de noyaux vos ordinateurs virtuels en passant en revue les [machines virtuelles de tailles pour Windows dans Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Le nombre des ordinateurs virtuels dans l’ensemble d’échelle VM, faites-vous travail sur chacun d’eux ?

    Une mise à l’échelle événement a lieu uniquement lorsque la moyenne de l’UC de **tous les** ordinateurs virtuels dans un jeu d’échelle dépasse la valeur du seuil, en fonction du temps interne défini dans les règles de l’échelle.

- Vous avez manqué d’événements échelle ?

    Vérifiez que les journaux d’audit dans le portail Azure pour les événements de l’échelle. Peut-être a une échelle haut et une échelle vers le bas qui a été manquée. Vous pouvez filtrer par « Echelle »...

    ![Journaux d’audit][audit]

- Sont les seuils de l’échelle et horizontale suffisamment différents ?

    Supposons que vous définissez une règle à l’échelle lorsque la moyenne du processeur est supérieur à 50 % plus de 5 minutes et à l’échelle lorsque la moyenne du processeur est inférieure à 50 %. Cela entraînera un problème « battements » lors de l’utilisation du processeur est proche de ce seuil, actions d’échelle constamment augmenter et réduire la taille de l’ensemble. De ce fait, le service autoscale tente d’empêcher « représentant », qui peuvent se manifester sous la forme non mise à l’échelle. Par conséquent, assurez-vous que votre montée et seuils d’échelle sont suffisamment différents pour permettre un espace entre la mise à l’échelle.

- Avez-vous écrit votre propre modèle JSON ?

    Il est facile de commettre, donc commencez avec un modèle comme celui ci-dessus qui est éprouvée de travailler et apporter de petites modifications incrémentielles. 

- Pouvez vous manuellement mettre à l’échelle ou arrière ?

    Essayez de redéployer la ressource d’ordinateur virtuel échelle définie avec un paramètre différent de « capacité » pour modifier le nombre de machines virtuelles manuellement. Un exemple de modèle pour ce faire est ici : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – vous devrez peut-être modifier le modèle pour s’assurer qu’il a la même taille de machine que votre échelle définie utilise. Si vous pouvez modifier le nombre de machines virtuelles manuellement, vous savez que le problème est isolé à l’échelle automatique.

- Vérifiez votre Microsoft.Compute/virtualMachineScaleSet et Microsoft.Insights des ressources dans l' [Explorateur de ressources Azure](https://resources.azure.com/)

    Il s’agit d’un outil indispensable de dépannage qui vous indique l’état de vos ressources d’Azure le Gestionnaire de ressources. Cliquez sur votre abonnement et examinez le groupe de ressources que vous dépannez. Sous le fournisseur de ressources de calcul examiner le jeu d’échelle de machine virtuelle que vous avez créé et vérifier la vue d’Instance, qui vous indique l’état d’un déploiement. Vérifiez également la vue d’instance des ordinateurs virtuels dans l’ensemble de l’échelle de machine virtuelle. Ensuite, allez dans le fournisseur de ressources Microsoft.Insights et vérifier que les règles de l’échelle sont de bonne qualités.

- L’extension de Diagnostic fonctionne et émission de données de performances ?

    __Mise à jour :__ Autoscale Azure a été améliorée pour utiliser un pipeline de mesures basée sur l’hôte qui ne requiert plus une extension de diagnostics d’être installée. Cela signifie que la prochaine que quelques paragraphes ne s’appliquent plus si vous créez une application autoscaling en utilisant le nouveau pipeline. Exemples de modèles Azure qui ont été convertis pour utiliser le pipeline de l’hôte : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    À l’aide de mesures de basée sur l’hôte pour l’échelle est la meilleure pour les raisons suivantes :

    - Moins de pièces mobiles en tant qu’aucune extension de tests de diagnostic doivent être installés.
    - Modèles plus simples. Il suffit d’ajouter les règles autoscale perspectives à un modèle de jeu d’échelle existant.
    - Reporting plus fiable et plus rapide de lancer de nouvelles machines virtuelles.

    Les raisons seulement que vous souhaiterez peut-être continuer à utiliser une extension de diagnostic serait si vous avez besoin de mémoire de diagnostic reporting/mise à l’échelle. Les audits basés sur l’hôte ne signalent pas de mémoire.

    Dans cette optique, suivez uniquement le reste de cet article si vous utilisez toujours des extensions de diagnostics pour votre autoscaling.

    Échelle automatique dans le Gestionnaire de ressources Azure peut fonctionner (mais il n’a plus à) au moyen d’une machine virtuelle, l’extension appelée l’Extension des tests de diagnostic. Il émet des données de performance pour un compte de stockage que vous définissez dans le modèle. Ces données sont regroupées puis par le service de surveillance d’Azure.

    Si le service d’informations ne peut pas lire les données à partir d’ordinateurs virtuels, il est supposé vous enverra un e-mail – par exemple si les ordinateurs virtuels ont été enfoncé, vérifiez votre adresse de courriel (celui spécifié lors de la création du compte Azure).

    Vous pouvez également accéder et consulter les données vous-même. Examinez le compte de stockage Azure à l’aide d’un Explorateur du nuage. Pour l’exemple à l’aide de l' [Explorateur de nuage de Visual Studio](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), ouvrez une session et choisir l’abonnement Azure vous utilisez, et le nom de compte de stockage Diagnostics référencé dans la définition d’extension de Diagnostics de votre modèle de déploiement...

    ![Explorateur de nuage][explorer]

    Vous y trouverez un certain nombre de tables de stockage de données à partir de chaque ordinateur virtuel. Prise de Linux et la mesure de l’unité centrale, par exemple, recherchez les lignes les plus récentes. L’Explorateur de nuage de Visual Studio prend en charge un langage de requête afin de pouvoir exécuter une requête comme « Timestamp gt datetime'2016-02-02T21:20:00Z' » pour vous assurer que vous obtenez les événements les plus récents (supposent que le temps est l’heure UTC). Fait les données que vous voyez dans il correspondent aux règles de l’échelle que vous avez configuré ? Dans l’exemple ci-dessous, le processeur de la machine 20 démarré augmente de 100 % sur les 5 dernières minutes...

    ![Tables de stockage][tables]

    Si les données n’y ne figurent pas, cela signifie que le problème est doté de l’extension de diagnostic en cours d’exécution sur les ordinateurs virtuels. Si les données sont conservées, il implique soit un problème avec vos règles d’échelle ou avec le service d’informations. Vérifier [l’état Azure](https://azure.microsoft.com/status/).

    Une fois que vous avez effectué ces étapes, si vous rencontrez toujours des problèmes autoscale vous pourriez essayez les forums sur [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp), ou un [dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/azure), ou enregistrer un appel. Soyez prêt à partager le modèle et un affichage des données de performances.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
