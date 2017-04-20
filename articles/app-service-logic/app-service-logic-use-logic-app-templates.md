<properties
 pageTitle="Modèles d’App logique | Microsoft Azure"
 description="Apprenez à utiliser la logique app modèles prédéfinis pour vous aider à démarrer"
 authors="kevinlam1"
 manager="dwrede"
 editor=""
 services="app-service\logic"
 documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="klam"/>

# <a name="logic-app-templates"></a>Modèles d’App logique

## <a name="what-are-logic-app-templates"></a>Quels sont les modèles d’application logique

Un modèle d’application logique est une application de logique prédéfinis que vous pouvez utiliser pour commencer rapidement à créer votre propre flux de travail. 

Ces modèles sont un bon moyen de découvrir les différents modèles qui peuvent être créés à l’aide d’applications de logique. Vous pouvez utiliser ces modèles comme-est ou les modifier pour les adapter à votre scénario.

## <a name="overview-of-available-templates"></a>Vue d’ensemble des modèles disponibles

Il existe de nombreux modèles disponibles actuellement publiés dans la plate-forme d’application logique. Certaines catégories d’exemples, ainsi que le type de connecteurs utilisés, est répertorié ci-dessous.

### <a name="enterprise-cloud-templates"></a>Modèles pour l’entreprise cloud
Modèles qui intègrent Dynamics CRM, force de vente, zone, Azure Blob et autres connecteurs pour les besoins de nuage de votre entreprise. Voici quelques exemples de ce qui peut être fait avec ces modèles incluent les organiser vos prospects et la sauvegarde des données de vos fichiers d’entreprise.

### <a name="enterprise-integration-pack-templates"></a>Modèles du pack d’intégration entreprise
Configurations de VETER (valider, extraire, transformer, enrichir, Router) pipelines, recevoir une X12 EDI sur AS2 de document et les transforme en XML, tout aussi bien en tant que message X12 et AS2 manutention.

### <a name="protocol-pattern-templates"></a>Modèles de modèle de protocole
Ces modèles se composent d’applications de logique qui contient les modèles de protocole comme réponse à la demande sur HTTP, ainsi que des intégrations entre FTP et SFTP. Utilisez-les tels qu’ils existent, ou comme base pour la création de modèles de protocole plus complexes.  

### <a name="personal-productivity-templates"></a>Modèles de productivité personnelle
Modèles pour aider à améliorer la productivité personnelle incluent des modèles de définir des rappels quotidiennes, activer des éléments de travail dans des listes de tâches et automatisent les tâches de longue durée à une étape d’approbation utilisateur unique.

### <a name="consumer-cloud-templates"></a>Modèles du consommateur cloud
Des modèles simples qui s’intègrent avec les services de support de réseaux sociaux tels que Twitter, la marge et la messagerie, en fin de compte capable de renforcer les initiatives marketing de médias sociaux. Cela inclut également des modèles tels que de la copie de trouble, qui permettent d’accroître la productivité en enregistrant le temps passé sur les tâches répétitives traditionnellement. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>Comment créer une application de logique à l’aide d’un modèle 

Pour démarrer à l’aide d’un modèle d’application logique, allez dans le Concepteur d’application logique. Si vous saisissez le concepteur en ouvrant une application logique existant, l’application logique se charge automatiquement dans le mode concepteur. Cependant, si vous créez une nouvelle application logique, vous voyez l’écran ci-dessous.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

À partir de cet écran, vous pouvez choisir de démarrer avec une application de logique vierge ou un modèle prédéfini. Si vous sélectionnez un des modèles, vous sont fournis avec des informations supplémentaires. Dans cet exemple, nous utilisons le modèle *lors de la création d’un nouveau fichier dans la boîte de dépôt, copiez-le dans OneDrive* .  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

Si vous choisissez d’utiliser le modèle, sélectionnez simplement le bouton *utiliser ce modèle* . Vous devrez vous connecter à vos comptes en fonction sur les connecteurs utilise le modèle. Ou, si vous avez précédemment établi une connexion avec ces connecteurs, vous pouvez sélectionner continuer comme indiqué ci-dessous.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

Après avoir établi la connexion et *Continuer*la sélection, l’application logique s’ouvre en mode concepteur.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

Dans l’exemple ci-dessus, comme c’est le cas avec de nombreux modèles, certains champs de propriété obligatoire peuvent être rempli dans les connecteurs ; Toutefois, certains peuvent toujours requièrent une valeur avant de pouvoir déployer correctement l’application logique. Si vous essayez de déployer sans entrer certains champs manquants, vous serez averti par un message d’erreur.

Si vous souhaitez revenir à l’afficheur du modèle, sélectionnez le bouton *modèles* dans la barre de navigation supérieure. En passant à l’afficheur du modèle, vous perdez toute progression non enregistrée. Avant de passer au visualiseur de modèle, vous verrez un message d’avertissement vous informant de ce.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>Comment déployer une application de logique créée à partir d’un modèle

Une fois que vous avez chargé votre modèle et effectué les modifications souhaitées, sélectionnez Enregistrer le bouton dans le coin supérieur gauche. Ceci enregistre et publie votre application logique.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

Si vous souhaitez plus d’informations sur comment ajouter des étapes supplémentaires dans un modèle d’application logique existant, ou apporter modifie en règle générale, plus à [créer une application de logique](app-service-logic-create-a-logic-app.md).