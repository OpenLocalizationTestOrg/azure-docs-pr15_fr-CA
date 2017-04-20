<properties 
   pageTitle="Engagement de Mobile Azure Guide - Analytique de dépannage" 
   description="Résolution des problèmes d’Analytique, de surveillance, de Segmentation et de tableau de bord dans Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guide de dépannage pour les problèmes Analytique, de surveillance, de Segmentation et de tableau de bord

Les éléments suivants sont des problèmes que vous pouvez rencontrer avec comment Azure Mobile Engagement rassemble des informations à propos des applications, des périphériques et des utilisateurs.

## <a name="missingdelayed-information"></a>Informations manquantes/différées

### <a name="issue"></a>Problème
- Informations sont retardées dans figurant dans le tableau de bord, Segmentation ou Analytique.
- Il manque des informations à partir de l’analyse.
- Il manque des informations à partir du tableau de bord, Segmentation ou Analytique.
- Limites de segmentation atteinte.

### <a name="causes"></a>Causes

- Vous pouvez utiliser l’API Analytique, API du moniteur, et les API de Segments pour voir si toutes les données vous sont manquants à partir de l’interface utilisateur est visible à travers les API.
- Si l’Engagement de Mobile Azure SDK n’est pas correctement intégré dans votre application vous sera en mesure de voir les informations dans l’Analytique, Segmentation, analyse ou tableaux de bord.
- Segments ne peut pas être modifié une fois qu’ils sont créés, segments ne peuvent être « clonés » (copié) ou la « destruction » (supprimés). Segments peuvent contenir uniquement les 10 critères.
- La meilleure méthode pour tester les informations manquantes de la surveillance est à un dispositif de test de l’installation, désinstaller et/ou réinstaller l’application sur le périphérique de test.
- Informations sont actualisées toutes les 24 heures pour l’Analytique, Segmentation ou tableaux de bord.
- Informations de nouveaux segments ne peuvent pas s’afficher jusqu'à 24 heures après leur création, même si le segment est basé sur les informations précédentes.
- Filtrage des données d’analytique dans l’interface utilisateur affiche tous les exemples de ce type, quelle que soit la version de votre application (par exemple, « se bloque » filtrées par nom s’affichera à partir de la version 1 et version 2 de votre application).
- La période d’Analytique est basée sur la date à partir des paramètres de périphérique de l’utilisateur, un utilisateur dont le téléphone a la date défini de manière incorrecte peut s’affiche pas dans la période de mauvais temps.
- Exécute un push d’aucun côté serveur que les données sont enregistrées lorsque vous utilisez le bouton « tester », seulement les données sont enregistrées pour les campagnes de transmission réel.

## <a name="cant-locate-items-in-ui"></a>Impossible de trouver des éléments dans l’interface utilisateur

### <a name="issue"></a>Problème
- Ne peut pas créer des segments en fonction de certaines intégré ou critères de balise info d’application personnalisée.
- Impossible de trouver certaines intégré ou de critères dans Analytique, de surveillance ou de tableaux de bord de balise info d’application personnalisée.
- Ne peut pas interpréter les données d’Analytique, analyse, Segmentation ou tableaux de bord.

### <a name="causes"></a>Causes

- Certains éléments intégrés et les balises app info sont uniquement disponibles en tant que critères de transmission mais ne peuvent être ajouté à un segment ou à visible d’Analytique, de surveillance ou du tableau de bord. 
- Pour les éléments intégrés et les balises app info qui ne peut pas être ajoutés à un segment, vous devez à la liste des paramètres de critères dans chaque campagne marketing de ciblage pour effectuer la même fonction que le ciblage basé sur un segment.
- Afficher les menus contextuels dans les sections Analytique, de surveillance, de Segmentation et de tableaux de bord de l’interface utilisateur de Azure Mobile Engagement pour obtenir de l’aide et aux informations.

## <a name="crash-troubleshooting"></a>Bloquer la résolution des problèmes

### <a name="issue"></a>Problème
- Application se bloque dans Analytique, de surveillance ou de tableau de bord.

### <a name="causes"></a>Causes

- Pour résoudre les problèmes de Application se bloque dans Analytique, surveillance, tableau de bord ou vérifiez les notes de publication pour les problèmes connus avec les versions précédentes du Kit de développement.
- Pour poursuivre le dépannage application tombe en panne effectuer un événement à partir d’un dispositif de test avec votre application installée et de la recherche de votre ID de périphérique dans la section « Événements de – moniteur » de l’interface utilisateur de Azure Mobile Engagement. Exécutez ensuite l’événement qui est à l’origine de votre application de se bloquer et consulter des informations supplémentaires dans la section « Blocage de – moniteur » de l’interface utilisateur de Azure Mobile Engagement. 

 
