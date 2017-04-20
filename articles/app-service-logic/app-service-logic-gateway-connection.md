<properties
   pageTitle="Logique d’applications local connexion de passerelle de données | Microsoft Azure"
   description="Informations sur la création d’une connexion à la passerelle de données local à partir d’une application de logique."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Se connecter à la passerelle de données local pour les applications de logique

Connecteurs d’applications de logique de prise en charge vous permettent de vous permet de configurer votre connexion pour accéder aux données de locaux via la passerelle de données sur site.  La procédure suivante vous guidera tout au long de l’installation et la configuration de la passerelle de données local pour travailler avec une application de logique.

## <a name="prerequisites"></a>Conditions préalables

* Doit être un volume de travail ou adresse de courriel dans Azure pour associer la passerelle de données local avec votre compte Azure Active Directory en fonction des cours
    * Si vous utilisez un Account Microsoft (par exemple, @outlook.com, @live.com) vous pouvez utiliser votre compte Azure pour créer un travail ou l’adresse e-mail d’établissement en [suivant les étapes décrites ici](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Il existe actuellement une limitation qui installation de passerelle ne se terminera que lorsque vous utilisez un compte qui a été inscrit avec BI de puissance sur site.  En attendant, enregistrez tout compte avec « Alimentation BI libre » pour terminer l’installation avec succès.

* Doit avoir le local de données passerelle [installé sur un ordinateur local](app-service-logic-gateway-install.md).
* Passerelle ne doit pas avoir été émanant d’une autre passerelle de données Azure sur site ([demander qui se produit lors de la création de l’étape 2 ci-dessous](#2-create-an-azure-on-premises-data-gateway-resource)), une installation peut uniquement être associée à une ressource d’une passerelle.

## <a name="installing-and-configuring-the-connection"></a>L’installation et la configuration de la connexion

### <a name="1-install-the-on-premises-data-gateway"></a>1. Installez la passerelle de données sur site

Vous trouverez des informations sur l’installation de la passerelle de données locaux [dans cet article](app-service-logic-gateway-install.md).  La passerelle doit être installée sur un ordinateur local avant de pouvoir continuer avec le reste de la procédure.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. création d’une ressource de passerelle de données Azure sur site

Une fois installé, vous devez associer votre abonnement Azure à la passerelle de données sur site.

1. Connexion vers Azure utilisant le même travail ou l’adresse e-mail de l’établissement qui a été utilisé lors de l’installation de la passerelle
1. Cliquez sur le bouton de **Nouveau** la ressource
1. Recherchez et sélectionnez la **passerelle de données sur site**
1. Toutes les informations pour associer la passerelle à votre compte - notamment en sélectionnant le **Nom de l’Installation**

    ![Connexion de passerelle de données sur site][1]
1. Cliquez sur le bouton **créer** pour créer la ressource

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. création d’une connexion d’application logique dans le Concepteur

Maintenant que votre abonnement Azure est associé à une instance de la passerelle de données local, vous pouvez créer une connexion à partir d’une application de logique.

1. Ouvrez une application logique et choisissez un connecteur qui prend en charge la connectivité locale (à ce jour, il s’agit de SQL Server)
1. Sélectionnez la case à cocher pour que la **connexion via une passerelle de données sur site**

    ![Création de passerelle de Concepteur de l’application logique][2]
1. Sélectionnez la **passerelle** pour se connecter à et complétez les autres informations de connexion requises
1. Cliquez sur **créer** pour créer la connexion

La connexion doit maintenant être configurée correctement pour une utilisation dans votre logique d’application.  

## <a name="next-steps"></a>Étapes suivantes
- [Commune des exemples et des scénarios pour les applications de logique](app-service-logic-examples-and-scenarios.md)
- [Fonctionnalités d’intégration entreprise](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG