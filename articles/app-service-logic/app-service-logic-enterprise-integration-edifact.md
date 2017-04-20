<properties 
    pageTitle="Intégration avec EDIFACT | Microsoft Azure" 
    description="Apprenez à utiliser les ententes EDIFACT pour créer des applications de logique" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="jonfan"/>

# <a name="enterprise-integration-with-edifact"></a>Intégration avec EDIFACT 

> [AZURE.NOTE] Cette page couvre les fonctionnalités EDIFACT de logique d’applications. Pour plus d’informations sur X12, cliquez [ici](app-service-logic-enterprise-integration-x12.md).

## <a name="create-an-edifact-agreement"></a>Créer un accord EDIFACT 
Avant de pouvoir échanger des messages EDIFACT, vous devez créer un accord EDIFACT et le stocker dans votre compte d’intégration. La procédure suivante vous guidera tout au long du processus de création d’un accord EDIFACT.

### <a name="heres-what-you-need-before-you-get-started"></a>Voici ce dont vous avez besoin avant de commencer
- Un [compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) définies dans votre abonnement Azure  
- Au moins deux [partenaires](./app-service-logic-enterprise-integration-partners.md) déjà défini dans votre compte d’intégration  

>[AZURE.NOTE]Lors de la création d’un accord, le contenu de messages vous allez que recevoir/envoyer vers et à partir du partenaire doit correspondre le type de contrat.    


Une fois que vous avez [créé un compte d’intégration](./app-service-logic-enterprise-integration-accounts.md) et [ajouté des partenaires](./app-service-logic-enterprise-integration-partners.md), vous pouvez créer un accord EDIFACT en suivant ces étapes :  

### <a name="from-the-azure-portal-home-page"></a>À partir de la page d’accueil de portail Azure

Après la connexion au [portail Azure](http://portal.azure.com "Azure portal"):  
1. Dans le menu de gauche, sélectionnez **Parcourir** .  

>[AZURE.TIP]Si vous ne voyez pas le lien **Parcourir** , vous devrez tout d’abord, développez le menu. Pour ce faire, cliquez sur le lien **Afficher le menu** qui est situé en haut à gauche du menu réduit.  

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)    
2. Type *d’intégration* dans la zone de filtre de recherche, puis sélectionnez les **Comptes de l’intégration** à partir de la liste des résultats.       
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)    
3. Dans la lame de **Comptes d’intégration** qui s’ouvre, sélectionnez le compte de l’intégration dans lequel vous allez créer l’accord. Si vous ne voyez pas une intégration des listes, comptes [créer un premier](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)  
4.  Sélectionnez la **accords de** mosaïque. Si vous ne voyez pas les accords de mosaïque, ajouter en premier.   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)     
5. Sélectionnez le bouton **Ajouter** dans la blade d’accords qui s’ouvre.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)  
6. Entrez un **nom** pour votre accord, puis sélectionnez le **type d’accord** pour EDIFACT, **Partenaire d’accueil**, **Identité de l’hôte**, **Partenaire de l’invité**, **Identité des invités**, de la lame d’accords qui s’ouvre.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)  
7. Après avoir défini les propriétés de l’accord, sélectionnez **Recevoir des paramètres** pour configurer la manière dont les messages reçus via le présent accord doivent être traités.  
8. Le contrôle de recevoir les paramètres est divisé dans les sections suivantes, y compris les identificateurs d’accusé de réception, schémas, numéros de contrôle, Validation, paramètres internes et traitement par lots. Configurer ces propriétés en fonction de votre accord avec le partenaire que vous échangez des messages avec. Voici un aperçu de ces contrôles, de les configurer en fonction de comment vous voulez que le présent accord pour identifier et gérer les messages entrants :  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)  
9. Cliquez sur le bouton **OK** pour enregistrer vos paramètres.  

### <a name="identifiers"></a>Identificateurs

|Propriété|Description |
|---|---|
|UNB6.1 (mot de passe de référence destinataire)|Saisissez une valeur alphanumérique, comprise entre 1 et 14 caractères.|
|UNB6.2 (qualificateur référence destinataire)|Saisissez une valeur alphanumérique dont au moins un caractère et un maximum de deux caractères.|

### <a name="acknowledgments"></a>Accusés de réception 

|Propriété|Description |
|----|----|
|Réception du Message (commande)|Activez cette case à cocher pour renvoyer un accusé de réception technique (commande) à l’expéditeur de l’échange. L’accusé de réception est envoyé à l’expéditeur de l’échange basé sur les paramètres d’envoi de l’accord.|
|Accusé de réception (commande)|Activez cette case à cocher pour renvoyer un accusé de réception fonctionnel (commande) à l’expéditeur de l’échange de l’accusé de réception est envoyé à l’expéditeur de l’échange basé sur les paramètres d’envoi de l’accord.|

### <a name="schemas"></a>Schémas

|Propriété|Description |
|----|----|
|UNH2.1 (TYPE)|Sélectionnez un type de jeu de transactions.|
|UNH2.2 (VERSION)|Entrez le numéro de version de message. (Minimum, un caractère, au maximum, trois caractères).|
|UNH2.3 (VERSION)|Entrez le numéro de version de message. (Minimum, un caractère, au maximum, trois caractères).|
|UNH2.5 (CODE ATTRIBUÉ ASSOCIÉ)|Permet d’entrer le code attribué. (Maximum de six caractères. Doit être alphanumérique).|
|UNG2.1 (ID DE L’EXPÉDITEUR APP)|Saisissez une valeur alphanumérique dont au moins un caractère et un maximum de 35 caractères.|
|UNG2.2 (QUALIFICATEUR DU CODE D’APPLICATION L’EXPÉDITEUR)|Saisissez une valeur alphanumérique, avec un maximum de quatre caractères.|
|SCHÉMA|Sélectionnez le schéma précédemment téléchargé que vous souhaitez utiliser à partir de votre compte d’intégration associé.|

### <a name="control-numbers"></a>Numéros de contrôle

|Propriété|Description |
|----|----|
|Interdire les doublons du numéro de contrôle d’échange|Cochez cette case pour bloquer les échanges en double. Si sélectionné, l’Action de décoder EDIFACT vérifie que le numéro de contrôle d’échange (UNB5) pour l’échange reçu ne correspond pas à un numéro de contrôle d’échange précédemment traitées. Si une correspondance est détectée, le l’échange n’est pas traité.
|Vérification des UNB5 en double tous les (jours)|Si vous choisi pour ne pas autoriser les numéros de contrôle d’échange en double, vous pouvez spécifier le nombre de jours auquel le contrôle est effectué en attribuant la valeur appropriée pour l’option **Check for UNB5 en double tous les (jours)** .|
|Interdire les doublons numéros de contrôle de groupe|Cochez cette case pour bloquer les échanges avec les numéros de contrôle de groupe en double (UNG5).|
|Interdire les doublons numéros de Transaction jeu de contrôle|Cochez cette case pour bloquer les échanges avec les numéros de contrôle de jeu de transaction en double (UNH1).|
|Numéro de contrôle EDIFACT accusé de réception|Pour désigner les numéros de référence de jeu de transaction à utiliser dans un accusé de réception, entrez une valeur pour le préfixe, une plage de numéros de référence et un suffixe.|

### <a name="validations"></a>Validations

|Propriété|Description |
|----|----|
|Type de message|Spécifiez le type de message. À la fin de chaque ligne de la validation, un autre sera automatiquement ajouté. Si aucune règle n’est spécifiée, la ligne marquée comme valeur par défaut est utilisée pour la validation.|
|Validation de l’EDI|Activez cette case à cocher pour effectuer une validation de l’EDI sur les types de données, tel que défini par les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vide et des séparateurs à droite.|
|Validation étendue|Activez cette case à cocher pour activer une validation étendue (XSD) d’échanges reçu de l’expéditeur de l’échange. Cela inclut la validation de longueur de champ, facultativité et nombre de répétitions en plus de la validation de type de données XSD.|
|Autoriser les zéros de début ou de fin|Sélectionnez **Autoriser** pour autoriser de début ou de fin zéros ; **Non autorisé** à ne pas autoriser le début ou de fin des zéros ou **Rogner** pour ajuster l’interligne et de fin zéros.|
|Supprimer les zéros de début ou de fin|Activez cette case à cocher pour ajuster les zéros de début ou de fin|
|Stratégie de séparateur à droite|Si vous ne souhaitez pas autoriser des délimiteurs de fin et les séparateurs dans un échange reçu de l’expéditeur de l’échange, sélectionnez **Pas autorisé** . Si l’échange contient des séparateurs et des délimiteurs de fin, il est déclaré non valide. Sélectionnez **facultatif** à accepter les échanges avec ou sans délimiteurs de fin et les séparateurs. Sélectionnez **obligatoire** si l’échange reçu doit contenir des séparateurs et des délimiteurs de fin.|

### <a name="internal-settings"></a>Paramètres internes

|Propriété|Description |
|----|----|
|Créer des balises XML vides si les séparateurs à droite sont autorisées.|Activez cette case à cocher pour inclure des balises XML vides pour les séparateurs à droite de l’expéditeur de l’échange.|
|Processus de traitement par lots entrant|Les options incluent :</br></br>**Échange de fractionnement en tant que jeux de transactions - suspendre des ensembles de Transaction sur l’erreur**: analyse de chaque opération définie dans un échange dans un document XML distinct en appliquant l’enveloppe appropriée à l’ensemble de la transaction. Avec cette option, si un ou plusieurs ensembles de transaction dans l’échange échouent de la validation, puis que les jeux de transaction sont suspendus. Échange de fractionnement en tant que jeux de transactions - suspendre l’échange sur erreur : analyse de chaque opération définie dans un échange dans un document XML distinct en appliquant l’enveloppe appropriée. Avec cette option, si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, puis l’échange entière sera suspendue.</br></br>**Échange de conserver - suspendre des ensembles de Transaction sur l’erreur**: toucher l’échange, la création d’un document XML pour l’échange de lots entier. Avec cette option, si un ou plusieurs ensembles de transaction dans l’échange échouent de la validation, puis que les jeux de transaction sont suspendus, pendant le traitement de tous les autres ensembles de transaction.</br></br>**Conserver un échange - suspendre l’échange sur erreur**: toucher l’échange, la création d’un document XML pour l’échange de lots entier. Avec cette option, si un ou plusieurs des transactions se définit dans l’échange Échec de la validation, puis l’échange entière est suspendue.|

Votre contrat est prêt à traiter les messages entrants qui sont conformes aux paramètres sélectionnés.

Pour configurer les paramètres qui gèrent les messages que vous envoyez aux partenaires :  
10. Sélectionnez **Envoyer des paramètres** pour configurer la manière dont les messages envoyés via le présent accord doivent être traités.  

Le contrôle des paramètres d’envoi est divisé dans les sections suivantes, y compris les identificateurs, accusé de réception, schémas, enveloppes, jeux de caractères et les séparateurs, les numéros de contrôle et la Validation. 

Voici un aperçu de ces contrôles. Effectuez les sélections en fonction de la façon dont vous souhaitez gérer les messages que vous envoyez aux partenaires via le présent accord :   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)    
11. Cliquez sur le bouton **OK** pour enregistrer vos paramètres.  

### <a name="identifiers"></a>Identificateurs
|Propriété|Description |
|----|----|
|UNB1.2 (version de la syntaxe)|Sélectionnez une valeur comprise entre **1** et **4**.|
|UNB2.3 (adresse routage inverse de l’expéditeur)|Saisissez une valeur alphanumérique dont au moins un caractère et un maximum de 14 caractères.|
|UNB3.3 (adresse de destinataire de routage inverse)|Saisissez une valeur alphanumérique dont au moins un caractère et un maximum de 14 caractères.|
|UNB6.1 (mot de passe de référence destinataire)|Saisissez une valeur alphanumérique dont un minimum et un maximum de 14 caractères.|
|UNB6.2 (qualificateur référence destinataire)|Saisissez une valeur alphanumérique dont au moins un caractère et un maximum de deux caractères.|
|UNB7 (ID de référence d’Application)|Entrez une valeur alphanumérique dont au moins un caractère et un maximum de 14 caractères|

### <a name="acknowledgment"></a>Accusé de réception
|Propriété|Description |
|----|----|
|Réception du Message (commande)|Activez cette case à cocher si le partenaire hébergé s’attend à recevoir pour recevoir un accusé de réception technique (commande). Ce paramètre spécifie que le partenaire hébergé, qui envoie le message, demande un accusé de réception du partenaire de l’invité.|
|Accusé de réception (commande)|Activez cette case à cocher si le partenaire hébergé s’attend à recevoir un accusé de réception fonctionnel (commande). Ce paramètre spécifie que le partenaire hébergé, qui envoie le message, demande un accusé de réception du partenaire de l’invité.|
|Générer la boucle SG1/SG4 pour jeux de transactions acceptées|Si vous avez choisi de demander un accusé de réception fonctionnel, activez cette case à cocher pour forcer la génération de SG1/SG4 boucles de commande de transactions opérationnelles pour les ensembles de transactions acceptées.|

### <a name="schemas"></a>Schémas
|Propriété|Description |
|----|----|
|UNH2.1 (TYPE)|Sélectionnez un type de jeu de transactions.|
|UNH2.2 (VERSION)|Entrez le numéro de version de message.|
|UNH2.3 (VERSION)|Entrez le numéro de version de message.|
|SCHÉMA|Sélectionnez le schéma à utiliser. Les schémas sont situés dans votre compte d’intégration. Pour accéder à vos schémas, d’abord lier votre compte d’intégration à votre application de logique.|

### <a name="envelopes"></a>Enveloppes
|Propriété|Description |
|----|----|
|UNB8 (Code de priorité de traitement)|Entrez une valeur alphabétique qui n’est pas plus d’un caractère de long.|
|UNB10 (accord de Communication)|Saisissez une valeur alphanumérique dont au moins un caractère et un maximum de 40 caractères.|
|UNB11 (indicateur de Test)|Activez cette case à cocher pour indiquer que l’échange généré des données de test|
|Appliquer le Segment UNA (conseils de chaîne de Service)|Activez cette case à cocher pour générer un segment UNA pour l’échange à envoyer.|
|Appliquer UNG Segments (en-tête de groupe de fonction)|Activez cette case à cocher pour créer des segments de regroupement dans l’en-tête de groupe fonctionnel dans les messages envoyés au partenaire invité. Les valeurs suivantes sont utilisées pour créer les segments UNG :</br></br>Pour **UNG1**, entrez une valeur alphanumérique dont au moins un caractère et un maximum de six caractères.</br></br>Pour **UNG2.1**, entrez une valeur alphanumérique dont au moins un caractère et un maximum de 35 caractères.</br></br>Pour **UNG2.2**, entrez une valeur alphanumérique, avec un maximum de quatre caractères.</br></br>Pour **UNG3.1**, entrez une valeur alphanumérique dont au moins un caractère et un maximum de 35 caractères.</br></br>Pour **UNG3.2**, entrez une valeur alphanumérique, avec un maximum de quatre caractères.</br></br>Pour **UNG6**, entrez une valeur alphanumérique dont un minimum et un maximum de trois caractères.</br></br>Pour **UNG7.1**, entrez une valeur alphanumérique dont au moins un caractère et un maximum de trois caractères.</br></br>Pour **UNG7.2**, entrez une valeur alphanumérique dont au moins un caractère et un maximum de trois caractères.</br></br>Pour **UNG7.3**, entrez une valeur alphanumérique avec un minimum de 1 caractère et un maximum de 6 caractères.</br></br>Pour **UNG8**, entrez une valeur alphanumérique dont au moins un caractère et un maximum de 14 caractères.|

### <a name="character-sets-and-separators"></a>Jeux de caractères et les séparateurs
Autres que le jeu de caractères, vous pouvez entrer un autre jeu de séparateurs à utiliser pour chaque type de message. Si un jeu de caractères n’est pas spécifié pour un schéma de message donné, le jeu de caractères par défaut est utilisé.

|Propriété|Description |
|----|----|
|UNB1.1 (identificateur de système)|Sélectionnez le caractère EDIFACT défini pour être utilisée sur l’échange sortant.|
|Schéma|Sélectionnez un schéma dans la liste déroulante. Une nouvelle ligne sera ajoutée que chaque ligne est terminée. Pour le schéma sélectionné, sélectionnez les séparateurs à utiliser :</br></br>**Séparateur d’éléments composant** – entrée un caractère pour séparer les éléments de données composite.</br></br>**Séparateur d’éléments de données** – entrée un caractère pour séparer les éléments de données simples au sein des éléments de données composite.</br></br></br></br>**Caractère de remplacement** – Activez cette case à cocher si la charge utile de données contient des caractères qui sont également utilisés comme données, segment ou des séparateurs de composant. Vous pouvez ensuite entrer un caractère de remplacement. Lors de la génération du message EDIFACT sortant, toutes les occurrences des caractères de séparation dans les données de charge utile sont remplacés par le caractère spécifié.</br></br>**Terminateur de segment** – entrée un seul caractère pour indiquer la fin d’un segment de l’EDI.</br></br>**Suffixe** – sélectionnez le caractère qui est utilisé avec l’identificateur de segment. Si vous désignez un suffixe, l’élément de données de terminateur de segment peut être vide. Si le terminateur de segment est vide, vous devez désigner un suffixe.|

### <a name="control-numbers"></a>Numéros de contrôle
|Propriété|Description |
|----|----|
|UNB5 (numéro de contrôle d’échange)|Entrez un préfixe, une plage de valeurs pour le numéro de contrôle d’échange et d’un suffixe. Ces valeurs sont utilisées pour générer un échange sortant. Le préfixe et le suffixe sont facultatifs ; le numéro de contrôle est obligatoire. Ce numéro est incrémenté pour chaque nouveau message ; le préfixe et le suffixe restent les mêmes.|
|UNG5 (numéro de contrôle de groupe)|Entrez un préfixe, une plage de valeurs pour le numéro de contrôle d’échange et d’un suffixe. Ces valeurs sont utilisées pour générer le numéro de contrôle de groupe. Le préfixe et le suffixe sont facultatifs ; le numéro de contrôle est obligatoire. Ce numéro est incrémenté pour chaque nouveau message jusqu'à ce que la valeur maximale est atteinte ; le préfixe et le suffixe restent les mêmes.|
|UNH1 (numéro de référence d’en-tête de Message)|Entrez un préfixe, une plage de valeurs pour le numéro de contrôle d’échange et d’un suffixe. Ces valeurs sont utilisées pour générer le numéro de référence des en-tête de message. Le préfixe et le suffixe sont facultatifs ; le numéro de référence est obligatoire. Le numéro de référence est incrémenté pour chaque nouveau message ; le préfixe et le suffixe restent les mêmes.|

### <a name="validations"></a>Validations
|Propriété|Description |
|----|----|
|Type de message|Cette option permet à la validation sur le récepteur de l’échange. Cette validation effectue une validation de l’EDI sur des éléments de données de l’ensemble de la transaction, la validation des types de données, les restrictions de longueur et les éléments de données vide et la formation des séparateurs.|
|Validation de l’EDI|Activez cette case à cocher pour effectuer une validation de l’EDI sur les types de données, tel que défini par les propriétés EDI du schéma, les restrictions de longueur, les éléments de données vide et des séparateurs à droite.|
|Validation étendue|Cette option permet une validation étendue d’échanges reçu de l’expéditeur de l’échange. Cela inclut la validation de longueur de champ, facultativité et nombre de répétitions en plus de la validation de type de données XSD. Vous pouvez activer le contrôle de l’extension sans activer la validation de l’EDI, ou vice versa.|
|Autoriser les zéros de début/fin|Cette option indique qu’un échange EDI a reçu de la partie n’échoue pas de validation si l’élément de données dans un échange EDI n’est pas conforme à sa longueur requise car ou les espaces de fin, mais il est conforme à ses critères de longueur lorsqu’ils sont supprimés.|
|Supprimer les zéros de début ou de fin|Cette option sera ajuster les zéros de début et de fin.|
|Séparateur à droite|Cette option indique un échange EDI a reçu de la partie n’échoue pas la validation si l’élément de données dans un échange EDI n’est pas conforme à ses critères de longueur en raison des zéros (début ou fin) ou des espaces, mais n’est conforme à ses critères de longueur lorsqu’ils sont supprimés.</br></br>Si vous ne souhaitez pas autoriser des délimiteurs de fin et les séparateurs dans un échange reçu de l’expéditeur de l’échange, sélectionnez **Pas autorisé** . Si l’échange contient des séparateurs et des délimiteurs de fin, il est déclaré non valide.</br></br>Sélectionnez **facultatif** à accepter les échanges avec ou sans délimiteurs de fin et les séparateurs.</br></br>Sélectionnez **obligatoire** si l’échange reçu doit contenir des séparateurs et des délimiteurs de fin.|

Après avoir sélectionné **OK** sur la blade d’ouvrir :  
12. Sélectionnez la mosaïque **d’accords** sur la lame de compte de l’intégration, et vous verrez l’accord nouvellement ajouté répertorié.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)   

## <a name="learn-more"></a>Pour en savoir plus
- [En savoir plus sur le Pack d’intégration entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack d’intégration entreprise")  
