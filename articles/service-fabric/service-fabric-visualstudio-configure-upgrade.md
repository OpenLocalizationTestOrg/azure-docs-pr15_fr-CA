<properties
   pageTitle="Configurer la mise à niveau d’une application de Service Fabric | Microsoft Azure"
   description="Apprenez à configurer les paramètres de mise à niveau d’une application de Service Fabric à l’aide de Visual Studio de Microsoft."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurer la mise à niveau d’une application de Service Fabric dans Visual Studio

Les outils Visual Studio pour Azure Service Fabric prennent en charge mise à niveau publication pour les clusters locaux ou distants. Il existe deux avantages de mise à niveau de votre application à une version plus récente au lieu de remplacer l’application pendant le test et le débogage :

- Les données d’application ne seront pas perdues lors de la mise à niveau.
- Disponibilité reste élevée, ce qui n’aura aucune interruption de service au cours de la mise à niveau, s’il y a que suffisamment d’instances de service réparties sur les domaines de mise à niveau.

Les tests peuvent être exécutés sur une application pendant qu’il est en cours de mise à niveau.

## <a name="parameters-needed-to-upgrade"></a>Paramètres nécessaires à la mise à niveau

Vous pouvez choisir parmi deux types de déploiement : régulière ou mise à niveau. Un déploiement standard efface les données sur le cluster et les informations sur le déploiement précédent lors d’un déploiement de mise à niveau conserve. Lorsque vous mettez à niveau une application de Service Fabric dans Visual Studio, vous devez fournir la santé et les paramètres de mise à niveau d’application vérifier les stratégies. Paramètres de mise à niveau d’application permettent de contrôler la mise à niveau, tandis que les stratégies d’intégrité à cocher déterminent si la mise à niveau a réussi. Consultez [mise à niveau des applications de Service Fabric : mise à niveau des paramètres](service-fabric-application-upgrade-parameters.md) pour plus de détails.

Il existe trois modes de mise à niveau : *surveillés*, *UnmonitoredAuto*et *UnmonitoredManual*.

  - Une mise à niveau contrôlées automatise la mise à niveau et la vérification de l’intégrité.

  - Une mise à niveau UnmonitoredAuto automatise la mise à niveau, mais il ignore la vérification de la santé des applications.

  - Lorsque vous effectuez une mise à niveau de UnmonitoredManual, vous devez mettre à niveau manuellement chaque domaine de mise à niveau.

Chaque mode de mise à niveau nécessite différents jeux de paramètres. Reportez-vous à la section [Application des paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md) pour en savoir plus sur les options de mise à niveau disponibles.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Mise à niveau d’une application de Service Fabric dans Visual Studio

Si vous utilisez les outils tissu de Service de Visual Studio pour mettre à niveau une application Fabric de Service, vous pouvez spécifier un processus de publication pour une mise à niveau au lieu d’un déploiement standard en activant la case à cocher **mise à niveau de l’application** .

### <a name="to-configure-the-upgrade-parameters"></a>Pour configurer les paramètres de mise à niveau

1. Cliquez sur le bouton **paramètres de** la case à cocher. La boîte de dialogue **Modifier les paramètres de mise à niveau** s’affiche. La boîte de dialogue **Modifier les paramètres de mise à niveau** prend en charge les modes de mise à niveau contrôlées, UnmonitoredAuto et UnmonitoredManual.

2. Sélectionnez le mode de mise à niveau que vous souhaitez utiliser et puis remplir la grille de paramètres.

    Chaque paramètre a des valeurs par défaut. Le paramètre facultatif *DefaultServiceTypeHealthPolicy* prend une entrée de table de hachage. Voici un exemple du format d’entrée de table de hachage pour *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* est un autre paramètre facultatif qui prend une entrée de table de hachage dans le format suivant :

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Voici un exemple réel :

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Si vous sélectionnez le mode de mise à niveau de UnmonitoredManual, vous devez démarrer manuellement une console PowerShell pour continuer et terminer le processus de mise à niveau. Reportez-vous à [mise à niveau des applications de Service Fabric : rubriques avancées](service-fabric-application-upgrade-advanced.md) pour en savoir plus de la mise à niveau manuelle comment fonctionne.

## <a name="upgrade-an-application-by-using-powershell"></a>Mise à niveau d’une application à l’aide de PowerShell

Vous pouvez utiliser des applets de commande PowerShell pour mettre à niveau une application de Service Fabric. Pour plus d’informations, reportez-vous à la section [application Fabric du Service de mise à niveau du didacticiel](service-fabric-application-upgrade-tutorial.md) et [Début-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Spécifier une stratégie de vérification de la santé dans le fichier de manifeste d’application

Chaque service dans une application de Service Fabric peut avoir ses propres paramètres de stratégie de la santé qui substituent les valeurs par défaut. Vous pouvez fournir ces valeurs de paramètre dans le fichier de manifeste d’application.

L’exemple suivant montre comment appliquer une stratégie de vérification d’intégrité unique pour chaque service dans le manifeste d’application.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le déploiement d’une application, voir [déploiement d’une application dans Azure Fabric de Service](service-fabric-deploy-existing-app.md).
