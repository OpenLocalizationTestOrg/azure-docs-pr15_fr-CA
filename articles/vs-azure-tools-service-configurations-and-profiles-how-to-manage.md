<properties
   pageTitle="Comment faire pour gérer les profils et les configurations de service | Microsoft Azure"
   description="Apprenez à travailler avec des fichiers de configuration des configurations et des profils de service | stocker des paramètres pour les environnements de déploiement et paramètres de publication des services en nuage."
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

# <a name="how-to-manage-service-configurations-and-profiles"></a>Comment faire pour gérer les profils et les configurations de service

## <a name="overview"></a>Vue d’ensemble

Lorsque vous publiez un service en nuage, Visual Studio stocke les informations de configuration dans deux types de fichiers de configuration : configurations et des profils de service. Les configurations de service (fichiers .cscfg) stockent les paramètres pour les environnements de déploiement d’un service cloud Azure. Azure utilise ces fichiers de configuration lorsqu’il gère vos services en nuage. D’autre part, le magasin de profils (fichiers .azurePubxml) paramètres de publication des services en nuage. Ces paramètres sont un enregistrement de ce que vous choisissez lorsque vous utilisez l’Assistant Publication et sont utilisés localement par Visual Studio. Cette rubrique explique comment utiliser les deux types de fichiers de configuration.

## <a name="service-configurations"></a>Configurations de service

Vous pouvez créer plusieurs configurations de service à utiliser pour chacun de vos environnements de déploiement. Par exemple, vous pouvez créer une configuration de service pour l’environnement local que vous utilisez pour exécuter et tester une application Azure et une autre configuration de service pour votre environnement de production.

Vous pouvez ajouter, supprimer, renommer et modifier ces configurations de service en fonction de vos besoins. Vous pouvez gérer les configurations de service à partir de Visual Studio, comme indiqué dans l’illustration suivante.

![Gérer les Configurations de Service](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Vous pouvez également ouvrir la boîte de dialogue **Gérer les Configurations** à partir des pages de propriétés du rôle. Pour ouvrir les propriétés d’un rôle dans votre projet d’Azure, ouvrez le menu contextuel pour ce rôle et puis cliquez sur **Propriétés**. Sous l’onglet **paramètres** , développez la liste de la **Configuration du Service** et puis sélectionnez **Gérer** pour ouvrir la boîte de dialogue **Gérer les Configurations** .

### <a name="to-add-a-service-configuration"></a>Pour ajouter une configuration de service

1. Dans l’Explorateur ouvrir le menu contextuel du projet Azure et sélectionnez **Gérer les Configurations**.

    La boîte de dialogue **Gérer les Configurations de Service** s’affiche.

1. Pour ajouter une configuration de service, vous devez créer une copie d’une configuration existante. Pour ce faire, choisissez la configuration que vous souhaitez copier dans la liste nom, puis choisissez **créer une copie**.

1. (Facultatif) Pour donner un autre nom à la configuration du service, choisissez la nouvelle configuration de service dans la liste nom et sélectionnez **Renommer**. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour la configuration de ce service, puis sélectionnez **OK**.

    Un nouveau fichier de configuration de service qui est nommé ServiceConfiguration. [Nouveau nom] .cscfg est ajouté au projet Azure dans l’Explorateur de solutions.


### <a name="to-delete-a-service-configuration"></a>Pour supprimer une configuration de service

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel du projet Azure et sélectionnez **Gérer les Configurations**.

    La boîte de dialogue **Gérer les Configurations de Service** s’affiche.

1. Pour supprimer une configuration de service, choisissez la configuration que vous souhaitez supprimer dans la liste **nom** , puis sélectionnez **Supprimer**. Une boîte de dialogue s’affiche pour vérifier que vous souhaitez supprimer cette configuration.

1. Sélectionnez **Supprimer**.

     Le fichier de configuration de service est supprimé du projet Azure dans l’Explorateur de solutions.


### <a name="to-rename-a-service-configuration"></a>Pour renommer une configuration de service

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour le projet d’Azure et puis sélectionnez **Gérer les Configurations**.

    La boîte de dialogue **Gérer les Configurations de Service** s’affiche.

1. Pour renommer une configuration de service, choisissez la nouvelle configuration de service dans la liste **nom** , puis cliquez sur **Renommer**. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour la configuration de ce service et cliquez sur **OK**.

    Le nom du fichier de configuration du service est modifié dans le projet Azure dans l’Explorateur de solutions.

### <a name="to-change-a-service-configuration"></a>Pour modifier une configuration de service

- Si vous souhaitez modifier une configuration de service, ouvrez le menu contextuel pour le rôle spécifique que vous souhaitez modifier dans le projet Azure et puis cliquez sur **Propriétés**. Consultez [Comment : configurer les rôles pour un Service de Cloud Azure avec Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) pour plus d’informations.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Vérifiez les différentes combinaisons de paramètres à l’aide de profils

À l’aide d’un profil, vous pouvez remplir automatiquement dans l' **Assistant Publication** avec différentes combinaisons de paramètres, à des fins différentes. Par exemple, vous pouvez avoir un profil pour le débogage et un autre pour la version génère. Dans ce cas, votre profil **Déboguer** aurait **IntelliTrace** est activé et la configuration **de débogage** est sélectionné, et que votre profil de **libération** aurait **IntelliTrace** est désactivé et la configuration de la **version** sélectionnée. Vous pouvez également utiliser différents profils pour déployer un service à l’aide d’un compte de stockage différents.

Lorsque vous exécutez l’Assistant pour la première fois, un profil par défaut est créé. Visual Studio stocke le profil dans un fichier portant l’extension .azurePubXml, qui est ajoutée à votre projet Azure sous le dossier **Profiles** . Si vous spécifiez manuellement les différents choix lorsque vous exécutez l’Assistant plus tard, le fichier met à jour automatiquement. Avant d’exécuter la procédure suivante, vous devez ont déjà publié votre service cloud au moins une fois.

### <a name="to-add-a-profile"></a>Pour ajouter un profil

1. Ouvrir le menu contextuel pour votre projet d’Azure et sélectionnez **Publier**.

1. En regard de la liste des **profils de la cible** , cliquez sur le bouton **Enregistrer le profil** , comme le montre l’illustration suivante. Cela crée un profil pour vous.

    ![Créer un nouveau profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Après avoir créé le profil, sélectionnez **< gérer... >** dans la liste des **profils de la cible** .

    La boîte de dialogue **Gérer les profils** s’affiche, comme le montre l’illustration suivante.

    ![Gérer la boîte de dialogue profils](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. Dans la liste **nom** , choisissez un profil et sélectionnez **Créer une copie**.

1. Cliquez sur le bouton **Fermer** .

    Le nouveau profil s’affiche dans la liste des profils cible.

1. Dans la liste des **profils de la cible** , sélectionnez le profil que vous venez de créer. Les paramètres de l’Assistant publication sont renseignés avec les choix du profil que vous avez sélectionné.

1. Cliquez sur les boutons **précédent** et **suivant** pour afficher chaque page de l’Assistant Publication et personnaliser les paramètres de ce profil. Pour plus d’informations, reportez-vous à la section [Publier Assistant Création d’applications Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Lorsque vous avez terminé de personnaliser les paramètres, sélectionnez **suivant** pour revenir à la page Paramètres. Le profil est enregistré lorsque vous publiez le service à l’aide de ces paramètres ou si vous sélectionnez l’option **Enregistrer** en regard de la liste des profils.

### <a name="to-rename-or-delete-a-profile"></a>Pour renommer ou supprimer un profil

1. Ouvrir le menu contextuel pour votre projet d’Azure et sélectionnez **Publier**.

1. Dans la liste des **profils de la cible** , sélectionnez **Gérer**.

1. Dans la boîte de dialogue **Gérer les profils** , sélectionnez le profil que vous souhaitez supprimer, puis sélectionnez **Supprimer**.

1. Dans la boîte de dialogue de confirmation qui s’affiche, sélectionnez **OK**.

1. Sélectionnez **Fermer**.

### <a name="to-change-a-profile"></a>Pour modifier un profil

1. Ouvrir le menu contextuel pour votre projet d’Azure et sélectionnez **Publier**.

1. Dans la liste des **profils de la cible** , sélectionnez le profil que vous souhaitez modifier.

1. Cliquez sur les boutons **précédent** et **suivant** pour afficher chaque page de l’Assistant Publication, puis modifiez les paramètres souhaités. Pour plus d’informations, reportez-vous à la section [Publier Assistant Création d’applications Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Lorsque vous avez terminé la modification des paramètres, sélectionnez **suivant** pour revenir à la page **paramètres** .

1. (Facultatif) cliquez sur **Publier** pour publier le service en nuage avec les nouveaux paramètres. Si vous ne souhaitez pas publier votre service cloud pour l’instant, et que vous fermez l’Assistant Publication, Visual Studio vous demande si vous souhaitez enregistrer les modifications dans le profil.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration des autres parties de votre projet Visual Studio Azure, consultez [configuration d’un projet d’Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)
