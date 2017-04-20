<properties
   pageTitle="Appliquer les mises à jour du système dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter les recommandations Azure le centre de sécurité, **appliquer des mises à jour du système** et **redémarrer après les mises à jour du système**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-system-updates-in-azure-security-center"></a>Appliquer les mises à jour du système dans le centre de sécurité Azure

Le centre de sécurité Azure surveille quotidiennement les ordinateurs Windows et Linux virtuelle (VM) manque pas des mises à jour du système d’exploitation. Centre de sécurité récupère une liste de mises à jour critiques et de sécurité disponibles à partir de Windows Update ou de Windows Server Update Services (WSUS), selon laquelle le service est configuré sur un ordinateur virtuel de Windows.  Centre de sécurité vérifie également les dernières mises à jour dans les systèmes Linux. Si votre ordinateur virtuel ne dispose pas d’une mise à jour du système, le centre de sécurité est recommandé que vous appliquez les mises à jour du système

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **appliquer les mises à jour**.
![Appliquer les mises à jour du système][1]

2. La blade **d’appliquer les mises à jour** s’ouvre en affichant une liste d’ordinateurs virtuels mises à jour du système. Sélectionnez un ordinateur virtuel.
![Sélectionnez un ordinateur virtuel][2]

3. Une blade s’ouvre en affichant une liste de mises à jour manquantes pour cet ordinateur virtuel. Sélectionnez une mise à jour du système. Dans cet exemple, sélectionnez KB3156016.
![Mises à jour de sécurité manquantes][3]

4. Suivez les étapes de la lame de **Mise à jour de sécurité** pour appliquer la mise à jour manquante.
![Mise à jour de sécurité][4]

## <a name="reboot-after-system-updates"></a>Redémarrage après les mises à jour du système

5. Revenir à la lame de **recommandations** . Une nouvelle entrée a été générée après avoir appliqué les mises à jour du système, appelées **redémarrage après les mises à jour du système**. Cette entrée permet de savoir que vous devez redémarrer la machine virtuelle pour terminer le processus d’application des mises à jour du système.
![Redémarrage après les mises à jour du système][5]

6. Sélectionnez **redémarrer après les mises à jour du système**. **Un redémarrage est en attente pour terminer les mises à jour système** lame, affichant une liste d’ordinateurs virtuels que vous devez redémarrer pour terminer le processus de mises à jour système appliquer s’ouvre.
![Redémarrage en attente][6]

Redémarrez la machine virtuelle à partir d’Azure pour terminer le processus.

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) --blog de recherche sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
