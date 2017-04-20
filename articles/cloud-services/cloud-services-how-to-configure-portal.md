<properties 
    pageTitle="Comment faire pour configurer un service en nuage (portail) | Microsoft Azure" 
    description="Apprenez à configurer les services en nuage dans Azure. Apprenez à mettre à jour de la configuration de service cloud et configurer l’accès distant à des instances de rôle. Ces exemples utilisent le portail Azure." 
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

Vous pouvez configurer les paramètres les plus couramment utilisés pour un service en nuage dans Azure portal. Ou, si vous souhaitez mettre à jour vos fichiers de configuration directement, télécharger un fichier de configuration de service pour mettre à jour, puis télécharger le fichier mis à jour et mettre à jour le service en nuage avec les modifications de configuration. Dans les deux cas, les mises à jour de la configuration sont transférées à toutes les instances de rôle.

Vous pouvez également gérer les instances de rôles du service nuage ou de bureau à distance dans les.

Azure peut uniquement garantir la disponibilité de service de 99,95 % pendant les mises à jour de configuration si vous disposez d’au moins deux instances de rôle pour chaque rôle. Qui permet à un ordinateur virtuel traiter les demandes de client alors que l’autre est en cours de mise à jour. Pour plus d’informations, consultez [Les contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Modifier un service cloud

Après avoir ouvert le [portail Azure](https://portal.azure.com/), accédez à votre service cloud. À partir de là, vous gérez de nombreux aspects. 

![Page Paramètres](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Les liens de **tous les paramètres** ou les **paramètres de** la lame de **paramètres** , où vous pouvez modifier les **Propriétés**, modifiez la **Configuration**, gestion des **certificats**, le programme d’installation de **règles d’alerte**et gérer les **utilisateurs** qui ont accès à ce service de cloud seront ouvre.

![Lame de paramètres Azure cloud service](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
>Impossible de modifier le système d’exploitation utilisé pour le service cloud à l’aide du **portail Azure**, vous ne pouvez modifier ce paramètre via le [Azure portal classique](http://manage.windowsazure.com/). Cela est décrite en détail [ici](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## <a name="monitoring"></a>Surveillance

Vous pouvez ajouter des alertes à votre service cloud. Cliquez sur **paramètres** > **Les règles d’alerte** > **Ajouter une alerte**. 

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

À partir d’ici, vous pouvez configurer une alerte. Avec la zone de liste déroulante **Mertic** , vous pouvez configurer une alerte pour les types de données suivants.

- Lecture du disque
- Écriture sur disque
- Réseau dans
- Sortie du réseau
- Pourcentage de l’UC 

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurer l’analyse à partir d’une mosaïque de métriques

Au lieu d’utiliser les **paramètres** > de**Règles d’alerte**, cliquez sur une des mosaïques métriques dans la section **analyse** de la lame de **service Cloud** .

![Analyse des services cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

À partir de là, vous pouvez personnaliser le graphique utilisé avec la mosaïque, ou ajoutez une règle d’alerte.


## <a name="reboot-reimage-or-remote-desktop"></a>Redémarrage, créer une nouvelle image ou Bureau à distance

À ce stade vous ne peut pas configurer le Bureau à distance à l’aide du **portail Azure**. Toutefois, vous pouvez configurer il l' [Azure portal classique](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), ou par l’intermédiaire de [Visual Studio](../vs-azure-tools-remote-desktop-roles.md). 

Cliquez d’abord sur l’instance de service cloud.

![Instance de Service cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)

À partir de la blade qu’ouvre uou permettant de lancer une connexion Bureau à distance, de redémarrer à distance l’instance ou créer une nouvelle image à distance (démarre avec une nouvelle image) l’instance.

![Boutons de Instance de Service cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## <a name="reconfigure-your-cscfg"></a>Reconfigurez votre .cscfg

Il est possible que vous deviez reconfigurer vous cloud service via le fichier de [configuration de service (cscfg)](cloud-services-model-and-package.md#cscfg) . Vous devez d’abord télécharger le fichier .cscfg, modifiez-le, puis téléchargez-le.

1. Cliquez sur l’icône de **paramètres** ou le lien de **tous les paramètres** pour ouvrir la lame de **paramètres** .

    ![Page Paramètres](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. Cliquez sur l’élément de **Configuration** .

    ![Lame de configuration](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. Cliquez sur le bouton **Télécharger** .

    ![Télécharger](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. Une fois que vous mettez à jour le fichier de configuration de service, télécharger et appliquer les mises à jour de la configuration :

    ![Téléchargement](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png) 
    
5. Sélectionnez le fichier .cscfg, puis cliquez sur **OK**.

            
## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurer un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* [Gérer votre service cloud](cloud-services-how-to-manage-portal.md).
* Configurer des [certificats ssl](cloud-services-configure-ssl-certificate-portal.md).