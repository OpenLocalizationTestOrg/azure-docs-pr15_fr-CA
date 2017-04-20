<properties
   pageTitle="Résoudre les rôles qui ne parviennent pas à démarrer | Microsoft Azure"
   description="Voici quelques-unes des raisons pourquoi un rôle de Service Cloud peut ne pas démarrer. Solutions à ces problèmes sont également fournies."
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

# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Résoudre les problèmes liés à des rôles de Service Cloud qui ne parviennent pas à démarrer

Voici quelques problèmes courants et solutions liés aux Services de Cloud Azure rôles qui ne parviennent pas à démarrer.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL manquantes ou les dépendances

Les rôles ne répond plus et les rôles qui sont hors puis sous tension entre **l’initialisation**et **l’arrêt** des états **occupé (e)**peuvent être dû de DLL manquantes ou des assemblys.

Problèmes de DLL ou assemblys manquants peuvent être :

- Votre instance de rôle est parcouru **initialisation**, **occupé (e)**et **arrêt des** États.
- Votre instance de rôle a été déplacé à **l’état prêt** , mais si vous accédez à votre application web, la page n’apparaît pas.

Il existe plusieurs méthodes recommandées pour l’analyse de ces problèmes.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostiquer les problèmes DLL manquants dans un rôle web

Lorsque vous accédez à un site Web qui est déployé dans un site web, rôle et que le navigateur affiche une erreur de serveur semblable à la suivante, il peut indiquer qu’une DLL est manquante.

![Erreur du serveur dans l’Application '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostiquer les problèmes en désactivant les erreurs personnalisées

Informations plus complètes sur les erreurs peut être affichée en configurant le fichier web.config pour le rôle web définir le mode d’erreurs personnalisées à Off et redéployer le service.

Pour afficher les erreurs plus complètes sans utiliser le Bureau à distance :

1. Ouvrez la solution dans Visual Studio de Microsoft.

2. Dans l' **Explorateur de solutions**, recherchez le fichier web.config et l’ouvrir.

3. Dans le fichier web.config, recherchez la section system.web et ajoutez la ligne suivante :

    ```xml
    <customErrors mode="Off" />
    ```

4. Enregistrez le fichier.

5. Reconditionnement et redéployer le service.

Une fois que le service est redéployé, vous verrez un message d’erreur avec le nom de l’assembly ou les DLL manquantes.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostiquer les problèmes en affichant l’erreur à distance

Vous pouvez utiliser Bureau à distance pour accéder au rôle et afficher des informations plus complètes sur les erreurs à distance. Pour afficher les erreurs à l’aide de bureau à distance, utilisez les étapes suivantes :

1. Assurez-vous que le SDK Azure 1.3 ou une version ultérieure est installé.

2. Lors du déploiement de la solution à l’aide de Visual Studio, cliquez sur « Configurer connexions Bureau à distance... ». Pour plus d’informations sur la configuration de la connexion Bureau à distance, consultez [L’aide de bureau à distance avec les rôles d’Azure](../vs-azure-tools-remote-desktop-roles.md).

3. Dans le portail classique de Microsoft Azure, une fois l’instance affiche l’état **prêt**, cliquez sur une des instances de rôle.

4. Cliquez sur l’icône de **connexion** dans la zone **d’Accès à distance** du ruban.

5. Connectez-vous à l’ordinateur virtuel en utilisant les informations d’identification qui ont été spécifiées lors de la configuration du Bureau à distance.

6. Ouvrez une fenêtre de commande.

7. Type de `IPconfig`.

8. Notez la valeur de l’adresse IPV4.

9. Ouvrez Internet Explorer.

10. Tapez l’adresse et le nom de l’application web. Par exemple, `http://<IPV4 Address>/default.aspx`.

Navigation vers le site Web va à présent retourner des messages d’erreur plus explicites :

* Erreur du serveur dans l’Application '/'.

* Description : Une exception non gérée s’est produite lors de l’exécution de la demande web en cours. Veuillez consulter la trace de la pile pour plus d’informations sur l’erreur et son origine dans le code.

* Détails de l’exception : System.IO.FIleNotFoundException : n’a pas pu charger fichier ou l’assembly ' Microsoft.WindowsAzure.StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35' ou une de ses dépendances. Le système ne peut pas trouver le fichier spécifié.

Par exemple :

![Erreur de serveur explicite dans l’Application '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostiquer les problèmes à l’aide de l’émulateur de calcul

Vous pouvez utiliser l’émulateur de calcul de Microsoft Azure à diagnostiquer et à résoudre les problèmes de dépendances et les erreurs de web.config manquants.

Pour de meilleurs résultats à l’aide de cette méthode de diagnostic, vous devez utiliser un ordinateur ou un ordinateur virtuel qui a une nouvelle installation de Windows. Pour mieux simuler l’environnement Azure, utilisez Windows Server 2008 R2 x64.

1. Installez la version autonome du [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/).

2. Sur l’ordinateur de développement, générez le projet de service cloud.

3. Dans l’Explorateur Windows, accédez au dossier bin\debug du projet de service cloud.

4. Copiez le fichier du dossier et de .cscfg .csx à l’ordinateur que vous utilisez pour déboguer les problèmes.

5. Sur l’ordinateur en mode minimal, ouvrez une fenêtre d’invite de commandes du Kit de développement logiciel Azure et un type `csrun.exe /devstore:start`.

6. À l’invite de commandes, tapez `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Lorsque le rôle démarre, vous verrez des informations d’erreur détaillées dans Internet Explorer. Vous pouvez également utiliser des outils de dépannage de Windows standard pour essayer de résoudre le problème.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostiquer les problèmes à l’aide d’IntelliTrace

Pour le travailleur et les rôles web qui utilisent le.NET Framework 4, vous pouvez utiliser [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), qui est disponible dans [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Suivez ces étapes pour déployer le service avec IntelliTrace est activé :

1. Confirmez que le SDK Azure 1.3 ou une version ultérieure est installé.

2. Déployez la solution à l’aide de Visual Studio. Au cours du déploiement, cochez la case **Activer l’IntelliTrace pour les rôles de .NET 4** .

3. Une fois que l’instance démarre, ouvrez l' **Explorateur de serveurs**.

4. Développez le **Azure\\les Services en nuage** nœud et recherchez le déploiement.

5. Développez le déploiement jusqu'à ce que vous voyiez les instances de rôle. Avec le bouton droit sur une des instances.

6. Choisissez **les fichiers journaux IntelliTrace d’affichage**. Le **Résumé IntelliTrace** s’ouvre.

7. Recherchez la section exceptions du résumé. S’il existe des exceptions, la section s’intitule **Données d’Exception**.

8. Développer les **Données de l’Exception** et recherchez les erreurs de **System.IO.FileNotFoundException** semblables à la suivante :

![Données d’exception, un fichier manquant ou assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adresse des DLL et des assemblys manquants

Pour résoudre les DLL manquante et les erreurs d’assemblage, procédez comme suit :

1. Ouvrez la solution dans Visual Studio.

2. Dans l' **Explorateur de solutions**, ouvrez le dossier **références** .

3. Cliquez sur l’assembly identifié dans l’erreur.

4. Dans le volet des **Propriétés** , recherchez la **propriété Copy Local** et affectez la valeur **True**.

5. Redéployer le service en nuage.

Une fois que vous avez vérifié que toutes les erreurs ont été corrigées, vous pouvez déployer le service sans vérifier la case à cocher **Activer l’IntelliTrace pour les rôles de .NET 4** .

## <a name="next-steps"></a>Étapes suivantes

Permet d’afficher plusieurs [articles de dépannage](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) des services en nuage.

Pour savoir comment résoudre les problèmes de rôle de service cloud à l’aide de données de diagnostic d’ordinateur Azure PaaS, consultez [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
