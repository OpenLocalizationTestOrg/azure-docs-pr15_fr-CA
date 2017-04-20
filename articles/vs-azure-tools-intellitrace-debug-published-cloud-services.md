<properties 
   pageTitle="Débogage d’un service cloud publié avec IntelliTrace et Visual Studio | Microsoft Azure"
   description="Débogage d’un service cloud publié avec IntelliTrace et Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>Débogage d’un service cloud publié avec IntelliTrace et Visual Studio

##<a name="overview"></a>Vue d’ensemble

Avec IntelliTrace, vous pouvez enregistrer des informations de débogage détaillées pour une instance de rôle lorsqu’il s’exécute dans Azure. Si vous devez rechercher la cause d’un problème, vous pouvez utiliser les journaux IntelliTrace pour parcourir votre code à partir de Visual Studio, comme s’il était exécuté dans Azure. En effet, les enregistrements IntelliTrace clé de l’exécution de code et les données d’environnement lorsque votre application Azure s’exécute comme un service en nuage dans Azure et vous permet de relire les données enregistrées à partir de Visual Studio. Comme alternative, vous pouvez utiliser le débogage distant d’attacher directement à un service en nuage qui s’exécute dans Azure. Consultez [débogage des Services en nuage](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace est destinée aux scénarios de débogage uniquement et ne doit pas être utilisé pour un déploiement de production.

>[AZURE.NOTE] Vous pouvez utiliser IntelliTrace si vous avez Visual Studio Enterprise installé et vos cibles Azure application.NET Framework 4 ou une version ultérieure. IntelliTrace collecte des informations pour vos rôles d’Azure. Les ordinateurs virtuels pour ces rôles toujours exécutés les systèmes d’exploitation 64 bits.

## <a name="to-configure-an-azure-application-for-intellitrace"></a>Pour configurer une application Azure pour IntelliTrace

Pour permettre à IntelliTrace d’une application Azure, vous devez créer et publier l’application à partir d’un projet Visual Studio Azure. Vous devez configurer IntelliTrace pour votre application Azure avant de le publier vers Azure. Si vous publiez votre application sans configurer IntelliTrace mais que vous décidez ensuite que vous souhaitez faire, vous devez publier l’application à partir de Visual Studio. Pour plus d’informations, voir [publication d’un Service Cloud à l’aide d’outils Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Lorsque vous êtes prêt à déployer votre application Azure, vérifiez que vos cibles de génération de projet sont définies pour le **débogage**.

1. Ouvrir le menu contextuel du projet Azure dans l’Explorateur de solutions et choisissez **Publier**.
 
    L’Assistant de publication des applications Azure s’affiche.

1. Pour collecter les fichiers journaux IntelliTrace pour votre application lorsqu’il est publié dans le nuage, activez la case à cocher **Activer IntelliTrace** .

    >[AZURE.NOTE] Vous pouvez activer IntelliTrace ou profilage lorsque vous publiez votre application Azure. Vous ne pouvez pas activer à la fois.

1. Pour personnaliser la configuration de base IntelliTrace, cliquez sur le lien **paramètres** .

    La boîte de dialogue paramètres IntelliTrace s’affiche, comme illustré dans la figure suivante. Vous pouvez spécifier les événements à un journal, s’il faut collecter des informations sur les appels, les modules et les processus pour collecter des journaux pour et la quantité d’espace à allouer à l’enregistrement. Pour plus d’informations sur IntelliTrace, consultez [débogage avec IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Le journal IntelliTrace est un fichier journal circulaire de la taille maximale spécifiée dans les paramètres IntelliTrace (la taille par défaut est 250 Mo). Les fichiers journaux IntelliTrace sont collectées dans un fichier du système de fichiers de l’ordinateur virtuel. Lorsque vous demandez les journaux, un instantané est pris à ce point dans le temps et téléchargé sur votre ordinateur local.

Une fois l’application Azure a été publiée sur Azure, vous pouvez déterminer si IntelliTrace a été activée à partir du nœud de calcul Azure dans l’Explorateur de serveurs, comme illustré dans l’image suivante :

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>Téléchargement de fichiers journaux IntelliTrace pour une Instance de rôle

Vous pouvez télécharger les fichiers journaux IntelliTrace pour une instance de rôle à partir du nœud **Services en nuage** dans **l’Explorateur de serveurs**. Développez le nœud **Services de nuage** jusqu'à ce que vous recherchez l’instance qui que vous intéressez, ouvrir le menu contextuel pour cette instance et choisir **d’Afficher les journaux IntelliTrace**. Les fichiers journaux IntelliTrace sont téléchargés vers un fichier dans un répertoire sur votre ordinateur local. Chaque fois que vous demandez la IntelliTrace se connecte, une nouvelle capture instantanée est créée.

Lorsque les journaux sont téléchargés, Visual Studio affiche la progression de l’opération dans la fenêtre du journal d’activité Azure. Comme illustré dans la figure suivante, vous pouvez développer la ligne de facturation pour afficher plus de détails de l’opération.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Vous pouvez continuer à travailler dans Visual Studio, tandis que les journaux IntelliTrace sont téléchargement. Lorsque le journal de téléchargement est terminé, il s’ouvre automatiquement dans Visual Studio.

>[AZURE.NOTE] Les fichiers journaux IntelliTrace peuvent contenir des exceptions que l’infrastructure génère et gère par la suite. Code interne framework génère ces exceptions dans le cadre normal de demarrage d’un rôle, et vous pouvez les ignorer en toute sécurité.

## <a name="see-also"></a>Voir aussi

[Débogage des Services en nuage](https://msdn.microsoft.com/library/ee405479.aspx)

