<properties 
    pageTitle="Comment faire pour configurer un service en nuage (portail classique) | Microsoft Azure" 
    description="Apprenez à configurer les services en nuage dans Azure. Apprenez à mettre à jour de la configuration de service cloud et configurer l’accès distant à des instances de rôle." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-configure-cloud-services"></a>Comment faire pour configurer les Services en nuage

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-configure-portal.md)
- [Azure portal classique](cloud-services-how-to-configure.md)

Vous pouvez configurer les paramètres les plus couramment utilisés pour un service en nuage dans Azure portal classique. Ou, si vous souhaitez mettre à jour vos fichiers de configuration directement, télécharger un fichier de configuration de service pour mettre à jour, puis télécharger le fichier mis à jour et mettre à jour le service en nuage avec les modifications de configuration. Dans les deux cas, les mises à jour de la configuration sont transférées à toutes les instances de rôle.

Le portail classique Azure vous permet également de pour [permettre une connexion Bureau à distance pour un rôle dans les Services en nuage Azure](cloud-services-role-enable-remote-desktop.md)

Azure peut uniquement garantir la disponibilité de service de 99,95 % pendant les mises à jour de configuration si vous disposez d’au moins deux instances de rôle pour chaque rôle. Qui permet à un ordinateur virtuel traiter les demandes de client alors que l’autre est en cours de mise à jour. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Modifier un service cloud

1. Dans [Azure portal classique](http://manage.windowsazure.com/), cliquez sur **Services dans le nuage**, cliquez sur le nom du service cloud et puis cliquez sur **configurer**.

    ![Page de configuration](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Sur la page de **configuration** , vous pouvez configurer l’analyse, mettre à jour les paramètres de rôle et choisissez le système d’exploitation invité et la famille pour les instances de rôles. 

2. Dans le **contrôle**, le niveau de surveillance sur commentaires ou minimales et configurer les chaînes de connexion de diagnostic qui sont nécessaires pour une analyse détaillée.

3. Pour les rôles du service (regroupés par rôle), vous pouvez mettre à jour les paramètres suivants :
    
    >**Paramètres**  
    >Modifier les valeurs de divers paramètres de configuration qui sont spécifiés dans les éléments *ConfigurationSettings* le service (.cscfg) du fichier de configuration.
    >
    >**Certificats**  
    >Modifier l’empreinte numérique du certificat qui est utilisé pour un rôle dans le cryptage SSL. Pour modifier un certificat, vous devez d’abord télécharger le nouveau certificat (sur la page de **certificats** ). Mettre à jour l’empreinte numérique dans la chaîne de certificat affichée dans les paramètres de rôle.

4. Dans le **système d’exploitation**, vous pouvez modifier la famille du système d’exploitation ou une version pour les instances de rôle ou choisissez **automatique** pour activer les mises à jour automatiques de la version du système d’exploitation actuel. Les paramètres de système d’exploitation s’appliquent pour les rôles web et worker, mais n’affectent pas les ordinateurs virtuels.

    Pendant le déploiement, la dernière version du système d’exploitation est installée sur toutes les instances de rôle, et les systèmes d’exploitation sont mis à jour automatiquement par défaut. 
    
    Si vous avez besoin pour votre service cloud à s’exécuter sur une version de système d’exploitation différent des exigences de compatibilité dans votre code, vous pouvez choisir une famille de système d’exploitation et la version. Lorsque vous choisissez une version spécifique du système d’exploitation, les mises à jour automatique du système d’exploitation pour le service en nuage sont suspendus. Vous devez vous assurer de que recevoir les mises à jour les systèmes d’exploitation.
    
    Si vous résolvez tous les problèmes de compatibilité par vos applications avec la dernière version du système d’exploitation, vous pouvez activer les mises à jour d’automatique du système d’exploitation en définissant la version du système d’exploitation sur **automatique**. 
    
    ![Paramètres du système d’exploitation](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Pour enregistrer vos paramètres de configuration et les dirige vers les instances de rôle, cliquez sur **Enregistrer**. (Cliquez sur **Annuler** pour annuler les modifications.) **Enregistrer** et **Annuler** sont ajoutés à la barre de commandes après avoir modifié un paramètre.

## <a name="update-a-cloud-service-configuration-file"></a>Mise à jour d’un fichier de configuration de service cloud

1. Télécharger un fichier de configuration de service cloud (.cscfg) avec la configuration actuelle. Sur la page **configurer** le service en nuage, cliquez sur **Télécharger**. Puis cliquez sur **Enregistrer**ou sur **Enregistrer sous** pour enregistrer le fichier.

2. Une fois que vous mettez à jour le fichier de configuration de service, télécharger et appliquer les mises à jour de la configuration :

    1. Sur la page de **configuration** , cliquez sur **Télécharger**.
    
        ![Télécharger la Configuration](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. Dans le **fichier de Configuration**, utilisez **Parcourir** pour sélectionner le fichier .cscfg mis à jour.
    
    3. Si votre service cloud contient les rôles qui ont une seule instance, activez la case à cocher **Appliquer la configuration même si un ou plusieurs rôles contiennent une seule instance** pour activer les mises à jour de la configuration des rôles continuer.
    
        Sauf si vous définissez au moins deux instances de chaque rôle, Azure ne peut pas garantir au moins 99,95 % la disponibilité de votre service cloud lors de mises à jour de configuration de service. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).
    
    4. Cliquez sur **OK** (coche). 


## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy.md).
* Configurer un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* [Gérer votre service cloud](cloud-services-how-to-manage.md).
* [Activer la connexion Bureau à distance pour un rôle dans les Services en nuage Azure](cloud-services-role-enable-remote-desktop.md)
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate.md).
