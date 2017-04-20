<properties
   pageTitle="Guide de création d’un Service de données pour le marché | Microsoft Azure"
   description="De la création, de certifier et de déployer un Service de données pour obtenir des instructions détaillées d’achat sur le marché d’Azure."
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
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Service de Guide à Azure Marketplace de publication des données

>[AZURE.IMPORTANT] **Pour l’instant nous ne sont plus arrivant des nouvelles publications de Service de données. Nouvelle dataservices ne sera pas obtenir approuvée pour la liste.** Si vous disposez d’une application d’entreprise SaaS vous souhaitez publier sur AppSource, vous pouvez trouver plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous possédez une applications IaaS ou service de développeur vous souhaitez publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Après l’exécution de l’étape 1, [la création de comptes et d’enregistrement](marketplace-publishing-accounts-creation-registration.md), nous guidé vous [Général non technique](marketplace-publishing-pre-requisites.md) et les [Exigences techniques](marketplace-publishing-data-service-creation-prerequisites.md) d’une offre de Service de données sur Azure Marketplace. Maintenant nous vous guiderons à travers les étapes de création d’une offre de Service de données sur le [Portail de publication] [ link-pubportal] pour le marché Azure.

## <a name="1---login-to-the-publishing-portal"></a>1. Connectez-vous à la publication Portal.

Accédez à [https://publish.windowsazure.com](https://publish.windowsazure.com. )

**Pour la première connexion au portail de publication, utilisez le même compte que celui avec lequel le profil de vendeur de votre société a été enregistré dans le centre de développement.**  (Vous pouvez ultérieurement ajouter n’importe quel employé de votre société en tant qu’administrateur de co dans le portail de publication).

Cliquez sur la mosaïque de **publier un service de données** , il s’agit de la première connexion au portail de publication.

## <a name="2---choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2. Choisissez les **Services de données** dans le menu de navigation sur le côté gauche.

  ![dessin](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3---create-a-new-data-service"></a>3. création d’un nouveau Service de données

Entrer un titre pour votre nouvelle offre de Service de données et cliquez sur « + » sur la droite.

  ![dessin](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4---review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4. passer en revue le sous-menu sous le Service de données nouvellement créé dans le menu de navigation.

Cliquez sur l’onglet de la **procédure pas à pas** et passez en revue toutes les étapes nécessaires requises pour publier correctement le Service de données sur le marché d’Azure.

> [AZURE.TIP] Vous pouvez toujours cliquer sur les liens dans la page « Procédure pas à pas » ou utiliser les onglets sur le sous-menu de l’offre Service de données sur le côté gauche.

## <a name="5---create-a-new-plan"></a>5. créer un nouveau Plan.

### <a name="offers-plans-transactions"></a>Les offres, les Plans, les transactions.

Chaque offre peut avoir plusieurs Plans, mais doit avoir au moins un (1) le Plan. Lorsque les utilisateurs finaux s’abonner à l’offre qu’ils s’abonner pour l’un de Plan de l’offre. Chaque plan définit comment les utilisateurs finaux seront en mesure d’utiliser votre service.

Azure Marketplace prend en charge actuellement uniquement modèle de base de Transaction d’abonnement mensuel pour les Services de données, c'est-à-dire les utilisateurs finaux paieront les frais mensuels en fonction du prix du plan spécifique qu’ils abonnés à et qu’il seront en mesure de consommer chaque numéro du mois de transaction définie par le plan.

Chaque Transaction généralement définie comme le nombre d’enregistrements renvoyés par votre Service de données basé sur la requête envoyée au Service. La valeur par défaut est 100. Nombre de transactions renvoyé à chaque requête sera number d’enregistrements divisés par 100 et arrondie à l’entier le plus proche.

Il incombe Azure Marketplace Service couche pour surveiller le nombre (compteur) de transactions consommée par chaque requête.

> [AZURE.IMPORTANT] Les utilisateurs finaux qui a atteint la limite de transaction au cours du mois seront bloqués de continuer à utiliser le service jusqu'à la fin de leur cycle d’abonnement mensuel.

> Le plan ou l’un des plans peut (mais ne doit pas) inclure un nombre illimité de transactions.

### <a name="create-a-plan"></a>Créer un plan.
1. Cliquez sur **« + »** en regard de « Ajouter un nouveau plan ».

2. Choisissez l’une des options : utilisation **illimitée** ou **limitée** pour ce plan.  Si limité ensuite fournit le numéro de transaction le plan permet de consommer dans un mois.

    ![dessin](media/marketplace-publishing-data-service-creation/step-5.1.png)  

    Portail de publication vous propose également des « Identificateur Plan », qui sera utilisé pour communiquer sur les utilisateurs finaux, le nom du plan dans l’interface utilisateur et également utilisée par le Service sur le marché pour identifier le Plan. Vous pouvez modifier le « identificateur de Plan » si vous le souhaitez.

    > [AZURE.NOTE] Le « identificateur de Plan » doit être unique dans la portée de chaque offre. Comme de nombreux autres identificateurs utilisés dans l’identificateur de portail de publication Plan seront verrouillés après que la première publication pour production et vous ne pourrez pas modifier cet identificateur.

3. Cliquez pour accepter votre choix.

4. Puis vous demandera quelques questions supplémentaires en ce qui concerne votre Plan nouvellement créé.

    ![dessin](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Question|Signification|
|----|----|
|**Ce Plan est gratuite et disponible à l’échelle mondiale ?**|Vous pouvez créer un plan entièrement libre de charge. S’il est le seul planning de cette offre, cela signifie que vous publiez « Offre gratuite » sur le marché. S’il s’agit uniquement pour des (peu), Plan de l’informatique vous donne la possibilité d’offrir aux utilisateurs finaux pour en savoir plus sur votre service avec un petit nombre de transactions par mois.  Si la réponse est « Oui », vous ne serez invitée aucun autre question.|

> [AZURE.NOTE] Les utilisateurs finaux peuvent toujours mettre à niveau vers les plans payés.

|Question|Signification|
|----|----|
|**Est la version d’évaluation gratuite disponible ?**|Vous pouvez choisir entre « No évaluation » tout ou proposer une option pour utiliser votre Plan pour « Mois ». Éditeurs que cette option permet de fournir aux utilisateurs finals la possibilité de comprendre les avantages de l’offre gratuitement pendant un mois.|

> [AZURE.IMPORTANT] Les utilisateurs finaux pourront uniquement acheter une version d’évaluation gratuite s’ils ont établi le moyen de paiement carte de crédit, par exemple, accord entreprise.

> Après un délai d’un mois la version d’évaluation gratuite, Azure Marketplace démarrera clients en cours de chargement à partir de la date de l’abonnement, le prix de, à moins que le client initié l’annulation de l’abonnement. Aucune notification spéciale n’est accordée aux utilisateurs finaux.

|Question|Signification|
|----|----|
|**Ce plan nécessite un code de promotion à l’achat ?**| Éditeurs ont une option permettant de limiter l’accès à leurs Plans de Service en fournissant un code spécial, appelé « A du Promocode » pour des clients spécifiques. Uniquement les utilisateurs qui ont du Promocode pourront s’abonner au Plan. Si vous choisissez « Non », vous acceptez que tout le monde de la région où l’offre est disponible (voir [Guide de contenu Marketing Marketplace](marketplace-publishing-push-to-staging.md) pour plus de détails) sera en mesure de vous abonner à ce plan. Aucune autre question ne sera demandée.|
|**Également masquer ce plan à partir de toute personne qui n’a pas un code de promotion valide ?**|Si la réponse à la question précédente est « Oui » l’éditeur dispose d’une option pour supprimer complètement ce plan d’apparaître dans l’interface utilisateur du marché. Cela signifie que, les clients ne verront pas ce plan dans la page de détails de l’offre. Les utilisateurs finaux qui reçoivent du promocode pour l’acheter, sera en mesure de vous abonner à l’aide du promocode.|

## <a name="6---create-your-marketplace-marketing-content"></a>6. créer votre contenu commercial de marché
Pour savoir comment fournir les informations requises dans les onglets **Marketing, tarification, prise en charge et catégories** veuillez visite [Guide contenu Marketing de marché](marketplace-publishing-push-to-staging.md) qui est commune à tous les artefacts publiés sur le marché d’Azure.  

## <a name="7---connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7. Connectez votre offre à votre Service (SQL Azure en fonction ou un Service Web en fonction).

Cliquez sur le sous-menu de **Services de données** .

Sur la moitié supérieure de la page vous demandera de fournir de l’offre **Namespace**.  

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.png)

La ci-dessous question définit comment l’éditeur va offre exposent nouvellement créé vers Azure Marketplace. (Pour plus de détails, voir le [Guide requis technique de Services de données](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Le service en fonction de la base de données de publication**

Cliquez sur la **base de données**. La page suivante s’affiche :

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.3.png)

Pour créer un mappage de CSDL pour le groupe de données basé sur la base de données SQL Azure :

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.4.png)

Puis, pour chaque table.

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.6.png)

Si Service Web

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT] Lire le [mappage d’un service web existant pour OData dans CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) pour obtenir des instructions détaillées et des exemples de création d’un Service Web de CSDL.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé votre offre de Service de données, veuillez vous assurer que vous suivez les instructions dans le [Guide de contenu Marketing Marketplace](marketplace-publishing-push-to-staging.md) avant de passer au [test de votre Service de données en transit](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Voir aussi
- [Mise en route : Comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)
- Si vous vous intéressez à comprendre le processus de mappage OData et l’objectif global, lisez ce [Mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping.md) pour examiner les définitions, les structures et les instructions de l’article.
- Si vous êtes intéressé par apprentissage et la compréhension des nœuds spécifiques et leurs paramètres, lisez cet article [Les nœuds de mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) pour des définitions et des explications, des exemples et utiliser le contexte de la demande de devis.
- Si vous êtes intéressés par l’étude des exemples, lisez l’article [Exemples de mappage de données Service OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour voir des exemples de code et de comprendre le contexte et la syntaxe du code.


[link-pubportal]:https://publish.windowsazure.com
