<properties title="" pageTitle="Noms de fichiers et d’emplacements pour les articles techniques sur Azure" description="Explique la structure de fichiers pour les articles et les conventions que vous devez suivre lorsque vous créez un nouvel article." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Noms de fichiers et d’emplacements pour les articles techniques sur Azure

Dans notre référentiel de contenu technique, nous utilisons un seul dossier (le dossier **articles** ) pour tous les articles. Il n’y a aucune hiérarchie de dossier - live de tous les articles dans la structure de fichier plat. Si vous créez des dossiers avec les articles, vos articles ne peuvent pas être publiés.

Au lieu d’utiliser une structure de fichier sous la forme d’un principe d’organisation, nous utilisons une convention de dénomination de fichier strictes qui identifie clairement les rubriques et qui contribue à la découverte sur le web.

Voici ce que vous devez savoir :

+ [Règles]
+ [Modèle]
+ [Exemples standard]
+ [Contenu du marché]
+ [Approbation de nom de fichier]

##<a name="rules"></a>Règles

- Les noms de fichiers peuvent contenir uniquement les lettres minuscules, nombres et des traits d’union. 
- Sans espaces ni les caractères de ponctuation. Utilisez les traits d’union pour séparer les mots et chiffres dans le nom de fichier.
- Pas plus de 80 caractères - il s’agit d’une limite de système de publication
- Les verbes d’action utilisez spécifiques, tels que développement, achètent, construire, résoudre les problèmes. Aucun mot - ing.
- Aucun mot small - ne pas inclure une et, le, en, ou, etc..
- Tous les fichiers doivent être en démarque et utiliser l’extension de fichier .md.
- Orthographe des mots éviter les acronymes inutiles ou non approuvés dans les noms de fichier

Acronymes et initialisms dans les noms de fichier - des lignes directrices spécifiques :

- Sans utiliser d’abréviation des noms de service Azure - les premiers mots du nom de fichier doivent être à la norme, nom de service ou d’une technologie Azure en toutes lettres. 
-   Ne pas autoriser les bras ou rm comme acronymes n’importe où dans un nom de fichier
- Autres abréviations standard de l’industrie ne sont acceptables que nécessaire dans les noms de fichiers. 

##<a name="pattern"></a>Modèle

Voici le modèle général :

 **service-Platform-Language-Content-Product-version.MD**

Utiliser les parties du modèle qui s’appliquent et passez en revue la liste des articles dans le référentiel pour avoir une idée des noms existants. Les noms ne commencent pas par une plate-forme de développement ou un nom de service est probablement suspect et glissement par le biais de.

##<a name="standard-examples"></a>Exemples standard

Voici quelques exemples de noms valides qui suivent le modèle. :

- nuage-services-dotnet-continu-delivery.md
- Mobile-services-ios-get-started.md
- gérer les documentdb-account.md
- Mobile-Services-dotnet-backend-Get-Started-Settings-Sync.MD
- Active-Directory-Java-Authenticate-Users-Access-Control-Eclipse.MD
- Virtual-Machines-Install-Windows-Server-2008R2.MD
- cache-aspnet-session-état-fournisseur
- Azure-SDK-dotnet-Release-Notes-2-8
- storsimple-Disaster-Recovery-Using-Azure-Site-Recovery

##<a name="marketplace-content"></a>Contenu du marché

Pour distinguer le contenu qui se concentre sur les contributions des partenaires sur le marché d’Azure, démarrez avec « marché », les noms de fichiers. Ce contenu ne doit pas être trop courant, comme la plupart des contenus de partenaire doivent être créés sur les sites web des partenaires.

- Marketplace-MongoDB-Virtual-Machines-Install-Windows-Server-2008R2.MD

##<a name="file-name-approval"></a>Approbation de nom de fichier

Il s’agit de la tâche de notre groupe de relecteurs de demande d’extraction pour examiner les noms de fichiers lorsqu’un fichier est envoyé dans le référentiel pour la première fois. Réviseurs de demande d’extraction doivent passer en revue le nom de fichier et fournir des commentaires via le flux de commentaire extraction demande si des modifications sont nécessaires. Le nom de fichier doit être corrigée avant l’acceptation de la demande d’extraction. Les collaborateurs peuvent distribuer facilement la mise à jour à la demande d’extraction en attente.

##<a name="folder-names-in-the-repo"></a>Noms de dossiers dans le mis en pension

Dossiers doivent être créés uniquement pour les services, et le nom du fichier doit correspondre à la ligne-bloc de service. Utilisez uniquement des lettres et des traits d’union et toutes les lettres minuscules. Obtenir l’approbation de l’administrateur du référentiel avant de créer un nouveau dossier qui n’est pas un service publié.

##<a name="changing-case-in-file-names"></a>Modification de la casse dans les noms de fichier

Systèmes d’exploitation Windows respectent la casse, si vous devez modifier le nom du fichier à corriger la casse, il est préférable d’apporter une modification substantielle, sauf si vous êtes en mesure d’effectuer la modification sur un Linux ou un Mac. Par exemple :

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Pour renommer un fichier, utilisez la commande suivante :
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Liens du Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)


<!--Anchors-->
[Règles]: #rules
[Modèle]: #pattern
[Exemples standard]: #standard-examples
[Contenu du marché]: #marketplace-content
[Approbation de nom de fichier]: #file-name-approval
