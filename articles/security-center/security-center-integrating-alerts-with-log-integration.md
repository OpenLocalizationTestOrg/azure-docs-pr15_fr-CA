<properties
   pageTitle="Intégrer les alertes du centre de sécurité Azure grâce à l’intégration de journal Azure (aperçu) | Microsoft Azure"
   description="Cet article vous permet de commencer avec l’intégration des alertes du centre de sécurité grâce à l’intégration du journal d’Azure."
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
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Intégrer les alertes du centre de sécurité grâce à l’intégration de journal Azure (aperçu)

De nombreuses opérations de sécurité et les équipes de réponse aux incidents s’appuient sur une solution de sécurité des informations et événements SIEM (gestion) comme point de départ pour le triage et analyse des alertes de sécurité. Grâce à l’intégration du journal d’Azure, les clients peuvent synchroniser les alertes du centre de sécurité Azure, ainsi que les événements de sécurité d’ordinateur virtuel collectées par les Diagnostics d’Azure et d’Azure les journaux d’Audit, avec leur journal analytique ou d’une solution SIEM presque en temps réel.

Intégration du journal d’Azure fonctionne avec HP ArcSight, Splunk, QRadar d’IBM et d’autres personnes.

## <a name="what-logs-can-i-integrate"></a>Les journaux puis-je intégrer ?

Azure génère la journalisation complète pour chaque service. Ces journaux est classés en tant que :

- **Les journaux de contrôle et de gestion** qui donnent une visibilité sur les opérations de création de gestionnaire de ressources Azure, UPDATE et DELETE.
- **Plan de données de journaux** qui donnent une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource d’Azure. Par exemple, le journal des événements Windows - sécurité et journaux d’applications dans une machine virtuelle.

Intégration du journal d’Azure prend actuellement en charge l’intégration de :

- Journaux d’Azure VM
- Journaux d’Audit Azure
- Alertes du centre de sécurité Azure

## <a name="install-azure-log-integration"></a>Installer l’intégration du journal d’Azure

Télécharger [Azure session d’intégration](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration Azure journal collecte les données de télémétrie à partir de l’ordinateur sur lequel il est installé.  Données de télémétrie recueillies sont :

- Exceptions qui se produisent pendant l’exécution de l’intégration du journal d’Azure
- Mesures sur le nombre de requêtes et de traitement des événements
- Statistiques sur les Azlog.exe les options de ligne de commande sont utilisées

> [AZURE.NOTE] Vous pouvez désactiver la collecte des données de télémétrie en désactivant cette option.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Intégrer les alertes du centre de sécurité et les journaux d’Audit de Azure

1. Ouvrez l’invite de commande et d’un **cd** dans **c:\Program Files\Microsoft Azure journal d’intégration**.

2. Exécutez la commande **azlog createazureid** pour créer une [Entité de Service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les locataires Azure Active Directory (AD) qui hébergent les abonnements Azure.

    Vous demandera votre nom d’utilisateur Azure.

    > [AZURE.NOTE] Vous devez être le propriétaire de l’abonnement ou un administrateur de collègues de l’abonnement.

    Azure l’authentification s’effectue via AD Azure.  Création d’un principal de service pour l’intégration de journal Azure créera l’identité AD Azure qui aura accès en lecture à partir des abonnements Azure.

3. Exécuter le **autorisent les azlog <SubscriptionID> ** commande pour attribuer un accès en lecture sur l’abonnement à l’entité de service créée à l’étape 2. Si vous ne spécifiez pas un **SubscriptionID**, puis l’entité service recevront le rôle de lecteur à tous les abonnements auxquels vous avez accès.

    > [AZURE.NOTE] Vous pouvez afficher les alertes si vous exécutez la commande **Autoriser** immédiatement après la commande **createazureid** , car il observe une certaine latence entre lors de la création du compte Azure AD et lorsque le compte est disponible pour utilisation. Si vous attendez environ 10 secondes après l’exécution de la commande **createazureid** pour exécuter la commande **Autoriser** , vous ne devriez pas voir ces avertissements.

4. Vérifiez les dossiers suivants pour vérifier que les fichiers JSON de journaux d’Audit sont présents :

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Vérifier pour confirmer que les alertes du centre de sécurité existent dans les dossiers suivants :

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Pointez le connecteur de redirecteur standard SIEM fichier vers le dossier approprié pour les données à l’instance SIEM de tuyau. Reportez-vous aux [configurations de SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) sur votre configuration SIEM.

Si vous avez des questions sur l’intégration du journal d’Azure, veuillez envoyer un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les journaux d’Audit Azure et des définitions de propriétés, consultez :

- [Auditer les opérations effectuées avec le Gestionnaire de ressources](../resource-group-audit.md)
- [Liste les événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) - pour récupérer les événements du journal d’audit.

Pour en savoir plus sur le centre de sécurité, consultez les rubriques suivantes :

- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité d’Azure](http://blogs.msdn.com/b/azuresecurity/) , obtenir les dernières informations de sécurité Azure et les informations.
