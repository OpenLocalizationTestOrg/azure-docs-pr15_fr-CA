<properties
    pageTitle="Sortie de flux Analytique données lac banque | Microsoft Azure"
    description="Configuration de l’authentification et l’autorisation d’un magasin du lac de données Azure dans une tâche de flux de données Analytique"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Sortie de flux Analytique données lac magasin

Travaux d’Analytique de flux prend en charge plusieurs méthodes de sortie, l’une est un [Magasin de LAC de données Azure](https://azure.microsoft.com/services/data-lake-store/). Magasin de LAC de données Azure est un référentiel d’une évolutivité à l’échelle de l’entreprise pour les charges de travail données analytiques. Magasin de données lac permet vous permet de stocker des données de n’importe quelle vitesse de taille, type et ingestion pour analytique opérationnel et exploratoires.

## <a name="authorize-a-data-lake-store-account"></a>Autoriser un compte de banque de données lac

1.  Lorsque le magasin de données lac est sélectionnée sous la forme d’une sortie dans le portail de gestion d’Azure, un message vous demandera d’autoriser l’utilisation de votre magasin de LAC de données existant ou pour demander l’accès à l’aperçu des données lac banque via le portail classique d’Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Si vous avez déjà accès au magasin de données lac, cliquez sur « Autoriser » et pendant un bref instant, une page s’affiche indiquant « Redirection d’une autorisation... ». La page se fermera automatiquement et s’affiche avec la page qui vous permet de configurer la sortie du magasin de données lac.

Si vous n'êtes pas inscrit pour l’aperçu de la banque de données lac, vous pouvez cliquez sur le lien « Inscription » pour lancer la requête, ou suivez [obtenir des instructions de démarrage](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurer les propriétés de sortie de magasin de données lac

Une fois le compte de la banque de données lac authentifié, vous pouvez configurer les propriétés pour la sortie de votre magasin de données lac. Le tableau ci-dessous est la liste des noms de propriétés et leur description pour configurer votre sortie du magasin de données lac.

<table>
<tbody>
<tr>
<td><B>NOM DE LA PROPRIÉTÉ</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Il s’agit d’un nom convivial permettant de diriger la sortie de requête vers le magasin de données lac dans les requêtes.</td>
</tr>
<tr>
<td>Compte de banque de données lac</td>
<td>Le nom du compte de stockage où vous envoyez votre sortie. S’affiche avec une liste de comptes de banque de données lac auquel l’utilisateur connecté au portail a accès à liste déroulante.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe modèle [<I>facultatif</I>]</td>
<td>Le chemin d’accès du fichier utilisé pour écrire les fichiers dans le compte de banque de données lac spécifié. <BR>{date}, {time}<BR>Exemple 1 : folder1/logs / {date} / {time}<BR>Exemple 2 : folder1/logs / {date}</td>
</tr>
<tr>
<td>Format de date [<I>facultatif</I>]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans laquelle les fichiers sont organisés. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [<I>facultatif</I>]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès de préfixe, spécifiez le format dans lequel les fichiers sont organisés. Actuellement, la seule valeur prise en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation d’événement</td>
<td>Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Codage</td>
<td>Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le seul format de codage pris en charge pour l’instant.</td>
</tr>
<tr>
<td>Séparateur</td>
<td>Applicable uniquement pour la sérialisation de CSV. Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation de données CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicable uniquement pour la sérialisation JSON. Ligne séparée Spécifie que la sortie sera formatée en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau spécifie que la sortie sera formatée comme un tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renouvellement de l’autorisation de magasin de données lac

Actuellement, est une limitation où le jeton d’authentification doit être actualisé manuellement tous les 90 jours pour toutes les tâches avec la sortie du magasin de données lac. Vous devrez également s’authentifier de nouveau votre compte de banque de données lac si vous avez modifié votre mot de passe depuis la création ou de dernière authentification de votre travail. Un symptôme de ce problème est aucune sortie des tâches et une erreur dans les journaux d’opération indiquant la nécessité de nouvelle autorisation.

Pour résoudre ce problème, arrêtez votre travail en cours d’exécution et accédez à votre sortie du magasin de données lac. Cliquez sur le lien « Renouveler l’autorisation » et pendant un bref instant, une page s’affiche indiquant « Redirection d’une autorisation... ». La page se fermera automatiquement et si l’opération réussit, indique « L’autorisation a été renouvelée ». Vous puis nécessaire de cliquer sur « Enregistrer » au bas de la page et peut se poursuivre en redémarrant votre travail à partir de la dernière fois arrêté afin d’éviter la perte de données.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
