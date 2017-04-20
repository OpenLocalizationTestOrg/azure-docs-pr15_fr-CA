<properties
    pageTitle="Tableau de bord d’alimentation sur le flux de données Analytique | Microsoft Azure"
    description="Un tableau de bord BI d’alimentation en continu en temps réel permet de rassembler des informations de l’entreprise et analyser l’important volume de données à partir d’une tâche de flux de données Analytique."
    keywords="tableau de bord analytique, tableau de bord en temps réel"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Flux Analytique & BI de l’alimentation : un tableau de bord analytique en temps réel pour la diffusion en continu de données

Azure Analytique de flux vous permet de tirer parti de l’un des principaux outils d’analyse décisionnelle, Microsoft BI de puissance. Découvrez comment Azure flux Analytique permet d’analyser le volume élevé, les données et obtenir les informations dans un tableau de bord analytique BI de puissance en temps réel en continu.

[Puissance de Microsoft BI](https://powerbi.com/) permet de créer un tableau de bord actif rapidement. [Regardez une vidéo illustrant le scénario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Dans cet article, découvrez comment créer vos propres outils d’analyse décisionnelle personnalisée à l’aide de BI d’alimentation sous la forme d’une sortie pour vos travaux d’Azure flux Analytique et utiliser un tableau de bord en temps réel.

## <a name="prerequisites"></a>Conditions préalables

* Compte Microsoft Azure
* Une entrée pour le travail de flux Analytique consommer des données en continu à partir. Flux de données Analytique accepte l’entrée du stockage Azure événement concentrateurs ou les Blob Azure.  
* Compte de travail ou à l’école de puissance BI

## <a name="create-azure-stream-analytics-job"></a>Créer le travail d’Azure flux Analytique

À partir du [Portail classique d’Azure](https://manage.windowsazure.com), cliquez sur **Nouveau, les Services de données Analytique de flux, création rapide**.

Spécifiez les valeurs suivantes, puis cliquez sur **créer une Analytique de flux de travail**:

* **Nom de la tâche** - Entrez un nom de travail. Par exemple, **DeviceTemperatures**.
* **Région** : sélectionnez la région où vous souhaitez que la tâche que se trouve. Envisagez de placer le travail et le concentrateur d’événements dans la même région pour éviter de supporter les coûts de transfert de données entre les régions et de garantir des performances optimales.
* **Compte de stockage** - choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données d’analyse pour tous les travaux d’Analytique de flux de données en cours d’exécution dans cette région. Vous avez l’option à choisir un compte de stockage existant ou créez-en un nouveau.

Dans le volet gauche pour afficher les tâches de flux de données Analytique, cliquez sur **Flux Analytique** .

![graphique1][graphic1]

> [AZURE.TIP] Le nouveau projet apparaît avec l’état **N’a pas démarré**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Ce comportement est normal car vous devez configurer la tâche entrée, de sortie, de requête, et ainsi de suite avant de commencer le travail.

## <a name="specify-job-input"></a>Spécifier une entrée de tâche

Pour ce didacticiel, nous supposons que vous utilisez un concentrateur d’événements en tant qu’entrée avec le codage UTF-8 et de la sérialisation JSON.

* Cliquez sur le nom du travail.
* Cliquez sur les **entrées** à partir du haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera à travers un certain nombre d’étapes pour configurer votre entrée.
*   Sélectionnez le **flux de données**, puis cliquez sur le bouton droit.
*   Sélectionnez le **Concentrateur d’événements**et puis cliquez sur le bouton de droite.
*   Tapez ou sélectionnez les valeurs suivantes sur la troisième page :
  * **Alias d’entrée** - Entrez un nom convivial pour cette tâche d’entrée. Notez que vous utiliserez ce nom dans la requête ultérieurement.
  * **Concentrateur d’événements** - si le concentrateur d’événements que vous avez créé est dans le même abonnement comme l’Analytique de flux de données, sélectionnez l’espace de noms est le concentrateur d’événements.
*   Si votre concentrateur d’événements se trouve dans un autre abonnement, sélectionnez le **Concentrateur d’événements utilisation à partir d’un autre abonnement** et entrez manuellement les informations de **Namespace de Bus de Service**, **Nom de concentrateur d’événements**, **Nom de concentrateur des événements à l’aide de la stratégie**, **Clé de concentrateur des événements à l’aide de la stratégie**et **Le nombre d’événements concentrateur Partition**.

> [AZURE.NOTE]  Cet exemple utilise le nombre par défaut de partitions, ce qui est de 16.

* **Nom d’événement Hub** - sélectionnez le nom du concentrateur événement Azure vous avez.
* **Nom de concentrateur des événements à l’aide de la stratégie** - sélectionner la stratégie d’événement Hub pour le concentrateur d’événements que vous utilisez. Assurez-vous que cette stratégie a gérer les autorisations.
*   **Groupe de consommateur d’événements concentrateur** – vous pouvez laissez cette zone vide, ou spécifier un groupe de consommateurs que vous avez sur votre concentrateur d’événements. Notez que chaque groupe de consommateurs d’un concentrateur d’événements peut avoir seulement 5 lecteurs à la fois. Par conséquent, décider du groupe de consommateurs de droit au travail en conséquence. Si vous laissez le champ vide, il utilisera le groupe de consommateurs de par défaut.

*   Cliquez sur le bouton de droite.
*   Spécifiez les valeurs suivantes :
  * **Format d’événement sérialiseur** - JSON
  * **Codage** : UTF8
*   Cliquez sur le bouton de vérification pour ajouter cette source et de vérifier que flux Analytique peut se connecter au concentrateur de l’événement.

## <a name="add-power-bi-output"></a>Ajouter une sortie de puissance BI

1.  Cliquez sur la **sortie** à partir du haut de la page, puis cliquez sur **Ajouter une sortie**. Vous verrez la que puissance BI présentée comme une option de sortie.

    ![alors que le graphique2][graphic2]  

2.  Sélectionnez **Alimentation BI** et puis cliquez sur le bouton de droite.
3.  Vous verrez un écran semblable à la suivante :

    ![graphic3][graphic3]  

4.  Dans cette étape, fournir un compte de travail ou de l’école pour la sortie de projet Analytique de flux de données. Si vous avez déjà compte de puissance BI, sélectionnez **Autoriser maintenant**. Si ce n’est pas le cas, cliquez sur **Inscrivez-vous maintenant**. [Voici un bon blog tout au long des détails de l’inscription d’alimentation BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  Ensuite, vous verrez un écran semblable à la suivante :

    ![graphic4][graphic4]  

Fournir des valeurs comme indiqué ci-dessous :

* **Alias de sortie** – vous pouvez placer n’importe quel alias de sortie qui est facile à faire. Cet alias de sortie est particulièrement utile si vous souhaitez avoir plusieurs sorties pour votre travail. Dans ce cas, vous devez faire référence à cette sortie dans votre requête. Par exemple, nous utiliserons la valeur d’alias de sortie = « OutPbi ».
* **Nom du groupe de données** - fournir un nom de groupe de données que vous souhaitez que votre BI de puissance de sortie d’avoir. Par exemple, nous allons utiliser des « pbidemo ».
*   **Nom de la table** - fournir un nom de table dans le dataset de la sortie de puissance BI. Supposons que nous appelons « pbidemo ». Actuellement, sortie de puissance BI de tâches de flux de données Analytique peut avoir uniquement une table dans un groupe de données.
*   **Espace de travail** – sélectionnez un espace de travail de vos clients de puissance BI sous lequel sera créé le groupe de données.

>   [AZURE.NOTE] Explicitement ne créez pas ce groupe de données et une table dans votre compte courant BI. Il seront créés automatiquement lorsque vous lancez votre tâche de flux de données Analytique et sortie de pompage de démarrage de la tâche en puissance BI. Si votre requête ne renvoie aucun résultat, le groupe de données et la table n’est créées.

*   Cliquez sur **OK**, **Tester la connexion** et maintenant vous exportez la configuration est terminée.

>   [AZURE.WARNING] Sachez également que si l’alimentation BI avait déjà un groupe de données et la table avec le même nom que celui que vous avez fourni cette tâche de flux de données Analytique, les données existantes seront remplacées.


## <a name="write-query"></a>Écriture de requête

Accédez à l’onglet **requête** de votre travail. Écrivez votre requête, dont la sortie que vous souhaitez dans votre puissance de BI. Par exemple, il peut être quelque chose comme la requête SQL suivante :

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Démarrez votre travail. Validez votre concentrateur d’événements reçoit des événements de votre requête génère les résultats attendus. Si votre requête renvoie 0 lignes, tables et alimentation BI dataset n’est automatiquement créées.

## <a name="create-the-dashboard-in-power-bi"></a>Créer le tableau de bord dans BI de puissance

Accédez à [Powerbi.com](https://powerbi.com) et connectez-vous avec votre compte de travail ou de l’école. Si la requête de travail Analytique de flux renvoie des résultats, vous verrez que votre groupe de données est déjà créée :

![graphic5][graphic5]

Pour créer le tableau de bord, cliquez sur l’option de tableaux de bord et de créer un tableau de bord.

![graphic6][graphic6]

Dans cet exemple, nous allons une étiquette qu’il « Démonstration de tableau de bord ».

Cliquez maintenant sur le groupe de données créé par votre travail de flux Analytique (pbidemo dans notre exemple). Vous allez être dirigé vers une page pour créer un graphique sur ce groupe de données. Le suivant est un exemple de le rapports que vous pouvez créer :

Sélectionnez note temp et l’heure des champs. Il seront automatiquement transmise à valeur et l’axe du graphique :

![graphic7][graphic7]

Avec cette option, vous obtenez automatiquement un graphique comme indiqué ci-dessous :

![graphic8][graphic8]

Dans la section valeur, sur la liste déroulante vers le bas pour temp et choisissez **moyenne** de la température et sur le graphique, cliquez sur la **visualisation** , choisissez **graphique en courbes**:

![graphic9][graphic9]

Vous obtenez un graphique en courbes de moyenne dans le temps.  À l’aide de l’option pin comme indiqué ci-dessous, vous pouvez ajouter cela à votre tableau de bord que vous avez créé précédemment :

![graphic10][graphic10]

Maintenant lorsque vous affichez le tableau de bord avec ce rapport affiché, vous verrez rapport mise à jour en temps réel. Essayez de modifier les données de vos événements – temp de Pique-notes ou quelque chose de similaire et vous verrez l’effet en temps réel qui apparaissent dans votre tableau de bord.

Notez que ce didacticiel explique comment créer, mais un type de graphique pour un groupe de données. Alimentation BI peut vous aider à créer des autres outils d’analyse décisionnelle client pour votre organisation. Pour un autre exemple d’un tableau de bord d’alimentation, regardez la vidéo de [Mise en route avec alimentation BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Pour plus d’informations sur la configuration d’une sortie de puissance BI et d’utiliser des groupes de puissance BI, consultez la [section d’alimentation BI](stream-analytics-define-outputs.md#power-bi) de [que compréhension flux Analytique sorties](stream-analytics-define-outputs.md "sorties de compréhension flux Analytique"). Une autre ressource utile pour en savoir plus sur la création des tableaux de bord avec alimentation BI est [BI d’alimentation des tableaux de bord](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limites et meilleures pratiques

Alimentation BI utilise des contraintes d’accès concurrentiel et de débit comme décrit ici : [https://powerbi.microsoft.com/pricing],(https://powerbi.microsoft.com/pricing "Prix de puissance BI")

En raison de celles d’alimentation BI chaque débarquement lui-même plus naturellement aux cas où Azure flux Analytique n’une réduction de la charge de données considérables.
Nous recommandons l’utilisation de la TumblingWindow ou HoppingWindow pour vous assurer que les push de données serait au plus 1 push/seconde et que votre requête arrive dans les conditions de débit – vous pouvez utiliser l’équation suivante pour calculer la valeur pour donner à votre fenêtre en secondes :

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Par exemple, si vous disposez de 1 000 périphériques envoi de données par seconde, vous êtes sur la puissance BI Pro qui prend en charge de 1 000 000 lignes/heure et vous souhaitez obtenir la moyenne des données par périphérique sur alimentation BI vous pouvez effectuer au plus un push toutes les 4 secondes par périphérique (comme indiqué ci-dessous) :  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Qui signifie que nous pouvons modifier la requête d’origine :

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Actualisation de l’affichage PowerBI

Une question courante est « Pourquoi ne le tableau de bord-mise à jour automatique dans le PowerBI ? ».

Pour ce faire, dans la PowerBI utiliser Q & r et poser une question comme « Valeur Maximum par temp où Timestamp est aujourd'hui » et épingler cette mosaïque pour le tableau de bord.

### <a name="renew-authorization"></a>Renouvellement de l’autorisation

Vous devrez ré-authentifier votre compte courant BI si son mot de passe a été modifié depuis la création ou de dernière authentification de votre travail. Si plusieurs facteurs d’authentification (AMF) est configurée sur votre client Azure Active Directory (DAS), vous devez également renouveler l’autorisation d’alimentation BI toutes les 2 semaines. Un symptôme de ce problème est aucune sortie des tâches et une « authentification utilisateur erreur » dans les journaux de l’opération :

![graphic12][graphic12]

De même, si une tâche tente de démarrer alors que le jeton a expiré, une erreur se produira et le début de la tâche échoue. L’erreur se présente comme ci-dessous :

![Erreur de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Pour résoudre ce problème, arrêtez votre travail en cours d’exécution et accédez à votre sortie de puissance BI.  Cliquez sur le lien « Renouvellement d’autorisation » et redémarrez votre travail à partir de la dernière fois arrêté afin d’éviter la perte de données.

![Renouvellement de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Une fois que l’autorisation est actualisée avec alimentation BI, vous verrez une alerte verte dans la zone autorisation :

![Renouvellement de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Introduction aux flux de données Azure Analytique](stream-analytics-introduction.md)
- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
