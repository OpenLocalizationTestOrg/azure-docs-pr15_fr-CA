<properties
    pageTitle="Points de terminaison des services Azure"
    description="Décrit les paramètres de point de terminaison de Service Azure dans le Shared Computer Toolkit Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Points de terminaison des services Azure #

Points de terminaison des services Azure déterminent que si votre application est déployée sur et gérée par la plateforme Azure globale, Azure exploités par 21Vianet en Chine ou privé plateforme Azure. La boîte de dialogue **Points de terminaison de Service** vous permet de spécifier les points de terminaison de service à utiliser. Pour ouvrir la boîte de dialogue **Points de terminaison de Service** au sein d’Eclipse, cliquez sur **la fenêtre**, cliquez sur **Préférences**, développez **Azure**, puis cliquez sur **Les points de terminaison de Service**. Le champ de **l’Ensemble actif** détermine quels points de terminaison de service Azure seront utilisés pour les projets d’Azure dans votre espace de travail actuel.

L’exemple suivant montre la boîte de dialogue **Points de terminaison de Service** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Pour définir les points de terminaison de service ##

Dans la boîte de dialogue **Points de terminaison de Service** , prendre une des actions suivantes :

* Si vous souhaitez utiliser la plateforme Azure globale, dans la liste déroulante **La valeur Active** , sélectionnez **windowsazure.com** et cliquez sur **OK**.
* Si vous souhaitez utiliser Azure exploité par 21Vianet en Chine, dans la liste déroulante **La valeur Active** , sélectionnez **windowsazure.cn (Chine)** et cliquez sur **OK**.
* Si vous souhaitez utiliser une plateforme Azure privée :
    1. Cliquez sur **Modifier**.
    2. Une boîte de dialogue s’ouvre, vous informant que la boîte de dialogue **Points de terminaison de Service** va être fermé, et le fichier de préférences de jeux s’ouvre. Cliquez sur **OK**.
    3. Dans le fichier preferencesets.xml, créez un nouveau `preferenceset` élément. Créer pour ce nouvel élément, `name`, `blob`, `management`, `portalURL` et `publishsettings` les attributs et ajoutez les valeurs qui correspondent aux votre plateforme Azure privé. Vous pouvez utiliser les valeurs fournies pour le `preferenceset` éléments en tant que modèles. **Remarque**: la valeur utilisée pour le `blob` attribut doit contenir le texte « blob » dans l’URL.
    4. Enregistrez et fermez preferencesets.xml.
    5. Rouvrez la boîte de dialogue **Points de terminaison de Service** .
    6. Dans la liste déroulante **Groupe actif** , sélectionnez l’actif que vous avez créé, puis cliquez sur **OK**.
    7. Une fois que vous avez créé votre plateforme Azure privé `preferenceset` élément, vous pouvez modifier les valeurs affectées à celle-ci en cliquant sur le bouton **Modifier** dans la boîte de dialogue **Services de point de terminaison** . Vous pouvez également créer plusieurs plates-formes Azure privé `preferenceset` éléments, si vous le souhaitez.

## <a name="see-also"></a>Voir aussi ##

[Azure Shared Computer Toolkit pour Éclipse][]

[L’installation de la Shared Computer Toolkit Azure pour Éclipse][] 

[Création d’une Application du monde Hello pour Azure dans Éclipse][]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre pour développeurs Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Shared Computer Toolkit pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application du monde Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[L’installation de la Shared Computer Toolkit Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
