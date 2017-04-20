<properties
   pageTitle="Installer la Protection du point de terminaison dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre la mise en œuvre de la recommandation du centre de sécurité Azure **Installer Endpoint Protection**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Installer la Protection du point de terminaison dans le centre de sécurité Azure

Centre de sécurité Azure recommande que vous devez configurer un programme contre les logiciels malveillants à vos machines virtuelles Azure (VMs) si contre les logiciels malveillants ne sont pas déjà activé. Cette recommandation s’applique uniquement aux ordinateurs virtuels de Windows.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.

## <a name="implement-the-recommendation"></a>Mettre en oeuvre la recommandation

1. Dans la lame de **recommandations** , sélectionnez **Installer Endpoint Protection**.
![Sélectionnez Installer Endpoint Protection][1]

2. La lame **Installer Endpoint Protection** s’ouvre en affichant une liste d’ordinateurs virtuels sans contre les logiciels malveillants activée. Dans la liste, sélectionnez les ordinateurs virtuels que vous souhaitez installer le logiciel anti-programme malveillant sur, puis cliquez sur **installer sur les ordinateurs virtuels**.
![Sélectionnez les ordinateurs virtuels pour installer des logiciels anti-programmes malveillants sur][2]

3. La lame **Sélectionnez Endpoint Protection** s’ouvre pour vous permettre de sélectionner la solution de logiciel anti-programme malveillant que vous souhaitez utiliser. Dans cet exemple, nous allons sélectionner le **Logiciel anti-programme malveillant de Microsoft**.
![Activez la Protection des postes clients][3]

4. Plus d’informations sur la solution contre les logiciels malveillants s’affiche. Sélectionnez **créer**.
![Créer des solutions de logiciels anti-programmes malveillants][4]

5. Entrez les paramètres de configuration requis sur **l’Ajout d’une Extension** de lame et sélectionnez **OK**. Pour en savoir plus sur les paramètres de configuration, consultez [Configuration des logiciels anti-programmes malveillants personnalisée et par défaut](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Logiciel anti-programme malveillant de Microsoft](../azure-security-antimalware.md) est maintenant active sur les ordinateurs virtuels sélectionnés.

## <a name="see-also"></a>Voir aussi

Cet article vous a montré comment implémentent la recommandation du centre de sécurité « Installation Endpoint Protection. » Pour en savoir plus sur l’activation d’un programme contre les logiciels malveillants dans Azure, consultez les rubriques suivantes :

- [Logiciel anti-programme malveillant de Microsoft pour les Services en nuage et les Machines virtuelles](../azure-security-antimalware.md) --Découvrez comment déployer le logiciel anti-programme malveillant de Microsoft.

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) : Apprenez à configurer des stratégies de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources d’Azure.
- [Surveillance de l’intégrité de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) --Découvrez comment surveiller la santé de vos ressources d’Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Solutions de partenaires de surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher fréquemment posées à propos de l’utilisation du service.
- Billets de [blog sur la sécurité d’azure](http://blogs.msdn.com/b/azuresecurity/) --blog de recherche sur la conformité et la sécurité Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
