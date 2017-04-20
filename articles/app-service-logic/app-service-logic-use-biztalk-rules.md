<properties
   pageTitle="En savoir plus sur et créez une application BizTalk règles API dans votre logique d’application | Microsoft Azure"
   description="Cette rubrique couvre les fonctionnalités du connecteur de règles de BizTalk et fournit des instructions sur son utilisation"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>Règles de BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Les règles d’entreprise encapsule les stratégies et les décisions qui contrôlent les processus d’entreprise. Ces stratégies peuvent être formellement définis dans les manuels de procédure, de contrats ou accords, ou qu’il existent en tant que les connaissances ou de savoir-faire représentatifs d’employés. Ces stratégies sont dynamiques et sujettes à des modifications de temps en raison de modifications dans les plans d’activités, de règlements ou d’autres raisons.

Mise en oeuvre de ces politiques dans les langages de programmation traditionnels nécessite la coordination et fastidieux et ne permet pas de non-programmeurs de participer à la création et la maintenance des stratégies d’entreprise. Les règles d’entreprise BizTalk permet de rapidement implémenter ces stratégies et dissocier le reste du processus métier. Cela permet d’apporter des modifications nécessaires aux stratégies d’entreprise sans impact sur le reste du processus métier.

##<a name="why-rules"></a>Pourquoi les règles

Il existe 3 principales raisons d’utiliser des règles d’entreprise BizTalk dans les processus d’entreprise :

* Dissocier la logique métier à partir de code d’Application
- Permettre aux analystes d’avoir davantage de contrôle sur la gestion de logique métier
+ Les modifications apportées à la logique métier atteindre plus rapidement la production

##<a name="rules-concepts"></a>Concepts de règles

##<a name="vocabulary"></a>Vocabulaire

Un _vocabulaire_ est une collection de définitions comprenant des noms conviviaux pour les objets informatiques utilisés dans des actions et des conditions de règle. Définitions de vocabulaire rendre ces règles plus faciles à lire, à comprendre et à partager par des personnes dans un domaine d’entreprise.

Vocabulaires sont conçus pour combler le fossé entre la sémantique métier et la mise en oeuvre. Par exemple, une liaison de données pour un statut d’approbation peut pointer vers une colonne donnée dans une certaine ligne dans une certaine base de données, représentée sous la forme d’une requête SQL. Au lieu d’insérer ce type de représentation complexe dans une règle, vous pouvez créer une définition de vocabulaire associée à cette liaison de données, avec un nom convivial de le « État ». Par la suite, vous pouvez inclure « État » dans n’importe quel nombre de règles et le moteur de règles peut extraire les données correspondantes de la table.

##<a name="rule"></a>Règle

Les règles d’entreprise sont les instructions déclaratives qui régissent le fonctionnement des processus d’entreprise. Une règle se compose d’une condition et d’actions. La condition est évaluée et si elle a la valeur true, le moteur de règles lance une ou plusieurs actions.
Dans le cadre de règles métier, les règles sont définies en utilisant le format suivant :

_IF_ _condition_ _Puis_ _action_

Prenons l’exemple suivant :

*IF est inférieur ou égal aux fonds disponibles.*  
*ENSUITE effectuer des transactions et la réception de l’impression*

Cette règle détermine si une transaction est effectuée par application de logique métier, sous la forme d’une comparaison de deux valeurs monétaires, sous la forme d’un montant de transaction et les fonds disponibles.
La règle d’entreprise vous permet de créer, de modifier et de déployer des règles d’entreprise. Sinon, vous pouvez effectuer les tâches précédentes par programme.

###<a name="conditions"></a>Conditions

Une condition est un expression (booléen) qui se compose d’un ou plusieurs prédicats vrai/faux.
Dans notre exemple, le prédicat inférieur ou égal à est appliqué à la quantité et les fonds disponibles. Cette condition sera toujours égale à true ou false.
Prédicats peuvent être combinées avec les opérateurs logiques et, ou et ne pas pour former une expression logique qui est potentiellement très volumineux, mais il aura toujours la valeur true ou false.

###<a name="actions"></a>Actions

Les actions sont les conséquences fonctionnelles de l’évaluation de la condition. Si une condition est remplie, une ou plusieurs actions correspondantes sont lancées.
Dans notre exemple, « transaction de conduite » et « imprimer le ticket de caisse » est des actions qui sont exécutées lorsque, et uniquement lorsque la condition (dans ce cas, « si montant est inférieur ou égal aux fonds disponibles ») a la valeur true.
Actions sont représentées dans l’infrastructure de règles d’entreprise en effectuant des opérations de jeu sur des documents XML.

##<a name="policy"></a>Stratégie de

Une stratégie est un regroupement logique de règles. Vous composez une stratégie, enregistrez, testez et, lorsque vous êtes satisfait du résultat, l’utiliser dans un environnement de production.

###<a name="policy-composition"></a>Composition de stratégie

Vous pouvez composer des stratégies dans le portail de règles. Une stratégie peut contenir un jeu arbitraire très élevé de règles, mais en général vous composez une stratégie à partir de règles qui s’appliquent à un domaine spécifique dans le contexte de l’application qui utilisera la stratégie.

###<a name="policy-testing"></a>Test de la stratégie
Vous pouvez exécuter de manière efficace une série de tests de votre stratégie avant d’être utilisée dans un environnement de production. Le portail de règles vous permet de fournir des entrées à une stratégie, exécuter la stratégie et afficher sa sortie. La sortie inclut les journaux, l’exécution de la règle, évaluation de la condition et les sorties qui en résulte.

##<a name="sample-scenario---insurance-claims"></a>Exemple de scénario - créances d’assurance
Examinons un exemple de scénario et le parcourir comme nous la même composition la logique métier.

![Texte de remplacement][1]

Dans un scénario très simple de créances d’assurance, le demandeur soumet sa demande d’assurance (par l’intermédiaire de n’importe quel client comme un site Web, application de téléphone, etc.). Cette demande de revendication obtient envoyée à l’unité de traitement de demande de l’entreprise et en fonction du résultat de la transformation, la demande peut être soit approuvé, rejeté ou transférées pour davantage de traitement manuel.
L’unité de traitement de demande dans notre scénario serait celui englobant la logique métier du système. Examiner de plus près à cette unité, nous pouvons voir les éléments suivants :

![Texte de remplacement][2]

Nous maintenant utiliser les règles d’entreprise pour implémenter cette logique métier.


##<a name="creation-of-rules-api-app"></a>Création de règles Api App


1. Connexion au portail Azure
2. Sélectionnez Nouveau -> Marketplace puis recherchez *Règles de BizTalk*
3. Sélectionnez règles de BizTalk à partir de la liste des résultats. La lame de règles de BizTalk s’ouvre.
4. Cliquez sur le bouton *créer* ![texte Alt][3]
1. Dans la nouvelle blade qui s’ouvre, entrez les informations suivantes :  
    1. Nom – donnez un nom pour votre application d’API de règles
    1. Plan de Service d’application – Sélectionnez ou créez un nouveau Plan de Service App
    1. Choisir le niveau de tarification que vous voulez que cette application se trouve dans la couche – tarification
    1. Groupe de ressources – permet de sélectionner ou de créer le groupe de ressources où l’application doit résider dans
    2. Abonnement - sélectionner l’abonnement que vous souhaitez utiliser
    1. Emplacement – choisissez la zone géographique où vous souhaitez que l’application doit être déployée.
4.  Sélectionnez *créer*. Votre application d’API de règles BizTalk serait créée dans quelques minutes.

##<a name="vocabulary-creation"></a>Création de vocabulaire
Après avoir créé une application API de règles BizTalk, l’étape suivante est de créer des vocabulaires. L’objectif est que le développeur est le personnage plus courant pour faire cet exercice. Voici comment faire cela au plus :


1. Lancement de votre BizTalk règles API App à partir du portail en cliquant sur Parcourir -> API d’applications - ><Your Rules API App>. Cela vous amènera au tableau de bord de règles API application similaire à ci-dessous :

   ![Texte de remplacement][4]

2 Sélectionnez « Définitions de vocabulaire ». Cela vous permet d’afficher les 3 de l’écran de création de vocabulaire sélectionnez « Ajouter » pour commencer l’ajout de nouvelles définitions de vocabulaire.
Il existe 2 types de définitions de vocabulaire actuellement pris en charge – littéral et XML.

##<a name="literal-definition"></a>Définition littérale
1.  Après avoir cliqué sur « Ajouter », une nouvelle lame « Ajouter la définition » s’ouvre. Entrez les valeurs suivantes
  1.    Nom – seuls les caractères alphanumériques sont supposés sans caractères spéciaux. Il doit également être unique à votre liste de définition de vocabulaire existante.
  2.    Description – champ facultatif.
  3.    Type de définition – 2 types sont pris en charge. Choisissez un littéral dans cet exemple
  4.    Type de données – Cela permet aux utilisateurs de sélectionner le type de données de la définition. Types de 4 données sont pris en charge actuellement : i.  Chaîne – ces valeurs doivent être entrées dans des guillemets doubles (« chaîne d’exemple »)  
    II. Boolean – cela peut être vrai ou faux  
    III.    Numéro – peut être un nombre décimal  
    IV. DateTime, cela signifie que la définition est de type de date. Données doivent être entrées à l’aide de ce format : jj/mm/aaaa hh : mm : AM\PM  
  5. Entrée – il s’agit d’où vous entrez la valeur de votre définition. Les valeurs saisies ici doivent être conforme au type de données choisi. Vous pouvez soit entrer une valeur unique, un ensemble de valeurs séparées par des virgules ou une plage de valeurs à l’aide du mot clé *à*. Par exemple, vous pouvez entrer la valeur unique 1 ; un jeu de 1, 2, 3 ; ou une plage de 1 à 5. Notez que la plage est pris en charge uniquement pour les nombres.
  6. Sélectionnez *OK*.

![Texte de remplacement][5]
##<a name="xml-definition"></a>Définition de XML
Si le Type de vocabulaire est choisi en tant que XML, les entrées suivantes doit être spécifiée  
  une barre d’outils.    Schéma – en cliquant sur ce ouvrira un nouvel utilisateur permettant de lame pour opérer un choix dans une liste de schémas déjà téléchargés ou permettant de télécharger un nouveau.
b.    XPATH – cette obtient d’entrée déverrouillé uniquement après sélection d’un schéma à l’étape précédente. Cliquez sur Ceci pour afficher le schéma qui a été sélectionné et permet à l’utilisateur de sélectionner le nœud pour lequel doit être créé une définition de vocabulaire.  
  c.    FAIT : cette entrée identifie l’objet de données devrait être nourris au moteur de règles pour le traitement. Ceci est une propriété avancée et par défaut est définie pour le parent de l’expression XPATH sélectionné. FAIT est particulièrement important pour les scénarios de chaînage et de la collection.

![Texte de remplacement][6]

### <a name="add-bulk"></a>Ajouter en bloc
Les étapes ci-dessus ont capturé l’expérience pour créer des définitions de vocabulaire. Une fois créés, les définitions de vocabulaire créé seront affiche sous forme de liste. Il existe des exigences pour être en mesure de générer plusieurs définitions à partir du même schéma au lieu de répéter les étapes ci-dessus chaque fois unique. Voici où la possibilité d’ajouter un bloc deviendrait très utile.
Sélectionnez « Ajouter en bloc », vous obtiendrez une nouvelle lame. La première étape consiste à sélectionner le schéma pour lequel plusieurs définitions doivent être créées. Cette option ouvre une nouvelle lame permettant que soit choisie dans la liste des schémas déjà téléchargés ou permettant de télécharger un nouveau.
Maintenant, la propriété XPath obtient déverrouillée. La sélection de cette ouvre la visionneuse des schémas où plusieurs nœuds peuvent être sélectionnés.
Les noms de définitions créées seront par défaut le nom du nœud sélectionné. Elles peuvent toujours être modifiées après la création.

![Texte de remplacement][7]

##<a name="policy-creation"></a>Création d’une stratégie
Une fois que le développeur a créé des vocabulaires requis, l’attente est que l’analyste métier serait l’une création des stratégies d’entreprise via le portail Azure.  
    1. sur l’application de règles créées, il est un objectif de stratégie en cliquant sur laquelle l’utilisateur sera insérée dans la page de création de stratégie.  
    2. cette page affiche la liste des stratégies qu'a de cette application de règles particulier. L’analyste peut ajouter une nouvelle stratégie en tapant un nom de stratégie simplement activer la touche tab deux fois. Plusieurs stratégies peuvent résider dans une seule application API de règles.  
    3. sélection de la stratégie créée prend l’utilisateur vers la page de détails de stratégie où on peut le voir les règles de la stratégie.  
    ![Texte ALT][8]
 4.  Sélectionnez « Ajouter » pour ajouter une nouvelle règle. Vous accédez alors à une nouvelle lame.

##<a name="rule-creation"></a>Création de la règle
Une règle est un ensemble d’instructions de condition et une action. Les actions sont exécutées si la condition a la valeur true. De la lame de créer une règle, donnez un nom unique de la règle (pour cette stratégie) et une description (facultative).
La zone Condition (IF) peut être utilisée pour créer des instructions conditionnelles complexes. Voici les mots clés pris en charge :  
1.  Et – opérateur conditionnel  
2.  Ou – opérateur conditionnel  
3.  est\_pas\_existe  
4.  Il existe  
5.  False  
6.  est\_égale\_pour  
7.  est\_plus\_que  
8.  est\_plus\_à\_égale\_pour  
9.  est\_dans  
10. est\_moins\_à  
11. est\_moins\_à\_égale\_pour  
12. est\_pas\_dans  
13. est\_pas\_égale\_pour  
14. Mod  
15. valeur True  

La boîte de Action(THEN) peut contenir plusieurs instructions, un par ligne, pour créer des actions qui doivent être exécutées. Voici les mots clés pris en charge :  
1.  est égal à  
2.  False  
3.  valeur True  
4.  arrêter  
5.  Mod  
6.  valeur null  
7.  mise à jour  

Les zones de condition et une action fournissent Intellisense pour vous aider à créer une règle rapidement. Cela peut être déclenchée en appuyant sur ctrl + espace ou en démarrant simplement à taper. Mots clés de la correspondance des caractères tapés seront automatiquement filtrés vers le bas et affichés. La fenêtre intellisense affiche tous les mots-clés et les définitions de vocabulaire.
![Texte de remplacement][9]

##<a name="explicit-forward-chaining"></a>Explicite de chaînage vers l’avant
Règles de BizTalk prend en charge explicite avant de chaînage, si les utilisateurs souhaitent réévaluer les règles en réponse à certaines actions, ils peuvent déclencher des cela à l’aide de certains mots clés. Les mots clés pris en charge sont les suivantes :  
   1.   mise à jour <vocabulary definition> – toutes les règles qui utilisent la définition de vocabulaire spécifié dans son état réévalue sur ce mot clé.  
   2.   Arrêt – ce mot clé s’arrête toutes les exécutions de règles

##<a name="enabledisable-rules"></a>Activer/désactiver les règles
Chaque règle de la stratégie peut être activé ou désactivé. Par défaut, toutes les règles sont activées. Les règles désactivées n’être exécutée lors de l’exécution de la stratégie. Activer/désactiver les règles peuvent être effectuées à partir de la blade de règle directement : les commandes sont disponibles dans la barre de commandes dans la partie supérieure de la lame, ou à partir de la stratégie, le menu contextuel (clic droit sur une règle) a l’option pour activer/désactiver.

##<a name="rule-priority"></a>Priorité de la règle
Toutes les règles d’une stratégie sont exécutées dans l’ordre. La priorité d’exécution est déterminée par l’ordre dans lequel ils se produisent dans la stratégie. Cette priorité peut être modifiée en faisant simplement glisser de la règle.

##<a name="test-policy"></a>Stratégie de test
Vous pouvez tester vos stratégies à l’aide de la commande « Stratégie de Test » de la lame de Test de la stratégie. Dans cette blade, vous voyez une liste de définitions de vocabulaire utilisés dans la stratégie qui nécessitent une entrée utilisateur. Les utilisateurs peuvent ajouter manuellement des valeurs pour ces entrées pour leur scénario de test. Également, les utilisateurs peuvent également choisir d’importer des entrées de test XMLs. Une fois toutes les entrées, le test peut être exécuté et les sorties pour chaque définition de vocabulaire seront affiche dans la colonne de sortie pour faciliter la comparaison. Pour afficher les journaux convivial d’analyste d’entreprise, cliquez sur « Afficher les journaux » pour afficher les journaux de l’exécution. Pour enregistrer les journaux, l’option « Enregistrer la sortie » est disponible pour stocker toutes les données liées pour l’analyse indépendante de test.

## <a name="using-rules-in-logic-apps"></a>Dans les applications de la logique à l’aide de règles
Une fois que la stratégie a été créée et testée, il est désormais prêt à la consommation. Vous pouvez créer une nouvelle application logique en sélectionnant les applications logique à partir de la partie gauche de la page d’accueil du portail. Une fois votre application logique a été créée, lancer, puis sélectionnez les *déclencheurs et les Actions*. Vous pouvez ensuite sélectionner la *créer de toutes pièces* un modèle. Suivez les étapes pour ajouter votre application d’API de règles BizTalk pour l’application de la logique. Une fois cette opération effectuée, il sera en mesure de choisir quelle application API règles (Action) cible. Les actions incluent la liste des stratégies qui doivent être exécutées. Choisissez une stratégie spécifique après laquelle les entrées requises pour la stratégie doit être donné. Les utilisateurs peuvent utiliser la sortie de l’application en aval de règles API pour la prise de décision plu.

## <a name="using-rules-via-apis"></a>À l’aide de règles via des API
L’application d’API de règles peut également être appelée à l’aide d’un ensemble riche d’API. Ainsi, les utilisateurs ne sont pas limité uniquement à l’aide de la logique d’applications et peuvent utiliser des règles dans n’importe quelle application en effectuant des appels de reste. L’API reste exacte disponibles peuvent être affichés en cliquant sur l’objectif de « Définition de l’API » dans le tableau de bord de règles.

![Texte de remplacement][10]

Voici un exemple de comment un peut utiliser cette API en langage C#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Notez que l’application d’API règles ci-dessus a la valeur « Public Anon » ses paramètres de sécurité. Cela peut être définie dans l’application de l’API à l’aide de-tous les paramètres -> Paramètres de l’Application -> niveau d’accès.

![Texte de remplacement][11]

## <a name="editing-vocabulary-and-policy"></a>Modifier le vocabulaire et stratégie
Un des principaux avantages de l’utilisation de règles métier est que les modifications apportées à la logique métier peuvent être transférées vers la production beaucoup plus rapide. Toute modification apportée aux stratégies de vocabulaire et est immédiatement appliquée dans la production. Utilisateur doit simplement accéder à la définition de vocabulaire respectifs ou la stratégie et de modifier de façon à entrer en vigueur.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
