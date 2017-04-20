<properties
    pageTitle="Suivre le flux dans une Application de Services de Cloud avec Azure Diagnostics | Microsoft Azure"
    description="Ajouter des messages de traçage vers une application Azure pour aider au débogage, mesure des performances, la surveillance, l’analyse du trafic et plus."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Suivre le flux d’une application de Services en nuage avec Azure Diagnostics

Le traçage est un moyen vous permettant de surveiller l’exécution de votre application pendant son exécution. Vous pouvez utiliser les classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)et [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) pour enregistrer les informations sur les erreurs et l’exécution de l’application dans des journaux, de fichiers texte ou d’autres périphériques pour une analyse ultérieure. Pour plus d’informations sur le traçage, consultez [traçage et instrumentation d’Applications](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Utilisez les instructions de traçage et des commutateurs de traçage

Suivi de la mise en œuvre dans votre application de Services en nuage en ajoutant de la [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) à la configuration de l’application et en appels System.Diagnostics.Trace ou System.Diagnostics.Debug dans votre code d’application. Utilisez le fichier de configuration *app.config* de rôles worker et le *web.config* pour les rôles web. Lorsque vous créez un nouveau service hébergé à l’aide d’un modèle de Visual Studio, les Diagnostics Azure est automatiquement ajouté au projet et le DiagnosticMonitorTraceListener est ajouté au fichier de configuration approprié pour les rôles que vous ajoutez.

Pour plus d’informations sur le placement d’instructions de traçage, consultez [Comment : ajouter des instructions de traçage au Code d’Application](https://msdn.microsoft.com/library/zd83saa2.aspx).

En plaçant [Des commutateurs de traçage](https://msdn.microsoft.com/library/3at424ac.aspx) dans votre code, vous pouvez contrôler si le traçage se produit et jusqu'à quel point. Cela vous permet de surveiller l’état de votre application dans un environnement de production. Ceci est particulièrement important dans une application qui utilise plusieurs composants exécutant sur plusieurs ordinateurs. Pour plus d’informations, consultez [Comment : configurer des commutateurs de traçage](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configuration de l’écouteur de traçage dans une application Azure

Traçage, débogage et TraceSource, nécessitent que vous pouvez paramétrer des « écouteurs » pour collecter et enregistrer les messages qui sont envoyés. Écouteurs de collectent, stockent et routent des messages de traçage. Ils dirigent la sortie du traçage vers une cible appropriée, par exemple un journal, une fenêtre ou un fichier texte. Diagnostics de Windows Azure utilise la classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Avant d’effectuer la procédure suivante, vous devez initialiser l’écran de diagnostic Azure. Pour ce faire, reportez-vous à la section [Activation des Diagnostics de Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Notez que si vous utilisez les modèles fournis par Visual Studio, la configuration de l’écouteur est ajoutée automatiquement pour vous.


### <a name="add-a-trace-listener"></a>Ajouter un écouteur de la trace

1. Ouvrez le fichier web.config ou app.config pour votre rôle.
2. Ajoutez le code suivant au fichier. Modifiez l’attribut de Version pour utiliser le numéro de version de l’assembly que vous référencez. La version d’assembly ne change pas nécessairement avec chaque version du SDK de Azure sauf s’il existe des mises à jour pour elle.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Assurez-vous que vous disposez d’une référence de projet à l’assembly Microsoft.WindowsAzure.Diagnostics. Mettre à jour le numéro de version dans le fichier xml ci-dessus pour le correspond à la version de l’assembly référencé de la Microsoft.WindowsAzure.Diagnostics.

3. Enregistrez le fichier de configuration.

Pour plus d’informations sur les ports d’écoute, consultez [Écouteurs de la Trace](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Après avoir terminé les étapes pour ajouter l’écouteur, vous pouvez ajouter des instructions de traçage à votre code.


### <a name="to-add-trace-statement-to-your-code"></a>Pour ajouter des instructions de traçage à votre code

1. Ouvrir un fichier de code source pour votre application. Par exemple, le <RoleName>fichier .cs pour le rôle de collaborateur ou web.
2. Ajoutez le code suivant à l’aide d’instruction si elle n’a pas déjà été ajoutée :
    ```
        using System.Diagnostics;
    ```
3. Ajouter des instructions de traçage dans lequel vous souhaitez capturer des informations sur l’état de votre application. Vous pouvez utiliser diverses méthodes pour mettre en forme la sortie de l’instruction Trace. Pour plus d’informations, consultez [Comment : ajouter des instructions de traçage au Code d’Application](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Enregistrez le fichier source.
