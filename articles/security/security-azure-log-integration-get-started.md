<properties
   pageTitle="Mise en route de l’intégration du journal d’Azure | Microsoft Azure"
   description="Découvrez comment installer le service d’intégration journal Azure et d’intégrer des journaux de stockage Azure, les journaux d’Audit Azure et les alertes du centre de sécurité Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Mise en route de l’intégration du journal d’Azure (aperçu)

Intégration du journal d’Azure vous permet d’intégrer des journaux bruts provenant de vos ressources d’Azure dans vos systèmes d’informations de sécurité et de gestion de l’événement (SIEM) sur site. Cette intégration fournit un tableau de bord unifiée pour tous vos actifs, sur site ou dans le nuage, afin que vous pouvez agréger, mettre en corrélation, analyse et alerte pour les événements de sécurité associées à vos applications.

Ce didacticiel vous explique comment installer l’intégration du journal d’Azure et d’intégrer des journaux de stockage Azure, Azure et les alertes du centre de sécurité Azure. Durée estimée pour compléter ce didacticiel est d’une heure.

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez disposer des éléments suivants :

- Un ordinateur (sur site ou dans le nuage) pour installer le service d’intégration de journal Azure. Cet ordinateur doit exécuter un système d’exploitation Windows de 64 bits avec .net 4.5.1 installé. Cet ordinateur est appelé l' **Intégrateur de Azlog**.
- Abonnement Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).
- Diagnostics de Windows Azure activés pour vos ordinateurs virtuels Azure (VMs). Pour activer les diagnostics pour les Services en nuage, consultez [Activation des Diagnostics de Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Pour activer les tests de diagnostic pour un Azure VM fonctionnant sous Windows, voir [Utiliser PowerShell activer les Diagnostics de Azure dans une Machine virtuelle en cours d’exécution](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Connectivité de l’intégrateur Azlog au stockage Azure et pour authentifier et autoriser à un abonnement Azure.
- Pour les journaux d’Azure VM, l’agent SIEM (par exemple, redirecteur universel de Splunk, agent de collecteur d’événements Windows HP ArcSight ou IBM QRadar WinCollect) doit être installé sur l’intégrateur Azlog.

## <a name="deployment-considerations"></a>Considérations relatives au déploiement

Vous pouvez exécuter plusieurs instances de l’intégrateur Azlog si le volume des événements est élevé. L’équilibrage de la charge des comptes de stockage Azure Diagnostics pour Windows *(WAD)* et le nombre d’abonnements pour fournir aux instances doit reposer sur la capacité.

Sur un ordinateur équipé de 8 processeurs (de base), une seule instance de Azlog Integrator peut traiter environ 24 millions d’événements par jour (~1M/hour).

Sur une machine à 4 processeurs (de base), une seule instance de Azlog Integrator peut traiter environ 1,5 million d’événements par jour (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Installer l’intégration du journal d’Azure

Télécharger [Azure session d’intégration](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration Azure journal collecte les données de télémétrie à partir de l’ordinateur sur lequel il est installé.  Données de télémétrie recueillies sont :

- Exceptions qui se produisent pendant l’exécution de l’intégration du journal d’Azure
- Mesures sur le nombre de requêtes et de traitement des événements
- Statistiques sur les Azlog.exe les options de ligne de commande sont utilisées

> [AZURE.NOTE] Vous pouvez désactiver la collecte des données de télémétrie en désactivant cette option.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Intégrer des journaux Azure VM à partir de vos comptes de stockage Azure Diagnostics

1. Vérifier les composants requis répertoriés ci-dessus pour vous assurer que votre compte de stockage WAD collecte des journaux avant de poursuivre l’intégration de votre journal Azure. N’effectuez pas les étapes suivantes si votre compte de stockage WAD ne recueille pas les journaux.

2. Ouvrez l’invite de commande et d’un **cd** dans **c:\Program Files\Microsoft Azure journal d’intégration**.

3. Exécutez la commande

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Remplacez StorageAccountName par le nom du compte de stockage Azure configuré pour recevoir des événements de diagnostics de votre machine virtuelle.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Si vous souhaitez que l’id d’abonnement s’affiche dans l’événement XML, ajouter l’ID d’abonnement pour le nom convivial :

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Attendre 30 à 60 minutes (il peut prendre jusqu'à une heure), puis afficher les événements qui sont extraites à partir du compte de stockage. Pour afficher, ouvrez **l’Observateur d’événements > journaux Windows > événements transmis** sur l’intégrateur Azlog.

5. Assurez-vous que votre connecteur SIEM standard installé sur l’ordinateur est configuré pour choisir les événements à partir du dossier des **Événements transmis** et transférez-les dans votre instance SIEM. Passez en revue la configuration spécifique de SIEM pour configurer et consultez les journaux d’intégration.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Que se passe-t-il si données ne s’affiche pas dans le dossier événements transmis ?

Si après une heure données ne s’affiche pas dans le dossier **Événements transmis** , puis :

1. Vérifier l’ordinateur et vérifiez qu’il peut accéder aux Azure. Pour tester la connectivité, essayez d’ouvrir le [portail Azure](http://portal.azure.com) à partir du navigateur.

2. Assurez-vous que compte d’utilisateur **azlog** a l’autorisation écriture sur dossier **users\azlog**.

3. Assurez-vous que le compte de stockage ajouté à la commande **azlog source ajouter** apparaît lorsque vous exécutez la commande **liste de source azlog**.
4. Accédez à **l’Observateur d’événements > journaux Windows > Application** pour voir si des erreurs sont signalées par l’intégration de journal Azure.

Si vous ne voyez toujours les événements, puis :

1. Télécharger [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

2. Se connecter sur le compte de stockage ajouté à la commande à **Ajouter de la source d’azlog**.

3. Dans l’Explorateur de stockage Microsoft Azure, accédez à **WADWindowsEventLogsTable** pour voir il s'existe des données de table. Si ce n’est pas le cas, puis diagnostics dans la machine virtuelle n’est pas configuré correctement.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Intégrer les alertes du centre de sécurité et les journaux d’audit Azure

1. Ouvrez l’invite de commande et d’un **cd** dans **c:\Program Files\Microsoft Azure journal d’intégration**.

2. Exécutez la commande

        azlog createazureid

      Cette commande vous demande votre nom d’utilisateur Azure. La commande crée alors une [Entité de Service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les locataires AD Azure qui hébergent les abonnements Azure dans laquelle l’utilisateur est un administrateur, un administrateur de coproduit ou un propriétaire. La commande échouera si l’utilisateur est un utilisateur invité dans les clients AD Azure. Authentification vers Azure s’effectue par le biais de Azure Active Directory (AD).  Création d’un principal de service pour l’intégration de Azlog crée l’identité AD Azure qui aura accès en lecture à partir des abonnements Azure.

3. Exécutez la commande

        azlog authorize <SubscriptionID>

      Cela affecte les accès en lecture sur l’abonnement au service principal créé à l’étape 2. Si vous ne spécifiez pas un SubscriptionID, puis il essaie d’en assigner le rôle de lecteur principal de service pour tous les abonnements à laquelle vous avez accès.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Vous pouvez afficher les alertes si vous exécutez la commande **Autoriser** immédiatement après la commande **createazureid** . Il observe une certaine latence entre lors de la création du compte Azure AD et lorsque le compte est disponible pour utilisation. Si vous attendez environ 10 secondes après l’exécution de la commande **createazureid** pour exécuter la commande **Autoriser** , vous ne devriez pas voir ces avertissements.

4. Vérifiez les dossiers suivants pour vérifier que les fichiers JSON de journaux d’Audit sont présents :

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Vérifier pour confirmer que les alertes du centre de sécurité existent dans les dossiers suivants :

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Pointez le connecteur de redirecteur standard SIEM fichier vers le dossier approprié pour les données à l’instance SIEM de tuyau. Vous devrez peut-être certains mappages de champ en fonction du produit SIEM que vous utilisez.

Si vous avez des questions sur l’intégration du journal d’Azure, veuillez envoyer un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment installer l’intégration du journal d’Azure et d’intégrer des journaux à partir du stockage Azure. Pour plus d’informations, consultez les rubriques suivantes :

- [Intégration du journal Microsoft Azure pour les journaux Azure (aperçu)](https://www.microsoft.com/download/details.aspx?id=53324) – centre de téléchargement pour plus d’informations, configuration système requise et instructions d’installation sur l’intégration du journal d’Azure.
- [Introduction à l’intégration du journal d’Azure](security-azure-log-integration-overview.md) – ce document présente intégration du journal d’Azure, ses fonctionnalités clées et comment il fonctionne.
- [Les étapes de configuration de partenaire](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ce billet de blog vous indique comment configurer l’intégration de journal Azure pour fonctionner avec les solutions partenaire Splunk ArcSight de HP et IBM QRadar.
- [Journal azure Integration Forum aux questions (FAQ)](security-azure-log-integration-faq.md) - Forum aux questions sur cette répond aux questions à propos de l’intégration du journal d’Azure.
- [Alertes du centre de sécurité intégration avec Azure journal intégration](../security-center/security-center-integrating-alerts-with-log-integration.md) – ce document vous montre comment synchroniser les alertes du centre de sécurité, ainsi que les événements de sécurité d’ordinateur virtuel collectées par les Diagnostics d’Azure et d’Azure les journaux d’Audit, avec votre journal analytique ou d’une solution SIEM.
- [Nouvelles fonctionnalités pour les diagnostics de Windows Azure et d’Azure les journaux d’Audit](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – ce billet de blog présente Azure les journaux d’Audit et d’autres fonctionnalités qui vous aident à obtenir des informations sur les opérations de vos ressources d’Azure.
