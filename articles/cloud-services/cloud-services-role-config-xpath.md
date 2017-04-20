<properties 
pageTitle="Fiche récapitulative de nuage rôle Services config XPath | Microsoft Azure" 
description="Les différents paramètres XPath à utiliser dans la configuration de rôle de service de cloud pour exposer les paramètres sous la forme d’une variable d’environnement." 
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
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Exposer les paramètres de configuration de rôle comme une variable d’environnement avec XPath

Dans le travail de service cloud ou le fichier de définition de service de rôle web, vous pouvez exposer les valeurs de configuration d’exécution en tant que variables d’environnement. Les valeurs de XPath suivantes sont prises en charge (qui correspondent aux valeurs de l’API).

Ces valeurs de XPath sont également disponibles via la bibliothèque [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) . 

## <a name="app-running-in-emulator"></a>Application qui s’exécute dans l’émulateur

Indique que l’application s’exécute dans l’émulateur.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Code  | var x = RoleEnvironment.IsEmulated ; |


## <a name="deployment-id"></a>ID du déploiement

Récupère le code de déploiement pour l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Code  | var deploymentId = RoleEnvironment.DeploymentId ; |


## <a name="role-id"></a>ID du rôle 

Récupère l’ID de rôle actuel de l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Code  | id de var = RoleEnvironment.CurrentRoleInstance.Id ; |


## <a name="update-domain"></a>Mettre à jour le domaine

Récupère le domaine de la mise à jour de l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Code  | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain ; |


## <a name="fault-domain"></a>Domaine d’erreur

Récupère le domaine de la panne de l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Code  | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain ; |


## <a name="role-name"></a>Nom de rôle

Récupère le nom du rôle des instances.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Code  | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name ;  |


## <a name="config-setting"></a>Paramètre de configuration

Récupère la valeur du paramètre de configuration spécifié.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Code  | paramètre de var = RoleEnvironment.GetConfigurationSettingValue("Setting1") ; |
 
## <a name="local-storage-path"></a>Chemin d’accès de stockage local

Récupère le chemin d’accès de stockage local pour l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Code  | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath ; |


## <a name="local-storage-size"></a>Taille de stockage local

Récupère la taille du stockage local pour l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Code  | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes ; |

## <a name="endpoint-protocol"></a>Protocole de point de terminaison 

Récupère le protocole de point de terminaison pour l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Code  | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1 »]. Protocole ; |

## <a name="endpoint-ip"></a>Point de terminaison IP

Obtient l’adresse IP du point de terminaison spécifié.

| Type de | Exemple |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Code  | adresse de var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1 »]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Port du point de terminaison 

Récupère le port du point de terminaison pour l’instance.

| Type de  | Exemple |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Code  | port de var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1 »]. IPEndpoint.Port ; |





## <a name="example"></a>Exemple

Voici un exemple d’un rôle de collaborateur qui crée une tâche de démarrage avec une variable d’environnement nommée `TestIsEmulated` définie sur le [ @emulated valeur xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le fichier [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Créer un package [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Activer le [Bureau à distance](cloud-services-role-enable-remote-desktop.md) pour un rôle.
