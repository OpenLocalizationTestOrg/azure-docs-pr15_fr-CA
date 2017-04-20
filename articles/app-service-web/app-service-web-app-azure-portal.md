<properties
    pageTitle="Référence pour la navigation dans le portail Azure"
    description="Découvrez l’expérience d’utilisateur différent pour l’application de Service Web entre le portail de gestion et le portail Azure"
    services="app-service"
    documentationCenter=""
    authors="jaime-espinosa"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/26/2016"
    ms.author="jaime-espinosa"/>

# <a name="reference-for-navigating-the-azure-portal"></a>Référence pour la navigation dans le portail Azure

Sites Web Azure sont maintenant appelées [Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Nous allons mettre à jour toute notre documentation pour refléter ce changement de nom et fournit des instructions pour le portail d’Azure. Jusqu'à ce que ce processus est terminé, vous pouvez utiliser ce document comme un guide pour l’utilisation avec les applications Web dans le portail Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 
 
## <a name="the-future-of-the-azure-classic-portal"></a>L’avenir du portail Azure classique

Pendant que vous remarquerez les modifications de personnalisation sur le portail classique d’Azure, ce portail est en cours de remplacement par le portail Azure. Comme le portail classique est progressivement, le focus pour un nouveau développement consiste au portail Azure. Toutes les nouvelles fonctionnalités à venir pour les applications Web sont issus dans le portail Azure. Commencer à utiliser le portail Azure pour bénéficier de la plus récente et plus grande que les applications Web ont à offrir.

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Différences de disposition entre le portail classique d’Azure et Azure Portal

Dans le portail classique, tous les services Azure sont répertoriés sur le côté gauche. Navigation dans le portail classique suit une structure arborescente, dans lequel vous démarrez à partir du service et naviguez dans chaque élément. Cette structure fonctionne bien lorsque vous gérez des composants indépendants. Toutefois, les applications créées sur Azure sont un ensemble de services interconnectés et l’arborescence n’est pas idéale pour travailler avec des collections de services. 

Le portail Azure facilite la création d’applications de bout en bout avec des composants à partir de plusieurs services. Le portail est organisé des *trajets*. Un *voyage* est une série de *lames*, qui sont des conteneurs pour les différents composants. Par exemple, configuration automatique-mise à l’échelle pour une application web est un *voyage* qui vous prend plusieurs lames comme indiqué dans l’exemple suivant : la lame de **site web** (que titre de lame n'a pas encore été mis à jour pour utiliser la terminologie nouvelle), la **paramètres** lame et la lame de **mise à l’échelle** . Dans l’exemple, mise à l’échelle est configuré à dépendre de l’utilisation du processeur, et il existe également une lame de **Pourcentage de l’UC** . Les composants dans les *lames* sont appelés des *parties*, afin de répondre en mosaïques. 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>Exemple de navigation : créer une application web

Création de nouvelles applications web est toujours aussi facile que 1-2-3. L’image suivante montre le portail classique et le portail côte à côte pour montrer que rien n’a changé dans le nombre d’étapes nécessaires pour préparer une application web et en cours d’exécution. 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

Dans le portail, vous pouvez choisir à partir de types les plus courants des applications web, y compris les applications de bibliothèque populaires comme WordPress. Pour obtenir une liste complète des applications disponibles, visitez le [Azure Marketplace].

Lorsque vous créez une application web, vous spécifiez les URL, plan de Service d’application et l’emplacement dans le portail comme vous le faites dans le portail classique. 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

En outre, le portail vous permet de définir les autres paramètres communs. Par exemple, [les groupes de ressources](../azure-resource-manager/resource-group-overview.md) facilitent visualiser et gérer les ressources connexes Azure. 

## <a name="navigation-example-settings-and-features"></a>Exemple de navigation : paramètres et fonctionnalités

Tous les paramètres et les fonctionnalités sont désormais logiquement regroupées dans une seule lame, à partir de laquelle vous pouvez naviguer.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Par exemple, vous pouvez créer des domaines personnalisés en cliquant sur **Custom domains et SSL** dans la lame de **paramètres** .

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Pour configurer une alerte de surveillance, cliquez sur **erreurs et demandes** , puis sur **Ajouter une alerte**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Pour activer les tests de diagnostic, cliquez sur **journaux de Diagnostics** dans la lame de **paramètres** .

![](./media/app-service-web-app-azure-portal/Diagnostics.png)
 
Pour configurer les paramètres de l’application, cliquez sur **paramètres de l’Application** de la lame de **paramètres** . 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Autre que le nom de la marque, quelques éléments du portail ont été renommés ou regroupées différemment pour faciliter leur recherche. Par exemple, voici une capture d’écran de la page correspondante pour l’application des paramètres (**configurer**) dans le portail classique.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>Plus de ressources

[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
 
