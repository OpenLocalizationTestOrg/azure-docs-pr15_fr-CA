<properties 
    pageTitle="Vue d’ensemble des comptes de l’intégration et le module d’intégration entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Apprenez tout sur les comptes de l’intégration, le Pack d’intégration entreprise et les applications de la logique" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-integration-accounts"></a>Vue d’ensemble des comptes de l’intégration

## <a name="what-is-an-integration-account"></a>Ce qu’est un compte d’intégration ?
Un compte d’intégration est un compte Azure qui permet aux applications d’intégration d’entreprise Gérer les artefacts, y compris des schémas, des cartes, des certificats, des partenaires et des accords. Vous devez utiliser un compte d’intégration afin d’accéder à un schéma, un plan ou un certificat, par exemple n’importe quelle application d’intégration que vous créez.

## <a name="create-an-integration-account"></a>Créer un compte d’intégration 
1. Sélectionnez **Parcourir**   
![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)  
2. Entrez **l’intégration** dans la zone de filtre de recherche et sélectionnez les **Comptes de l’intégration** à partir de la liste des résultats     
 ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)  
3. Sélectionnez le bouton *Ajouter* dans le menu en haut de la page      
![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)  
4. Entrez le **nom**, sélectionnez l' **abonnement** que vous souhaitez utiliser, soit créer un nouveau **groupe de ressources** ou sélectionnez un groupe de ressources existant, sélectionnez un **emplacement** dans lequel votre compte d’intégration sera hébergée, sélectionnez un **niveau de tarification**, puis cliquez sur le bouton **créer** .   

  À ce stade le compte d’intégration sera déployé dans l’emplacement sélectionné. Il doit se terminer dans une minute.    
![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)  
5. Actualisez la page. Vous verrez votre nouveau compte d’intégration répertorié. Félicitations !  
![](./media/app-service-logic-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Comment lier un compte de l’intégration à une application de logique
Afin que vos applications de logique accéder à des cartes, des schémas, des accords et autres artefacts qui sont trouvent dans votre compte d’intégration, vous devez d’abord lier le compte de l’intégration à votre application de logique.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>Voici la procédure pour lier un compte de l’intégration à une application de logique 

#### <a name="prerequisites"></a>Conditions préalables
- Un compte d’intégration
- Une application de logique

>[AZURE.NOTE]Assurez-vous que votre compte d’intégration et d’une application de logique sont dans **même emplacement Azure** avant de commencer

1. Sélectionnez les **paramètres de** lien dans le menu de votre application logique  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)   
2. Sélectionnez l’élément de **Compte de l’intégration** de la lame de paramètres  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)   
3. Sélectionnez le compte d’intégration que vous souhaitez lier à votre logique d’application à partir de la, **Sélectionnez un compte d’intégration** déplacer la zone de liste  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)   
4. Enregistrez votre travail.  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)   
5. Vous verrez une notification indiquant que votre compte d’intégration a été lié à votre application logique et que tous les artefacts dans votre compte d’intégration sont désormais disponibles pour votre application logique.  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)   

Maintenant que votre compte d’intégration est lié à votre application logique, vous pouvez accéder à votre application de logique et d’utiliser des connecteurs B2B telles que la Validation XML, le fichier plat coder/décoder ou la transformation pour créer des applications avec les fonctionnalités B2B.  
    
## <a name="how-to-delete-an-integration-account"></a>Comment faire pour supprimer un compte d’intégration ?
1. Sélectionnez **Parcourir**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Entrez **l’intégration** dans la zone de filtre de recherche et sélectionnez les **Comptes de l’intégration** à partir de la liste des résultats     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Sélectionnez le **compte d’intégration** que vous souhaitez supprimer  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Cliquez sur le lien **Supprimer** situé dans le menu   
![](./media/app-service-logic-enterprise-integration-accounts/delete.png)  
5. Confirmer votre choix.    

## <a name="how-to-move-an-integration-account"></a>Comment faire pour déplacer un compte d’intégration ?
Vous pouvez facilement déplacer un compte d’intégration à un nouvel abonnement et un nouveau groupe de ressources. Si vous devez déplacer votre compte d’intégration, procédez comme suit :

>[AZURE.IMPORTANT] Vous devez mettre à jour tous les scripts pour utiliser le nouveaux ID de ressource, une fois que vous déplacez un compte d’intégration.

1. Sélectionnez **Parcourir**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Entrez **l’intégration** dans la zone de filtre de recherche et sélectionnez les **Comptes de l’intégration** à partir de la liste des résultats     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Sélectionnez le **compte d’intégration** que vous souhaitez supprimer  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Cliquez sur le lien qui se trouve dans le menu **déplacer**   
![](./media/app-service-logic-enterprise-integration-accounts/move.png)  
5. Confirmer votre choix.    

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les accords] (./app-service-logic-enterprise-integration-agreements.md "Obtenir des informations sur les accords d’intégration entreprise")  


 