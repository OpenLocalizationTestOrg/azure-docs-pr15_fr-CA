<properties
   pageTitle="Connecteur pour Lotus Domino | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur Microsoft pour Lotus Domino."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="lotus-domino-connector-technical-reference"></a>Référence technique de Lotus Domino connecteur
Cet article décrit le connecteur pour Lotus Domino. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Doit utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Vue d’ensemble du connecteur Lotus Domino
Le connecteur pour Lotus Domino vous permet d’intégrer le service de synchronisation avec le serveur de Lotus Domino d’IBM.

À partir d’une perspective de haut niveau, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

Fonctionnalité | Prise en charge
--- | ---
Source de données connectée | Serveur : <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Client :<li>Lotus Notes 9.x</li>
Scénarios | <li>Gestion du cycle de vie des objets</li><li>Gestion de groupe</li><li>Gestion de mot de passe</li>
Opérations | <li>Complet et importation de Delta</li><li>Exporter</li><li>Définir et modifier le mot de passe mot de passe HTTP</li>
Schéma | <li>Personne (utilisateur itinérants, Contact (personnes sans certificat))</li><li>Groupe</li><li>Ressource (réunion en ligne de la ressource, espace)</li><li>Messages dans la base de données</li><li>Découverte dynamique des attributs pour les objets pris en charge</li>

Le connecteur de Lotus Domino utilise le client Lotus Notes pour communiquer avec le serveur Lotus Domino. En raison de cette dépendance, une prise en charge le Client Lotus Notes doit être installé sur le serveur de synchronisation. La communication entre le client et le serveur est implémentée par le biais de l’interface Lotus Notes .NET Interop (Interop.domino.dll). Cette interface facilite la communication entre la plate-forme Microsoft.NET et le client Lotus Notes et prend en charge de l’accès aux documents de Lotus Domino et les vues. Pour l’importation de delta, il est également possible que l’interface native C++ est utilisé (selon la méthode d’importation delta sélectionnée).

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou une version ultérieure
- Le client Lotus Notes doit être installé sur le serveur de synchronisation
- Le connecteur pour Lotus Domino nécessite la par défaut Lotus Domino LDAP schéma de base de données (schema.nsf) se trouvent sur le serveur de l’annuaire Domino. Si elle n’est pas présent, vous pouvez l’installer en exécutant ou en redémarrant le service LDAP sur le serveur Domino.

### <a name="connected-data-source-permissions"></a>Autorisations de Source de données connectées
Pour effectuer les tâches prises en charge dans le connecteur de Lotus Domino, vous devez être membre des groupes suivants :

- Administrateurs de l’accès complets
- Administrateurs
- Administrateurs de base de données

Le tableau suivant répertorie les autorisations qui sont obligatoires pour chaque opération :

Opération | Droits d’accès
--- | ---
Importation | <li>Lire les documents publics</li><li> Accès administrateur intégral (lorsque vous êtes membre du groupe Administrateurs de l’accès complet, vous avez automatiquement l’accès efficace aux ACL.)</li>
Exporter et définir le mot de passe | Accès efficace : <li>Créer des documents</li><li>Supprimer des documents</li><li>Lire les documents publics</li><li>Écrire des documents publics</li><li>Répliquer ou copie de documents</li>Pour les opérations d’exportation, vous devez également les rôles suivants : <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Les opérations directes et AdminP
Opérations soit, passez directement à l’annuaire Domino via le processus AdminP. Les tableaux ci-après objets de la liste de toutes les prises en charge, opérations et, le cas échéant, la méthode de mise en œuvre associée :

**Carnet d’adresses principal**

Objet | Créer | Mise à jour | Supprimer
--- | --- | --- | ---
Personne | AdminP | Direct | AdminP
Groupe | AdminP | Direct | AdminP
MailInDB | Direct | Direct | Direct
Ressources | AdminP | Direct | AdminP

**Carnet d’adresses secondaires**

Objet | Créer | Mise à jour | Supprimer
--- | --- | --- | ---
Personne | N/A | Direct | Direct
Groupe | Direct | Direct | Direct
MailInDB | Direct | Direct | Direct
Ressources | N/A | N/A | N/A

Lorsqu’une ressource est créée, un document Notes est créé. De même, lorsqu’une ressource est supprimée, le document Notes est supprimé.

### <a name="ports-and-protocols"></a>Ports et protocoles
Client IBM Lotus Notes et serveurs Domino de communiquer à l’aide des Notes à distance procédure appeler (NRPC) où NRPC devez utiliser TCP/IP. Le numéro de port par défaut est 1352, mais peut être modifié par l’administrateur Domino.

### <a name="not-supported"></a>Non pris en charge
Les opérations suivantes ne sont pas pris en charge par la version actuelle du connecteur Lotus Domino :

- Déplacer des boîtes aux lettres entre des serveurs.

## <a name="create-a-new-connector"></a>Créer un nouveau connecteur

### <a name="client-software-installation-and-configuration"></a>Configuration et Installation du logiciel client
Lotus Notes doit être installé sur le serveur **avant** que le connecteur est installé.

Lors de l’installation, assurez-vous que vous effectuez une **Installation unique de l’utilisateur**. La valeur par défaut **Utilisateur multiples installer** ne fonctionne pas.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Sur la page fonctionnalités, installez uniquement les fonctionnalités Lotus Notes et le **Client unique d’ouverture de session**. Ouverture de session unique est nécessaire pour le connecteur afin de pouvoir ouvrir une session sur le serveur Domino.  
![Concernant l’installation2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Remarque :** Démarrez Lotus Notes une fois avec un utilisateur qui se trouve sur le même serveur que le compte que vous utilisez comme compte de service du connecteur. Assurez-vous également de fermer le client Lotus Notes sur le serveur. Il ne peut pas être exécuté en même temps que le connecteur a tente de se connecter au serveur Domino.

### <a name="create-connector"></a>Créer le connecteur
Pour créer un connecteur de Lotus Domino, sélectionnez **Agent de gestion** et de **Création**de **Service de synchronisation** . Sélectionnez le connecteur de **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Si votre version du service de synchronisation offre la possibilité de configurer **l’Architecture**, assurez-vous que le connecteur est défini à sa valeur par défaut pour s’exécuter dans le **processus**.

### <a name="connectivity"></a>Connectivité
Dans la page connectivité, vous devez spécifier le nom du serveur Lotus Domino et entrez les informations d’identification d’ouverture de session.  
![Connectivité](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La propriété de serveur Domino prend en charge les deux formats de nom de serveur :

- Nom du serveur
- Nom du serveur/nom du répertoire

Le format du **Nom du serveur/nom du répertoire** est le format par défaut pour cet attribut, car il fournit une réponse plus rapide lorsque le connecteur contacte le serveur Domino.

Le fichier d’ID utilisateur fourni est stocké dans la base de données de configuration du service de synchronisation.

Pour **l’Importation de Delta** vous disposez des options suivantes :

- **Aucun**. Le connecteur ne les importations delta.
- **Ajouter/Mettre à jour**. Le connecteur fait delta import ajouter et mettre à jour des opérations. Pour supprimer une opération **d’Importation complète** est nécessaire. Cette opération est à l’aide de l’interopérabilité .net.
- **Ajouter/Mettre à jour/supprimer**. L’importation de delta est connecteur ajouter, mettre à jour et supprimer des opérations. Cette opération est à l’aide de l’interface C++ native.

Dans **Options de schéma** vous disposez des options suivantes :

- La **valeur par défaut du schéma**. Le connecteur détecte le schéma à partir du serveur Domino. Il s’agit de l’option par défaut.
- **Schéma de DSML**. Utilisé uniquement si le serveur Domino n’expose pas le schéma. Puis, vous pouvez créer un fichier DSML avec le schéma et l’importer à la place. Pour plus d’informations sur DSML, consultez [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Lorsque vous cliquez sur Suivant, les paramètres de configuration d’ID utilisateur et le mot de passe sont vérifiés.

### <a name="global-parameters"></a>Paramètres globaux
Dans la page Paramètres généraux, vous configurez le fuseau horaire et l’importation et d’exportez d’option de l’opération.  
![Paramètres globaux](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Le paramètre de **fuseau horaire du serveur Domino** définit l’emplacement de votre serveur Domino.

Cette option de configuration est requise pour prendre en charge les opérations **d’importation de delta** car elle permet la synchronisation service déterminent les modifications entre les deux dernières importations.

#### <a name="import-settings-method"></a>Paramètres d’importation, méthode
**Effectuer une importation complète par** possède ces options :

- Recherche
- Affichage (recommandé)

**Recherche** est l’indexation dans Domino, mais il est courant que les index ne sont pas mis à jour en temps réel et les données renvoyées par le serveur ne sont pas toujours correctes. Pour un système comporte de nombreuses modifications, cette option ne fonctionne pas bien généralement et fournit la valeur false supprime dans certaines situations. Toutefois, la **recherche** est plus rapide que la **vue**.

**Vue** est l’option recommandée dans la mesure où il fournit l’état correct de données. Il est légèrement plus lente que la recherche.

#### <a name="creation-of-virtual-contact-objects"></a>Création d’objets de Contact virtuels
La **permettre la création de \_objet de Contact** possède ces options :

- Aucun
- Valeurs non-référence
- Valeurs de référence et Non

Dans Domino, les attributs de référence peuvent contenir différents formats pour référencer d’autres objets. Pour être en mesure de représenter les différentes variations, met en œuvre le connecteur \_contacter les objets, également connu sous le nom **Des Contacts virtuels** (VC). Ces objets sont créés afin de pouvoir se joindre aux objets existants de MV ou projetés en tant que nouveaux objets. De cette façon, les références d’attribut peuvent être conservés.

En activant ce paramètre et si le contenu d’un attribut de référence n’est pas un format de nom unique, un \_objet de Contact est créé. Par exemple, un attribut de membre d’un groupe peut contenir des adresses SMTP. Il est également possible d’avoir shortName et autres attributs présents dans les attributs de référence. Dans ce scénario, sélectionnez les **Valeurs de référence Non**. Cette configuration est le paramètre le plus courant pour les implémentations de Domino.

Lorsque Lotus Domino est configuré pour que les carnets d’adresses distincts avec différents noms uniques représentant le même objet, il est possible de créer également \_objets de Contact pour toutes les valeurs de référence qui sont trouvent dans un carnet d’adresses. Dans ce scénario, sélectionnez l’option de **référence et des valeurs Non-référence** .

Si vous avez plusieurs valeurs dans l’attribut **FullName** dans Domino, puis vous souhaitez également activer la création de Contacts virtuel afin que les références peuvent être résolues. Par exemple, cet attribut peut avoir plusieurs valeurs après un mariage ou le divorce. Activez la case à cocher Activer de **... FullName a plusieurs valeurs** pour ce scénario.

En joignant les attributs appropriés, de la \_objets de Contact peut être joint à l’objet MV.

Ces objets ont VC =\_Contact ajouté à leur nom de domaine.

#### <a name="import-settings-conflict-object"></a>Importer les paramètres, objet de conflit
**Exclure l’objet de conflits**

Dans une implémentation de Domino de grande taille, il est possible que plusieurs objets ont le même nom de domaine en raison de problèmes de réplication. Dans ces cas, le connecteur doit voir deux objets avec UniversalIDs différents mais le même nom de domaine. Ce conflit provoque un objet transitoire dans l’espace de connecteur en cours de création. Le connecteur peut ignorer les objets qui ont été sélectionnés dans Domino comme des victimes de la réplication. Il est recommandé de conserver cette case à cocher activée.

#### <a name="export-settings"></a>Paramètres d’exportation
Si l’option **Utiliser l’AdminP de mise à jour de références** n’est pas cochée, l’exportation d’attributs de référence, comme membre, est un appel direct et n’utilise pas le processus de AdminP. Utilisez cette option uniquement lorsque AdminP n’a pas été configuré pour maintenir l’intégrité référentielle.

#### <a name="routing-information"></a>Informations de routage
Domino, il est possible qu’un attribut de référence comporte des informations de routage incorporées sous la forme d’un suffixe de nom unique. Par exemple, l’attribut de membre d’un groupe peut contenir **CN=example/organization@ABC**. Le suffixe @ABC les informations de routage. Les informations de routage sont utilisées par Domino pour envoyer des e-mails sur le système Domino approprié, ce qui peut être un système dans une autre organisation. Dans le champ informations de routage, vous pouvez spécifier les routage des suffixes utilisés au sein de l’organisation dans la portée du connecteur. Si une de ces valeurs est trouvée en tant que suffixe dans un attribut de référence, les informations de routage sont supprimées à partir de la référence. Si le suffixe de routage sur une valeur de référence ne correspondent pas à l’une de ces valeurs spécifiées, une \_objet de Contact est créé. Ces \_objets Contact sont créés avec ** RO=@ ** inséré dans le nom de domaine. Pour ces \_les attributs suivants sont également ajoutés pour permettre la jonction à un objet réel si nécessaire des objets Contact : \_routingName, \_contactName, \_displayName et UniversalID.

#### <a name="additional-address-books"></a>Carnets d’adresses supplémentaires
Si vous n’avez pas **l’assistance du répertoire** installé, qui fournit le nom du carnet d’adresses secondaires, vous pouvez entrer manuellement ces carnets d’adresses.

#### <a name="multivalued-transformation"></a>Transformation à valeurs multiples
De nombreux attributs de Lotus Domino sont à valeurs multiples. Les attributs du métaverse correspondant sont généralement unique à valeurs. En configurant l’importation et l’option opération d’exportation, vous activez le connecteur aider à la traduction requise des attributs concernés.

**Exporter**  
L’option opération d’exportation prend en charge deux modes :

- Ajouter l’élément
- Remplacer l’élément

**Remplacer l’élément** – lorsque vous sélectionnez cette option, le connecteur toujours supprimer les valeurs actuelles de l’attribut dans Domino et les remplacer par les valeurs fournies. L’évaluation peut être à valeur unique ou à valeurs multiples.

Exemple : L’attribut de l’Assistant d’un objet person a les valeurs suivantes :

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un nouvel Assistant nommé **David Alexander** est affecté à cet objet de la personne, le résultat est :

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Ajouter un élément** – lorsque vous sélectionnez cette option, le connecteur conserve les valeurs existantes de l’attribut dans Domino et insérez les nouvelles valeurs en haut de la liste de données.

Exemple : L’attribut de l’Assistant d’un objet person a les valeurs suivantes :

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un nouvel Assistant nommé **David Alexander** est affecté à cet objet de la personne, le résultat est :

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importation**  
L’option opération d’importation prend en charge deux modes :

- Par défaut
- À valeurs multiples à valeur unique

**Par défaut** , lorsque vous sélectionnez l’option par défaut, toutes les valeurs de tous les attributs sont importées.

**Multivalued à valeur unique** – lorsque vous sélectionnez cette option, un attribut à valeurs multiples est converti en un attribut à valeur unique. Si plus d’une valeur existe, la valeur de la partie supérieure (cette valeur est en général également la plus récente) est utilisée.

Exemple : L’attribut de l’Assistant d’un objet person a les valeurs suivantes :

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

La mise à jour plus récente pour cet attribut est **David Alexander**. Étant donné que l’option opération d’importation a Multivalued à valeur unique, connecteur importe uniquement **David Alexander** dans l’espace de connecteur.

La logique permettant de convertir des attributs à valeurs multiples en les attributs à valeur unique ne s’applique pas à l’attribut de membre de groupe et à l’attribut fullname de personne.

Il est également possible de configurer l’importation et l’exportation des règles de transformation pour les attributs à valeurs multiples par attribut, en tant qu’exception à la règle générale. Pour configurer cette option, entrez [Type_Objet]. [nom_attribut] dans les zones de texte **Importer la liste d’exclusion d’attributs** et **d’Exporter la liste d’exclusion d’attributs** . Par exemple, si vous entrez Person.Assistant et que l’indicateur global est défini pour importer toutes les valeurs, uniquement la première valeur est importée de l’assistant.

#### <a name="certifiers"></a>Certificateurs
Toutes les unités d’organisation / d’organisation sont répertoriées par le connecteur. Pour être en mesure d’exporter des objets de la personne dans le carnet d’adresses principal, un certificateur avec son mot de passe est requis.

Si tous les certificateurs ont le même mot de passe, le **mot de passe pour tous les Certifers** peut être utilisé. Vous pouvez entrer ici le mot de passe et spécifier uniquement le fichier certificateur.

Si vous importez uniquement, vous n’avez pas spécifier les certificateurs.

### <a name="configure-provisioning-hierarchy"></a>Configurer la mise en service de hiérarchie
Lorsque vous configurez le connecteur de Lotus Domino, ignorez cette page de la boîte de dialogue. Le connecteur de Lotus Domino ne gère pas la hiérarchie mise en service.  
![Mise en service de la hiérarchie](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurez des Partitions et des hiérarchies
Lorsque vous configurez des partitions et des hiérarchies, vous devez sélectionner le carnet d’adresses principal appelé NAB=names.nsf. Outre le carnet d’adresse principale, vous pouvez sélectionner les carnets d’adresses secondaires si elles existent.  
![Partitions](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Sélectionnez les attributs
Lorsque vous configurez vos attributs, vous devez sélectionner tous les attributs qui sont précédés de ** \_MMS\_**. Ces attributs sont requis lorsque vous configurez de nouveaux objets pour Lotus Domino

![Attributs](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestion du cycle de vie des objets
Cette section fournit une vue d’ensemble des différents objets de Domino.

### <a name="person-objects"></a>Objets Person
L’objet person représente les utilisateurs dans l’organisation et des unités d’organisation. En plus des attributs par défaut, l’administrateur Domino peut ajouter des attributs personnalisés pour un objet Person. Au minimum, un objet Person doit inclure tous les attributs obligatoires. Pour obtenir une liste complète des attributs obligatoires, consultez [Propriétés de Lotus Notes](#lotus-notes-properties). Pour enregistrer un objet person, les conditions préalables suivantes doivent être remplies :

- Le carnet d’adresses (names.nsf) doit avoir été défini, et il doit être le carnet d’adresses principal.
- Vous devez disposer de l’Id du certificateur O/unité d’organisation et le mot de passe pour inscrire un utilisateur particulier de l’organisation / unité d’organisation.
- Vous devez définir un ensemble spécifique de propriétés de Lotus Notes pour un objet person. Ces propriétés sont utilisées pour la mise en service de l’objet person. Pour plus de détails, consultez la section [Propriétés Lotus Notes](#lotus-notes-properties) plus loin dans ce document.
- Le mot de passe HTTP initiale pour une personne est un attribut et un jeu au cours de la mise en service.
- L’objet de personne doit être un des trois types de prises en charge suivantes :
    1. Utilisateur normal qui possède un fichier de messagerie et un fichier d’id utilisateur
    2. Itinérance (utilisateur Normal qui inclut tous les fichiers de base de données itinérant)
    3. Contacts (utilisateur sans fichier d’id)

Personnes (à l’exception des contacts) peuvent encore être regroupés en nous utilisateurs et International tel que défini par la valeur de la \_MMS\_propriété du IDRegType. Ces personnes d’utiliser le Client Notes pour accéder aux serveurs Lotus Domino, ont un Id Lotus Notes et un document de la personne. S’ils utilisent la messagerie Notes, ils ont également un fichier de courrier. L’utilisateur doit être inscrit pour devenir active. Pour plus d’informations, voir :

- [Configuration des utilisateurs de Notes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Enregistrement de l’utilisateur](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Gestion des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Modification du nom des utilisateurs](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Toutes ces opérations sont effectuées dans Lotus Domino, puis importées dans le service de synchronisation.

### <a name="resources-and-rooms"></a>Ressources et salles
Une ressource est un autre type d’une base de données Lotus Domino. Les ressources peuvent être des salles de conférence avec divers types d’équipements, tels que les projecteurs. Il existe des sous-types de ressources pris en charge par le connecteur de Lotus Domino sont définies par l’attribut de Type de ressource :

Type de ressource | Attribut de Type de ressource
--- | ---
Salle | 1
Ressources (autres) | 2
Réunion en ligne | 3

Pour le type d’objet ressource fonctionne, les éléments suivants est nécessaire :

- Base de données de réservation de ressource doit déjà exister sur le serveur Domino connecté
- Le site est déjà défini pour la ressource

La base de données de réservation de ressources contient trois types de documents :

- Profil du site
- Ressources
- Réservation

Pour plus d’informations sur la configuration de base de données de réservation de ressources, consultez [configuration de la base de données de réservation de ressources](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Mise à jour, créer et supprimer des ressources**  
Les opérations Create, Update et Delete sont exécutées par le connecteur de Lotus Domino dans la base de données de réservation de ressources. Les ressources sont créées en tant que documents dans Names.nsf (autrement dit, le carnet d’adresse principale). Pour plus d’informations sur la modification et la suppression des ressources, voir [suppression de documents de ressource et d’édition](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importation et exportation des ressources**  
Les ressources peuvent être importés à et exportés à partir du service de synchronisation, comme tout autre type d’objet. Sélectionnez le type d’objet en tant que ressource lors de la configuration. Pour l’opération d’exportation réussie, vous devez disposer des détails pour le type de ressource de base de données de conférence et nom du Site.

### <a name="mail-in-databases"></a>Bases de données de messagerie
Une base de données de messagerie est une base de données est conçu pour recevoir des messages. Il s’agit d’une boîte aux lettres Lotus Domino qui n’est pas associé à un compte d’utilisateur Lotus Domino spécifique (c'est-à-dire qu’il n’a pas son propre fichier d’ID et un mot de passe). Une base de données de messages a un IDutilisateur unique (« nom court ») associée et sa propre adresse de messagerie.

Si il est nécessaire pour une boîte aux lettres distincte avec sa propre adresse de messagerie qui peut être partagé entre différents utilisateurs (par exemple, group@contoso.com), création d’une base de données de messagerie. L’accès à cette boîte aux lettres est contrôlé par le biais de son contrôle d’accès liste (ACL), qui contient les noms des utilisateurs qui sont autorisés à ouvrir la boîte aux lettres Notes.

Pour obtenir une liste des attributs requis, consultez la section [Attributs obligatoires](#mandatory-attributes) plus loin dans cet article.

Lorsqu’une base de données est conçue pour recevoir un courrier électronique, un document de publipostage dans la base de données est créé dans Lotus Domino. Ce document doit exister dans l’annuaire Domino sur chaque serveur qui stocke une copie de la base de données. Pour obtenir une description détaillée sur la création d’un document de publipostage dans la base de données, consultez [Création d’un document de publipostage dans la base de données](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Avant de créer une base de données de messagerie, la base de données doit déjà exister (aurait dû être créée par l’administrateur de Lotus) sur le serveur Domino.

### <a name="group-management"></a>Gestion de groupe
Vous pouvez obtenir une vue d’ensemble détaillée de la gestion de groupe Lotus Domino à partir des ressources suivantes :

- [L’utilisation de groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Création d’un groupe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Création et modification de groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Gestion des groupes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Modification du nom d’un groupe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestion de mot de passe
Pour un utilisateur Lotus Domino enregistré, il existe deux types de mots de passe :

1. Mot de passe utilisateur (stockée dans le fichier User.id)
2. Internet / mot de passe HTTP

Le connecteur de Lotus Domino prend en charge que les opérations avec le mot de passe HTTP.

Pour effectuer la gestion de mot de passe, vous devez activer la gestion des mots de passe pour le connecteur dans le Concepteur de l’Agent de gestion. Pour activer la gestion de mot de passe, sélectionnez **Activer la gestion de mot de passe** sur la page de la boîte de dialogue **Configurer les Extensions** .  
![Configurer les Extensions](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

La prise en charge connecteur Lotus Domino opérations de mot de passe Internet suivantes :

- Définir le mot de passe : Mot de passe de jeu définit un nouveau mot de passe HTTP/Internet de l’utilisateur dans Domino. Par défaut le compte est également déverrouillé. L’indicateur de déverrouillage est exposée dans l’interface WMI du moteur de synchronisation.
- Modifier le mot de passe : Dans ce scénario, un utilisateur pouvez souhaiter modifier le mot de passe ou est invité à changer de mot de passe après un certain temps. Pour prendre cette opération effectuée, à la fois (l’ancien et le nouveau mot de passe) sont obligatoires. Une fois modifiée, le nouveau mot de passe est mis à jour dans Lotus Domino.

Pour plus d’informations, voir :

- [À l’aide de la fonctionnalité de verrouillage de Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Gestion des mots de passe Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informations de référence
Cette section répertorie les descriptions des attributs des exigences d’attribut pour le connecteur de Lotus Domino.

### <a name="lotus-notes-properties"></a>Propriétés de Lotus Notes
Lorsque vous configurez des objets Person dans votre répertoire de Lotus Domino, vos objets doivent avoir un jeu spécifique de propriétés contient des valeurs spécifiques. Ces valeurs sont uniquement requises pour les opérations de création.

Le tableau suivant répertorie ces propriétés ainsi que leur description.

Propriété | Description
--- | ---
\_MMS_AltFullName | Nom de remplacement complet de l’utilisateur.
\_MMS_AltFullNameLanguage | La langue à utiliser pour la spécification de l’autre nom complet de l’utilisateur.
\_MMS_CertDaysToExpire | Le nombre de jours à compter de la date du jour avant que le certificat expire. Si non spécifié, la date par défaut est de deux ans à compter de la date actuelle.
\_MMS_Certifier | Propriété qui contient le nom de la hiérarchie d’organisation de l’autorité. Par exemple : OU = OrganizationUnit, O = Org, C = pays.
\_MMS_IDPath | Si la propriété est vide, aucun fichier d’identification utilisateur n’est créée localement sur le serveur de synchronisation. Si la propriété contient un nom de fichier, un fichier d’ID utilisateur est créé dans le dossier madata. La propriété peut également contenir un chemin d’accès complet.
\_MMS_IDRegType | Personnes peuvent être classés en tant que contacts, utilisateurs nous et utilisateurs internationaux. Le tableau suivant répertorie les valeurs possibles : <li>0 - contact</li><li>1 - utilisateur d’États-Unis</li><li>2 - utilisateur international</li>
\_MMS_IDStoreType | Propriété requise pour les États-Unis et les utilisateurs internationaux. La propriété contient une valeur entière qui spécifie si l’identification de l’utilisateur est stockée en tant que pièce jointe dans le carnet d’adresses Notes ou dans le fichier de messagerie de la personne. Si le fichier d’ID d’utilisateur est une pièce jointe dans le carnet d’adresses, il peut éventuellement être créé en tant que fichier avec \_MMS_IDPath. <li>Vide - fichier d’ID magasin dans le coffre-fort ID, aucun fichier d’identification (utilisé pour les Contacts).</li><li> 1 - la pièce jointe dans le carnet de Notes. La \_MMS_Password propriété doit être définie pour les fichiers d’identification utilisateur qui sont des pièces jointes</li><li>2 - ID de magasin dans le fichier de messagerie de la personne. La \_MMS_UseAdminP doit être défini sur false pour laisser le fichier de messages créé au cours de l’inscription de la personne. La \_MMS_Password propriété doit être définie pour les fichiers d’identification utilisateur.</li>
\_MMS_MailQuotaSizeLimit | Le nombre de mégaoctets qui sont autorisées pour la base de données de fichier de courrier électronique.
\_MMS_MailQuotaWarningThreshold | Le nombre de mégaoctets qui sont autorisées pour la base de données de fichier de courrier électronique avant qu’un avertissement soit émis.
\_MMS_MailTemplateName | Le fichier de modèle de courrier électronique qui est utilisé pour créer le fichier de messagerie de l’utilisateur. Si un modèle est spécifié, le fichier de courrier est créé à l’aide du modèle spécifié. Si aucun modèle n’est spécifié, le fichier de modèle par défaut est utilisé pour créer le fichier.
\_MMS_OU | Propriété facultative qui est le nom de l’unité d’organisation sous le certificateur. Cette propriété doit être vide pour les contacts.
\_MMS_Password | Propriété requise pour les utilisateurs. La propriété contient le mot de passe pour le fichier d’identification de l’objet.
\_MMS_UseAdminP | Propriété doit être définie sur true si le fichier de messagerie doit être créé par le processus AdminP sur le serveur Domino (asynchrone pour le processus d’exportation). Si la propriété est définie sur false, le fichier de courrier est créé avec l’utilisateur Domino du (synchrone dans le processus d’exportation).

Pour un utilisateur avec un fichier d’identification, le \_MMS_Password propriété doit contenir une valeur. Pour l’accès à la messagerie via le client Lotus Notes, les propriétés de serveur de courrier et MailFile d’un utilisateur doivent contenir une valeur.

Pour accéder au courrier électronique via un navigateur Web, les propriétés suivantes doivent contenir des valeurs :

- MailFile - propriété requise qui contient le chemin d’accès sur le serveur Lotus Domino où se trouve le fichier de courrier.
- MailServer - propriété requise qui contient le nom du serveur Lotus Domino. Cette valeur est le nom à utiliser lorsque vous avez créé le fichier de messagerie Lotus sur le serveur Domino.
- HTTPPassword - il s’agit d’une propriété facultative qui contient le mot de passe de l’accès Web pour l’objet.

Pour accéder au serveur Domino sans fonctionnalité de messagerie, la propriété HTTPPassword doit contenir une valeur. La propriété MailFile et la propriété MailServer peuvent être vides.

Avec \_MMS_ IDStoreType = 2 (id de la banque dans le fichier de messagerie), la propriété MailSystem de NotesRegistrationclass a la valeur REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Attributs obligatoires
Le connecteur de Lotus Domino prend en charge principalement les ces types d’objets (document types) :

- Groupe
- Messagerie de base de données
- Personne
- Contact (personne avec aucune autorité)
- Ressources

Cette section répertorie les attributs qui sont obligatoires pour chaque objet pris en charge exporter vers un serveur Domino.

Type d’objet | Attributs obligatoires
--- | ---
Groupe | <li>Nom de la liste</li>
Principal dans la base de données | <li>FullName</li><li>MailFile</li><li>Serveur de courrier</li><li>MailDomain</li>
Personne | <li>Nom</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Contact (personne avec aucune autorité) | <li>\_MMS_IDRegType</li>
Ressources | <li>FullName</li><li>Type de ressource</li><li>ConfDB</li><li>CapacitéRessource</li><li>Site</li><li>DisplayName</li><li>MailFile</li><li>Serveur de courrier</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Problèmes courants et des questions

### <a name="schema-detection-does-not-work"></a>Détection de schéma ne fonctionne pas
Pour être en mesure de détecter le schéma, il est nécessaire que le fichier schema.nsf est présent sur le serveur Domino. Ce fichier s’affiche uniquement si LDAP est installé sur le serveur. Si le schéma n’est pas détectable, vérifiez les points suivants :

- Le fichier schema.nsf est présent dans le dossier racine du serveur Domino
- L’utilisateur dispose des autorisations pour voir le fichier schema.nsf.
- Forcer un redémarrage du serveur LDAP. Ouvrez la **Console de Lotus Domino** et commande **d’Indiquer à la ReloadSchema de LDAP** permet de recharger le schéma.

### <a name="not-all-secondary-address-books-are-visible"></a>Pas de tous les livres d’adresse secondaire sont visibles
Le connecteur Domino repose sur la fonctionnalité d' **Assistance d’annuaire** pour être en mesure de trouver les carnets d’adresses secondaires. Si les carnets d’adresses secondaires sont manquants, vérifiez si [L’Assistance du répertoire](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) a été activé et configuré sur le serveur Domino.

### <a name="custom-attributes-in-domino"></a>Attributs personnalisés dans Domino
Il existe plusieurs façons de Domino pour étendre le schéma, afin qu’elle apparaisse sous la forme d’un attribut personnalisé consommable par le connecteur.

**Approche 1 : Étendre le schéma de Lotus Domino**

1. Créer une copie du modèle d’annuaire Domino {PUBNAMES. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (vous ne devez pas personnaliser le répertoire d’IBM Lotus Domino par défaut modèle) :
2. Ouvrez le modèle de répertoire de copie de Domino {CONTOSO. Modèle NTF} qui a été créé dans le Concepteur de Domino et procédez comme suit :
    - Cliquez sur partager des éléments et développez des sous-formulaires
    - Double-cliquez sur le sous-formulaire de InheritableSchema ${ObjectName} (où {ObjectName} est le nom de la classe d’objet structurelle par défaut, par exemple : personne).
    - Nom de l’attribut que vous souhaitez ajouter dans le schéma {MyPersonAtrribute} et correspondant à cet attribut. Créer un champ, sélectionnez le Menu **créer** et puis sélectionnez le **champ** à partir du menu.
    - Dans le champ ajouté, définir ses propriétés en sélectionnant son Type, Style, taille, polices et autres paramètres associés dans la fenêtre des propriétés de champ.
    - Conserver l’attribut de valeur par défaut identique à celui indiqué pour cet attribut (par exemple, si le nom de l’attribut est MyPersonAttribute, gardez la valeur par défaut avec le même nom).
    - Enregistrer le sous-formulaire de InheritableSchema ${ObjectName} avec les valeurs mises à jour.
3. Remplacer le modèle d’annuaire Domino {PUBNAMES. NTF} avec le nouveau modèle personnalisé {CONTOSO. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Fermez l’administrateur de Domino et ouvrir la Console Domino pour redémarrer le service LDAP et recharger le schéma LDAP :
    - Dans la Console Domino, insérer la commande dans le texte de la **Commande de Domino** déposée pour redémarrer le service LDAP - [LDAP de tâche redémarrer]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Pour recharger LDAP schéma utiliser la commande LDAP de dire - dire de ReloadSchema de LDAP
5. Ouvrir Domino Admin et onglet Sélection des personnes et des groupes pour afficher l’attribut ajouté est reflétée dans domino ajoutent personne.
6. Ouvrez Schema.nsf à partir de l’onglet **fichiers** et voir l’attribut ajouté est reflétée dans la classe d’objet LDAP dominoPerson.

**L’approche 2 : Créer un auxClass avec l’attribut personnalisé et l’associer à la classe d’objet**

1. Créer une copie du modèle d’annuaire Domino {PUBNAMES. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (jamais personnaliser le répertoire d’IBM Lotus Domino par défaut modèle) :
2. Ouvrez le modèle de répertoire de copie de Domino {CONTOSO. Modèle NTF} a été créé, dans le Concepteur de Domino.
3. Dans le volet gauche, sélectionnez Partage de Code et des sous-formulaires.
4. Cliquez sur Nouveau sous-formulaire
5. Procédez comme suit pour spécifier les propriétés du nouveau sous-formulaire :
    - Avec le nouveau sous-formulaire ouvert, choisissez conception - propriétés du sous-formulaire
    - En regard de la propriété Name, entrez un nom pour la classe d’objet auxiliaire--par exemple, TestSubform.
    - Conserver la propriété Options « Inclure dans le sous-formulaire de Insert... dialogue » est activée
    - Désélectionnez la Options propriété « passe de rendu par le biais de code HTML dans les Notes. »
    - Laissez les autres propriétés identiques et fermer la boîte de propriétés du sous-formulaire.
    - Enregistrez et fermez le nouveau sous-formulaire.
6. Procédez comme suit pour ajouter un champ pour définir la classe d’objet auxiliaire :
    - Ouvrez le sous-formulaire que vous avez créé.
    - Choisissez Créer - champ.
    - En regard du nom de l’onglet bases de la boîte de dialogue champ, spécifier un nom quelconque, par exemple : {MyPersonTestAttribute}.
    - Dans le champ ajouté, définir ses propriétés en sélectionnant son Type, Style, taille, police et les propriétés connexes.
    - Conserver l’attribut de valeur par défaut identique à celui indiqué pour cet attribut (par exemple, si le nom de l’attribut est MyPersonTestAttribute, gardez la valeur par défaut avec le même nom).
    - Enregistrer le sous-formulaire avec les valeurs mises à jour et effectuez les opérations suivantes :
        - Dans le volet gauche, sélectionnez le partage de Code et les sous-formulaires
        - Sélectionnez le nouveau sous-formulaire et choisissez conception - propriétés de conception.
        - Cliquez sur l’onglet tiers à partir de la gauche et sélectionnez **Modifier les propager cette interdiction de conception**.
7. Ouvrez le sous-formulaire ExtensibleSchema ${ObjectName} (où {ObjectName} est le nom de la classe d’objet structurelle par défaut, par exemple – personne).
8. Insérer une ressource et sélectionnez le sous-formulaire (que vous avez créé, par exemple – TestSubform) et enregistrer le sous-formulaire de ExtensibleSchema ${ObjectName}.
9. Remplacer le modèle d’annuaire Domino {PUBNAMES. NTF} avec le nouveau modèle personnalisé {CONTOSO. NTF} en suivant [ces étapes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Fermez l’administrateur de Domino et ouvrir la Console Domino pour redémarrer le service LDAP et recharger le schéma LDAP :
    - Dans la Console Domino, insérer la commande dans le texte de la **Commande de Domino** déposée pour redémarrer le service LDAP - [LDAP de tâche redémarrer](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Pour recharger l’utilisation de schéma LDAP **ReloadSchema de LDAP dire**de la commande LDAP de dire.
11. Ouvrez l’administrateur de Domino et sélectionnez l’onglet personnes et groupes pour afficher l’attribut ajouté est reflétée dans domino ajouter une personne (sous autres onglet).
12. Ouvrez Schema.nsf à partir de l’onglet **fichiers** et voir l’attribut ajouté est indiqué sous la classe d’objet auxiliaire de TestSubform LDAP.

**Méthode 3 : Ajouter l’attribut personnalisé pour la classe ExtensibleObject**

1. Ouvrir le fichier {Schema.nsf} placé dans le répertoire racine
2. Sélectionnez les Classes d’objets LDAP dans le menu de gauche sous **Tous les Documents de schéma** et cliquez sur le bouton **Ajouter un objet classe** :
3. Fournir le nom LDAP sous la forme {zzzExtensibleSchema} (où zzz est le nom de la classe d’objet structurelle par défaut, par exemple personne). Par exemple, pour étendre le schéma pour une personne de classe d’objet, vous devez fournir le nom LDAP {PersonExtensibleSchema}.
4. Fournir un nom de classe d’objet de qualité supérieure, pour lequel vous voulez étendre le schéma. Par exemple, pour étendre le schéma de classe d’objets Person, fournir un nom de classe supérieur objet {dominoPerson} :
5. Fournir un OID valide correspondant à la classe d’objet.
6. Sélectionnez les attributs étendue/personnalisée sous champs obligatoires ou facultatifs Types d’attribut par le besoin :
7. Après avoir ajouté les attributs requis pour le ExtensibleObjectClass, cliquez sur **Enregistrer & fermer**.
8. Un ExtensibleObjectClass est créée pour la classe d’objet par défaut respectives ayant des attributs étendus.

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes de connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
