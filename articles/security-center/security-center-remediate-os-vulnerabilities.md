<properties
   pageTitle="Correction des vulnérabilités du système d’exploitation dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du centre de sécurité Azure **OS de remédier aux vulnérabilités**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Correction des vulnérabilités du système d’exploitation dans le centre de sécurité Azure

Azure le centre de sécurité analyse quotidienne de votre système d’exploitation de l’ordinateur virtuel (VM) (OS) pour les configurations qui peuvent rendre l’ordinateur virtuel plus vulnérable aux attaques et recommande des modifications de configuration pour résoudre ces problèmes. Pour plus d’informations sur les configurations spécifiques en cours d’analyse, consultez la [liste des règles de configuration recommandée](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centre de sécurité vous recommande de résoudre les vulnérabilités lors de la configuration du système d’exploitation de votre ordinateur virtuel ne correspond pas à des règles de la configuration recommandée.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **les vulnérabilités de mettre à jour le système d’exploitation**.
![Correction des vulnérabilités du système d’exploitation][1]

    La lame de **vulnérabilités de mettre à jour le système d’exploitation** s’ouvre et répertorie vos ordinateurs virtuels avec les configurations du système d’exploitation qui ne correspondent pas aux règles de la configuration recommandée.  Pour chaque machine virtuelle, la lame identifie :

   - **Échec des règles** : le nombre de règles de configuration de système d’exploitation de l’ordinateur virtuel a échoué.
   - **Durée de l’analyse du dernier** --la date et l’heure que le centre de sécurité analysé dernière configuration de système d’exploitation de la machine virtuelle.
   - **État** : l’état actuel de la vulnérabilité :

        - Ouvrir : La vulnérabilité n'a pas encore été satisfaite
        - En cours : Cette vulnérabilité est actuellement appliquée, aucune action n’est requise par vous
        - Résolu : La vulnérabilité a déjà été traitée (lorsque le problème a été résolu, l’entrée est désactivée)
  - **Gravité** : toutes les vulnérabilités sont définies à un niveau de gravité faible, ce qui signifie une vulnérabilité doit être traitée, mais ne nécessite pas une attention immédiate.

Sélectionnez un ordinateur virtuel. Une lame pour cet ordinateur virtuel s’ouvre et affiche les règles qui ont échoué.
   ![Règles de configuration qui ont échoué][2]

Sélectionnez une règle. Dans cet exemple, permet de sélectionner le **mot de passe doit respecter des exigences de complexité**. Une blade s’affiche décrivant la règle en échec et l’impact. Examinez les détails et prendre en compte les configurations de système d’exploitation comment seront appliquées.
  ![Description de la règle en échec][3]

  Centre de sécurité utilise l’énumération de Configuration commun (CCE) pour affecter des identificateurs uniques pour les règles de configuration. Les informations suivantes sont fournies sur cette blade :

  - NOM : Nom de la règle
  - GRAVITÉ : Valeur de gravité CCE critique, important ou avertissement
  - CCIED--CCE identificateur de la règle
  - DESCRIPTION : Description de la règle
  - VULNÉRABILITÉ d’explication vulnérabilité ou des risques si la règle n’est pas appliquée.
  - IMPACT : Impact sur l’entreprise lorsque la règle est appliquée.
  - : Attendu valeur attendue lorsque le centre de sécurité analyse la configuration de votre système d’exploitation de la machine virtuelle par rapport à la règle
  - -RÈGLE règle opération utilisée par le centre de sécurité lors de la configuration de votre système d’exploitation de la machine virtuelle par rapport à la règle d’analyse
  - VALEUR réelle : Valeur retournée après l’analyse de la configuration de votre système d’exploitation de la machine virtuelle par rapport à la règle
  - RÉSULTAT de l’évaluation :-résultat d’analyse : passez, échec


## <a name="see-also"></a>Voir aussi

Cet article vous a montré comment implémentent la recommandation du centre de sécurité « Correction du système d’exploitation des vulnérabilités. » Vous pouvez consulter l’ensemble des règles de configuration [ici](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centre de sécurité utilise la CCE (énumération de Configuration commun) pour affecter des identificateurs uniques pour les règles de configuration. Visitez le site [CCE](http://cce.mitre.org) pour plus d’informations.

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) --blog de recherche sur la conformité et la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
