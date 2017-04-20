<properties
    pageTitle="Choisissez entre les flux, les applications de logique, les fonctions et les WebJobs | Microsoft Azure"
    description="Comparez le nuage intégration des services de Microsoft et décider quels services vous devez utiliser."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft flux, flux, applications de logique, fonctions azure, fonctions, azure webjobs, webjobs, de traitement des événements, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Choisissez entre les flux, les applications de logique, les fonctions et les WebJobs

Cet article compare et distingue les services dans le nuage de Microsoft, ce qui peut résoudre des problèmes d’intégration et d’automatisation des processus professionnels suivants :

- [Flux de Microsoft](https://flow.microsoft.com/)
- [Applications Azure logique](https://azure.microsoft.com/services/logic-apps/)
- [Fonctions Azure](https://azure.microsoft.com/services/functions/)
- [Service d’application Azure WebJobs](../app-service-web/web-sites-create-web-jobs.md)

Tous ces services sont utiles lors de la « collant » des systèmes disparates. Ils peuvent définir input, actions, conditions et sortie. Vous pouvez exécuter chacun d’eux sur une grille ou un déclencheur. Cependant, chaque service ajoute un ensemble unique de valeur et en les comparant ne sont pas une question « quel service est le meilleur ? » mais un des « quel service le mieux cette situation ? » Souvent, une combinaison de ces services est la meilleure façon de créer rapidement une solution évolutive, complète intégration proposés.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>Applications de flux de comparaison logique

Nous pouvons en discuter Flow de Microsoft et les applications de logique d’Azure ensemble parce qu’ils sont les deux services d’intégration *d’abord configuration* , qui permet de facilement créer des workflows et des processus et l’intégration avec diverses applications d’entreprise et SaaS. 

- Flux est construit sur la logique d’applications
- Ils ont le même concepteur de flux de travail
- Les [connecteurs](../connectors/apis-list.md) qui fonctionnent dans un fonctionne également dans l’autre

Flux donne tout travailleur office pour effectuer des intégrations simples (par exemple, recevoir un SMS pour les e-mails importants) sans passer par les développeurs ou informatique. D’autre part, logique d’applications permettent des intégrations avancées ou critiques (par exemple, le processus B2B) où les pratiques DevOps et la sécurité au niveau de l’entreprise sont nécessaires. Il est généralement utilisé pour un workflow métier en heures supplémentaires de la complexité. En conséquence, vous pouvez démarrer avec un flux dans un premier temps, puis la convertir en une application logique en fonction des besoins.

Le tableau suivant vous aide à déterminer si les flux ou les applications de logique est adaptées à une intégration donnée.

|               | Flux                                                                             | Applications de logique                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Public      | employés de bureau, les utilisateurs professionnels                                                   | Professionnels de l’informatique, aux développeurs                                                                                 |
| Scénarios     | Libre-service                                                                     | Critiques                                                                                    |
| Outil de conception   | Dans un navigateur, l’interface utilisateur uniquement                                                              | Dans le navigateur et [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), le [mode Code](../app-service-logic/app-service-logic-author-definitions.md) disponible |
| DevOps        | Ad-hoc, développer en production                                                    | contrôle de code source, test, support et automation et la facilité de gestion dans la [Gestion des ressources Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Expérience de l’administration| [https://Flow.Microsoft.com](https://flow.microsoft.com)                       | [https://Portal.Azure.com](https://portal.azure.com)                                                |
| Sécurité      | Les pratiques standard : [souveraineté des données](https://wikipedia.org/wiki/Technological_Sovereignty), [cryptage au reste](https://wikipedia.org/wiki/Data_at_rest#Encryption) des données sensibles, etc.. | Garantie de sécurité d’Azure : [Sécurité d’Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Le centre de sécurité](https://azure.microsoft.com/services/security-center/), [des journaux d’audit](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)et bien plus encore. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Fonctions et WebJobs

Nous pouvez discuter des fonctions d’Azure et d’Azure Application Service WebJobs ensemble parce qu’ils sont les deux services d’intégration *d’abord code* et conçu pour les développeurs. Ils vous permettent d’exécuter un script ou un morceau de code en réponse à différents événements, tels que le [Nouveau stockage BLOB](functions-bindings-storage.md) ou [une demande de WebHook](functions-bindings-http-webhook.md). Voici leurs points communs : 

- Les deux reposent sur le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md) et profiter des fonctionnalités de [contrôle de code source](../app-service-web/app-service-continuous-deployment.md), [l’authentification](../app-service/app-service-authentication-overview.md)et la [surveillance](../app-service-web/web-sites-monitor.md).
- Les deux sont destinés aux développeurs des services.
- Les deux prennent en charge des langages de programmation et de script standard.
- Les deux ont NuGet et NPM prennent en charge.

Fonctions est l’évolution naturelle de WebJobs dans la mesure où il prend de meilleures choses WebJobs et les améliore. Les améliorations sont les suivantes : 

- Le développement rationalisée, tester et exécuter du code, directement dans le navigateur.
- Intégration avec des services plus Azure et services de 3 rd-party comme [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Payer par utilisation, pas besoin de payer pour un [Service d’application plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automatique, [la mise à l’échelle dynamique](functions-scale.md).
- Pour les clients existants de Service d’application, en cours d’exécution sur le plan de Service d’application toujours possible (pour tirer parti de ressources de sous-utilisées).
- Intégration avec les applications de la logique.

Le tableau suivant résume les différences entre les fonctions et WebJobs :

|                        | Fonctions                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Mise à l’échelle                | Mise à l’échelle configurationless                                                                                                                                                | mettre à l’échelle avec le service d’application        |
| Tarification                | Paie-utilisation ou une partie d’un plan de Service d’application                                                                                                                                  | Partie d’un plan de Service d’application           |
| Type d’exécution               | déclenchée, planifiées (par le déclenchement de la minuterie)                                                                                                                                  | déclenchée, continue, planifiée   |
| Déclencher des événements         | [timer](functions-bindings-timer.md), [DocumentDB d’Azure](functions-bindings-documentdb.md), [Azure événement concentrateurs](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, marge)](functions-bindings-http-webhook.md), [Le Service Azure App Apps Mobile](functions-bindings-mobile-apps.md), [Azure Notification concentrateurs](functions-bindings-notification-hubs.md), [Bus des services Azure](functions-bindings-service-bus.md), [Le stockage Azure](articles/functions-bindings-storage.md) | [Stockage azure](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Bus des services Azure](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Développement de dans le navigateur | x                                                                                                                                                                        |                                    |
| Fenêtre de script       | expérimentale                                                                                                                                                             | x                                  |
| PowerShell             | expérimentale                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Bash                   | expérimentale                                                                                                                                                             | x                                  |
| PHP                    | expérimentale                                                                                                                                                             | x                                  |
| Python                 | expérimentale                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | expérimentale                                                                                                                                                             | x                                  |

Si vous souhaitez utiliser les fonctions ou WebJobs dépend de ce que vous faites déjà avec Service d’application. Si vous disposez d’une application de Service de l’application pour laquelle vous souhaitez exécuter des extraits de code, et vous souhaitez les gérer ensemble dans le même environnement DevOps, vous devez utiliser WebJobs. Si vous souhaitez exécuter des extraits de code pour d’autres services Azure ou les 3 rd-party applications, ou si vous souhaitez gérer séparément de vos extraits de code d’intégration de vos applications de Service de l’application, ou si vous souhaitez appeler vos extraits de code à partir d’une application de logique, vous devez tirer parti de toutes les améliorations dans les fonctions.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Forment, applications de logique et de fonctions

Comme mentionné précédemment, le service qui est la mieux adapté pour vous dépend de votre situation. 

- Pour une optimisation métier simples, puis utiliser le flux.
- Si votre scénario d’intégration est trop avancé pour le flux, ou vous devez DevOps capacités et intentionnelle de sécurité, utilisez Apps de logique.
- Si une étape dans votre scénario d’intégration nécessite la transformation hautement personnalisée ou code spécialisé, puis écrire une application de la fonction et ensuite déclencher une fonction sous la forme d’une action dans votre logique d’application.

Vous pouvez appeler une application logique dans un flux. Vous pouvez également appeler une fonction dans une application de logique et d’une application logique dans une fonction. L’intégration entre le flux logique d’applications et fonctions de continuer à améliorer des heures supplémentaires. Vous pouvez créer quelque chose dans un service et l’utiliser dans d’autres services. Par conséquent, mieux vaut tout vous investirez dans ces trois technologies.

## <a name="next-steps"></a>Étapes suivantes

Mise en route avec chacun des services en créant votre premier flux logique app, fonction app ou WebJob. Cliquez sur un des liens suivants :

- [Mise en route de Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Créer votre première fonction Azure](../azure-functions/functions-create-first-azure-function.md)
- [Déployer des WebJobs à l’aide de Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Ou obtenez plus d’informations sur ces services d’intégration avec les liens suivants :

- [Exploitation des fonctions d’Azure et d’Azure Application Service pour les scénarios d’intégration par Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Intégration simplifiée par Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Présentation technique des applications de logique](http://aka.ms/logicappslive)
- [Microsoft flux Forum aux questions](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Ressources de documentation WebJobs Azure](../app-service-web/websites-webjobs-resources.md)
