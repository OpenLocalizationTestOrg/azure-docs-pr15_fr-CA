<properties
   pageTitle="Ajouter un pare-feu d’application web dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter les recommandations du centre de sécurité Azure **Ajouter un pare-feu d’application web** et la **protection d’application Finalize**."
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

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Ajouter un pare-feu d’application web dans le centre de sécurité Azure

Le centre de sécurité Azure peut recommander que vous ajoutez un pare-feu d’application web (WAF) d’un partenaire Microsoft pour assurer la sécurité de vos applications web. Ce document vous guide à travers un exemple de cette procédure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **l’application web à l’aide du pare-feu pour applications web sécurisé**.
![Secure web Application][1]

2. Dans la lame de **sécuriser vos applications web à l’aide de pare-feu d’application web** , sélectionnez une application web. La lame de **Ajouter un pare-feu d’Application Web** s’ouvre.
![Ajouter un pare-feu pour applications web][2]
3. Vous pouvez choisir d’utiliser un pare-feu d’application web existant s’il est disponible, ou vous pouvez créer un nouveau. Dans cet exemple il n’existe aucun WAFs existants afin que nous allons créer un nouveau WAF.

4. Pour créer un nouveau WAF, sélectionnez une solution dans la liste des partenaires intégrés. Dans cet exemple, nous allons sélectionner **Barracuda Web Application pare-feu**.
5. La lame **Barracuda Web Application pare-feu** s’ouvre à vous fournir des informations sur les partenaires solution. Sélectionnez **créer** dans la blade d’informations.
![Lame d’informations de pare-feu][3]

6. La lame de **Nouveau pare-feu d’Application Web** s’ouvre, dans laquelle vous pouvez effectuer les étapes de **Configuration de machine virtuelle** et fournissent des **Informations de WAF**. Sélectionnez **Configuration de la machine virtuelle**.

7. Dans la **Configuration de la machine virtuelle** de lame vous permet d’entrer les informations requises pour faire tourner l’ordinateur virtuel qui exécutera le WAF.
![Configuration de machine virtuelle][4]
8. Revenir à la lame de **Nouveau pare-feu d’Application Web** et sélectionnez **Les informations de WAF**. La lame **WAF d’informations** vous permet de configurer la WAF lui-même. Étape 7 vous permet de configurer l’ordinateur virtuel sur lequel le WAF s’exécutera et étape 8 vous permet de mettre en service le WAF lui-même.

## <a name="finalize-application-protection"></a>Finalisation de la protection d’application

1. Revenir à la lame de **recommandations** . Une nouvelle entrée a été générée une fois que vous avez créé le WAF, appelée **protection des applications Finalize**. Cette entrée permet de savoir que vous devez effectuer le processus de réellement mettre en place le WAF au sein du réseau virtuel Azure afin qu’il peut protéger l’application.
![Finalisation de la protection d’application][5]

2. Sélectionnez **protection d’application Finalize**. Une nouvelle lame s’ouvre. Vous pouvez voir qu’il existe une application web qui doit avoir son trafic redirigé.
3. Sélectionnez l’application web. Une blade s’affiche qui vous donne les étapes pour la finalisation de l’installation de pare-feu d’application web. Effectuez les étapes et sélectionnez **restreindre trafic**. Centre de sécurité effectuera ensuite la connexion à distance pour vous.
![Restreindre le trafic][6]

> [AZURE.NOTE] Vous pouvez protéger plusieurs applications web dans le centre de sécurité en ajoutant ces applications à vos déploiements WAF existants. Les appliances WAF (créés avec le modèle de déploiement du Gestionnaire de ressources) doivent être déployés sur un réseau virtuel distinct. Les appliances WAF (créés avec le modèle de déploiement classique) sont limités à l’utilisation d’un groupe de sécurité du réseau. Cette prise en charge sera étendue à l’avenir à un déploiement personnalisé d’un appareil de WAF (classique). Pour en savoir plus sur [standard et les modèles de déploiement Gestionnaire de ressources](../azure-classic-rm.md) pour les ressources d’Azure.

Les journaux de cette WAF sont désormais totalement intégrées. Centre de sécurité peut démarrer automatiquement collecte et analyse des journaux de sorte qu’il de surface des alertes de sécurité importantes pour vous.

## <a name="see-also"></a>Voir aussi

Ce document vous a montré comment implémentent la recommandation du centre de sécurité « Ajouter une application web ». Pour plus d’informations sur la configuration d’un pare-feu d’application web, consultez les rubriques suivantes :

- [Configuration d’un pare-feu pour applications Web (WAF) pour un environnement de Service d’application](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) --blog de recherche sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
