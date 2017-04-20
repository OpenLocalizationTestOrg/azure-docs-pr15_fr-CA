<properties
   pageTitle="Test de votre offre de Service de données pour le marché | Microsoft Azure"
   description="Comprendre comment tester votre offre de Service de données pour le marché Azure."
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

# <a name="testing-your-data-service-offer-in-staging"></a>Test de votre offre de Service de données en transit

>[AZURE.IMPORTANT] **Pour l’instant nous ne sont plus arrivant des nouvelles publications de Service de données. Nouvelle dataservices ne sera pas obtenir approuvée pour la liste.** Si vous disposez d’une application d’entreprise SaaS vous souhaitez publier sur AppSource, vous pouvez trouver plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous possédez une applications IaaS ou service de développeur vous souhaitez publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Les deux premières étapes de la [Création de votre compte Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) et la [Création de votre offre de Service de données dans le portail de publication](marketplace-publishing-data-service-creation.md) à la fin, vous êtes prêt pour la mise à disposition de votre offre sur le marché d’Azure. Cette rubrique va vous guider tout d’abord, intermédiaires, étape appelée « Stockage temporaire »

Transit signifie déployer votre offre privé « sandbox », où vous pouvez tester et vérifier ses fonctionnalités avant de la placer à la production. L’offre s’affiche dans la zone de transit comme il le ferait pour un client qui a déployé.

## <a name="step-1-pushing-your-offer-to-staging"></a>Étape 1. En exécutant un push de votre offre d’intermédiaire
En exécutant un push de votre offre d’intermédiaire vous permet de tester l’offre avant de devenir disponible pour les abonnés futurs.  Vous pouvez voir comment votre offre s’affichent et fonctionnent pour ceux à vos données d’abonnement.  

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Ouverture de session dans le [portail de publication](https://publish.windowsazure.com)
2.  Sélectionnez les **Services de données** dans la fenêtre de navigation de gauche
3.  Sélectionnez votre offre que vous souhaitez distribuer à mis en œuvre. L’écran ci-dessus s’affiche.
4.  Cliquez sur le bouton de **Pousser à la mise en attente** .  
5.  S’il existe des problèmes avec l’offre qui doivent être effectuées avant de pousser à mis en œuvre, vous verrez une liste affichée.  Corriger ces éléments en cliquant sur chaque élément de la liste. Lorsque toutes les corrections apportées, cliquez à nouveau sur le bouton de **pousser à mis en œuvre** .

Si il n’y a pas de problèmes avec votre offre, vous verrez la fenêtre ci-dessous.  

Si vous utilisez non pas la planification approuvé pour votre offre dans Azure Portal de surface (actuellement a capacité limitée), puis fermez la fenêtre contextuelle.

Pour tester votre Service de données dans Azure Portal (en plus du portail DataMarket), vous devez une ID d’abonnement Azure pour le test.  Ce code d’abonnement identifie le compte qui sera autorisé à tester votre offre.  

Couper et coller votre ID d’abonnement et cliquez sur la coche pour continuer.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Ces abonnements Azure ID sont uniquement requis pour les tests et la zone de transit dans le [Portail de gestion Azure](https://manage.windowsazure.com). Ils ne doivent pas tester dans Azure Marketplace.

L’écran suivant qui s’affiche indique que la publication a lieu en affichant l’icône « en cours » en surbrillance jaune ci-dessous. En exécutant un push de transit prend entre 10 à 15 minutes.  Si elle prend plus de temps, d’abord actualiser votre navigateur (appuyez sur la touche F5 dans Internet Explorer).  Dans les rares cas où votre offre est toujours en exécutant un push de transit après une heure, cliquez sur le lien de contact faites-nous savoir qu’il existe un problème.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Lors de la diffusion de mise en attente termine à l’icône « en cours » seront arrêter le déplacement et le statut sera mises « Intermédiaire ».  Vous êtes maintenant prêt à tester votre offre.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Étape 2. Tester votre offre progressif dans DataMarket

Cliquez sur le lien qui suit le texte **« Voir votre service offre à... »** Pour afficher l’écran que l’abonné s’affiche lorsque votre offre passe à la production et s’affiche dans DataMarket.

  ![dessin](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Tester ou vérifier chacun des 12 éléments marqués ci-dessus pour assurer tous les logos, les prix/transactions, texte, images, documentation et les liens sont corrects et qu’il fonctionne correctement.  C’est le bon moment pour garantir des valeurs de test que vous avez entré lors de la création de votre offre ont été remplacés par des valeurs réelles.

1. Logo de l’offre
2. Nom de l’offre
3. Nom/lien de l’éditeur au site Web de votre société
4. Catégories de recherche pour votre offre
5. Lien de prise en charge de votre offre d’aider les abonnés
6. Description contextuelle pour votre offre
7. Proposer le plan représentant les détails de facturation
8. Créer un lien vers le code d’implémentation
9. Utilisent des exemples d’images qui illustrent des données de l’offre
10. Métadonnées d’entrée/sortie pour chaque service au sein de l’offre
11. Conditions d’utilisation de l’offre
12. Aperçu des données de l’offre


Enfin, vérifiez que le service fonctionne par le biais de la Datamarket en cliquant sur le lien « Explorer ce groupe de données ».  Une nouvelle fenêtre s’ouvre dans l’outil, nous appelons « Service Explorateur » pour voir les résultats d’une requête par rapport à votre service.  Dans cette fenêtre, vous pouvez entrer les paramètres nécessaires et consultez les résultats affichés à partir d’une requête sur votre service.   L’URL affichée est également pour votre requête.  

> [AZURE.NOTE] Veillez à consulter le texte de description du service affiché en haut.  Si votre offre est constituée de plus d’un service appeler, cliquez sur les onglets en bas pour passer au prochain service pour passer en revue et tester.



## <a name="next-step"></a>Étape suivante
Si vous rencontrez des problèmes et que vous devez résoudre les contactez le [Support technique de Publisher Azure]( http://go.microsoft.com/fwlink/?LinkId=272975).

Si vous êtes satisfait et prêt à publier votre offre Lisez la documentation de [l’Approbation de la demande à envoyer à la Production](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Voir aussi
- [Mise en route : Comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)
