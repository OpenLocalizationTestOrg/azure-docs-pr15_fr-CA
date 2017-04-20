<properties
    pageTitle="Notes de publication pour les Services BizTalk Azure | Services BizTalk Microsoft Azure"
    description="Répertorie les problèmes connus des Services de BizTalk Azure" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Notes de publication pour les Services BizTalk Azure

Les notes de version pour les Services de BizTalk Microsoft Azure contiennent les problèmes connus dans cette version.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Quelles sont les nouveautés dans la mise à jour de novembre des Services BizTalk
* Cryptage au repos peut être activé dans le portail de Services de BizTalk. Reportez-vous à la section [Activer le cryptage au repos dans le portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Mise à jour de l’historique

### <a name="october-update"></a>Mise à jour d’octobre

* Comptes d’organisation sont prises en charge :  
 * **Scénario**: vous vous êtes inscrit à un déploiement de BizTalk Service à l’aide d’un compte Microsoft (comme user@live.com). Dans ce scénario, seuls les utilisateurs Microsoft Account peuvent gérer le BizTalk Service via le portail de Services de BizTalk. Un compte d’organisation ne peut pas être utilisé.  
 * **Scénario**: vous vous êtes inscrit à un déploiement de BizTalk Service à l’aide d’un compte d’organisation dans Active Directory Azure (comme user@fabrikam.com ou user@contoso.com). Dans ce scénario, seuls les utilisateurs Azure Active Directory au sein de la même organisation peuvent gérer le BizTalk Service via le portail de Services de BizTalk. Un compte Microsoft ne peut pas être utilisé.  
* Lorsque vous créez un BizTalk Service dans Azure portal classique, vous êtes automatiquement enregistré dans le portail de Services de BizTalk.
 * **Scénario**: vous vous connectez au portail Azure classique, création d’un BizTalk Service et sélectionnez **Gérer** pour la première fois. Lorsque le portail de Services BizTalk s’ouvre, le BizTalk Service inscrit automatiquement et est prêt pour vos déploiements.  
 Reportez-vous à la section [enregistrement et de mise à jour d’un déploiement de Service BizTalk sur le portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Mise à jour le 14 août
* Contrat et pont découplage – la négociation d’accords de partenariat et de ponts sont dissociés maintenant dans le portail de Services de BizTalk. Vous maintenant créez les accords et les ponts séparément, et lors de l’exécution des ponts résoudre à un accord basé sur les valeurs du message EDI. Voir [Créer des accords dans les Services BizTalk d’Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), de [créer un pont EDI à l’aide du portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), de [créer un pont AS2 à l’aide du portail de Services de BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx), et [comment ponts résoudre au moment de l’exécution des accords ?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* La possibilité de créer des modèles pour les accords de production est arrêtée.  
* De l’accord du côté envoi, vous pouvez maintenant spécifier des ensembles de délimiteur différente pour chaque schéma. Cette configuration est spécifiée dans les paramètres du protocole d’accord du côté envoi. Pour plus d’informations, consultez [créer une X12 contrat de services de BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx) et [créer un accord EDIFACT dans Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Deux nouvelles entités sont également ajoutées à l’API de modèle objet de module de plateforme sécurisée dans le même but. Consultez [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) et [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Constructions XSD standard, y compris les Types dérivés, sont maintenant pris en charge. Consultez [XSD standard d’utilisation construit dans vos mappages](https://msdn.microsoft.com/library/azure/dn793987.aspx) et [Utiliser des Types dérivés dans les scénarios de mappage et les exemples](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 prend en charge les nouveaux algorithmes de micro pour la signature des messages et des nouveaux algorithmes de cryptage. Reportez-vous à la section [créer un accord AS2 dans les Services BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Problèmes connus

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problèmes de connectivité après la mise à jour de portail de Services de BizTalk

  Si le portail de Services de BizTalk est ouvert alors que les Services BizTalk est mis à niveau pour la version précédente de modifications au service, vous pouvez rencontrer des problèmes de connectivité avec le portail de Services de BizTalk.  
  Pour résoudre ce problème, vous pouvez redémarrer le navigateur, supprimer le cache du navigateur ou démarrer le portail en mode privé.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>IDE de Visual Studio ne peut pas localiser l’artefact si vous cliquez sur une erreur ou un avertissement dans un projet BizTalk Services
Installez Visual Studio 2012 mise à jour 3 RC 1 pour résoudre le problème.  

### <a name="custom-binding-project-reference"></a>Référence de projet de liaison personnalisée
Tenez compte des situations avec un projet BizTalk Services dans une solution Visual Studio suivantes :  
* Dans la même solution Visual Studio, il existe un projet BizTalk Services et un projet de liaison personnalisée. Le projet de BizTalk Service possède une référence à ce fichier de projet de liaison personnalisée.
* Le projet de BizTalk Service possède une référence à une DLL liaison/comportement personnalisé.

Vous « Build » la solution dans Visual Studio avec succès. Ensuite, vous « Reconstruisez » ou « Nettoyez » la solution. Après cela, lorsque vous reconstruisez ou nettoyez à nouveau, l’erreur suivante se produit :  
  Impossible de copier le fichier <Path to DLL> à « bin\Debug\FileName.dll ». Le processus ne peut pas accéder le fichier 'bin\Debug\FileName.dll', car il est utilisé par un autre processus.  

#### <a name="workaround"></a>Solution de contournement
* Si la [mise à jour 3 de Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=39305) est installé, vous disposez des deux options suivantes :

  * Redémarrez Visual Studio, ou

  * Redémarrer la solution. Puis, effectuez uniquement une génération sur la solution.  

* Si [3 mise à jour de Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=39305) n’est pas installé, ouvrir le Gestionnaire des tâches, cliquez sur l’onglet processus, cliquez sur le processus MSBuild.exe et puis cliquez sur le bouton Terminer le processus.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routage aux points de terminaison BasicHttpRelay n'est pas pris en charge à partir de ponts et de portail de Services de BizTalk si les caractères non imprimables sont promus en tant qu’en-têtes HTTP

Si vous utilisez les caractères non imprimables dans le cadre des propriétés promues des messages, ces messages ne peuvent pas être acheminés vers les destinations de relais qui utilisent la liaison BasicHttpRelay. En outre, les propriétés promues qui sont disponibles comme partie du suivi sont codés en URL pour les objets BLOB et non encodée pour les destinations.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN est envoyé de façon asynchrone même si l’option de MDN Send asynchrone est désactivée  
Prenez ce scénario – si vous activez la case à cocher **Envoyer MDN asynchrone** , spécifier une URL pour envoyer l’async MDN et puis désactivez la case à cocher **Envoyer MDN asynchrone** à nouveau, le MDN est toujours envoyée à l’URL spécifiée même si l’option d’envoi asynchrone MDNs n’est pas sélectionnée.  
Pour résoudre ce problème, vous devez effacer l’URL spécifiée avant la désactivation de la case à cocher **Envoyer MDN asynchrone** et puis déployer l’accord AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Caractères d’espace blanc au-delà d’un échange valide provoquent un message vide à être envoyé au point de terminaison de mise en veille  
S’il y a les espaces situés au-delà d’un segment IEA, le désassembleur traite cela comme une fin d’échange en cours et examine l’ensemble suivant d’espaces sous forme de message suivant. Puisqu’il ne s’agit pas d’échange valide, vous pouvez observer qu’un message de réussit est envoyé à la destination de l’itinéraire et un message vide est envoyé au point de terminaison de mise en veille.  
### <a name="tracking-in-biztalk-services-portal"></a>Le suivi dans le portail de Services de BizTalk  
Capture des événements de suivi du traitement des messages EDI et toute corrélation. Si un message ne parvient pas à l’extérieur de la phase de protocole, suivi affiche comme réussie. Dans ce cas, reportez-vous à la section du journal sous la colonne de **Détails** dans le **suivi** de détails de l’erreur.
Le X12 recevoir et envoyer des paramètres ([créer une X12 contrat de services de BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fournissent des informations sur la phase de protocole.  

### <a name="update-agreement"></a>Accord de mise à jour  
Le portail de Services BizTalk vous permet de modifier l’identificateur d’une identité lorsqu’un accord est configuré. Cela peut entraîner les propriétés est incohérent. Par exemple, est un accord à l’aide de ZZ:1234567 et ZZ:7654321 le qualificateur. Dans les paramètres de profil de portail de Services de BizTalk, vous modifiez ZZ:1234567 pour être 01:ChangedValue. Vous ouvrez l’accord et 01:ChangedValue s’affiche au lieu de ZZ:1234567.
Pour modifier l’identificateur d’une identité, supprimer l’accord, mettre à jour des **identités** dans le profil du partenaire et puis recréer l’accord.  
> AZURE. AVERTISSEMENT Ce comportement affecte X12 et AS2.  

### <a name="as2-attachments"></a>Pièces jointes de AS2  
Envoyer ou de recevoir des pièces jointes pour AS2 messages ne sont pas pris en charge dans. En particulier, les pièces jointes sont ignorées en mode silencieux, et le corps du message est traité comme un message AS2 ordinaire.  
### <a name="resources-remembering-path"></a>Ressources : Chemin d’accès de mémorisation  
Lors de l’ajout de **ressources**, la boîte de dialogue peut se souvient pas du chemin d’accès utilisé précédemment pour ajouter une ressource. Pour vous souvenir du chemin d’accès utilisé précédemment, essayez d’ajouter le site portail de Services BizTalk aux **Sites de confiance** dans Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Si vous renommez le nom de l’entité d’un pont et que vous fermez le projet sans enregistrer les modifications, rouvrir l’entité entraîne une erreur
Imaginez un scénario dans l’ordre suivant :  
* Ajouter un pont (par exemple un pont unidirectionnel XML) à un projet de BizTalk Service  

* Renommer le pont en spécifiant une valeur pour la propriété nom de l’entité. Il renomme le fichier .bridgeconfig associé avec le nom spécifié.  

* Fermez le fichier .bcs (en refermant l’onglet dans Visual Studio) sans enregistrer les modifications.  

* Ouvrez de nouveau le fichier .bcs à partir de l’Explorateur de solutions.  
Vous remarquerez que lorsque le fichier .bridgeconfig associé est le nouveau nom que vous avez spécifié, le nom de l’entité sur l’aire de conception est toujours l’ancien nom. Si vous essayez d’ouvrir la Configuration de pont en double-cliquant sur le composant de pont, vous obtenez l’erreur suivante :  
  '<old name>'Entité associé du fichier'<old name>.bridgeconfig' n’existe pas  
Pour éviter de rencontrer ce scénario, assurez-vous de que vous enregistrez les modifications après avoir renommé les entités dans un projet de BizTalk Service.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Le BizTalk Service projet généré avec succès même si un objet a été exclu d’un projet Visual Studio
Considérez un scénario dans lequel vous ajoutez un artefact (par exemple, un fichier XSD) à un projet de BizTalk Service et inclure cet artefact dans la Configuration du pont (par exemple, en le spécifiant comme un type de message de demande) puis l’exclure du projet Visual Studio. Dans ce cas, la génération du projet laissera pas d’erreur tant que l’objet supprimé est disponible sur le disque à l’emplacement même où il a été inclus dans le projet Visual Studio.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Le projet de BizTalk Service ne vérifie pas disponibilité du schéma lors de la configuration les ponts
Dans un projet de BizTalk Service, si un schéma est ajouté au projet importe un autre schéma, le projet de BizTalk Service ne vérifie pas si le schéma importé est ajouté au projet. Si vous essayez de générer un projet de ce type, vous n’obtenez pas les erreurs de génération.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Le message de réponse d’un pont de demande-réponse XML est toujours du jeu de caractères UTF-8
Pour cette version, le jeu de caractères du message de réponse à partir d’un pont de demande-réponse XML est toujours définie sur UTF-8.
### <a name="user-defined-datatypes"></a>Les types de données définis par l’utilisateur
Les adaptateurs BizTalk carte Pack dans la fonction de Service de l’adaptateur BizTalk peuvent utiliser les types de données définis par l’utilisateur pour les opérations de carte.
Lorsque vous utilisez des types de données définis par l’utilisateur, copier les fichiers (.dll) pour le lecteur : \Program Files\Microsoft Service\BAServiceRuntime\bin\ d’adaptateur BizTalk ou dans le Global Assembly Cache (GAC) sur le serveur qui héberge le service de Service de l’adaptateur BizTalk. Dans le cas contraire, l’erreur suivante peut se produire sur le client :  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Il est recommandé d’utiliser GACUtil.exe pour installer un fichier dans le Global Assembly Cache. GACUtil.exe documente l’utilisation de cet outil et les options de ligne de commande de Visual Studio.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Redémarrer le Site Web du Service BizTalk carte
Installation du **Runtime de Service d’adaptateur BizTalk** * crée la * *Service d’adaptateur BizTalk* * site web dans IIS qui contient le * *BAService* * application.* *BAService** application utilise en interne la liaison de relais pour étendre la portée de point de terminaison sur site service dans le nuage. Pour un service hébergé sur site, le point de terminaison correspondant du relais est inscrits sur le Bus de Service uniquement lorsque le démarrage du service sur site.  

Si vous arrêtez, démarrez une application, la configuration d’une application à démarrage automatique n’est pas respectée. Ainsi, lorsque **BAService** est arrêté, vous devez toujours redémarrer le site web **Service de l’adaptateur BizTalk** à la place. Ne permet pas de démarrer ou d’arrêter l’application **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Les caractères spéciaux ne doivent pas être utilisés pour les noms d’adresse et d’entité de composants de métier
Vous n’employez pas les caractères spéciaux pour les noms d’adresse et d’entité de composants de métier. Si vous procédez ainsi, vous obtiendrez une erreur lors du déploiement du projet de BizTalk Service. Pour certains caractères like '%', le site Web du Service de l’adaptateur BizTalk peut entrer en état arrêté et que vous devrez démarrer manuellement.
### <a name="test-map-with-get-context-property"></a>Tester le mappage avec la propriété de contexte Get
Si une transformation contient une opération de mappage **d’Obtenir la propriété de contexte** , **Tester le mappage** échoue. Comme solution temporaire, remplacez l’opération de mappage **d’Obtenir la propriété de contexte** avec une chaîne de concaténer opération de mappage qui contient des données fictives. Vous remplir le schéma cible et que vous testez les autres fonctionnalités de transformation.
### <a name="test-map-property-does-not-display"></a>Mapper la propriété test ne s’affiche pas
Les propriétés de **Test Map** ne s’affichent pas dans Visual Studio. Cela peut se produire si la fenêtre **Propriétés** et la fenêtre de **L’Explorateur de solutions** ne sont pas simultanément ancrés. Pour résoudre ce problème, ancrez les **Propriétés** et les fenêtres de **L’Explorateur de solutions** .  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Nouvelle mise en forme de DateTime déroulante est grisée.
Lorsqu’une opération de mappage de remettre en forme DateTime est ajoutée à l’aire de conception et configurée, la liste déroulante Format peut-être être grisée. Cela peut se produire si l’affichage de l’ordinateur est la valeur **Moyenne-125 %** ou **grande – 150 %**. Pour le résoudre, définissez l’affichage **plus petite-100 % (par défaut)** à l’aide de la procédure ci-dessous :  
1. Ouvrez le **Panneau de configuration** , puis cliquez sur **apparence et personnalisation**.
2. Cliquez sur **Afficher**.
3. Cliquez sur **plus petite-100 % (par défaut)** et cliquez sur **Appliquer**.

La liste déroulante **Format** doit maintenant fonctionner comme prévu.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Accords en double dans le portail de Services de BizTalk
Envisagez le scénario suivant :
1. Créer un contrat à l’aide de l’API OM de la gestion des commerciaux du partenaire.
2. Ouvrir l’accord dans le portail de Services de BizTalk dans deux onglets différents.
3. Déployer l’accord à partir des deux onglets.
4. Par conséquent, les deux accords se déploie entraînant des entrées en double dans le portail de Services de BizTalk

**Solution de contournement**. Ouvrez l’un des accords en double dans le portail de Services de BizTalk et annuler le déploiement.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Ponts n’utilisent pas de certificat mis à jour même après qu’un certificat qui a été mis à jour dans le magasin de l’artefact
Examinons les scénarios suivants :  

**Scénario 1 : Utilisation de certificats basés sur l’empreinte numérique pour sécuriser le transfert de messages à partir d’un pont vers un point de terminaison de service**  
Imaginez un scénario où vous utilisez des certificats basés sur l’empreinte numérique dans votre projet de BizTalk Service. Vous mettre à jour le certificat dans le portail de Services de BizTalk avec le même nom mais une empreinte numérique différente, mais ne mettez pas à jour le projet BizTalk Service en conséquence. Dans un tel scénario, la passerelle peut continuer à traiter les messages, car les anciennes données de certificat peuvent rester dans le cache de canal. Après cela, le traitement des messages échoue.  

**Solution de contournement**: le certificat dans le projet de BizTalk Service de mise à jour et de redéployer le projet.  

**Scénario 2 : À l’aide de comportements basés sur un nom pour identifier des certificats pour sécuriser le transfert de messages à partir d’un pont vers un point de terminaison de service**

Imaginez un scénario où vous utilisez des comportements basés sur le nom pour identifier des certificats dans votre projet de BizTalk Service. Mettre à jour le certificat dans le portail de Services de BizTalk, mais ne mettez pas à jour le projet BizTalk Service en conséquence. Dans un tel scénario, la passerelle peut continuer à traiter les messages, car les anciennes données de certificat peuvent rester dans le cache de canal. Après cela, le traitement des messages échoue.  

**Solution de contournement**: le certificat dans le projet de BizTalk Service de mise à jour et de redéployer le projet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Ponts de continuent à traiter les messages, même lorsque la base de données SQL est en mode hors connexion
Les ponts de BizTalk Services continuent à traiter des messages pendant un certain temps, même si Microsoft Azure SQL (qui stocke les informations en cours d’exécution, tels que des artefacts déployés et pipelines), est en mode hors connexion. C’est parce que les Services BizTalk utilise les artefacts de mise en cache et de la configuration du pont.
Si vous ne souhaitez pas les ponts pour traiter les messages lorsque la base de données SQL est en mode hors connexion, vous pouvez utiliser les applets de commande PowerShell de Services BizTalk pour arrêter ou suspendre le BizTalk Service. Consultez un [Exemple de gestion de Service de BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=329019) pour les applets de commande Windows PowerShell pour gérer les opérations.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Lire le message XML dans un composant de code personnalisé d’un pont inclut un caractère supplémentaire de la nomenclature
Imaginez un scénario où vous souhaitez lire un message XML au sein d’un code personnalisé d’un pont. Si vous utilisez la System.Text.Encoding.UTF8.GetString(bytes) API .NET un caractère supplémentaire de la nomenclature est inclus dans la sortie, au début du message. Par conséquent, si vous ne souhaitez pas que la sortie pour inclure le caractère supplémentaire de la nomenclature, vous devez utiliser ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Envoi de messages à un pont à l’aide de WCF n’évolue pas
Les messages envoyés à un pont à l’aide de WCF n’est pas adapté. Vous devez utiliser à la place de HttpWebRequest si vous souhaitez un client évolutif.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>Mise à niveau : Erreur de fournisseur de jeton après la mise à niveau à partir de l’aperçu des Services BizTalk à disponibilité générale
Il y a accord de AS2 ou EDI avec les lots actives. Lorsque le BizTalk Service est mis à niveau à partir de l’aperçu vers la GA, les éléments suivants peuvent se produire :
* Erreur : Le fournisseur de jetons n’a pas pu fournir un jeton de sécurité. Fournisseur de jeton a retourné le message : le nom distant n’a pas pu être résolu.

* Les tâches de traitement par lots sont annulées.

**Solution**: une fois le BizTalk Service est mis à jour à disponibilité générale, redéployez l’accord.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>Mise à niveau : Boîte à outils affiche les icônes de pont ancien après la mise à niveau de la SDK des Services BizTalk
Une fois que vous mettez à niveau une version antérieure de la SDK des Services BizTalk, qui avait des anciennes icônes représentant les ponts, la boîte à outils continue à afficher les anciennes icônes pour les ponts. Toutefois, si vous ajoutez un pont vers l’aire du concepteur projet BizTalk Service, la surface affiche l’icône de nouveau.  

**Solution de contournement**. Vous pouvez contourner ce problème en supprimant les fichiers de .tbd sous <system drive>: \Users\<utilisateur > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>Mise à niveau : Mise à jour du portail de BizTalk à partir de l’aperçu à GA peut afficher un message d’erreur indiquant que la fonctionnalité EDI n’est pas disponible
Si vous êtes connecté au portail de Services BizTalk tandis que les Services BizTalk est mis à niveau à partir de l’aperçu vers la GA, vous pouvez obtenir le message d’erreur suivant sur le portail :  

Cette fonctionnalité n’est pas disponible dans cette édition de Microsoft Azure BizTalk Services. Pour utiliser ces fonctionnalités de passer à l’édition appropriée.  

**Résolution**: déconnexion à partir du portail, fermer et ouvrir le navigateur, puis journal dans le portail.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>Mise à niveau : Suivi des nouvelles données ne s’affiche pas une fois que les Services BizTalk est mis à niveau vers la GA
Supposons un scénario où vous avez un pont XML déployé sur l’abonnement de l’aperçu des Services BizTalk. Vous envoyez des messages vers la passerelle et les données de suivi correspondant est disponible sur le portail de Services de BizTalk. Maintenant, si les bits de runtime portail de Services de BizTalk et les Services BizTalk sont mis à niveau vers la GA et vous envoyez un message au même point de terminaison pont déployé précédemment, les données de suivi ne s’affiche pas pour les messages envoyés après la mise à niveau.  

### <a name="pipelines-vs-bridges"></a>Ponts de pipelines v/s
Tout au long de ce document, le terme « pipelines » et « ponts » sont utilisés indifféremment. Les deux signifient essentiellement la même chose, c'est-à-dire, une unité de traitement de message déployée sur BizTalk Services.  

### <a name="concepts"></a>Concepts  

[Services BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
