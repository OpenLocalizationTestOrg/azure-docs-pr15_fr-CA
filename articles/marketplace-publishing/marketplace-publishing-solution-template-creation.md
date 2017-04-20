<properties
   pageTitle="Guide de création d’un modèle de solution pour le marché | Microsoft Azure"
   description="Pour obtenir des instructions détaillées de la création, de certifier et de déployer un modèle de Solution d’Image Multi-VM pour acheter sur Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guide pour créer un modèle de solution pour Azure Marketplace
Après l’étape 1, [d’enregistrement et de création de compte][link-acct-creation], nous vous interactive sur la création d’un modèle de solution compatible avec Azure à [des conditions techniques requises pour la création d’un modèle de solution](marketplace-publishing-solution-template-creation-prerequisites.md). Maintenant nous vous guiderons à travers les étapes de création d’un modèle de solution pour plusieurs ordinateurs virtuels sur le [Portail de publication] [ link-pubportal] pour le marché Azure.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Créez votre modèle d’offre sur le portail de publication
Accédez à [https://publish.windowsazure.com](http://publish.windowsazure.com). Lorsque vous vous connectez pour la première fois sur le [Portail de publication](https://publish.windowsazure.com/), utilisez le même compte que celui avec lequel le profil de vendeur de votre société a été enregistré. Ultérieurement, vous pouvez ajouter n’importe quel employé de votre société en tant qu’administrateur de co dans le portail de publication.

### <a name="1-select-solution-templates"></a>1. Cliquez sur « Modèles de Solution »

  ![dessin][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. créer un nouveau modèle de solution

  ![dessin][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Démarrer avec les topologies
Un modèle de solution est un « parent » à toutes les topologies. Vous pouvez définir plusieurs topologies dans un modèle d’offre/solution. Lorsqu’une offre est poussée vers la mise en œuvre, il est poussé avec tous ses topologies. Suivez les étapes ci-dessous pour définir votre offre :     

- Créer une topologie : « Identificateur de topologie » est généralement le nom de la topologie pour le modèle de la solution. L’identificateur de topologie est utilisé dans l’URL, comme indiqué ci-dessous :

  Azure Marketplace : http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Azure Portal : https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Ajouter une nouvelle version.

### <a name="4-get-your-topology-versions-certified"></a>4. Obtenez vos versions de topologie certifiées
Télécharger un fichier zip qui contient tous les fichiers requis pour mettre en service cette version particulière de la topologie. Ce fichier zip doit contenir les éléments suivants :

- fichiers *mainTemplate.json* et *createUiDefinition.json* dans son répertoire racine.
- Tous les modèles liés et scripts obligatoires.

  > [AZURE.TIP] Alors que vos développeurs travaillent sur la création de topologies de modèle de la solution et en incitant certifié, l’entreprise, marketing, juridiques et/ou départements de votre entreprise peuvent travailler sur le contenu de la marketing et juridique.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous créé le modèle de votre solution et téléchargez le fichier zip, veuillez suivre les instructions dans le [guide du contenu marketing du marché](marketplace-publishing-push-to-staging.md) avant de pousser l’offre de mise en attente. Pour voir l’ensemble de la publication d’articles de marché, visitez [mise en route : comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md).

Pourraient également vous intéresser dans ces articles connexes :

- Images VM : [Images de Machine virtuelle dans Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- Extensions de la machine virtuelle : [Agent de la machine virtuelle et de la vue d’ensemble des Extensions de machine virtuelle](https://msdn.microsoft.com/library/azure/dn832621.aspx) et [Azure VM d’Extensions et fonctionnalités](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- [Création de modèles ARM Azure](../resource-group-authoring-templates.md) et des [exemples de modèle ARM Simple](https://github.com/rjmax/ArmExamples) Azure le Gestionnaire de ressources :
- Limite de compte de stockage : [comment moniteur de limitation du compte de stockage](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) et [stockage de prime](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
