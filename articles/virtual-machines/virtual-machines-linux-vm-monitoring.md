<properties
   pageTitle="Activation ou désactivation de l’analyse d’Azure VM"
   description="Décrit comment activer ou désactiver le contrôle de la machine virtuelle Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Activer ou désactiver la surveillance d’Azure VM

Cette section décrit comment activer ou désactiver l’analyse sur les machines virtuelles s’exécutant sur Azure. Par défaut surveillance est activée sur les machines virtuelles Azure si déployé à partir du [portail Azure](https://portal.azure.com) et graphiques d’analyse sont fournis par défaut avec un délai de 1 minute. Vous pouvez activer ou désactiver l’analyse à l’aide du portail du ou Azure Line Interface pour Windows (l’infrastructure CLI Azure), Linux et Mac. 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Activer / désactiver l’analyse via le portail Azure
 
Vous pouvez activer l’analyse de votre machine virtuelle Azure, qui fournit des données relatives à votre instance de périodes de 1 minute. (les modifications de stockage s’appliquent). Les données de diagnostic détaillées seront ensuite disponibles pour la machine virtuelle dans les graphiques de portail ou via l’API. Par défaut, Azure portal permet de surveiller, mais vous pouvez le désactiver comme décrit ci-dessous. Vous pouvez activer la surveillance pendant que l’ordinateur virtuel est en cours d’exécution ou l’état arrêté.

- Ouvrez le portail au **Azure [https://portal.azure.com](https://portal.azure.com)**

- Dans la navigation de gauche, cliquez sur ordinateurs virtuels.

- Dans la liste des machines virtuelles, sélectionnez une instance en cours d’exécution ou arrêtée. Blad de la machine virtuelle s’ouvre.

- Cliquez sur « Tous les paramètres ».

- Cliquez sur « Diagnostics ».

- Modifier le statut sur On ou Off. Vous pouvez également prélever dans cette blade le niveau de contrôle les informations que vous souhaitez activer pour votre ordinateur virtuel.

[Azure.Note] Le commutateur sur les tests de diagnostic est la valeur par défaut lorsque vous créez un nouvel ordinateur virtuel

![Activer / désactiver l’analyse via le portail Azure.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Activer / désactiver l’analyse avec l’interface CLI Azure
 
Pour activer le contrôle d’un ordinateur virtuel d’Azure.

- Créez un fichier nommé comme PrivateConfig.json ayant le contenu suivant.
        {« storageAccountName » : « compte de stockage pour recevoir les données », « storageAccountKey » : « la clé du compte »}
- Exécutez la commande suivante de la CLI d’Azure.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Vous pouvez modifier à partir de la version 2.0 vers une version ultérieure lorsqu’ils sont disponibles. 

Pour plus d’informations sur la configuration de surveillance des métriques et des exemples, consultez le document - **[À l’aide de Linux Diagnostic Extension VM Linux moniteur de performances et de données de diagnostic](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

