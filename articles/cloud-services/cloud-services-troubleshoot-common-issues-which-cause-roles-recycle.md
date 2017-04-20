<properties
   pageTitle="Les causes courantes des rôles de Service Cloud recyclage | Microsoft Azure"
   description="Un rôle de service de cloud qui recycle soudainement peut provoquer des temps d’arrêt important. Voici certains problèmes courants entraînant des rôles doit être recyclé, qui peuvent vous aider à réduire les interruptions de service."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Problèmes courants susceptibles de provoquer des rôles à recycler

Cet article présente certaines des causes courantes des problèmes de déploiement et fournit des conseils de dépannage pour vous aider à résoudre ces problèmes. L’indication qu’il existe un problème avec une application est lorsque l’instance de rôle ne démarre pas, ou qu’il permet de se déplacer entre les États lors de l’initialisation, occupés et l’arrêt.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dépendances de runtime manquantes

Si un rôle dans votre application s’appuie sur un assembly qui ne fait pas partie du.NET Framework ou l’Azure managé de bibliothèque, vous devez inclure explicitement l’assembly dans le package d’application. Gardez à l’esprit que les autres structures de Microsoft ne sont pas disponibles sur Azure par défaut. Si un tel cadre dépend de votre rôle, vous devez ajouter ces assemblys dans le package d’application.

Avant de générer et d’empaqueter votre application, vérifiez les points suivants :

- Si vous utilisez Visual studio, assurez-vous que la propriété **Copie locale** a la valeur **True** pour chaque assembly référencé dans votre projet qui ne fait pas partie de l’Azure SDK ou le.NET Framework.

- Assurez-vous que le fichier web.config ne fait pas référence à des assemblys non utilisés dans l’élément de compilation.

- L' **Action de génération** de chaque fichier .cshtml est défini sur le **contenu**. Cela garantit que les fichiers s’affichent correctement dans le package et permet des autres fichiers référencés dans le package.

## <a name="assembly-targets-wrong-platform"></a>Plate-forme de mauvais assembly cibles

Azure est un environnement 64 bits. Par conséquent, les assemblys .NET compilés pour une cible de 32 bits ne fonctionneront pas sur Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rôle lève des exceptions non gérées lors de l’initialisation ou arrêt

Toutes les exceptions qui sont levées par les méthodes de la classe [RoleEntryPoint] , qui inclut les [OnStart], [OnStop]et [exécuter] des méthodes, sont des exceptions non gérées. Si une exception non gérée se produit dans une de ces méthodes, recycle le rôle. Si le rôle est de recyclage à plusieurs reprises, il peut être levée d’une exception non gérée, chaque fois qu’il tente de démarrer.

## <a name="role-returns-from-run-method"></a>Rôle renvoie à partir de la méthode Run

La méthode [Run] est destinée à s’exécuter indéfiniment. Si votre code se substitue à la méthode [Run] , il doit rester en veille indéfiniment. Si la méthode [Run] retourne, le rôle de recyclage.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Paramètre incorrect de DiagnosticsConnectionString

Si application utilise des Diagnostics d’Azure, votre fichier de configuration de service doit spécifier le `DiagnosticsConnectionString` de configuration. Ce paramètre doit spécifier une connexion HTTPS à votre compte de stockage dans Azure.

Pour vous assurer que votre `DiagnosticsConnectionString` paramètre est correct avant de déployer le package d’application dans Azure, vérifiez les éléments suivants :  

- Le `DiagnosticsConnectionString` définition de points d’un compte de stockage valide dans Azure.  
  Par défaut, ce paramètre pointe vers le compte de stockage émulée, donc vous devez le modifier explicitement ce paramètre avant de déployer le package d’application. Si vous ne modifiez pas ce paramètre, une exception est levée lorsque l’instance de rôle tente de démarrer le moniteur de diagnostic. Cela peut entraîner l’instance de rôle de recycler indéfiniment.

- La chaîne de connexion est spécifiée au [format](../storage/storage-configure-connection-string.md)suivant. (Le protocole doit être spécifié en tant que HTTPS.) Remplacez *MyAccountName* par le nom de votre compte de stockage et *MyAccountKey* avec votre clé d’accès :    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Si vous développez votre application à l’aide d’Azure Tools pour Visual Studio de Microsoft, vous pouvez utiliser les [pages de propriétés](https://msdn.microsoft.com/library/ee405486) pour définir cette valeur.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificat exporté n’inclut pas de clé privée

Pour exécuter un rôle web sous SSL, vous devez vous assurer que votre certificat exporté gestion inclut la clé privée. Si vous utilisez le *Gestionnaire de certificats de Windows* pour exporter le certificat, veillez à sélectionner **Oui** pour l’option **Exporter la clé privée** . Le certificat doit être exporté au format PFX, ce qui est le seul format pris en charge actuellement.

## <a name="next-steps"></a>Étapes suivantes

Permet d’afficher plusieurs [articles de dépannage](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) des services en nuage.

Permet d’afficher plus de rôle recyclage des scénarios à une [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Exécuter]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
