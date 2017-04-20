<properties
   pageTitle="Gestion des rôles dans le nuage Azure services de projets avec Visual Studio | Microsoft Azure"
   description="Apprenez à ajouter de nouveaux rôles à votre projet de service cloud Azure ou supprimer des rôles existants à partir de celui-ci à l’aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gestion des rôles dans les projets de service cloud Azure avec Visual Studio

Après avoir créé votre projet de service cloud Azure, vous pouvez ajouter de nouveaux rôles lui ou supprimer des rôles existants à partir de celui-ci. Vous pouvez également importer un projet existant et convertissez-le à un rôle. Par exemple, vous pouvez importer une application web ASP.NET et le désigner comme un rôle web.

## <a name="adding-or-removing-roles"></a>Ajout ou suppression de rôles

**Pour ajouter un rôle**

Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le nœud **rôles** dans votre projet de service cloud et cliquez sur **Ajouter**. Vous pouvez sélectionner un rôle existant de web ou travailleur de la solution en cours ou en créer un nouveau projet de rôle web ou travailleur. Ou bien, vous pouvez sélectionner un projet approprié, tel qu’un projet d’application web ASP.NET et l’associer à un projet de rôle.

**Pour supprimer une association de rôle**

Dans **le nœud du projet de service cloud dans l’Explorateur de solutions,** ouvrez le menu contextuel pour le rôle à supprimer et sélectionnez **Supprimer**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Suppression et ajout de rôles dans votre service cloud

Si vous supprimez un rôle à partir de votre projet de service cloud mais que vous décidez ensuite d’ajouter le rôle dans le projet, seulement la déclaration de rôle et les attributs de base, telles que les informations des points de terminaison et les tests de diagnostic, sont ajoutés. Aucune des ressources supplémentaires ou des références ne sont ajoutées dans le fichier ServiceDefinition.csdef ou le fichier ServiceConfiguration.cscfg. Si vous voulez ajouter ces informations, vous devrez l’ajouter manuellement dans ces fichiers.

Par exemple, vous pouvez supprimer un rôle de service web et vous décidez ensuite d’ajouter ce rôle dans votre solution. Si vous le faites, une erreur se produit. Pour éviter cette erreur, vous devez ajouter la `<LocalResources>` élément indiqué dans le code XML suivant dans le fichier ServiceDefinition.csdef. Utilisez le nom du rôle de service web que vous avez ajoutés dans le projet en tant que partie de l’attribut de nom pour le **<LocalStorage>** élément. Dans cet exemple, le nom du rôle de service web est **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer des rôles dans Visual Studio en lisant [configurer les rôles pour un Service de Cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
