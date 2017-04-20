<properties
   pageTitle="Configuration de l’authentification avec Amazon Web Services | Microsoft Azure"
   description="Cet article décrit comment créer et valider des informations d’identification AWS de procédures opérationnelles dans Automation Azure Gestion des ressources de l’AWS."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="authentification de l’AWS, configurer aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Authentifier les procédures opérationnelles avec Amazon Web Services
Automatisation des tâches courantes avec des ressources dans Amazon Web Services (AWS) peut être accomplie avec les procédures opérationnelles d’Automation dans Azure.  Vous pouvez automatiser de nombreuses tâches de AWS à l’aide de procédures d’automatisation opérationnelles tout comme vous pouvez le faire avec les ressources dans Azure.  Tout ce qui est requis sont deux choses :

* Un abonnement AWS et un ensemble d’informations d’identification.  Spécifiquement votre clé d’accès AWS et clé secrète.  Pour plus d’informations, veuillez consulter l’article [En utilisant les informations d’identification de l’AWS](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Un abonnement Azure et le compte de l’Automation.  Pour plus d’informations sur la configuration d’un compte Azure Automation, veuillez consulter l’article [Configurer l’exécuter en tant que compte Azure](../automation/automation-sec-configure-azure-runas-account.md).  

Pour authentifier avec AWS, vous devez spécifier un ensemble d’informations d’identification de l’AWS pour authentifier les procédures opérationnelles en cours d’exécution à partir de l’automatisation d’Azure. Si vous avez déjà créé un compte d’Automation et que vous souhaitez l’utiliser pour s’authentifier avec AWS, vous pouvez suivre les étapes décrites dans la section suivante.  Si vous souhaitez dédié un compte pour les ressources de procédures opérationnelles ciblé AWS, vous devez tout d’abord créer un nouveau [compte d’Automation exécuter en tant que](../automation/automation-sec-configure-azure-runas-account.md) (ignorer l’option de création d’un principal de service) et puis suivez les étapes ci-dessous.

## <a name="configure-automation-account"></a>Configurer le compte de l’Automation
Pour l’automatisation d’Azure communiquer avec l’AWS, vous devez d’abord récupérer vos informations d’identification de l’AWS et de les stocker en tant qu’actifs dans Azure Automation.  Effectuez les opérations suivantes, décrites dans le document AWS [Gestion des touches d’accès rapide pour votre compte AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) pour créer une touche d’accès rapide et de copier les **ID de clé d’accès** et d’une **Touche d’accès rapide Secret** (éventuellement télécharger votre fichier de clé pour la stocker dans un endroit sûr).

Une fois que vous avez créé et copié vos clés de sécurité AWS, vous devez créer une ressource d’informations d’identification avec un compte Azure Automation pour les stocker en toute sécurité et de les référencer avec les procédures opérationnelles.  Suivez les étapes de la section **pour créer de nouvelles informations d’identification** de l’article de [ressources d’informations d’identification dans Azure Automation](../automation/automation-certificates.md/###To create a new credential with the Azure portal) et entrez les informations suivantes :

1. Dans la zone **nom** , entrez **AWScred** ou une valeur appropriée suivant vos normes d’attribution de noms.  
2. Dans la zone **nom d’utilisateur** , tapez votre **ID d’accès** et votre **Clé d’accès Secret** dans la zone **mot de passe** et **Confirmer le mot de passe** .   

## <a name="next-steps"></a>Étapes suivantes

- Reivew l’article de la solution de [déploiement d’automatisation d’un ordinateur virtuel dans les Services Web d’Amazon](../automation/automation-scenario-aws-deployment.md) pour apprendre à créer des procédures opérationnelles pour automatiser des tâches dans AWS.
