<properties 
   pageTitle="Débogage des Services Cloud Azure | Microsoft Azure"
   description="Débogage des Services Cloud Azure"
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

# <a name="debugging-cloud-services"></a>Débogage des services en nuage

Vous pouvez utiliser différentes approches pour déboguer une application Azure pour Visual Studio de Microsoft et le SDK Azure à l’aide des outils Azure :

- Vous pouvez déboguer une application Azure à partir de Visual Studio lorsque vous développez, tout comme vous le feriez pour n’importe quelle application Visual C# ou Visual Basic. Pour plus d’informations, consultez [débogage de votre service cloud sur votre ordinateur local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Vous pouvez utiliser Azure Diagnostics pour enregistrer des informations détaillées à partir du code s’exécutant dans des rôles, si les rôles sont en cours d’exécution dans l’environnement de développement ou dans Azure. Pour plus d’informations, reportez-vous à la section [collecte journalisation des données à l’aide des Diagnostics d’Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Si vous utilisez Visual Studio Enterprise pour écrire des rôles ciblés.NET Framework 4 ou le 4.5 de.NET Framework, vous pouvez activer IntelliTrace au moment que vous déployez un service cloud Azure à partir de Visual Studio. IntelliTrace fournit un journal que vous pouvez utiliser avec Visual Studio pour déboguer votre application comme s’il était exécuté dans Azure. Pour plus d’informations, consultez [débogage d’un service cloud publié avec IntelliTrace et Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Vous pouvez activer le débogage distant sur vos services en nuage à la fois lorsque vous déployez le service en nuage à partir de Visual Studio. Si vous choisissez d’activer le débogage distant pour un déploiement, les services de débogage distant sont installés sur les ordinateurs virtuels qui s’exécutent à chaque instance de rôle. Ces services, tels que de msvsmon.exe, n’affecte les performances ou entraîner des coûts supplémentaires. Pour plus d’informations, consultez [débogage d’un service cloud dans Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).



