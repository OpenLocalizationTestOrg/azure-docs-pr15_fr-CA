<properties 
    pageTitle="Utiliser les paramètres de Service Web de formation de Machine Azure | Microsoft Azure" 
    description="Comment utiliser des paramètres de Service Web Azure Machine Learning pour modifier le comportement de votre modèle lors de l’accès au service web." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>Utiliser les paramètres de Service Web de formation de Machine Azure

Un service web de formation de Machine Azure est créé en publiant une expérience qui contient des modules avec des paramètres configurables. Dans certains cas, vous souhaiterez modifier le comportement du module alors que le service web est en cours d’exécution. *Paramètres de Service Web* vous autorise à effectuer cette tâche. 

Un exemple courant consiste à configurer l' [Importation de données] [ reader] module afin que l’utilisateur du service web publié peut spécifier une autre source de données lors de l’accès au service web. Ou la configuration de l' [Exportation de données] [ writer] module afin qu’une autre destination peut être spécifiée. D’autres exemples incluent le nombre de bits de la modification de la [Fonction de hachage] [ feature-hashing] module ou le nombre de fonctionnalités de désirées pour la [sélection des fonctionnalités basées sur le filtre] [ filter-based-feature-selection] module. 

Vous pouvez définir les paramètres de Service Web et les associer à un ou plusieurs paramètres de module de votre expérience, et vous pouvez spécifier si elles sont obligatoires ou facultatives. L’utilisateur du service web peut ensuite fournir des valeurs pour ces paramètres lorsqu’ils appellent le service web. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>Comment définir et utiliser des paramètres de Service Web

Vous définissez un paramètre du Service Web en cliquant sur l’icône en regard du paramètre pour un module et en sélectionnant « Définir en tant que paramètre du service web ». Cela crée un nouveau paramètre de Service Web et qu’il connecte à ce paramètre du module. Ensuite, lors de l’accès au service web, l’utilisateur peut spécifier une valeur pour le paramètre du Service Web et il est appliqué au paramètre de module.

Lorsque vous définissez un paramètre du Service Web, il est disponible à n’importe quel autre paramètre module à l’expérimentation. Si vous définissez un paramètre du Service Web associé à un paramètre pour un module, vous pouvez utiliser ce même paramètre de Service Web d’un autre module, dans la mesure où le paramètre attend le même type de valeur. Par exemple, si le paramètre du Service Web est une valeur numérique, puis il peut uniquement être utilisé pour les paramètres de module qui attendent une valeur numérique. Lorsque l’utilisateur définit une valeur pour le paramètre du Service Web, elle sera appliquée à tous les paramètres de module associé.

Vous pouvez décider s’il faut fournir une valeur par défaut pour le paramètre du Service Web. Dans ce cas, le paramètre est facultatif pour l’utilisateur du service web. Si vous ne fournissez pas une valeur par défaut, l’utilisateur est tenu d’entrer une valeur lorsque le service web est accessible.

La documentation sur l’API du service web consacrée des informations pour l’utilisateur du service web sur la façon de spécifier le paramètre du Service Web par programme, lors de l’accès au service web.

>[AZURE.NOTE] La documentation de l’API d’un service web classique est fournie via le lien **page d’aide API** dans le **tableau de bord** dans un Studio de formation ordinateur du service web. La documentation sur les API pour un service web est fourni via le portail [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) sur les pages de **consommer** et **d’API de Swagger** pour votre service web.


##<a name="example"></a>Exemple

Par exemple, supposons que nous disposons d’une expérience avec une [Exportation de données] [ writer] module qui envoie des informations sur le stockage des objets blob Azure. Nous définirons un paramètre du Service Web nommé « Chemin d’accès de Blob » qui permet à l’utilisateur de service web modifier le chemin d’accès pour le stockage des objets blob lorsque le service est accessible.

1.  Dans un Studio de formation de Machine, cliquez sur [Exporter les données] [ writer] module pour le sélectionner. Ses propriétés sont affichées dans le volet de propriétés à droite de la zone d’essai.

2.  Spécifiez le type de stockage :

    - **Veuillez spécifier la destination des données**, cliquez sur « Stockage de Blob Azure ».
    - Sous **Spécifiez le type d’authentification**, sélectionnez « Compte ».
    - Entrez les informations de compte pour le stockage blob Azure. 
    <p />

3.  Cliquez sur l’icône à droite du **chemin d’accès au blob en commençant par le paramètre de conteneur**. Il ressemble à ceci :

    ![Icône de paramètre du Service Web][icon]

    Sélectionnez « Définir en tant que paramètre du service web ».

    Une entrée est ajoutée sous l’onglet **Paramètres de Service Web** en bas du volet Propriétés portant le nom « Chemin d’accès commençant par conteneur de tache ». C’est le paramètre du Service Web qui est maintenant associé à cette [Exportation de données] [ writer] paramètre du module.

4.  Pour renommer le paramètre du Service Web, cliquez sur le nom, entrez le « Chemin d’accès de Blob » et appuyez sur la touche **entrée** . 
 
5.  Pour fournir une valeur par défaut pour le paramètre du Service Web, cliquez sur l’icône à droite du nom de sélectionner ni « Fournir la valeur par défaut », entrez une valeur (par exemple, « container1/output1.csv ») et appuyez sur la touche **entrée** .

    ![Paramètre du Service Web][parameter]

6.  Cliquez sur **exécuter**. 

7.  Cliquez sur **Déployer le Service Web** et sélectionnez le **Service Web de déploiement [standard]** ou de **Déploiement du Service Web [nouveau]** pour déployer le service web.

L’utilisateur du service web peut désormais spécifier une nouvelle destination pour l' [Exportation de données] [ writer] module lors de l’accès au service web.

##<a name="more-information"></a>Plus d’informations

Pour un exemple plus détaillé, consultez l’entrée [Paramètres de Service Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) dans le [Blog d’apprentissage Machine](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Pour plus d’informations sur l’accès à un service web d’apprentissage Machine, consultez [comment consommer un ordinateur publié, formation de service web](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
