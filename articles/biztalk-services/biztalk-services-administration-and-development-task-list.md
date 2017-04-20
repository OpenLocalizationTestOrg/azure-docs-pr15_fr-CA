<properties
    pageTitle="Administration et développement de liste dans les Services BizTalk la tâche | Microsoft Azure"
    description="Les aides pour le déploiement de Services de BizTalk Azure planification et travail."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administration et liste des tâches de développement dans les Services BizTalk  

## <a name="getting-started"></a>Mise en route
Lorsque vous travaillez avec Microsoft Azure BizTalk Services, il existe plusieurs composants de nuage à prendre en compte et locaux. Pour commencer, prenez en compte le flux de processus suivant :  

|Étape|Qui est responsable|Tâche|Liens connexes|
|----|----|----|----|
|1.|Administrateur|Créer l’abonnement Azure de Microsoft à l’aide d’un compte Microsoft ou un compte d’organisation|[Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Administrateur|Créer ou configurer un BizTalk Service.|[Création d’un BizTalk Service à l’aide d’Azure portal classique](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Administrateur|Enregistrement ou déploiement de BizTalk Services de votre société|[Inscription et mise à jour d’un déploiement de Service BizTalk sur le portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Administrateur|S’applique si l’application utilise un Service de l’adaptateur BizTalk pour se connecter à un système de ligne d’activité (LOB) sur site ou utilise une file d’attente ou la rubrique Destination.  Créer le Namespace de Bus de Service Azure. Donner à cet espace de noms, nom de l’émetteur de Bus de Service et les valeurs de clé de l’émetteur de Bus de Service pour le développeur.|[Comment : créer ou modifier un Namespace de Service de Bus de Service](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) et [obtenir le nom de l’émetteur et des valeurs de clé de l’émetteur](biztalk-issuer-name-issuer-key.md)|
|5.|Développeur|Installer le SDK et créer le projet de BizTalk Service dans Visual Studio.|[Installer les Services de BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) et [créer des points de terminaison de messagerie sur Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Développeur|Déployez votre projet à votre BizTalk Service hébergé sur Azure le BizTalk Service.|[Le déploiement et l’actualisation du projet de Services de BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Administrateur|S’applique si vous utilisez l’EDI.  Vous pouvez ajouter des partenaires et créer les accords sur le portail Microsoft Azure BizTalk Services. Lorsque vous créez un contrat, vous pouvez ajouter le pont et/ou transformations créées par le développeur pour les paramètres de l’accord.|[Configuration EDIFACT EDI et AS2 sur le portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Administrateur|Le portail classique Azure, de surveiller la santé de votre BizTalk Service, y compris les mesures de performances.|[Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Administrateur|À l’aide du portail de Services BizTalk Microsoft Azure, gérer les artefacts utilisés par les Services BizTalk et suivre les messages lors de leur traitement par les fichiers de pont.|[L’utilisation du portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Administrateur|Créer un plan de sauvegarde pour sauvegarder le BizTalk Service.|[La continuité d’activité et de reprise après sinistre dans les Services BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Étapes suivantes
[Exemples et didacticiels](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Créez le projet dans Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installer les Services de BizTalk Azure SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Concepts
[Créez le projet dans Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 et EDIFACT messagerie (Business to Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Autres ressources  
[Ajouter une Source, Destination et le pont de messagerie des points de terminaison](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[En savoir plus et de créer des tables des messages et des transformations](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[L’utilisation du Service d’adaptateur BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)
