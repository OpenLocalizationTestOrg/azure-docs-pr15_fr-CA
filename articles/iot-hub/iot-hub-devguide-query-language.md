<properties
 pageTitle="Guide du développeur - langage de requête | Microsoft Azure"
 description="Guide du développeur Azure IoT Hub - description du langage de requête utilisé pour récupérer des informations sur jumeaux, des méthodes et des tâches à partir de votre concentrateur IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Référence - langage de requête pour les travaux et jumeaux

## <a name="overview"></a>Vue d’ensemble

IoT concentrateur fournit un puissant langage de type SQL pour récupérer des informations concernant [des jumeaux de périphérique] [ lnk-twins] et [travaux][lnk-jobs]. Cet article présente :

* Présente les principales fonctionnalités de langage de requête du concentrateur IoT, et
* La description détaillée du langage.

## <a name="getting-started-with-twin-queries"></a>Mise en route avec les requêtes de double

[Des jumeaux de périphérique] [ lnk-twins] peut contenir des objets arbitraires de JSON en tant que balises et propriétés. IoT Hub permet de jumeaux de dispositif de requête sous la forme d’un seul document JSON contenant toutes les informations de double.
Par exemple, supposons que vos jumeaux de concentrateur IoT ont la structure suivante :

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT concentrateur expose le jumeaux de périphérique sous la forme d’une série de documents appelée des **périphériques**.
Par conséquent, la requête suivante extrait l’ensemble des jumeaux de périphérique :

        SELECT * FROM devices

> [AZURE.NOTE] [Kits de développement de concentrateur IoT] [ lnk-hub-sdks] prise en charge de la pagination des résultats de grande taille.

IoT Hub permet de récupérer des jumeaux filtrage avec des conditions arbitraires. Par exemple,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

Récupère le jumeaux avec la balise **location.region** définie sur **nous**.
Les opérateurs booléens et des comparaisons arithmétiques sont prises en charge, par exemple

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

Récupère tous les jumeaux situés aux États-Unis configuré pour l’envoi de télémétrie moins souvent que toutes les minutes. Plus de commodité, il est également possible d’utiliser des constantes de matrice en conjonction avec **les et les opérateurs de **NDANS** (pas dans)** . Par exemple,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

Récupère tous les jumeaux qui a signalé le Wi-Fi ou de connectivité de réseau câblé. Reportez-vous à la [clause WHERE] [ lnk-query-where] section de la référence complète des fonctionnalités de filtrage.

Regroupement et agrégations sont également pris en charge. Par exemple,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Retourne le nombre des périphériques dans l’état de chaque configuration de télémétrie.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

L’exemple ci-dessus illustre une situation où trois périphériques signalés configuration réussie, deux s’appliquent encore de la configuration et un a signalé une erreur.

### <a name="c-example"></a>Exemple C#

La fonctionnalité de requête est exposée par le [service C# SDK] [ lnk-hub-sdks] dans la la classe **RegistryManager** .
Voici un exemple d’une requête simple :

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Notez la façon dont l’objet de **requête** est instancié avec une taille de page (jusqu'à 1000), et ensuite plusieurs pages peuvent être récupérées en appelant les méthodes **GetNextAsTwinAsync** plusieurs fois.
Il est important de noter que l’objet de requête expose plusieurs **Suivant\***, selon l’option de désérialisation requise par la requête, c'est-à-dire à deux, ou objets ou Json brut à utiliser lors de l’utilisation des prévisions de la tâche.

### <a name="node-example"></a>Exemple de nœud

La fonctionnalité de requête est exposée par le [service de nœud SDK] [ lnk-hub-sdks] dans le l’objet **Registry** .
Voici un exemple d’une requête simple :

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Notez la façon dont l’objet de **requête** est instancié avec une taille de page (jusqu'à 1000), et ensuite plusieurs pages peuvent être récupérées en appelant les méthodes **nextAsTwin** plusieurs fois.
Il est important de noter que l’objet de requête expose plusieurs **Suivant\***, selon l’option de désérialisation requise par la requête, c'est-à-dire à deux, ou objets ou Json brut à utiliser lors de l’utilisation des prévisions de la tâche.

### <a name="limitations"></a>Limitations

Actuellement, les projections sont pris en charge uniquement lors de l’utilisation des agrégations, c'est-à-dire non agrégées les requêtes peuvent uniquement utiliser `SELECT *`. En outre, agrégation sont uniquement pris en charge conjointement avec le regroupement.

## <a name="getting-started-with-jobs-queries"></a>Mise en route avec les requêtes de travaux

[Tâches] [ lnk-jobs] fournissent un moyen d’exécuter des opérations sur des ensembles de périphériques. Chaque double DISPOSITIF contient les informations des tâches dont il fait partie d’une collection de **tâches**.
Logiquement,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Actuellement, cette collection est queriable en tant que **devices.jobs** dans le langage de requête IoT concentrateur.

Par exemple, pour obtenir tous les travaux (passés et planifiées) qui affectent un seul périphérique, vous pouvez utiliser la requête suivante :

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Notez comment cette requête fournit l’état spécifique au périphérique (et éventuellement la réponse de la méthode directe) de chaque tâche retournée.
Il est également possible de filtrer avec des conditions booléennes arbitraires sur toutes les propriétés des objets dans la collection **devices.jobs** .
Par exemple, la requête suivante :

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

Récupère toutes les double terminé mise à jour de tâches pour dispositif **myDeviceId** qui ont été créées après septembre 2016.

Il est également possible de récupérer les résultats de chaque périphérique d’un travail unique.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitations
Actuellement, les requêtes sur le **devices.jobs** ne gèrent pas :

* Projections, c'est-à-dire que `SELECT *` est possible ;
* Conditions relatives à la double dispositif en plus des propriétés de la tâche comme indiqué ci-dessus.
* Réaliser des agrégations, par exemple, count, avg, regroupement par.

## <a name="basics-of-an-iot-hub-query"></a>Notions de base d’une requête IoT concentrateur

Chaque requête IoT concentrateur compose d’une instruction SELECT et des clauses et par facultatif WHERE et GROUP BY clauses. Chaque requête est exécutée sur une collection de documents JSON, par exemple des jumeaux de périphérique. La clause FROM indique la collection de documents à itérer sur (**périphériques** ou **devices.jobs**). Ensuite, le filtre de la clause WHERE est appliqué. Dans le cas d’agrégations, les résultats de cette étape sont regroupées comme spécifié dans la clause GROUP BY et, pour chaque groupe, une ligne est générée comme indiqué dans la clause SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>La clause FROM

La clause **FROM < from_specification >** peut prendre que deux valeurs : **de périphériques**, jumeaux de dispositif de requête ou **de devices.jobs**, à la requête par périphérique détails.

## <a name="where-clause"></a>Clause WHERE

Le **où < filter_condition >** clause est facultative. Il spécifie l’ou les conditions que les documents de la collection de JSON doivent satisfaire pour être inclus dans le résultat. N’importe quel document JSON doit évaluer les conditions spécifiées à « true » à inclure dans le résultat.

Les conditions autorisées sont décrites dans la section [des Expressions et des conditions][lnk-query-expressions].

## <a name="select-clause"></a>Clause SELECT

La clause SELECT (**Sélectionnez < liste_de_sélection >**) est obligatoire et spécifie les valeurs qui seront extraites à partir de la requête. Il spécifie les valeurs JSON permet de générer de nouveaux objets JSON pour chaque élément du sous-ensemble filtré (et éventuellement regroupé) de la collection de la phase de projection génère un nouvel objet JSON, construit avec les valeurs spécifiées dans la clause SELECT.

Il s’agit de la grammaire de la clause SELECT :

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

où **attribute_name** fait référence à n’importe quelle propriété du document dans la collection de JSON. Vous trouverez quelques exemples de clauses SELECT dans la [mise en route avec les requêtes twin] [ lnk-query-getstarted] section.

Actuellement, les clauses de sélection différentes de **Sélectionnez \* ** sont pris en charge uniquement dans les requêtes d’agrégation sur jumeaux.

## <a name="group-by-clause"></a>Clause GROUP BY

La clause **GROUP BY < group_specification >** est une étape facultative qui peut être exécutée après le filtre spécifié dans la clause WHERE et avant la projection spécifiée dans l’instruction SELECT. Il regroupe les documents en fonction de la valeur d’un attribut. Ces groupes sont utilisés pour générer des valeurs agrégées comme spécifié dans la clause SELECT.

Est un exemple de requête utilisant GROUP BY :

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

La syntaxe formelle pour grouper par est :

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

où **attribute_name** fait référence à n’importe quelle propriété du document dans la collection de JSON. 

Actuellement, la clause GROUP BY est uniquement pris en charge lors de l’interrogation des jumeaux.

## <a name="expressions-and-conditions"></a>Les conditions et les expressions

À un niveau élevé, une *expression*:

* Correspond à une instance d’un type JSON (c'est-à-dire booléen, nombre, chaîne, tableau ou objet), et
* Est défini par la manipulation de données provenant du document JSON de périphérique et constantes à l’aide des fonctions et opérateurs intégrés.

*Les conditions* sont des expressions dont le résultat est une valeur booléenne, c'est-à-dire n’importe quelle constante différente de la valeur booléenne **true** est considéré comme **false** (y compris **null**, **undefined**, toute instance d’objet ou un tableau, n’importe quelle chaîne et clairement Boolean **false**).

La syntaxe des expressions est la suivante :

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

où :

| Symbole | Définition |
| -------------- | -----------------|
| nom_attribut | Toutes les propriétés du document dans la collection de JSON. |
| UNARY_OPERATOR | Un opérateur unaire, en fonction de la section opérateurs. |
| Opérateur_binaire | Tout opérateur binaire en fonction de la section opérateurs. |
| decimal_literal | Valeur float exprimée en notation décimale. |
| hexadecimal_literal | Un nombre exprimé par la chaîne « 0 x », suivi d’une chaîne de chiffres hexadécimaux. |
| string_literal | Les littéraux de chaîne sont des chaînes Unicode représentés par une séquence de zéro ou plusieurs caractères Unicode ou des séquences d’échappement. Les littéraux de chaîne sont placées entre guillemets simples (apostrophe : ') ou guillemets (guillemet : »). Autorisé d’échappement : `\'`, `\"`, `\\`, `\uXXXX` pour les caractères Unicode définies par 4 chiffres hexadécimaux. |

### <a name="operators"></a>Opérateurs

Les opérateurs suivants sont pris en charge :

| Famille | Opérateurs |
| -------------- | -----------------|
| Opérations arithmétiques | +,-,*,/,% |
| Logique | ET, OU, PAS |
| Comparaison |  =, ! =, <>,, < =, > =, <> |

## <a name="next-steps"></a>Étapes suivantes

Apprenez à exécuter des requêtes dans vos applications à l’aide du [SDK de concentrateur IoT][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md