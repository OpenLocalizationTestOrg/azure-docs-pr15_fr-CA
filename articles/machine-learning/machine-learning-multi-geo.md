<properties
   pageTitle="La documentation d’aide de multi-Geo | Microsoft Azure"
   description="Apprenez à créer un espace de travail et publier un service web dans une zone Azure différente à partir du Sud Central des États-Unis d’Amérique (SCUS) région Azure."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Documentation d’aide de multi-Geo

Cet article décrit comment vous pouvez créer un espace de travail et publier un service web dans d’autres régions d’Azure.

## <a name="create-a-workspace"></a>Créer un espace de travail

1. Connectez-vous au portail Azure classique.

2.  Cliquez sur **+ Nouveau** > **les SERVICES de données** > **apprentissage automatique** > **Création rapide**.  Sous **emplacement** , sélectionnez une autre région, par exemple de **l’Asie du Sud-est**.
![Aide de multi-Geo image 1][1]
3. Sélectionnez l’espace de travail, puis cliquez sur **connecter en Studio de ML**.
![Aide de multi-Geo image 2][2]

4. Vous disposez maintenant d’un espace de travail dans une autre région que vous pouvez utiliser comme tout autre espace de travail. Pour basculer entre les espaces de travail, recherchez dans le coin supérieur droit de votre écran. Cliquez sur la liste déroulante, sélectionnez la zone et puis sélectionnez l’espace de travail. Tout ce qui est local à la zone de l’espace de travail ; par exemple, tous vos services web créés à partir d’un espace de travail sera dans la même région, situé dans l’espace de travail.
![Aide de multi-Geo image 3][3]

## <a name="open-an-experiment-from-gallery"></a>Ouvrir une expérience de galerie

Si vous ouvrez une expérience de galerie, vous pouvez également sélectionner la région que vous voulez copier l’expérimentation.

![Aide de multi-Geo image 4][4a]

## <a name="web-service-management"></a>Gestion des services Web

Pour gérer par programme les services web, tels que pour la formation de reconversion, utilisez l’adresse spécifique à la région :
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Points à noter

1.  Vous ne pouvez copier que des expériences entre les espaces de travail qui appartiennent à la même région de cette manière. Si vous devez copier l’expérimentation sur des espaces de travail dans différentes régions, vous pouvez utiliser l’applet de commande [PowerShell](http://aka.ms/amlps) [*AmlExperiment-copie*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) d’accomplir cela. Une autre solution consiste à publier l’expérience dans la galerie en mode non listé, puis ouvrez-le dans l’espace de travail à partir de l’autre région.
2.  Le sélecteur de région affiche uniquement les espaces de travail pour une seule région à la fois. À l’avenir, vous serez en mesure de voir une liste complète des espaces de travail que vous avez accès à travers toutes les régions en même temps.  
3.  Un espace libre ou un espace de travail (anonyme) accès invité sera créé et hébergé dans le sud américain À l’avenir, vous serez en mesure de créer des espaces de travail accès libre/invité dans la région que vous choisissez.  
4.  Services Web déployés à partir d’un espace de travail de l’Asie du Sud-est va également être hébergés d’Asie du Sud-est. À l’avenir, vous pourrez avoir la possibilité de créer des expériences dans une région, et déploiement généré des points de terminaison de service web dans les différentes régions.  

## <a name="more-information"></a>Plus d’informations

Poser une question sur le [forum de formation de Machine Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
