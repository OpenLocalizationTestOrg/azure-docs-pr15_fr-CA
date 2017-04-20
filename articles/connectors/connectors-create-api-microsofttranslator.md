<properties
    pageTitle="Ajouter Microsoft Translator dans les applications de logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur Microsoft Translator avec les paramètres de l’API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Mise en route avec le connecteur Microsoft Translator
Se connecter à Microsoft Translator pour traduire du texte, de détecter une langue et bien plus encore. Avec Microsoft Translator, vous pouvez : 

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de Microsoft Translator. 
- Utiliser des actions pour traduire du texte, de détecter une langue et bien plus encore. Ces actions Obtient une réponse et vérifiez la sortie disponible pour d’autres actions. Par exemple, lorsqu’un nouveau fichier est créé dans la boîte de dépôt, vous pouvez traduire le texte dans le fichier pour une autre langue à l’aide de Microsoft Translator.

Pour ajouter une opération dans les applications de la logique, voir [Création d’une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Microsoft Translator inclut les actions suivantes. Il n’y a pas de déclencheurs.

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Détecter la langue</li><li>Conversion de texte par synthèse vocale</li><li>Traduire le texte</li><li>Obtenir des langues</li><li>Obtenir les langues de reconnaissance vocale</li></ul>

Tous les connecteurs prennent en charge les données au format JSON et XML.


## <a name="create-a-connection-to-microsoft-translator"></a>Créer une connexion à Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Swagger de référence de l’API REST
S’applique à la version : 1.0.

### <a name="detect-language"></a>Détecter la langue    
Détecte la langue source du texte.  
```GET: /Detect```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|requête|chaîne|Oui|requête|Aucun |Texte dont la langue est identifiée.|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="text-to-speech"></a>Conversion de texte par synthèse vocale    
Convertit un texte donné la parole sous la forme d’un flux audio dans le format wave.  
```GET: /Speak```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|requête|chaîne|Oui|requête|Aucun |Texte à convertir|
|langue|chaîne|Oui|requête|Aucun |Code de langue à générer vocale (exemple : « en-us)|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="translate-text"></a>Traduire le texte    
Traduit le texte pour une langue spécifique à l’aide de Microsoft Translator.  
```GET: /Translate```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|requête|chaîne|Oui|requête|Aucun |Texte à traduire|
|languageTo|chaîne|Oui|requête| Aucun|Code de la langue cible (exemple : 'fr')|
|languageFrom|chaîne|aucune|requête|Aucun |Langue source ; Si n’est fourni, Microsoft Translator essaie de détecter automatiquement. (exemple : fr)|
|catégorie|chaîne|aucune|requête|général |Catégorie de traduction (par défaut : « général »)|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-languages"></a>Obtenir des langues    
Récupère toutes les langues prises en charge de Microsoft Translator.  
```GET: /TranslatableLanguages```

Il n’y a pas de paramètres pour cet appel. 

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-speech-languages"></a>Obtenir les langues de reconnaissance vocale    
Récupère les langues disponibles pour la synthèse vocale.  
```GET: /SpeakLanguages``` 

Il n’y a pas de paramètres pour cet appel.

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|

## <a name="object-definitions"></a>Définitions d’objets

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Langue : modèle de langage pour les langues traduisibles Microsoft Translator

|Nom de la propriété | Type de données | Obligatoire|
|---|---|---|
|Code|chaîne|aucune|
|Nom|chaîne|aucune|


## <a name="next-steps"></a>Étapes suivantes

[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
