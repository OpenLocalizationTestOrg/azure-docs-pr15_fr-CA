<properties
   pageTitle="Intégration de journal Azure FAQ | Microsoft Azure"
   description="Ce forum aux questions répond aux questions à propos de l’intégration du journal d’Azure."
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
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Intégration du journal Azure Forum aux questions (FAQ)

Ce forum aux questions répond aux questions sur l’intégration du journal d’Azure, un service qui vous permet d’intégrer des journaux bruts provenant de vos ressources d’Azure dans vos systèmes d’informations de sécurité et de gestion de l’événement (SIEM) sur site. Cette intégration fournit un tableau de bord unifiée pour tous vos actifs, sur site ou dans le nuage, afin que vous pouvez agréger, mettre en corrélation, analyse et alerte pour les événements de sécurité associées à vos applications.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Comment puis-je voir les comptes de stockage à partir duquel intégration du journal d’Azure est retiré de journaux Azure VM de ?

Exécutez la commande **liste de source azlog**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Comment puis-je mettre à jour la configuration de proxy ?

Si le paramètre de proxy ne permet pas directement accès au stockage Azure, ouvrez le **AZLOG. EXE. CONFIG** fichier dans **c:\Program Files\Microsoft Azure journal d’intégration**. Mettre à jour le fichier pour inclure la section **defaultProxy** avec l’adresse de proxy de votre organisation. Après que la mise à jour est terminée, arrêtez et démarrez le service à l’aide des commandes **azlog de net stop** et **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Comment puis-je voir les informations d’abonnement à des événements de Windows ?

Ajouter le **subscriptionid** au nom convivial lors de l’ajout de la source.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

L’événement XML possède les métadonnées comme indiqué ci-dessous, y compris l’id d’abonnement.

![Événements XML][1]

## <a name="error-messages"></a>Messages d’erreur

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Lorsque vous exécutez la commande **azlog createazureid**, je reçois le message d’erreur suivant ?

Erreur :

  *Impossible de créer l’Application de DAS - client 72f988bf-86f1-41af-91ab-2d7cd011db37-raison = « Interdit » - Message = « Privilèges insuffisants pour terminer l’opération. »*

**Azlog createazureid** tente de créer une entité de sécurité de service dans tous les locataires AD Azure pour les abonnements sur lequel la connexion Azure a accès à. Si votre connexion Azure n’est seulement un utilisateur invité dans cette locataire AD Azure, puis la commande échoue avec « Privilèges insuffisants pour terminer l’opération. » Demande d’administration des clients pour ajouter votre compte en tant qu’utilisateur dans le client.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Lorsque vous exécutez la commande **azlog autoriser**, je reçois le message d’erreur suivant ?

Erreur :

  *Avertissement création d’affectation de rôle - AuthorizationFailed : le client janedo@microsoft.com' avec l’objet 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' n’a pas l’autorisation d’exécuter l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue ' abonnements/70 d 95299-d689-4c 97-b971-0d8ff0000000'.*

**Azlog autoriser** la commande attribue le rôle de lecteur au service AD Azure principal (créé avec **Azlog createazureid**) pour les abonnements. Si la connexion Azure n’est pas un coproduit administrateur ou un propriétaire de l’abonnement, il échoue avec le message d’erreur « Échec de l’autorisation ». Le contrôle d’accès Azure de basée sur les rôles (RBAC) de collègues administrateur ou propriétaire est nécessaire pour terminer cette opération.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Où puis-je trouver la définition des propriétés dans le journal d’audit ?

Voir :

- [Auditer les opérations effectuées avec le Gestionnaire de ressources](../resource-group-audit.md)
- [Liste les événements de gestion dans un abonnement dans l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Où puis-je trouver des détails sur les alertes du centre de sécurité Azure ?

Reportez-vous à la section [gestion et répondre aux alertes de sécurité dans le centre de sécurité Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Comment puis-je modifier ce qui sont collecté par les diagnostics de la mémoire virtuelle ?

Voir [Utiliser PowerShell activer les tests de diagnostic sur un ordinateur virtuel exécutant Windows Azure](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) pour plus d’informations sur la manière d’obtenir, de modifier et de définir les tests de diagnostic d’Azure dans Windows *(WAD)* configuration. Voici un exemple :

### <a name="get-the-wad-config"></a>Obtenir la configuration WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modifier le fichier de Config WAD

L’exemple suivant est une configuration où seuls EventID 4624 et ID d’événement 4625 sont collectés dans le journal des événements sécurité. Les événements Microsoft Antimalware sont collectés dans le journal des événements système. Reportez-vous à la section [consommation d’événements] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) pour plus d’informations sur l’utilisation des expressions XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Définir la configuration WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Après avoir apporté les modifications, vérifiez le compte de stockage pour garantir que les événements appropriés sont collectées.

Si vous avez des questions sur l’intégration du journal d’Azure, veuillez envoyer un e-mail à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
