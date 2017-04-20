<properties 
    pageTitle="Vue d’ensemble de le X12 et le module d’intégration entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Apprenez à utiliser X12 accords pour créer des applications de logique" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-x12"></a>Intégration avec X12 

>[AZURE.NOTE]Cette fonctionnalité de couvre le X12 page de logique d’applications. Pour plus d’informations sur EDIFACT, cliquez [ici](app-service-logic-enterprise-integration-edifact.md).

## <a name="create-an-x12-agreement"></a>Créer une X12 contrat 
Avant de X12 pouvez échanger des messages, vous devez créer un X12 accord et les stocker dans votre compte d’intégration. Les étapes suivantes vous guidera dans le processus de création d’un X12 accord.

### <a name="heres-what-you-need-before-you-get-started"></a>Voici ce dont vous avez besoin avant de commencer
- Un [compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) définies dans votre abonnement Azure  
- Au moins deux [partenaires](./app-service-logic-enterprise-integration-partners.md) déjà défini dans votre compte d’intégration  

>[AZURE.NOTE]Lors de la création d’un accord, le contenu du fichier de contrat doit correspondre le type d’accord.    


Une fois que vous avez [créé un compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) et [ajouté des partenaires](./app-service-logic-enterprise-integration-partners.md), vous pouvez créer un X12 accord en suivant ces étapes :  

### <a name="from-the-azure-portal-home-page"></a>À partir de la page d’accueil de portail Azure

Après la connexion au [portail Azure](http://portal.azure.com "Azure portal"):  
1. Dans le menu de gauche, sélectionnez **Parcourir** .  

>[AZURE.TIP]Si vous ne voyez pas le lien **Parcourir** , vous devrez tout d’abord, développez le menu. Pour ce faire, cliquez sur le lien **Afficher le menu** qui est situé en haut à gauche du menu réduit.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Type *d’intégration* dans la zone de filtre de recherche, puis sélectionnez les **Comptes de l’intégration** à partir de la liste des résultats.       
![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)    
3. Dans la lame de **Comptes d’intégration** qui s’ouvre, sélectionnez le compte de l’intégration dans lequel vous allez créer l’accord. Si vous ne voyez pas une intégration des listes, comptes [créer un premier](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)  
4.  Sélectionnez la **accords de** mosaïque. Si vous ne voyez pas les accords de mosaïque, ajouter en premier.   
![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)     
5. Sélectionnez le bouton **Ajouter** dans la blade d’accords qui s’ouvre.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Entrez un **nom** pour votre accord, puis sélectionnez le **type d’accord** **Partenaire d’accueil**, **Identité de l’hôte**, **Partenaire de l’invité**, **Identité des invités**, de la lame d’accords qui s’ouvre.  
![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)  
7. Une fois que vous avez défini la réception des propriétés de paramètres, cliquez sur le bouton **OK**  
Nous allons continuer :  
8. Sélectionnez **Recevoir des paramètres** pour configurer la manière dont les messages reçus via le présent accord doivent être traités.  
9. Le contrôle de recevoir les paramètres est divisé dans les sections suivantes, y compris les identificateurs, accusé de réception, schémas, des enveloppes, des numéros de contrôle, des Validations et paramètres internes. Configurer ces propriétés en fonction de votre accord avec le partenaire que vous échangez des messages avec. Voici un aperçu de ces contrôles, de les configurer en fonction de comment vous voulez que le présent accord pour identifier et gérer les messages entrants :  
![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)  
10. Cliquez sur le bouton **OK** pour enregistrer vos paramètres.  

### <a name="identifiers"></a>Identificateurs

|Propriété|Description |
|---|---|
|Serveur ISA1 (qualificateur d’autorisation)|Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation.|
|ISA2|Facultatif. Entrez la valeur des informations d’autorisation. Si la valeur entrée pour le serveur ISA1 est pas 00, entrez au moins un caractère alphanumérique et un maximum de 10.|
|ISA3 (identificateur de sécurité)|Dans la liste déroulante, sélectionnez la valeur d’identificateur de sécurité.|
|ISA4|Facultatif. Entrez la valeur des informations de sécurité. Si la valeur entrée pour ISA3 est pas 00, entrez au moins un caractère alphanumérique et un maximum de 10.|

### <a name="acknowledgments"></a>Accusés de réception 

|Propriété|Description |
|----|----|
|TA1 et un attendu|Activez cette case à cocher pour renvoyer un accusé de réception technique (TA1 et un) à l’expéditeur de l’échange. Ces accusés de réception sont envoyés à l’expéditeur de l’échange basé sur les paramètres d’envoi de l’accord.|
|Immobilisation attendue|Activez cette case à cocher pour renvoyer un accusé de réception fonctionnel (FA) à l’expéditeur de l’échange. Sélectionnez si vous souhaitez que les accusés de réception 997 ou 999, basés sur les versions de schéma que vous utilisez. Ces accusés de réception sont envoyés à l’expéditeur de l’échange basé sur les paramètres d’envoi de l’accord.|
|Inclure la boucle de AK2/IK2|Cochez cette case pour activer la génération de boucles de AK2 dans des accusés de réception fonctionnels pour les ensembles de transactions acceptées. Remarque : Cette case à cocher est activée uniquement si vous avez sélectionné la case à cocher immobilisation attendue.|

### <a name="schemas"></a>Schémas

Sélectionnez un schéma pour chaque type de transaction (ST1) et l’Application de l’expéditeur (GS2). Le pipeline de réception désassemble le message entrant en faisant correspondre les valeurs de ST1 et GS2 dans le message entrant avec les valeurs que vous définissez ici et le schéma du message entrant avec le schéma que vous définissez ici.

|Propriété|Description |
|----|----|
|Version|Sélectionnez le X12 version|
|Type de transaction (ST01)|Sélectionnez le type de transaction|
|Application de l’expéditeur (GS02)|Sélectionnez l’application de l’expéditeur|
|Schéma|Sélectionnez le fichier de schéma que vous souhaitez nous. Fichiers de schéma se trouvent dans votre compte d’intégration.|

### <a name="envelopes"></a>Enveloppes

|Propriété|Description |
|----|----|
|Utilisation de ISA11|Ce champ permet de spécifier le séparateur dans un ensemble de transactions :</br></br>Sélectionnez l’identificateur Standard d’utiliser la notation décimale de «. » pipeline de réception au lieu de la notation décimale du document entrant dans l’EDI.</br></br>Sélectionnez séparateur de répétition pour spécifier le séparateur pour les occurrences répétées d’un élément de données simple ou d’une structure de données répétées. Par exemple, (^) est généralement utilisée comme séparateur de répétition. Pour les schémas HIPAA, vous ne pouvez utiliser que (^).|

### <a name="control-numbers"></a>Numéros de contrôle

|Propriété|Description |
|----|----|
|Interdire les doublons du numéro de contrôle d’échange|Activez cette option pour bloquer les échanges en double. Si sélectionné, le portail de Services BizTalk vérifie que le numéro de contrôle d’échange (ISA13) pour l’échange reçu ne correspond pas au nombre de contrôle d’échange. Si une correspondance est détectée, le pipeline de réception ne traite pas de l’échange.<br/>Si vous choisi pour ne pas autoriser les numéros de contrôle d’échange en double, vous pouvez spécifier le nombre de jours auquel le contrôle est effectué en attribuant la valeur appropriée pour la vérification des doublons ISA13 tous les x jours.|
|Interdire les doublons numéros de contrôle de groupe|Activez cette option pour bloquer les échanges avec les numéros de contrôle de groupe en double.|
|Interdire les doublons numéros de Transaction jeu de contrôle|Activez cette option pour bloquer les échanges avec les numéros de contrôle de jeu de transaction en double.|

### <a name="validations"></a>Validations

|Propriété|Description |
|----|----|
|Type de message|Type de Message EDI, comme commande fournisseur-850 ou accusé de réception de 999-mise en œuvre.|
|Validation de l’EDI|Exécute la validation de l’EDI sur les types de données, tel que défini par les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vide et des séparateurs à droite.|
|Validation étendue|Si le type de données n’est pas EDI, validation est sur la spécification d’élément de données et répétition, les énumérations et la validation des données élément durée (min. / max.).|
|Autoriser les zéros de début ou de fin|L’espace supplémentaire et zéro caractères de début ou de fin sont conservés. Ils ne sont pas supprimés.|
|Stratégie de séparateur à droite|Génère des séparateurs à droite sur l’échange reçu. Les options incluent obligatoire, facultatif et non autorisé.|

### <a name="internal-settings"></a>Paramètres internes

|Propriété|Description |
|----|----|
|Convertir du format décimal implicite Nn à 10 valeur numérique de base|Convertit un numéro EDI est spécifié avec le format Nn en base 10 valeur numérique dans le XML intermédiaire dans le portail de Services de BizTalk.|
|Créer des balises XML vides si les séparateurs à droite sont autorisées.|Activez cette case à cocher pour inclure des balises XML vides pour les séparateurs à droite de l’expéditeur de l’échange.|
|Processus de traitement par lots entrant|Échange de fractionnement en tant que jeux de transactions - suspendre des ensembles de transaction sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct en appliquant l’enveloppe appropriée à l’ensemble de la transaction. Avec cette option, si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, puis interrompt les Services BizTalk que les jeux de transactions. </br></br>Échange de fractionnement en tant que jeux de transactions - suspendre l’échange sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Avec cette option, si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, puis Services BizTalk suspend l’échange complet.</br></br>Conserver l’échange - suspendre des ensembles de transaction sur l’erreur : toucher l’échange, la création d’un document XML pour l’échange de lots entier. Avec cette option, si onAe ou transaction plus se définit dans l’échange Échec de la validation, puis interrompt les Services BizTalk uniquement les jeux de transactions, tout en continuant à traiter tous les autres ensembles de transaction.</br></br>Conserver l’échange - échange de suspension sur l’erreur : toucher l’échange, la création d’un document XML pour l’échange de lots entier. Avec cette option, si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, puis Services BizTalk suspend l’échange complet.</br></br>|

Votre contrat est prêt à traiter les messages entrants qui sont conformes au schéma que vous avez sélectionné.

Pour configurer les paramètres qui gèrent les messages que vous envoyez aux partenaires :  
11. Sélectionnez **Envoyer des paramètres** pour configurer la manière dont les messages envoyés via le présent accord doivent être traités.  

Le contrôle des paramètres d’envoi est divisé dans les sections suivantes, y compris les identificateurs, accusé de réception, schémas, enveloppes, numéros de contrôle, les jeux de caractères et les séparateurs et la Validation. 

Voici un aperçu de ces contrôles. Effectuez les sélections en fonction de la façon dont vous souhaitez gérer les messages que vous envoyez aux partenaires via le présent accord :   
![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)  
12. Cliquez sur le bouton **OK** pour enregistrer vos paramètres.  

### <a name="identifiers"></a>Identificateurs
|Propriété|Description |
|----|----|
|Qualificateur d’autorisation (ISA1)|Dans la liste déroulante, sélectionnez la valeur du qualificateur d’autorisation.|
|ISA2|Entrez la valeur des informations d’autorisation. Si cette valeur est différente de 00, puis entrez au moins un caractère alphanumérique et un maximum de 10.|
|Identificateur de sécurité (ISA3)|Dans la liste déroulante, sélectionnez la valeur d’identificateur de sécurité.|
|ISA4|Entrez la valeur des informations de sécurité. Si cette valeur n’est pas 00, pour la zone de texte valeur (ISA4), puis entrez une valeur alphanumérique au minimum et un maximum de 10.|

### <a name="acknowledgment"></a>Accusé de réception
|Propriété|Description |
|----|----|
|TA1 et un attendu|Activez cette case à cocher pour renvoyer un accusé de réception technique (TA1 et un) à l’expéditeur de l’échange. Ce paramètre spécifie que le partenaire de l’hôte qui envoie le message demande un accusé de réception du partenaire invité dans l’accord. Ces accusés de réception sont attendus par le partenaire d’accueil selon les paramètres de réception de l’accord.|
|Immobilisation attendue|Activez cette case à cocher pour renvoyer un accusé de réception fonctionnel (FA) à l’expéditeur de l’échange, puis sélectionnez si vous souhaitez que les accusés de réception 997 ou 999, basés sur les versions de schéma que vous utilisez. Ces accusés de réception sont attendus par le partenaire d’accueil selon les paramètres de réception de l’accord.|
|Version de l’immobilisation|Sélectionnez la version de l’immobilisation|

### <a name="schemas"></a>Schémas
|Propriété|Description |
|----|----|
|Version|Sélectionnez le X12 version|
|Type de transaction (ST01)|Sélectionnez le type de transaction|
|SCHÉMA|Sélectionnez le schéma à utiliser. Les schémas sont situés dans votre compte d’intégration. Pour accéder à vos schémas, d’abord lier votre compte d’intégration à votre application de logique.|

### <a name="envelopes"></a>Enveloppes
|Propriété|Description |
|----|----|
|Utilisation de ISA11|Ce champ permet de spécifier le séparateur dans un ensemble de transactions :</br></br>Sélectionnez l’identificateur Standard d’utiliser la notation décimale de «. » pipeline de réception au lieu de la notation décimale du document entrant dans l’EDI.</br></br>Sélectionnez séparateur de répétition pour spécifier le séparateur pour les occurrences répétées d’un élément de données simple ou d’une structure de données répétées. Par exemple, (^) est généralement utilisée comme séparateur de répétition. Pour les schémas HIPAA, vous ne pouvez utiliser que (^).</br>|
|Séparateur de répétition|Entrez le séparateur de répétition|
|Numéro de Version de contrôle (ISA12)|Sélectionnez la version de la X12 standard qui est utilisée par le portail de Services de BizTalk pour générer un échange sortant.|
|Indicateur d’utilisation (ISA15)|Si le contexte d’un échange est informations (I), les données de production (P), permet d’entrer ou tester des données (T). Réception de l’EDI pipeline favorise cette propriété au contexte.|
|Schéma|Vous pouvez saisir la façon dont le portail de Services BizTalk génère les segments GS et ST pour un échange codé en X12 qu’il envoie au Pipeline d’envoi.</br></br>Vous pouvez associer les valeurs des éléments de données GS1, GS2, GS3, GS4, GS5, GS7 et GS8 avec les valeurs du Type de Transaction et les éléments de données de Version/version. Lorsque le portail de Services BizTalk détermine qu’un message XML a les valeurs définies pour le Type de Transaction et les éléments de Version/version sur une ligne de la grille, puis il remplit les éléments de données GS1, GS2, GS3, GS4, GS5, GS7 et GS8 dans l’enveloppe de l’échange sortant avec les valeurs de la même ligne de la grille. Les valeurs du Type de Transaction et des éléments de la Version/version doivent être uniques.</br></br>Facultatif. Pour GS1, sélectionnez une valeur pour le code fonctionnel dans la liste déroulante.</br></br>Obligatoire. Pour GS2, entrez une valeur alphanumérique pour l’expéditeur de l’application avec un minimum de deux caractères et un maximum de 15 caractères.</br></br>Obligatoire. Pour GS3, entrez une valeur alphanumérique pour le récepteur de l’application avec un minimum de deux caractères et un maximum de 15 caractères.</br></br>Facultatif. Pour GS4, sélectionnez CCYYMMDD ou aammjj.</br></br>Facultatif. Pour GS5, sélectionnez HHMM, HHMMSS ou HHMMSSdd.</br></br>Facultatif. Pour GS7, sélectionnez une valeur pour l’organisme responsable de la liste déroulante.</br></br>Facultatif. Pour GS8, entrez une valeur alphanumérique pour le document identifié avec un minimum d’un caractère et un maximum de 12 caractères.</br></br>**Remarque**: ce sont les valeurs que le portail de Services BizTalk entre les champs GS de l’échange de génération si le Type de Transaction et les éléments de Version/version dans la même ligne sont une correspondance pour ceux associés à l’échange.|

### <a name="control-numbers"></a>Numéros de contrôle
|Propriété|Description |
|----|----|
|Numéro de contrôle (ISA13) d’échange|Obligatoire. Entrez une plage de valeurs pour le numéro de contrôle d’échange utilisé par le portail de Services de BizTalk pour générer un échange sortant. Entrez une valeur numérique avec un minimum de 1 et 999999999 au maximum.|
|Numéro de contrôle de groupe (GS06)|Obligatoire. Entrez la plage de nombres que le portail de Services BizTalk doit utiliser pour le numéro de contrôle de groupe. Entrez une valeur numérique avec un minimum d’un caractère et un maximum de neuf caractères.|
|Numéro de contrôle de jeu de transaction (ST02)|Pour le numéro de contrôle de la valeur de Transaction (ST02), entrez une plage de valeurs numériques pour les champs obligatoires de milieu et de valeurs alphanumériques pour le préfixe facultatif et d’un suffixe. La longueur maximale de tous les champs de quatre est de neuf caractères.|
|Préfixe|Pour désigner la plage de numéros de contrôle de jeu de transaction utilisés dans un accusé de réception, entrez des valeurs dans les champs de nombre (ST02) de contrôle de l’accusé de réception. Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont nécessaires et contiennent les valeurs minimales et maximales pour le numéro de contrôle ; le préfixe et le suffixe sont facultatifs. La longueur maximale pour les trois champs est de neuf caractères.|
|Suffixe|Pour désigner la plage de numéros de contrôle de jeu de transaction utilisés dans un accusé de réception, entrez des valeurs dans les champs de nombre (ST02) de contrôle de l’accusé de réception. Entrez une valeur numérique pour les deux champs du milieu et une valeur alphanumérique (le cas échéant) pour les champs préfixe et suffixe. Les champs du milieu sont nécessaires et contiennent les valeurs minimales et maximales pour le numéro de contrôle ; le préfixe et le suffixe sont facultatifs. La longueur maximale pour les trois champs est de neuf caractères.|

### <a name="character-sets-and-separators"></a>Jeux de caractères et les séparateurs
Autres que le jeu de caractères, vous pouvez entrer un autre jeu de séparateurs à utiliser pour chaque type de message. Si un jeu de caractères n’est pas spécifié pour un schéma de message donné, le jeu de caractères par défaut est utilisé.

|Propriété|Description |
|----|----|
|Jeu de caractères à utiliser|Sélectionnez le X12 jeu de caractères à valider les propriétés que vous entrez pour l’accord.</br></br>**Remarque**: le portail de Services BizTalk utilise uniquement ce paramètre pour valider les valeurs entrées pour les propriétés de contrat associé. Le pipeline de réception ou d’un pipeline d’envoi ignore cette propriété de jeu de caractères lors du traitement de l’exécution.|
|Schéma|Symbole de sélectionner le signe plus (+) et sélectionnez un schéma dans la liste déroulante. Pour le schéma sélectionné, sélectionnez les séparateurs à utiliser :</br></br>Séparateur d’éléments composant – entrée un caractère pour séparer les éléments de données composite.</br></br>Séparateur d’élément de données – entrée un caractère pour séparer les éléments de données simples au sein des éléments de données composite.</br></br></br></br>Caractère de remplacement – Activez cette case à cocher si la charge utile de données contient des caractères qui sont également utilisés comme données, segment ou des séparateurs de composant. Vous pouvez ensuite entrer un caractère de remplacement. Lors de la génération de la sortie X12 de message, toutes les occurrences des caractères de séparation dans la charge utile de données sont remplacées par le caractère spécifié.</br></br>Terminateur de segment, tapez un seul caractère pour indiquer la fin d’un segment de l’EDI.</br></br>Le suffixe – sélectionnez le caractère qui est utilisé avec l’identificateur de segment. Si vous désignez un suffixe, l’élément de données de terminateur de segment peut être vide. Si le terminateur de segment est vide, vous devez désigner un suffixe.|

### <a name="validation"></a>Validation
|Propriété|Description |
|----|----|
|Type de message|Cette option permet à la validation sur le récepteur de l’échange. Cette validation effectue une validation de l’EDI sur des éléments de données de l’ensemble de la transaction, la validation des types de données, les restrictions de longueur et les éléments de données vide et séparateurs de fin.|
|Validation de l’EDI||
|Validation étendue|Cette option permet une validation étendue d’échanges reçu de l’expéditeur de l’échange. Cela inclut la validation de longueur de champ, facultativité et nombre de répétitions en plus de la validation de type de données XSD. Vous pouvez activer le contrôle de l’extension sans activer la validation de l’EDI, ou vice versa.|
|Autoriser les zéros de début / fin|Cette option indique qu’un échange EDI a reçu de la partie n’échoue pas de validation si l’élément de données dans un échange EDI n’est pas conforme à sa longueur requise car ou les espaces de fin, mais il est conforme à ses critères de longueur lorsqu’ils sont supprimés.|
|Séparateur à droite|Cette option indique un échange EDI a reçu de la partie n’échoue pas la validation si l’élément de données dans un échange EDI n’est pas conforme à ses critères de longueur en raison des zéros (début ou fin) ou des espaces, mais n’est conforme à ses critères de longueur lorsqu’ils sont supprimés.</br></br>Si vous ne souhaitez pas autoriser des délimiteurs de fin et les séparateurs dans un échange reçu de l’expéditeur de l’échange, sélectionnez pas autorisé. Si l’échange contient des séparateurs et des délimiteurs de fin, il est déclaré non valide.</br></br>Sélectionnez manuel pour accepter les échanges avec ou sans délimiteurs de fin et les séparateurs.</br></br>Sélectionnez obligatoire si l’échange reçu doit contenir des séparateurs et des délimiteurs de fin.|

Après avoir sélectionné **OK** sur les blades d’ouvrir :  
13. Sélectionnez la mosaïque **d’accords** sur la lame de compte de l’intégration, et vous verrez l’accord nouvellement ajouté répertorié.  
![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Pour en savoir plus
- [En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise")  
