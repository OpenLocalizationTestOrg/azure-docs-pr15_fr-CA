<properties
   pageTitle="Azure Connect de AD sync : présentation de l’architecture | Microsoft Azure"
   description="Cette rubrique décrit l’architecture de synchronisation d’Azure Connect de AD et explique les termes utilisés."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure Connect de AD sync : présentation de l’architecture
Cette rubrique décrit l’architecture de base pour la synchronisation Azure Connect d’Active Directory. Dans de nombreux aspects, il est similaire à ses prédécesseurs MIIS 2003, ILM 2007 et FIM 2010. Azure Connect de AD sync est l’évolution de ces technologies. Si vous êtes familiarisé avec ces technologies antérieures, le contenu de cette rubrique sera également familière. Si vous ne connaissez pas la synchronisation, cette rubrique est pour vous. Il n’est toutefois pas besoin de connaître les détails de cette rubrique pour réussir à effectuer des personnalisations à synchroniser Azure AD Connect (appelé le moteur de synchronisation dans cette rubrique).

## <a name="architecture"></a>Architecture
Le moteur de synchronisation crée une vue intégrée des objets qui sont stockées dans plusieurs sources de données connectées et gère les informations d’identité dans les sources de données. Cette vue intégrée est déterminée par les informations d’identité extraites des sources de données connectées et un ensemble de règles qui déterminent le mode de traitement de ces informations.

### <a name="connected-data-sources-and-connectors"></a>Sources de données connectées et connecteurs
Le moteur de synchronisation traite les informations d’identité à partir de référentiels de données différentes, comme Active Directory ou une base de données SQL Server. Chaque référentiel de données qui organise ses données dans un format de base de données et qui fournit les méthodes standard d’accès aux données est un candidat potentiel de source données pour le moteur de synchronisation. Les référentiels de données sont synchronisées avec le moteur de synchronisation sont appelés des **sources de données connectées** ou les **annuaires connectés** (CD).

Le moteur de synchronisation encapsule l’interaction avec une source de données connectée au sein d’un module appelé **connecteur**. Chaque type de source de données connectée a un connecteur spécifique. Le connecteur se traduit par une opération requise dans le format qui comprend de la source de données connectée.

Connecteurs effectuer des appels d’API pour échanger des informations d’identité (à la fois en lecture et écriture) avec une source de données connectée. Il est également possible d’ajouter un lien personnalisé à l’aide de l’infrastructure de connectivité extensibles. L’illustration suivante montre comment un connecteur connecte une source de données connectée au moteur de synchronisation.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Données peuvent circuler dans les deux sens, mais il ne peut pas circuler dans les deux directions simultanément. En d’autres termes, un connecteur peut être configuré pour autoriser des données de circuler à partir de la source de données connectée au moteur de synchronisation ou de moteur de synchronisation pour la source de données connectée, mais uniquement une de ces opérations peut se produire à tout moment pour un objet et d’attribut. La direction peut être différente pour différents objets et des attributs différents.

Pour configurer un connecteur, vous spécifiez les types d’objets que vous souhaitez synchroniser. Spécifier les types d’objet définit la portée d’objets qui sont inclus dans le processus de synchronisation. L’étape suivante consiste à sélectionner les attributs à synchroniser, qui est connu en tant que liste d’inclusion d’un attribut. Ces paramètres peuvent être modifiés à tout moment en réponse à des modifications apportées aux règles de votre entreprise. Lorsque vous utilisez l’Assistant d’installation Azure Connect d’Active Directory, ces paramètres sont configurés pour vous.

Pour exporter des objets à une source de données connectée, la liste d’inclusion attribut doit inclure au moins les attributs minimaux requis pour créer un type d’objet spécifique dans une source de données connectée. Par exemple, l’attribut **sAMAccountName** doit être inclus dans la liste d’inclusion pour exporter un objet utilisateur dans Active Directory parce que tous les objets utilisateur dans Active Directory doivent avoir un attribut **sAMAccountName** défini par l’attribut. Encore une fois, l’Assistant installation effectue cette configuration pour vous.

Si la source de données connectée utilise des éléments de construction, comme des partitions ou des conteneurs pour organiser des objets, vous pouvez limiter les zones de la source de données connectée qui sont utilisés pour une solution donnée.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Structure interne de l’espace de noms de moteur de synchronisation
L’espace de noms de moteur de synchronisation complète se compose de deux espaces de noms qui stockent les informations d’identité. Les deux espaces de noms sont les suivants :

- L’espace de connecteur (CS)
- Le méta-verse (MV)

L' **espace de connecteur** est une zone de transit et contenant les représentations des objets désignés à partir d’une source de données connectée et les attributs spécifiés dans la liste d’inclusion attribut. Le moteur de synchronisation utilise l’espace de connecteur pour déterminer ce qui a changé dans la source de données connectée et à préparer les modifications entrantes. Le moteur de synchronisation utilise également l’espace de connecteur pour reclasser les modifications sortantes pour l’exportation vers la source de données connectée. Le moteur de synchronisation tient à jour un espace connecteur distinct sous la forme d’une zone de transit pour chaque connecteur.

À l’aide de la zone de transit, le moteur de synchronisation reste indépendant des sources de données connectées et n’est pas affecté par leur disponibilité et leur accessibilité. Par conséquent, vous pouvez traiter les informations d’identité à tout moment en utilisant les données dans la zone de transit. Le moteur de synchronisation peut demander uniquement les modifications apportées à l’intérieur de la source de données connectés depuis la dernière session de communication terminée ou push uniquement les modifications d’informations d’identité qui n’a pas encore reçu la source de données connectée, qui réduisant le trafic réseau entre le moteur de synchronisation et de la source de données connectée.

En outre, moteur de synchronisation stocke les informations d’état sur tous les objets qu’il stades dans l’espace de connecteur. Lors de la réception de nouvelles données, le moteur de synchronisation prend toujours la valeur si les données ont déjà été synchronisées.

Le **méta-verse** est une zone de stockage qui contient les informations d’identité agrégées à partir de plusieurs sources de données connectées, fournissant ainsi une vue globale et intégrée unique de tous les objets combinés. Les objets métaverse sont créés avec les informations d’identité qui sont récupérées à partir de sources de données connectées et un ensemble de règles qui vous permettent de personnaliser le processus de synchronisation.

L’illustration suivante montre les espaces de noms espace connecteur et le métaverse dans le moteur de synchronisation.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objets d’identité de moteur de synchronisation
Les objets dans le moteur de synchronisation sont des représentations de deux objets dans la source de données connectée ou l’affichage intégré que le moteur de synchronisation a de ces objets. Chaque objet de moteur de synchronisation doit avoir un identificateur global unique (GUID). GUID fournissent l’intégrité des données et express relations entre les objets.

### <a name="connector-space-objects"></a>Objets espace de connecteur
Lorsque le moteur de synchronisation communique avec une source de données connectée, il lit les informations d’identité de la source de données connectée et utilise ces informations pour créer une représentation de l’objet identity de l’espace de connecteur. Impossible de créer ou de supprimer ces objets individuellement. Toutefois, vous pouvez supprimer manuellement tous les objets dans un espace de connecteur.

Tous les objets dans l’espace de connecteur ont deux attributs :

- Un identificateur global unique (GUID)
- Un nom unique (également appelé DN)

Si la source de données connectée affecte un attribut unique à l’objet, les objets dans l’espace de connecteur peuvent également avoir un attribut d’ancrage. L’attribut d’ancrage identifie de manière unique un objet dans la source de données connectée. Le moteur de synchronisation utilise l’ancre pour localiser la représentation correspondante de cet objet dans la source de données connectée. Moteur de synchronisation suppose que le point d’ancrage d’un objet ne modifie jamais la durée de vie de l’objet.

Un identificateur unique connu la plupart des connecteurs permet de générer une ancre automatiquement pour chaque objet lors de son importation. Par exemple, le connecteur Active Directory utilise l’attribut **objectGUID** d’un point d’ancrage. Pour les sources de données connectées qui ne fournissent pas un identificateur unique clairement défini, vous pouvez spécifier la génération de point d’ancrage dans le cadre de la configuration du connecteur.

Dans ce cas, le point d’ancrage est construit à partir d’un ou plusieurs attributs uniques d’un objet, ni les modifications et identifiant de manière unique identifie l’objet dans l’espace de connecteur (par exemple, un numéro d’employé ou un ID utilisateur).

Un objet espace connecteur peut être une des opérations suivantes :

- Un objet de zone de transit
- Un espace réservé

### <a name="staging-objects"></a>Objets de mise en attente
Un objet intermédiaire représente une instance des types d’objet désigné de la source de données connectée. Outre le GUID et le nom unique, un objet de zone de transit a toujours une valeur qui indique le type d’objet.

Les objets de mise en attente qui ont été importés de toujours ont une valeur pour l’attribut d’ancrage. Les objets de mise en attente qui ont été nouvellement approvisionnés par le moteur de synchronisation et qui sont en cours de création dans la source de données connectée n’ont pas d’une valeur pour l’attribut d’ancrage.

Les objets mis en œuvre aussi comporter des valeurs en cours des attributs de l’entreprise et des informations opérationnelles nécessaires par le moteur de synchronisation pour effectuer le processus de synchronisation. Informations opérationnelles incluent des indicateurs qui indiquent le type de mises à jour qui sont transférées sur l’objet de zone de transit. Si un objet de zone de transit a reçu de nouvelles informations d’identité à partir de la source de données connectée qui n’a pas encore été traitée, l’objet est marqué comme **importation en attente**. Si un objet de zone de transit a de nouvelles informations d’identité qui n’ont pas encore été exportées vers la source de données connectée, elle est signalée comme **une exportation en attente**.

Un objet de zone de transit peut être un objet d’importation ou une exportation. Le moteur de synchronisation crée un objet d’importation à l’aide des informations de l’objet reçues à partir de la source de données connectée. Lorsque le moteur de synchronisation reçoit des informations sur l’existence d’un nouvel objet qui correspond à l’un des types d’objet sélectionnés dans le connecteur, il crée un objet d’importation dans l’espace de connecteur comme une représentation de l’objet dans la source de données connectée.

L’illustration suivante montre un objet d’importation qui représente un objet dans la source de données connectée.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Le moteur de synchronisation crée un objet de l’exportation à l’aide des informations de l’objet dans le méta-verse. Exporter des objets sont exportés vers la source de données connectée au cours de la prochaine session de communication. Du point de vue du moteur de synchronisation, exporter des objets n’existent pas dans la source de données connectée encore. Par conséquent, l’attribut d’ancrage d’un objet d’exportation n’est pas disponible. Après avoir reçu l’objet de moteur de synchronisation, la source de données connectée crée une valeur unique pour l’attribut d’ancrage de l’objet.

L’illustration suivante montre comment un objet de l’exportation est créé à l’aide des informations d’identité dans le métaverse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Le moteur de synchronisation confirme l’exportation de l’objet par réimporter l’objet à partir de la source de données connectée. Exporter des objets deviennent des objets d’importation lorsque le moteur de synchronisation les reçoit au cours de la prochaine importation provenant de cette source de données connectée.

### <a name="placeholders"></a>Espaces réservés
Le moteur de synchronisation utilise un espace de noms plat pour stocker des objets. Toutefois, certaines sources de données connectées, telles que Active Directory utilisent un espace de noms hiérarchique. Pour transformer les informations à partir d’un espace de noms hiérarchique en un espace de noms plat, le moteur de synchronisation utilise des espaces réservés à conserver la hiérarchie.

Chaque espace réservé représente un composant (par exemple, une unité d’organisation) d’un nom d’objet hiérarchique qui n’ont pas été importé dans le moteur de synchronisation mais qui est nécessaire pour construire le nom hiérarchique. Ils combler les lacunes créées par des références à des objets qui ne sont pas mis en œuvre les objets dans l’espace de connecteur dans la source de données connectée.

Le moteur de synchronisation utilise également des espaces réservés pour stocker des objets référencés n’ont pas encore été importés. Par exemple, si la synchronisation est configurée pour inclure l’attribut de gestionnaire pour l’objet *Abbie Spencer* et la valeur reçue est un objet qui n’a pas été importé encore, comme *CN = Lee Sperry, CN = utilisateurs, DC = fabrikam, DC = com*, les informations du gestionnaire sont stockées en tant qu’espaces réservés dans l’espace de connecteur. Si l’objet de gestionnaire est importé ultérieurement, l’objet espace réservé est remplacé par l’objet de zone de transit qui représente le gestionnaire.

### <a name="metaverse-objects"></a>Objets du métaverse
Un objet du méta-verse contient l’affichage agrégé ce moteur de synchronisation a des objets dans l’espace de connecteur intermédiaires. Moteur de synchronisation crée des objets du métaverse dans les objets d’importation à l’aide des informations. Plusieurs objets espace de connecteur peuvent être liés à un objet du méta-verse unique, mais un objet espace connecteur ne peut pas être lié à plusieurs objets du métaverse.

Objets du métaverse ne peut pas être créés manuellement ou supprimés. Le moteur de synchronisation supprime automatiquement les objets métaverse qui n’ont pas un lien vers n’importe quel objet espace de connecteur dans l’espace de connecteur.

Pour mapper des objets dans une source de données connectée à un type d’objet correspondant dans le métaverse, moteur de synchronisation fournit un schéma extensible avec un ensemble prédéfini de types d’objet et attributs associés. Vous pouvez créer de nouveaux types d’objets et d’attributs pour les objets du métaverse. Des attributs peuvent être à valeur unique ou à valeurs multiples et les types d’attributs peuvent être des chaînes, des références, des nombres et des valeurs booléennes.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relations entre les objets intermédiaires et objets du métaverse
L’espace de noms du moteur de synchronisation, le flux de données est activé par la relation de liaison entre les objets de zone de transit et des objets du métaverse. Un objet de zone de transit qui est lié à un objet du méta-verse est appelé un **joint d’objet** (ou **objet connecteur**). Un objet de zone de transit n’est pas lié à un objet du méta-verse est appelé **disjoint d’objet** (ou **objet de sectionneur**). Les termes joint et disjoint sont préférables à la ne confondez pas avec les connecteurs responsables de l’importation et l’exportation de données à partir d’un annuaire connecté.

Espaces réservés ne sont jamais liées à un objet du méta-verse

Un objet joint comprend un objet de zone de transit et de ses relations liées à un objet seul métaverse. Objets joints sont utilisées pour synchroniser les valeurs d’attributs entre un objet espace de connecteur et un objet du méta-verse.

Lorsqu’un objet intermédiaire devient un objet joint lors de la synchronisation, les attributs peuvent circuler entre l’objet de mise en attente et l’objet du métaverse. Flux des attributs est bidirectionnelle et est configuré à l’aide de règles d’attribut d’importation et exportation attribut.

Un objet espace connecteur unique peut être lié à un seul objet du métaverse. Cependant, chaque objet du méta-verse peut être lié à plusieurs objets espace de connecteur dans le même ou dans les locaux de l’autre connecteur, comme indiqué dans l’illustration suivante.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

La relation entre des liens entre l’objet de zone de transit et un objet du méta-verse est persistante et peut être supprimée uniquement par les règles que vous spécifiez.

Un un objet est un objet de zone de transit n’est pas lié à n’importe quel objet du métaverse. L’attribut de valeurs d’un objet indépendantes ne sont pas traités les pages dans le métaverse. Les valeurs d’attribut de l’objet correspondant dans la source de données connectée ne sont pas mis à jour par le moteur de synchronisation.

À l’aide d’un objets, vous pouvez stocker des informations d’identité dans le moteur de synchronisation et le traiter ultérieurement. Conserver un objet de zone de transit en tant qu’un objet dans l’espace de connecteur présente de nombreux avantages. Étant donné que le système a déjà présente les informations requises sur cet objet, il n’est pas nécessaire de créer une représentation sous forme de cet objet à nouveau lors de la prochaine importation à partir de la source de données connectée. De cette manière, le moteur de synchronisation a toujours une capture instantanée complète de la source de données connectée, même s’il n’existe aucune connexion active à la source de données connectée. Des objets indépendantes peuvent être convertis en objets joints et vice versa, en fonction des règles que vous spécifiez.

Un objet d’importation est créé sous forme d’un objet. Un objet d’exportation doit être un objet joint. La logique du système applique cette règle et supprime chaque objet de l’exportation qui n’est pas un objet joint.

## <a name="sync-engine-identity-management-process"></a>Processus de gestion des identités synchronisation moteur
Le processus de gestion d’identité détermine comment les informations d’identité sont mis à jour entre les sources de données connectées. Gestion d’identité se produit dans trois processus :

- Importation
- Synchronisation
- Exporter

Pendant le processus d’importation, le moteur de synchronisation évalue les informations d’identité entrante à partir d’une source de données connectée. Lorsque des modifications sont détectées, il crée de nouveaux objets intermédiaires ou met à jour des objets de zone de transit existantes dans l’espace de connecteur pour la synchronisation.

Pendant le processus de synchronisation, moteur de synchronisation met à jour le métaverse pour refléter les modifications qui ont eu lieu dans l’espace de connecteur et mises à jour de l’espace de connecteur pour refléter les modifications qui sont sont produites dans le métaverse.

Pendant le processus d’exportation, moteur de synchronisation exécute un push sur des modifications qui sont montés sur des objets de la zone de transit et qui sont signalés en attente d’exportation.

L’illustration suivante montre où chaque processus se produit en tant que flux de d’informations d’identité à partir d’une source de données connectée à un autre.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processus d’importation
Pendant le processus d’importation, le moteur de synchronisation évalue les mises à jour des informations d’identité. Moteur de synchronisation compare les informations d’identité provenance de la source de données connectée avec les informations d’identité sur un objet de zone de transit et détermine si l’objet de zone de transit requiert des mises à jour. S’il est nécessaire de mettre à jour l’objet de zone de transit avec de nouvelles données, l’objet de zone de transit est signalée en attente d’importation.

Par l’intermédiaire des objets dans l’espace connecteur avant la synchronisation, le moteur de synchronisation peut traiter uniquement les informations d’identité qui a été modifié. Ce processus offre les avantages suivants :

- **Synchronisation efficace**. La quantité de données traitées pendant la synchronisation est réduite.
- **Resynchronisation efficace**. Vous pouvez modifier comment le moteur de synchronisation traite les informations d’identité sans vous reconnecter à la source de données, le moteur de synchronisation.
- **Permet d’afficher un aperçu de la synchronisation**. Vous pouvez afficher un aperçu de la synchronisation pour vérifier que vos hypothèses sur le processus de gestion d’identité sont corrects.

Pour chaque objet spécifié dans le connecteur, le moteur de synchronisation essaie d’abord de localiser une représentation de l’objet dans l’espace de connecteur du connecteur. Moteur de synchronisation examine tous les objets de mise en attente dans l’espace de connecteur et tente de trouver un objet de zone de transit correspondant qui a un attribut d’ancrage correspondant. Si aucun objet de zone de transit existante ne possède un attribut d’ancrage correspondant, le moteur de synchronisation essaie de trouver un objet intermédiaire correspondant portant le même nom unique.

Lorsque le moteur de synchronisation détecte un objet intermédiaire correspondant par un nom unique, mais pas par un point d’ancrage, le comportement spécial suivant se produit :

- Si l’objet que se trouve dans l’espace de connecteur n’a aucun point d’ancrage, le moteur de synchronisation supprime cet objet de l’espace de connecteur et marque l’objet du métaverse lié comme **recommencer la mise en service de la synchronisation suivante**. Puis il crée le nouvel objet d’importation.
- Si l’objet que se trouve dans l’espace de connecteur a un point d’ancrage, le moteur de synchronisation suppose que cet objet a été renommé ou supprimé dans l’annuaire connecté. Il assigne un temporaire, nouveau nom unique de l’objet espace de connecteur afin qu’il peut préparer l’objet entrant. L’ancien objet devient alors **en régime transitoire**, en attente pour le connecteur importer le changement de nom ou de la suppression pour résoudre le problème.

Si le moteur de synchronisation recherche un objet de zone de transit qui correspond à l’objet spécifié dans le connecteur, il détermine le type de modifications à appliquer. Par exemple, le moteur de synchronisation peut renommer ou supprimer l’objet de la source de données connectée, ou il peut uniquement mettre à jour les valeurs d’attribut de l’objet.

Objets mis en œuvre avec des données mises à jour sont marquées en attente d’importation. Différents types d’en attente d’importations sont disponibles. En fonction du résultat du processus d’importation, un objet de mise en attente dans l’espace de connecteur a une des valeurs suivantes en attente de types d’importation :

- **Aucun**. Aucune modification à un des attributs de l’objet de zone de transit n’est disponibles. Moteur de synchronisation ne marque pas de ce type en attente d’importation.
- **Ajouter**. L’objet de zone de transit est un nouvel objet d’importation dans l’espace de connecteur. Les indicateurs de moteur de synchronisation de ce type en attente d’importation pour tout traitement supplémentaire dans le métaverse.
- **Mise à jour**. Moteur de synchronisation détecte un objet intermédiaire correspondant dans l’espace de connecteur et indicateurs de ce type comme en attente d’importation afin que les mises à jour les attributs peuvent être traités dans le métaverse. Mises à jour incluent le changement de nom d’objet.
- **Supprimer**. Moteur de synchronisation détecte un objet intermédiaire correspondant dans l’espace de connecteur et indicateurs de ce type comme en attente d’importation afin que l’objet joint peut être supprimé.
- **Supprimer/ajouter**. Moteur de synchronisation détecte un objet intermédiaire correspondant dans l’espace du connecteur, mais les types d’objets ne correspondent pas. Dans ce cas, une suppression d’ajout de modification est mis en place. A supprimer-ajouter modification indique au moteur de synchronisation qu’une resynchronisation complète de cet objet doit avoir lieu, car les jeux différents de règles s’appliquent à cet objet et lors de modifications du type de l’objet.

En définissant le statut de l’importation en attente d’un objet de zone de transit, il est possible de réduire de manière significative la quantité de données traitées pendant la synchronisation, car vous pouvez ainsi le système traiter uniquement les objets qui ont mis à jour des données.

### <a name="synchronization-process"></a>Processus de synchronisation
Synchronisation se compose de deux processus connexes :

- Synchronisation entrante, lorsque le contenu du méta-verse est mis à jour en utilisant les données dans l’espace de connecteur.
- Synchronisation sortante, lorsque le contenu de l’espace de connecteur est mis à jour à l’aide de données dans le métaverse.

En utilisant les informations placées dans l’espace connecteur, le processus de synchronisation entrante crée dans le métaverse l’affichage intégré des données qui sont stockées dans des sources de données connectées. Tous les objets de mise en attente ou uniquement ceux dont une importation en attente d’informations sont regroupées en fonction de la manière dont les règles sont configurées.

Les mises à jour du processus de synchronisation sortants exporter des objets lors de la modifient des objets du métaverse.

Synchronisation entrante crée la vue intégrée dans le métaverse, les informations d’identité qui sont reçus à partir de sources de données connectées. Moteur de synchronisation peut traiter les informations d’identité à tout moment en utilisant les toutes dernières informations d’identité qu’il a à partir de la source de données connectée.

**Synchronisation entrante**

Synchronisation entrante inclut les processus suivants :

- **Disposition** (également appelée **Projection** s’il est important de distinguer ce processus à partir de la mise en service de synchronisation sortante). Le moteur de synchronisation crée un nouvel objet de méta-verse basé sur un objet de zone de transit et les relie. Est une opération au niveau de l’objet.
- **Joindre**. Le moteur de synchronisation relie un objet intermédiaire à un objet de méta-verse existant. Une jointure est une opération au niveau de l’objet.
- **Flux des attributs importation**. Moteur de synchronisation met à jour les valeurs d’attribut, appelés flux des attributs de l’objet dans le méta-verse. Flux des attributs importation est une opération au niveau de l’attribut qui requiert un lien entre un objet de zone de transit et un objet du méta-verse.

Est le seul processus qui crée des objets dans le métaverse. Disposition affecte uniquement importer des objets qui sont indépendantes des objets. Au cours de la fourniture, le moteur de synchronisation crée un objet de méta-verse qui correspond au type d’objet de l’objet de l’importation et établit un lien entre les deux objets, créant ainsi un objet joint.

Le processus de jointure établit également un lien entre importer des objets et un objet du méta-verse. La différence entre la jointure et disposition est que le processus de jointure requiert que l’objet d’importation sont liés à un objet du méta-verse, où le processus de disposition crée un nouvel objet de méta-verse.

Moteur de synchronisation essaie de joindre un objet d’importation pour un objet du méta-verse à l’aide des critères spécifiés dans la configuration de la règle de la synchronisation.

Au cours du processus de fourniture et de jointure, moteur de synchronisation lie un un objet à un objet du méta-verse, ce qui les rend joint. Après que ces opérations au niveau de l’objet sont terminées, le moteur de synchronisation peut mettre à jour les valeurs d’attribut de l’objet du métaverse associé. Ce processus est appelé flux des attributs importation.

Flux des attributs importation se produit sur tous les objets d’importation comportant de nouvelles données qui sont liées à un objet du méta-verse.

**Synchronisation sortante**

Mises à jour de synchronisation sortants exporter des objets lorsqu’un objet du méta-verse modifier mais n’est pas supprimé. L’objectif de synchronisation sortante est d’évaluer si les modifications apportées aux objets du métaverse nécessitent des mises à jour de la zone de transit d’objets dans l’espace de connecteur. Dans certains cas, les modifications peuvent demander ce transit mis à jour les objets dans tous les espaces du connecteur. Les objets de mise en attente qui ont été modifiées sont signalées en attente d’exportation, ce qui les rend d’exporter des objets. Ces objets sont ensuite transférées à la source de données connectée pendant le processus d’exportation d’exportation.

Synchronisation sortante a trois processus :

- **Mise en service**
- **Mise hors service**
- **Exporter le flux des attributs**

Mise en service et hors service sont deux opérations au niveau de l’objet. Mise hors service dépend de la mise en service de provisionnement seulement peut enclencher il. Mise hors service est déclenchée lors de la mise en service supprime le lien entre un objet du méta-verse et un objet de l’exportation.

Mise en service se déclenche toujours lorsque les modifications sont appliquées à des objets dans le métaverse. Lorsque des modifications sont apportées aux objets du métaverse, moteur de synchronisation pouvez effectuer l’une des tâches suivantes dans le cadre du processus de mise en service :

- Créer des objets joints, où un objet du méta-verse est lié à un objet d’exportation nouvellement créé.
- Renommer un objet joint.
- Disjoindre les liens entre un objet du méta-verse et le test des objets, création d’un objet indépendantes.

Si le provisionnement nécessite l’utilisation de moteur de synchronisation pour créer un nouvel objet de connecteur, l’objet de zone de transit à laquelle l’objet du métaverse est liée est toujours un objet de l’exportation, car l’objet n’existe pas encore dans la source de données connectée.

Si le provisionnement nécessite l’utilisation de disjoindre un objet joint, création d’un objet indépendantes, moteur de synchronisation de la mise hors service est déclenchée. Le processus d’annulation supprime l’objet.

Au cours de la mise hors service, la suppression d’un objet de l’exportation ne supprime pas physiquement l’objet. L’objet est marqué comme **supprimé**, ce qui signifie que l’opération de suppression est mis en place sur l’objet.

Flux des attributs exportation se produit également durant le processus de synchronisation sortants, similaire à celle que les flux des attributs importation se produit lors de la synchronisation entrante. Flux des attributs exportation se produit uniquement entre le métaverse et exporter les objets qui sont jointes.

### <a name="export-process"></a>Processus d’exportation
Pendant le processus d’exportation, le moteur de synchronisation examine tous les objets d’exportation qui sont signalés en attente d’exportation dans l’espace connecteur, puis envoie des mises à jour à la source de données connectée.

Le moteur de synchronisation peut déterminer la réussite d’une exportation, mais il ne peut pas suffisamment de déterminer que le processus de gestion d’identité est terminé. Objets dans la source de données connectée peuvent toujours être modifiés par d’autres processus. Étant donné que le moteur de synchronisation ne dispose pas d’une connexion permanente à la source de données connectée, il n’est pas suffisant de faire des hypothèses sur les propriétés d’un objet dans la source de données connectée basée uniquement sur une notification de réussite de l’exportation.

Par exemple, un processus dans la source de données connectée peut modifier les attributs de l’objet à leur valeur d’origine (c'est-à-dire, de la source de données connectée pourrait écraser des valeurs immédiatement après que les données sont transférées par le moteur de synchronisation et appliquées avec succès dans la source de données connectée).

Les magasins de moteur de synchronisation exporter et importer des informations d’état sur chaque objet de zone de transit. Si les valeurs des attributs qui sont spécifiés dans la liste d’inclusion attribut ont changé depuis la dernière exportation, le stockage d’importation et exportation de moteur de synchronisation permet de statut pour réagir de façon appropriée. Moteur de synchronisation utilise le processus d’importation pour confirmer les valeurs d’attributs qui ont été exportés vers la source de données connectée. Une comparaison entre les informations importées et exportées, comme indiqué dans l’illustration suivante, Active le moteur de synchronisation pour déterminer si l’exportation a réussi ou si elle doit être répétée.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Par exemple, si le moteur de synchronisation exporte l’attribut C, qui a une valeur de 5, à une source de données connectée, elle stocke C = 5 dans sa mémoire de statut d’exportation. Chaque exportation supplémentaires de cet objet entraîne une tentative d’exportation C = 5 à la source de données connectée à nouveau, car le moteur de synchronisation suppose que cette valeur n'a pas été régulièrement appliquée à l’objet (c'est-à-dire, sauf si une valeur différente a été importée récemment à partir de la source de données connectée). La mémoire de l’exportation est désactivée lors de la réception au cours d’une opération d’importation sur l’objet C = 5.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration [d’Azure AD connexion de synchronisation](active-directory-aadconnectsync-whatis.md) .

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
