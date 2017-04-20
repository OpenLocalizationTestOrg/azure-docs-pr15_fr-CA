<properties
   pageTitle="Connecteur LDAP générique | Microsoft Azure"
   description="Cet article décrit comment configurer le connecteur LDAP générique de Microsoft."
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

# <a name="generic-ldap-connector-technical-reference"></a>Référence technique de connecteur de LDAP générique
Cet article décrit le connecteur LDAP générique. L’article s’applique aux produits suivants :

- Gestionnaire d’identité Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Doit utiliser le correctif logiciel 4.1.3671.0 ou une version ultérieure [KB3092178](https://support.microsoft.com/kb/3092178).

Pour MIM2016 et FIM2010R2, le connecteur est disponible en téléchargement à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Lorsque vous faites référence aux RFC de l’IETF, ce document est au format (RFC [numéro de RFC] / [section dans le document RFC]), par exemple (RFC 4512/4.3).
Vous trouverez plus d’informations à http://tools.ietf.org/html/rfc4500 (vous devez remplacer 4500 avec le bon numéro RFC).

## <a name="overview-of-the-generic-ldap-connector"></a>Vue d’ensemble du connecteur LDAP générique
Le connecteur LDAP générique vous permet d’intégrer le service de synchronisation avec le serveur LDAP v3.

Certaines opérations et les éléments de schéma, telles que celles nécessaires pour effectuer une importation de delta, ne sont pas spécifiées dans les RFC de l’IETF. Pour ces opérations, les annuaires LDAP uniquement explicitement spécifiés sont pris en charge.

À partir d’une perspective de haut niveau, les fonctionnalités suivantes sont prises en charge par la version actuelle du connecteur :

Fonctionnalité | Prise en charge
--- | --- |
Source de données connectée | Le connecteur est pris en charge par tous les serveurs de v3 LDAP (RFC conforme de 4510). Il a été testé avec les éléments suivants : <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Catalogue Global de Microsoft Active Directory (AD GC)</li><li>Serveur d’annuaire 389</li><li>Serveur de répertoire Apache</li><li>IBM Tivoli DS</li><li>Répertoire de Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Ouvrir DJ</li><li>Ouvrir DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (précédemment Sun) Directory Server Enterprise Edition</li><li>RadiantOne serveur de répertoire virtuel (VDS)</li><li>Serveur d’annuaire Sun un</li>**Répertoires notables non pris en charge :** <li>Microsoft Active Directory Domain Services (AD DS) [utiliser à la place du connecteur Active Directory intégré]</li><li>Oracle Internet Directory (OID)</li>
Scénarios   | <li>Gestion du cycle de vie des objets</li><li>Gestion de groupe</li><li>Gestion de mot de passe</li>
Opérations |Les opérations suivantes sont prises en charge sur tous les répertoires LDAP : <li>Importation intégrale</li><li>Exporter</li>Les opérations suivantes sont pris en charge uniquement dans les répertoires spécifiés :<li>Importation d’écart</li><li>Définir le mot de passe, modifier le mot de passe</li>
Schéma | <li>Schéma est détectée à partir du schéma LDAP (RFC3673 et RFC4512/4.2)</li><li>Prend en charge les classes structurelles, aux classes et classe de l’objet extensibleObject (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Support de gestion de mot de passe et importation delta
Répertoires pris en charge pour l’importation de Delta et de la gestion de mot de passe :

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe
- Catalogue Global de Microsoft Active Directory (AD GC)
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe
- Serveur d’annuaire 389
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- Serveur de répertoire Apache
    - Ne gère pas les importer de delta dans la mesure où ce répertoire ne dispose pas d’un journal des modifications permanentes
    - Prend en charge la valeur mot de passe
- IBM Tivoli DS
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- Répertoire de Isode
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- Novell eDirectory et eDirectory de NetIQ
    - Prend en charge les opérations d’ajout, mise à jour et changement de nom pour l’importation de delta
    - Ne gère pas les opérations de suppression pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- Ouvrir DJ
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- Ouvrir DS
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- Open LDAP (openldap.org)
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe
    - Ne prend pas en charge Modifier mot de passe
- Oracle (précédemment Sun) Directory Server Enterprise Edition
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
- RadiantOne serveur de répertoire virtuel (VDS)
    - Doit utiliser la version 7.1.1 ou supérieur
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification
-  Serveur d’annuaire Sun un
    - Prend en charge toutes les opérations pour l’importation de delta
    - Prend en charge la valeur mot de passe et le mot de passe de modification

### <a name="prerequisites"></a>Conditions préalables
Avant d’utiliser le connecteur, vérifiez que vous disposez des éléments suivants sur le serveur de synchronisation :

- 4.5.2 de Microsoft .NET Framework ou une version ultérieure

### <a name="detecting-the-ldap-server"></a>Détection de serveur LDAP
Le connecteur utilise diverses techniques pour détecter et identifier le serveur LDAP. Le connecteur utilise le DSE racine, le nom de fournisseur/version et qu’il inspecte le schéma pour rechercher des objets uniques et attributs connus dans certains serveurs LDAP. Ces données, si trouvé, est utilisée pour pré-remplir les options de configuration du connecteur.

### <a name="connected-data-source-permissions"></a>Autorisations de Source de données connectées
Pour effectuer une importation et exportation des opérations sur les objets de l’annuaire connecté, le compte de connecteur doit disposer des autorisations suffisantes. Le connecteur exige des autorisations pour être en mesure d’exporter en écriture et lecture pour être en mesure d’importer. Configuration de l’autorisation est effectuée dans les expériences de gestion de l’annuaire cible lui-même.

### <a name="ports-and-protocols"></a>Ports et protocoles
Le connecteur utilise le numéro de port spécifié dans la configuration par défaut est 389 pour LDAP et 636 pour le protocole LDAPS.

Pour le protocole LDAPS, vous devez utiliser SSL 3.0 ou TLS. SSL 2.0 n’est pas pris en charge et ne peut pas être activé.

### <a name="required-controls-and-features"></a>Fonctionnalités et contrôles requis
Les contrôles/fonctionnalités LDAP suivantes doivent être disponibles sur le serveur LDAP pour le connecteur fonctionne correctement :  
`1.3.6.1.4.1.4203.1.5.3`Filtres de vrai/faux

Le filtre de valeur True/False est fréquemment déclaré comme non pris en charge par les annuaires LDAP et risque d’apparaître sur la **Page Global** sous **Fonctionnalités obligatoire introuvable**. Il est utilisé pour créer des filtres **ou** dans les requêtes LDAP, par exemple lors de l’importation de multiples types d’objet. Vous pouvez importer plus d’un type d’objet, puis votre serveur LDAP prend en charge cette fonctionnalité.

Si vous utilisez un répertoire où un identificateur unique est le point d’ancrage doivent également être disponible (voir la section [Configurer des ancres](#configure-anchors) plus loin dans cet article pour plus d’informations) :  
`1.3.6.1.4.1.4203.1.5.1`Tous les attributs opérationnels

Si le répertoire a plus d’objets que ce qui peut tenir dans un seul appel au répertoire, il est recommandé d’utiliser la pagination. Pour la pagination fonctionne, vous devez une des options suivantes :

**Option 1 :**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Option 2 :**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Si les deux options sont activées dans la configuration du connecteur, pagedResultsControl est utilisé.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl est uniquement utilisé avec la méthode d’importation delta USNChanged pour être en mesure de voir les objets supprimés.

Le connecteur tente de détecter les options présentes sur le serveur. Si les options ne peuvent pas être détectées, un avertissement est présent sur la page Global dans les propriétés du connecteur. Les serveurs LDAP pas toutes les fonctionnalitésent présentes de tous les contrôles qu’ils prennent en charge, et même si cet avertissement s’affiche, le connecteur peut fonctionner sans problème.

### <a name="delta-import"></a>Importation d’écart
Importation de delta n’est disponible que lorsqu’un répertoire de prise en charge a été détecté. Les méthodes suivantes sont actuellement utilisés :

- Accesslog LDAP. Consultez [l’enregistrement de http://www.openldap.org/doc/admin24/overlays.html#Access](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- Changelog LDAP. Voir [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Horodatage. Pour Novell/NetIQ eDirectory, le connecteur utilise la dernière date/heure pour obtenir créé et mis à jour les objets. Novell/NetIQ eDirectory ne fournit pas qu'un équivalent signifie récupérer des objets supprimés. Cette option peut également être utilisée si aucune autre méthode d’importation delta n’est actif sur le serveur LDAP. Cette option n’est pas en mesure d’importer de supprimer des objets.
- USNChanged. Voir : [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Non pris en charge
Les fonctionnalités LDAP suivantes ne sont pas prises en charge :

- Références LDAP entre serveurs (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Créer un nouveau connecteur
Pour créer un connecteur de LDAP générique, sélectionnez **Agent de gestion** et de **Création**de **Service de synchronisation** . Sélectionnez le connecteur **LDAP générique (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Connectivité
Sur la page de connexion, vous devez spécifier les informations de l’hôte, de Port et de liaison. En fonction de laquelle la liaison est sélectionné, d’autres informations peuvent être fournies dans les sections suivantes.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- Le paramètre de délai de connexion est uniquement utilisé pour la première connexion au serveur lors de la détection du schéma.
- Si la liaison est anonyme, puis ni nom d’utilisateur / mot de passe ni de certificat sont utilisés.
- Pour les autres liaisons, entrez un nom d’utilisateur dans les informations / mot de passe ou de la sélection d’un certificat.
- Si vous utilisez Kerberos pour l’authentification, fournissent également le domaine Kerberos ou du domaine de l’utilisateur.

La zone de texte **alias d’attribut** est utilisée pour des attributs définis dans le schéma avec la syntaxe de RFC4522. Ces attributs ne peuvent pas être détectés lors de la détection de schéma et que le connecteur a besoin d’aide pour identifier ces attributs. Par exemple ce qui suit est nécessaire pour être entré dans la zone alias attribut d’identifier correctement l’attribut userCertificate en tant qu’attribut binaire :

`userCertificate;binary`

Voici un exemple de la façon dont cette configuration pourrait ressembler à :

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Sélectionnez la case à cocher **inclure les attributs opérationnels dans le schéma** d’inclure également les attributs créés par le serveur. Citons notamment les attributs lorsque l’objet a été créé et dernière heure de mise à jour.

Sélectionnez **inclure les attributs extensibles dans le schéma** si les objets extensibles (RFC4512/4.3) sont utilisées et l’activation de cette option permet à chaque attribut à utiliser sur tous les objets. La sélection de cette option rend le schéma très volumineux pour que, à moins que l’annuaire connecté à l’aide de cette fonctionnalité est recommandé de conserver l’option désactivée.

### <a name="global-parameters"></a>Paramètres globaux
Dans la page Paramètres globaux, vous configurez le nom unique pour le journal des modifications delta et autres fonctionnalités LDAP. La page est préremplie avec les informations fournies par le serveur LDAP.

![Connectivité](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

La partie supérieure affiche les informations fournies par le serveur lui-même, telles que le nom du serveur. Le connecteur vérifie également que les contrôles obligatoires sont présents dans la DSE racine. Si ces contrôles ne sont pas répertoriés, un avertissement est présenté. Certains répertoires LDAP ne répertorient pas toutes les fonctionnalités de la DSE racine et il est possible que le connecteur fonctionne sans problème, même si un avertissement est présent.

Les cases à cocher **prise en charge des contrôles de** contrôlent le comportement de certaines opérations :

- Avec la suppression de l’arborescence sélectionnée, une hiérarchie est supprimée avec un appel LDAP. Avec la suppression de l’arborescence sélectionnée, le connecteur effectue une suppression récursive si nécessaire.
- Avec des résultats paginés sélectionnés, le connecteur effectue une importation paginée avec la taille spécifiée dans les étapes d’exécution.
- Le VLVControl et le SortControl est une alternative à le pagedResultsControl pour lire les données de l’annuaire LDAP.
- Si les trois options (pagedResultsControl, VLVControl et SortControl) sont désélectionnées le connecteur importe tous les objets en une seule opération, ce qui peut échouer s’il s’agit d’un répertoire volumineux.
- ShowDeletedControl est utilisé uniquement lorsque la méthode d’importation Delta est USNChanged.

Le journal de modification DN est le contexte d’attribution de noms utilisé par le journal des modifications delta, par exemple **cn = changelog**. Cette valeur doit être spécifiée pour être en mesure d’effectuer l’importation de delta.

Voici une liste par défaut de journal des modifications DNs :

Répertoire | Journal des modifications delta
--- | ---
GC Microsoft AD LDS et AD | Détecté automatiquement. USNChanged.
Serveur de répertoire Apache | N’est pas disponible.
Répertoire 389 | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
IBM Tivoli DS | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
Répertoire de Isode | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
/ NetIQ de Novell eDirectory | N’est pas disponible. Horodatage. Les utilisations de connecteur mis à jour date/heure pour obtenir des mises à jour des enregistrements.
Ouvrir DJ/DS | Journal des modifications.  Valeur à utiliser par défaut : **cn = changelog**
LDAP Open | Journal d’accès. Valeur à utiliser par défaut : **cn = accesslog**
Oracle DSEE | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**
RadiantOne VDS | Répertoire virtuel. Dépend de l’annuaire connecté à VDS.
Serveur d’annuaire Sun un | Journal des modifications. Valeur à utiliser par défaut : **cn = changelog**

L’attribut de mot de passe est le nom de l’attribut, que le connecteur doit utiliser pour définir le mot de passe dans nouveau mot de passe et le mot de passe défini des opérations.
Cette valeur est définie à **userPassword** par défaut mais peut être modifiée lorsque cela est nécessaire pour un système particulier de LDAP.

Dans la liste des partitions supplémentaires, il est possible d’ajouter des espaces de noms supplémentaires pas automatiquement détectés. Par exemple, ce paramètre peut être utilisé si plusieurs serveurs constituent un cluster logique, qui doit être importé en même temps. Tout comme Active Directory peut avoir plusieurs domaines d’une forêt, mais tous les domaines partagent un schéma, le même peut être simulé en entrant les espaces de noms supplémentaires dans cette zone. Chaque espace de noms peut importer à partir de différents serveurs et plus configuré sur la page de configuration des Partitions et des hiérarchies. Ctrl + Entrée permet d’obtenir une nouvelle ligne.

### <a name="configure-provisioning-hierarchy"></a>Configurer la mise en service de hiérarchie
Cette page permet de mapper le composant de nom unique, par exemple unité d’organisation, au type d’objet qui doit être mis en service, par exemple unité d’organisation.

![Mise en service de la hiérarchie](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

En configurant la hiérarchie de configuration, vous pouvez configurer le connecteur pour créer automatiquement une structure lorsque cela est nécessaire. Par exemple, s’il y a un espace de noms dc = contoso, dc = com et un nouveau cn d’objet = Joe, unité d’organisation = Seattle, c = US, dc = contoso, dc = com est mis en service, puis le connecteur peut créer un objet de pays de type pour la France et d’une unité d’organisation pour Seattle si ceux ne sont pas déjà présents dans le répertoire.

### <a name="configure-partitions-and-hierarchies"></a>Configurez des Partitions et des hiérarchies
Dans la page des partitions et des hiérarchies, sélectionnez tous les espaces de noms avec les objets que vous envisagez d’importer et d’exporter.

![Partitions](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Pour chaque espace de noms, il est également possible de configurer les paramètres de connectivité qui seraient de substituer les valeurs spécifiées sur l’écran de connexion. Si ces valeurs sont laissés à leur valeur vide par défaut, les informations à partir de l’écran de connexion sont utilisées.

Il est également possible de sélectionner les conteneurs et les unités d’organisation, le connecteur doit importer et exporter vers.

### <a name="configure-anchors"></a>Configurer les points d’ancrage
Cette page a toujours une valeur préconfigurée et ne peut pas être modifiée. Si le fournisseur du serveur a été identifié, le point d’ancrage peut être rempli avec un attribut immuable, par exemple le GUID d’un objet. S’il n’a pas été détectée ou est connue pour ne pas avoir un attribut immuable, le connecteur utilise le dn (nom unique) comme le point d’ancrage.

![points d’ancrage](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Voici une liste de serveurs LDAP et le point d’ancrage utilisé :

Répertoire | Attribut d’ancrage
--- | ---
GC Microsoft AD LDS et AD | GUID d’objet
Serveur d’annuaire 389 | nom de domaine
Répertoire Apache | nom de domaine
IBM Tivoli DS | nom de domaine
Répertoire de Isode | nom de domaine
/ NetIQ de Novell eDirectory | GUID
Ouvrir DJ/DS | nom de domaine
LDAP Open | nom de domaine
Oracle ODSEE | nom de domaine
RadiantOne VDS | nom de domaine
Serveur d’annuaire Sun un | nom de domaine

## <a name="other-notes"></a>Autres notes
Cette section fournit des informations des aspects qui sont spécifiques à ce connecteur ou pour d’autres raisons sont importants à connaître.

### <a name="delta-import"></a>Importation d’écart
Le filigrane de delta dans l’annuaire LDAP Open est date/heure UTC. Pour cette raison, les horloges entre le Service de synchronisation FIM et Open LDAP doivent être synchronisées. Si ce n’est pas le cas, certaines entrées dans le journal des modifications delta peuvent être omises.

Pour Novell eDirectory, l’importation delta ne détecte pas les suppressions d’objets. Pour cette raison, il est nécessaire d’exécuter une importation intégrale régulièrement pour trouver tous les objets supprimés.

Pour les répertoires avec un journal des modifications delta qui est basée sur la date/heure, il est recommandé d’exécuter une importation complète à des moments périodiques. Ce processus permet au moteur de synchronisation afin de trouver et d’un disparités entre le serveur LDAP et ce qui est actuellement dans l’espace de connecteur.

## <a name="troubleshooting"></a>Résolution des problèmes

-   Pour plus d’informations sur la façon d’activer la journalisation résoudre les problèmes de connecteur, voir [comment activer le suivi ETW des connecteurs](http://go.microsoft.com/fwlink/?LinkId=335731).
