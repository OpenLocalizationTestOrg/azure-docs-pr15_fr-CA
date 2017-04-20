# <a name="quality-criteria-for-pull-request-review"></a>Passez en revue les critères de qualité pour la demande d’extraction

Ces critères sont destinés aux auteurs pour créer et gérer des articles techniques et les extraits demande réviseurs d’examiner les demandes d’extraction de contenu. Si votre demande d’extraction n’est pas qualifié pour [la fusion automatique](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically), il sera examiné par un réviseur de demande d’extraction humaine. Réviseurs de demande d’extraction revoir généralement uniquement ce qui est nouveau ou modifié. Tirez la demande réviseurs évaluent les modifications apportées à une demande d’extraction selon la bloquant et non bloquante qualité révision figurant dans cet article.

## <a name="blocking-content-quality-items"></a>Blocage des éléments de contenu de qualité

Les mises à jour dans la demande d’extraction doivent satisfaire aux critères suivants pour être fusionnées. Réviseurs de demande d’extraction commentaires dans les commentaires de demande d’extraction pour ces éléments et le type de `#hold-off` dans la demande d’extraction pour les renvoyer (l’auteur) avec vos commentaires.

| Catégorie | Élément de révision de qualité |
|----------|---------------------|
|Conditions préalables| La « prêt-à-fusion » et les étiquettes « la validation a réussi » sont affectés à la PR.|
|Conditions préalables| La demande d’extraction ne peut pas être bloquée par un conflit de fusion.|
|Intégrité de mis en pension|    Demande d’extraction ne contient aucun régressions de contenu évidentes.|
|Intégrité de mis en pension|    Demande d’extraction n’inclut pas un mis en pension incorporé ou tous les fichiers inhabituelles, superflues.|
|Intégrité de mis en pension |Demande d’extraction contient moins de 100 fichiers modifiés, sauf si la PR intentionnellement met à jour une branche de la version à partir du maître. (Vraiment, une PR doit contenir moins éloignés que celle, mais après 100 fichiers modifiés, GitHub n’affiche pas les diffs).|
|D’attribution de noms |Les noms de fichier pour les nouveaux fichiers suivent les [règles de dénomination de fichier](file-names-and-locations.md).|
|D’attribution de noms |Nouveaux dossiers introduits, le suivi de la mis en pension, les [instructions d’affectation de noms de dossier](file-names-and-locations.md#folder-names-in-the-repo).|
|Contenu    |L’article est un document technique et par conséquent dans le canal de contenu approprié. Consultez le [ce qui va où conseils](content-channel-guidance.md).|
|Contenu    |Le sujet dans le document technique est approprié pour un article technique. Consultez le [ce qui va où conseils](content-channel-guidance.md).|
|Contenu    |Cet article contient un paragraphe d’introduction et un corps procédural ou conceptuel de contenu. L’article doit contenir de contenu suffisamment complète reposer sa propre en tant qu’article. Il ne doit pas être un petit fragment d’informations. (Exception : une rubrique « Limites » si elle est dans le contexte d’un article de grande taille qui répertorie l’ensemble des limites d’un service.)|
|Contenu| Les éléments qui doivent être des listes numérotées sont numérotées, les éléments qui doivent être non ordonnés des listes à puces. Il s’agit de facilité d’utilisation de base.|
|Contenu| Graphiques inhabituelles ou nouvelles, architecture d’informations ou des structures ou modèles de toute évidence non standard doivent être examinées avec le réviseur de PR prospect. Les équipes qui sont expérimenter de nouvelles choses ont besoin d’avoir un plan ou un canevas de problème/solution en place pour l’évaluation des expériences.|
|Conception du site/fonctionnalités| Mélangeurs sont utilisés uniquement pour la commutation entre plusieurs versions de ce même article.|
|Conception du site/fonctionnalités| Les titres des articles du switchered contiennent des informations qui différencie chaque article dans les autres articles sur l’ensemble de switchered.|
|Conception du site/fonctionnalités| Créé manuellement aux tables des matières ne sont pas autorisés. L’article doit s’appuyer sur H2s pour sa table des matières sur la page.|
|Conception du site/fonctionnalités| Si les en-têtes de H2 sont présents, l’article contient au moins deux positions de H2. À l’aide d’un en-tête de H2 crée un article unique de la table des matières. En-têtes de H2 doivent servir avant H3 en-têtes afin de garantir qu'une table des matières sont créée.|
|Démarque| HTML : Contenu Source ne contient-elle pas de code HTML au niveau du bloc – secondaire inline HTML est autorisé, par exemple l’exposant, indice, caractères spéciaux et autres choses secondaires que vous ne pouvez pas faire avec la démarque. Les tables HTML sont autorisées uniquement si la table contient des listes à puces ou numérotées, mais qui est généralement une indication que le contenu doit être simplifiées afin que la source peut être codée dans la démarque.|
|Démarque   |Les éléments personnalisés de démarque sont utilisées le cas échéant. Ex : Notes sont codées à l’aide de l’AZURE. Remarque l’extension, pas en tant que texte brut.|
|SEO    |Le « & #124 ; Identificateur de site Microsoft Azure » est nécessaire|
|SEO    |Le titre H1 contient suffisamment d’informations pour décrire le contenu de l’article, pour le différencier d’autres articles Azure et de mapper des mots clés susceptibles de client. Par exemple, « Présentation » comme titre H1 est un échec.|
|Terminologie| L’utilisation de l’acronyme ARM, V1 ou V2 comme références standard et les modèles de déploiement Gestionnaire de ressources est un élément de la terminologie de blocage.|
|Images |Les images GIF animées ne sont pas acceptés dans le mis en pension.|
|Images | Les images ont une résolution claire, sont libres de mots mal orthographiés et ne contiennent aucune information privée | 
|Mise en attente|L’aperçu de l’article doit être propre sur le transit. Il ne peut pas contenir des problèmes de mise en forme évidentes : <br><br>-Une liste numérotée ou à puce qui s’affiche sous la forme d’un paragraphe <br> -Le code dans un bloc de code qui apparaissent en partie dans le bloc de code et en partie à l’extérieur <br>-Numérotées étapes numérotées correctement en raison de la mise en retrait défectueux|

## <a name="non-blocking-content-quality-items"></a>Non bloquant du contenu des éléments de qualité

Pour ces éléments, réviseurs de demande d’extraction fournissent des commentaires et des instructions pour l’auteur de suivi avec les correctifs inclus dans une demande d’extraction ultérieure. Toutefois, cette évaluation ne bloque pas la décision de fusion. Auteurs doivent suivre dans les 3 jours ouvrables avec correctifs.

| Catégorie | Élément de révision de qualité |
|----------|---------------------|
|Contenu|Les articles doivent avoir un « étapes suivantes » à la fin avec les étapes 1 à 3 pertinentes et intéressantes. Texte court convient qui aide le client à comprendre pourquoi les étapes suivantes sont pertinentes. (Nouveaux articles uniquement). Exemple : <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|Contenu|Orthographe, de grammaire et autres problèmes d’écriture - réviseurs de demande d’extraction peuvent fournir des commentaires sur quelques problèmes mineurs comme non-bloquante de commentaires. S’il existe plusieurs problèmes éditoriales, réviseurs enregistrer une demande de modification de l’article pour une modification de la composition après.|
|Images|Les images utilisent le style de légende correcte et la couleur, et captures d’écran utilisent le style de bordure et d’espace réservé correct. [Reportez-vous à l’aide de l’image](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Images|Images incluent le texte de remplacement. [Reportez-vous à l’aide de l’image](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Conception du site/fonctionnalités|Les en-têtes de H2, lors du rendu de la table des matières sur la page, doivent idéalement encapsuler à pas plus de 2 lignes. Titres plus rendre l’article de la table des matières plus difficile à analyser.|
|Conventions de style|Tous les titres et les en-têtes sont de casse de la phrase, par style Azure.|
|Processus|Si la demande d’extraction peut facilement ont été reconfigurée pour bénéficier de l’automatisation de la PRmerger, réviseurs de demande d’extraction fournissent des commentaires à l’auteur sur l’utilisation des branches afin que les modifications peuvent être fusionnées automatiquement. Reportez-vous à [l’article d’etiquette de PR](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically).|
|Processus|Lorsque vous supprimez ou renommez un article, assurez-vous de que suivre le processus. Extraire les réviseurs doivent ajouter le commentaire et le lien suivant dans un commentaire de demande :<br><br>*Vérifiez que vous avez suivi le processus dans le guide des collaborateurs pour la suppression d’articles : <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> .*|

## <a name="related"></a>Liées

- [Etiquette de demande d’extraction et de meilleures pratiques pour les collaborateurs de Microsoft](contributor-guide-pull-request-etiquette.md)

- [Extraction d’automation de commentaire de demande](contributor-guide-pull-request-comments.md)
