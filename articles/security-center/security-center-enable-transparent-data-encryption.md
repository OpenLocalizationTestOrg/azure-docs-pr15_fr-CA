<properties
   pageTitle="Activer le chiffrement de données Transparent dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment implémenter la recommandation du centre de sécurité Azure **Activer le chiffrement de données Transparent**."
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

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Activer le chiffrement de données Transparent dans le centre de sécurité Azure

Le centre de sécurité Azure recommandera activer le chiffrement Transparent des données (TDE) sur les bases de données SQL si TDE n’est pas déjà activé. TDE protège vos données et vous permet de répondre aux exigences de conformité grâce au cryptage de votre base de données, les sauvegardes associés et les fichiers journaux des transactions au repos, sans nécessiter des modifications à votre application. Pour en savoir plus, consultez [Chiffrement Transparent des données avec la base de données de SQL Azure](https://msdn.microsoft.com/library/dn948096).

Cette recommandation s’applique au service Azure SQL uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **Activer le chiffrement de données Transparent**.
![Activer le chiffrement de données Transparent][1]

2. La blade **d’Activer le chiffrement Transparent des données sur les bases de données SQL** s’ouvre. Sélectionnez une base de données SQL pour activer TDE sur.
![Sélectionnez la base de données SQL pour activer TDE sur][2]
3. La lame de **chiffrement de données Transparent** , sélectionnez **ON** sous chiffrement des données et sélectionnez **Enregistrer** dans le ruban supérieur de la lame.
![Activer TDE][3]

  Une fois TDE est activé sur la base de données SQL sélectionnée, l' **état de chiffrement** devient **Encrypted**.    

  ![État de chiffrement][4]

## <a name="see-also"></a>Voir aussi

Cet article vous a expliqué comment mettre en œuvre de la recommandation du centre de sécurité « Activer le chiffrement de données Transparent ». Pour en savoir plus sur SQL TDE, consultez les rubriques suivantes :

- [Chiffrement de données transparent avec la base de données SQL Azure](https://msdn.microsoft.com/library/dn948096)
- [Mise en route avec le chiffrement de données Transparent (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- [Blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) : obtenir les informations et les dernières informations de sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
