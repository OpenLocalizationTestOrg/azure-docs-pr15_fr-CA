<properties 
    pageTitle="Configurer et utiliser l’API de recommandations d’apprentissage Machine | Microsoft Azure" 
    description="API de recommandations de Microsoft créée avec le Forum aux questions sur Azure Machine Learning" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Paramétrage et l’utilisation de Machine Learning recommandations API Forum aux questions


**Ce qui est des recommandations ?**

>[AZURE.NOTE]Vous devez démarrer à l’aide du Service COGNITIF API de recommandations au lieu de cette version. Le Service COGNITIF recommandations remplacera ce service et toutes les nouvelles fonctionnalités seront développées il. Il a de nouvelles fonctionnalités telles que le traitement par lots à la prise en charge, une meilleure API Explorer, expérience de surface et plus cohérente d’inscription/facturation nettoyeur API, etc..
> Pour en savoir plus sur la [migration vers le nouveau Service COGNITIF](http://aka.ms/recomigrate)

Pour les organisations et les entreprises qui s’appuient sur les recommandations pour la vente croisée et de vente des produits et services à leurs clients, les recommandations dans l’apprentissage automatique de Azure fournit un moteur de recommandations de libre-service. Il s’agit d’une implémentation de filtrage collaboratif qui utilise une factorisation de matrice comme algorithme de base. Les développeurs d’applications peuvent accéder à recommandations en utilisant des API de reste. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Que puis-je faire avec des recommandations ?**

Prend comme entrée un élément ou un ensemble d’éléments et renvoie une liste de recommandations pertinentes des recommandations. Par exemple : un client d’un détaillant en ligne clique sur un produit. Le détaillant en ligne envoie ce produit en tant qu’entrée aux recommandations, obtient une liste de produits en retour et décide laquelle de ces produits sera visible pour le client. Vous souhaiterez peut-être utiliser les recommandations pour optimiser votre Boutique en ligne ou encore pour informer votre intérieur vente centre d’appel ou de service.

**Y a-t-il des restrictions d’utilisation ?**

Recommandations a les restrictions d’utilisation suivantes :
* Nombre maximal de modèles par abonnement : 10
* Nombre maximal d’éléments pouvant être un catalogue : 100 000
* Le nombre maximal de points de l’utilisation sont conservés est ~ 5 000 000. Le plus ancien sera supprimé si de nouveaux est téléchargées ou signalés.
* Taille maximale des données qui peuvent être envoyées par courrier électronique (par exemple, Importer données de catalogue, Importer données d’utilisation) est de 200 Mo
* Nombre de transactions par seconde (TPS) pour la construction d’un modèle recommandations qui n’est pas active est ~ 2 TPS. Construction d’un modèle recommandations active peut contenir jusqu'à 20 TPS.

##<a name="purchase-and-billing"></a>Achat et facturation 


**Combien coût de recommandations au cours de la période de lancement ?**

Recommandations est un service sur abonnement. En cours de chargement est basée sur le volume de transactions par mois. Vous pouvez vérifier la [page de l’offre] (https://datamarket.azure.com/dataset/amla/recommendations) dans Microsoft Azure Marketplace pour les informations de tarification.

**Y a-t-il des tous les coûts liés à la nécessité de recommandations, suivre et enregistrer l’activité des utilisateurs pour moi ?**

Pas pour le moment.

**Recommandations disposent d’une version d’évaluation gratuite ?**

Puisqu’il est gratuit qui est limité à 10 000 transactions par mois.

**Lorsque vous être facturé pour les recommandations ?**

Un abonnement est un abonnement pour lequel il existe un abonnement mensuel. Lorsque vous achetez un abonnement payant, vous sont immédiatement débité pour l’utilisation du premier du mois. Vous êtes chargé de la quantité qui est associée à l’offre sur la page d’abonnement (plus taxes applicables). Ce prix mensuel est effectué chaque mois à la même date de calendrier comme votre achat d’origine jusqu'à l’annulation de l’abonnement. 

**Comment passer d’un service de niveau supérieur ?**

Vous pouvez acheter ou mettre à jour votre abonnement à partir de la [page de l’offre] page (https://datamarket.azure.com/dataset/amla/recommendations) sur Microsoft Azure Marketplace.

Lorsque vous mettez à niveau un abonnement :

* Les transactions qui demeurent sur votre ancien abonnement ne sont pas ajoutées à votre nouvel abonnement. 
* Vous payez des prix complet pour le nouvel abonnement, même si vous avez des transactions non utilisées sur votre ancien abonnement.

Processus de mise à niveau d’un abonnement :

* Nevigate à la page offre (https://datamarket.azure.com/dataset/amla/recommendations).
* Ouvrez une session sur le marché si vous n’êtes pas déjà connecté.
* Dans le volet de droite, tous les plans disponibles sont répertoriés. Cliquez sur le bouton radio pour le plan que vous souhaitez mettre à niveau vers.
* Si vous souhaitez mettre à niveau, cliquez sur **OK**. Si vous ne souhaitez pas mettre à niveau, cliquez sur **Annuler**.

**Important** Lisez attentivement la boîte de dialogue Mise à niveau, car il y a des implications de la facturation et utilisation.

**Quand mon abonnement aux recommandations se terminera ?**

Votre inscription prendra fin lorsque vous l’annulez. Si vous souhaitez annuler votre abonnement, consultez les instructions ci-dessous.

**Comment annuler mon abonnement recommandations ?**

Pour annuler votre abonnement, procédez comme suit. Si votre abonnement actuel est un abonnement payant, votre abonnement continue en vigueur jusqu'à la fin de la période de facturation actuelle. Si vous avez besoin d’être immédiatement l’effet de l’annulation, contactez-nous au [Support technique de Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Remarque** Aucune restitution n’est donnée si vous annulez avant la fin d’une période de facturation, ou pour les transactions dans une période de facturation.

* Accédez à la page offre (https://datamarket.azure.com/dataset/amla/recommendations).
* Ouvrez une session sur le marché si vous n’êtes pas déjà connecté.
* Cliquez sur **Annuler** à droite du nom du groupe de données et de l’état. Vous pouvez utiliser cet abonnement jusqu'à la fin de la période de facturation actuelle ou votre limite de transaction atteinte (selon ce qui se produit en premier).

Si vous souhaitez annuler votre abonnement immédiatement par conséquent, vous pouvez acheter un nouvel abonnement, fichier un ticket au [Support technique de Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Mise en route de recommandations

**Consiste en recommandations pour moi ?** 

Recommandations dans l’apprentissage automatique est pour les entreprises qui s’appuient sur les recommandations pour la vente croisée et de vente des produits ou services à leurs clients. Si vous avez un site client, une force de vente, un intérieur force de vente ou un centre d’appels, et si vous proposez un catalogue de plus de quelques dizaines de produits ou de services, votre chiffre d’affaires peut bénéficier de l’utilisation de recommandations. 

L’expérimentation de recommandations est conçu pour être relativement simple. La version actuelle basée sur l’API nécessite des compétences de programmation de base. Si vous avez besoin d’aide, contactez le fournisseur qui a développé votre site Web. Si vous disposez d’un service informatique interne ou un développeur interne, ils doivent être en mesure d’obtenir des recommandations à travailler pour vous. 

**Quelles sont les conditions requises pour la configuration des recommandations ?**

Recommandations nécessite d’avoir un journal de choix de l’utilisateur par rapport à votre catalogue. Si vous ne pas disposer d’un journal de ce type et vous n’avez pas besoin d’un site Web en vis-à-vis du client, recommandations peuvent collecter à l’activité des utilisateurs pour vous. 

Recommandations requiert également un catalogue de vos produits ou services. Si vous ne pas avoir le catalogue, recommandations peuvent utiliser les données d’utilisation client réel et diffuser un catalogue. Un catalogue implicite n’inclut pas les éléments qui n’ont pas signalés dans le cadre de transactions utilisateur.

**Comment définir des recommandations pour la première fois ?**

Après [abonnement] (https://datamarket.azure.com/dataset/amla/recommendations) pour obtenir des recommandations, vous devez utiliser la documentation de l’API dans [Azure Machine Learning recommandations Guide de démarrage rapide](machine-learning-recommendation-api-quick-start-guide.md) pour configurer le service.

**Où trouver la documentation de l’API ?** 

La documentation de l’API est [Azure Machine Learning recommandations Guide de démarrage rapide](machine-learning-recommendation-api-quick-start-guide.md).

**Les options disposez charger des données de catalogue et l’utilisation des recommandations ?**

Vous disposez de deux options pour le transfert de vos données de catalogue et d’utilisation : vous pouvez exporter les données de votre système CRM ou d’autres journaux et télécharger des recommandations, ou vous pouvez ajouter des balises à votre site Web qui fera le suivi des activités de l’utilisateur. Si vous utilisez cette dernière méthode, les données seront stockées dans Azure.

##<a name="maintenance-and-support"></a>Maintenance et support

**La taille peut être l’ensemble de mes données ?**

Chaque ensemble de données peut contenir jusqu'à 100 000 éléments du catalogue et jusqu'à 2 048 Mo de données d’utilisation.
En outre, un abonnement peut contenir jusqu'à 10 ensembles de données (modèles).

**Où puis-je obtenir un support technique pour obtenir des recommandations ?**

Support technique est disponible sur le site de [Support de Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**Où puis-je trouver les conditions d’utilisation ?**

[Apprentissage des conditions d’API de recommandations de Service de l’ordinateur Microsoft Azure](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 
