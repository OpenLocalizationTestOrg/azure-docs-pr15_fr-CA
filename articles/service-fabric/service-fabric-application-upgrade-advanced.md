<properties
   pageTitle="Mise à niveau de l’application : rubriques avancées | Microsoft Azure"
   description="Cet article décrit certaines rubriques avancées relatives à la mise à niveau d’une application de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Mise à niveau des applications de service Fabric : rubriques avancées

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Ajout ou suppression de services au cours d’une mise à niveau de l’application

Si un service est ajouté à une application qui est déjà déployée et publiée comme une mise à niveau, le nouveau service est ajouté à l’application déployée.  Une mise à niveau n’affecte pas les services qui ont fait déjà partie de l’application. Toutefois, une instance du service qui a été ajouté doit être démarrée pour le nouveau service soit actif (à l’aide de la `New-ServiceFabricService` applet de commande).

Les services peuvent également être supprimés à partir d’une application dans le cadre d’une mise à niveau. Toutefois, toutes les instances en cours du service à supprimer doivent être arrêtés avant de procéder à la mise à niveau (à l’aide de la `Remove-ServiceFabricService` applet de commande). 

## <a name="manual-upgrade-mode"></a>Mode de mise à niveau manuel

> [AZURE.NOTE]  Le mode manuel non contrôlé doit être considéré uniquement pour une mise à niveau a échoué ou suspendu. Le mode surveillé est le mode de mise à niveau recommandé pour les applications de Service Fabric.

Azure Fabric de Service fournit plusieurs modes de mise à niveau pour prendre en charge des clusters de développement et de production. Options de déploiement choisies peuvent être différentes pour les différents environnements.

La mise à niveau propagée application contrôlée est la mise à niveau plus courant à utiliser dans la production. Lorsque la stratégie de mise à niveau n’est spécifiée, Fabric du Service garantit que l’application est en bonne santé avant la mise à niveau se poursuit.

 L’administrateur d’application peut utiliser des mode de mise à niveau propagée application manuelle pour avoir un contrôle total sur la progression de mise à niveau par le biais de différents domaines de mise à niveau. Ce mode est utile lorsqu’une stratégie d’évaluation de la santé personnalisés ou complexe est requise, ou une mise à niveau non conventionnelles se produit (par exemple, l’application est déjà dans la perte de données).

Enfin, la mise à niveau propagée application automatisée est utile pour le développement ou des environnements de test pour fournir un cycle d’itération rapide lors du développement du service.

## <a name="change-to-manual-upgrade-mode"></a>Passer en mode de mise à niveau manuel
**Manuel**--arrêter la mise à niveau de l’application à la UD actuelle et passer en mode de mise à niveau manuelle non surveillée. L’administrateur doit manuellement appeler **MoveNextApplicationUpgradeDomainAsync** pour procéder à la mise à niveau, ou de déclencher une annulation en lançant une nouvelle mise à niveau. Une fois la mise à niveau Active le mode manuel, il reste dans le mode manuel, jusqu'à ce qu’une nouvelle mise à niveau est initiée. La commande **GetApplicationUpgradeProgressAsync** renvoie FABRIC\_APPLICATION\_mise à niveau\_état\_matériel\_vers\_en attente.

## <a name="upgrade-with-a-diff-package"></a>Mise à niveau avec un package diff

Une application de Service Fabric peut être mis à niveau par la mise en service avec un package d’application complète et autonome. Une application peut également être mis à niveau à l’aide d’un package de différences qui contient uniquement les fichiers d’application mis à jour, le manifeste d’application mis à jour et les fichiers manifeste du service.

Un package d’application complet contient tous les fichiers nécessaires pour démarrer et exécuter une application de Service Fabric. Un package de comparaison contient uniquement les fichiers modifiés entre la dernière de la disposition et la mise à niveau en cours, ainsi que le manifeste d’application complet et le service de fichiers manifeste. Toute référence dans le manifeste d’application ou d’un manifeste de service qui ne figurent pas dans la présentation de la génération est recherchée dans le magasin d’image.

Packages d’applications complètes sont requis pour la première installation d’une application sur le cluster. Les mises à jour peuvent être un package d’application complet ou un package de diff.

Occasions, lorsqu’à l’aide d’un package diff seraient un bon choix :

* Un package de comparaison est préférable lorsque vous avez un package d’application de grande taille qui fait référence à plusieurs fichiers de manifeste de service et/ou plusieurs packages de code, des packages de configuration ou les packages de données.

* Un package de comparaison est préférable lorsque vous avez un système de déploiement qui génère la mise en page de génération directement à partir de votre processus de génération d’application. Dans ce cas, même si le code n’a pas changé, assemblys générés récemment Obtient un total de contrôle différent. À l’aide d’un package d’application complet vous devrez mettre à jour la version de tous les modules de code. À l’aide d’un package de comparaison, vous assurent uniquement les fichiers modifiés et les fichiers manifeste dont la version a changé.

Lorsqu’une application est mise à niveau à l’aide de Visual Studio, le package de comparaison est publié automatiquement. Pour créer un package de diff manuellement, le manifeste d’application et les manifestes de service doivent être mis à jour, mais uniquement les modules modifiés doivent être inclus dans le package de l’application finale. 

Par exemple, commençons par l’application suivante (les numéros de version fournis pour faciliter la compréhension) :

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Maintenant, supposons que vous souhaitiez mettre à jour uniquement le code lot de service1 à l’aide d’un package de comparaison à l’aide de PowerShell. À présent, votre application mise à jour a la structure de dossiers suivante :

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Dans ce cas, vous mettez à jour le manifeste d’application 2.0.0 et le manifeste de service de service1 afin de refléter la mise à jour du package de code. Le dossier de votre package d’application aurait la structure suivante :

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Étapes suivantes

[Mise à niveau de votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

[Mise à niveau de votre Powershell à l’aide d’Application](service-fabric-application-upgrade-tutorial-powershell.md) vous présente une mise à niveau de l’application à l’aide de PowerShell.

Contrôler comment votre application de mises à niveau à l’aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Assurer la compatibilité de vos mises à niveau de l’application en apprenant comment utiliser la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en vous reportant aux étapes de [Dépannage les mises à niveau de l’Application](service-fabric-application-upgrade-troubleshooting.md).
 
