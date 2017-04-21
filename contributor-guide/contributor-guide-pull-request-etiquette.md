# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Extraire l’étiquette de demande et les meilleures pratiques pour les collaborateurs de Microsoft Azure documentation

Pour publier les modifications apportées à la documentation, vous soumettez des demandes de l’extraction à partir de votre branche. Chaque demande d’extraction doit être revue avant la fusion. Lisez cet article pour comprendre comment vous devez travailler avec les réviseurs de demande d’extraction, et comment vous pouvez créer des demandes d’extraction qui sont plus faciles et plus rapides à analyser la file d’attente de la demande par réception fonctionne mieux pour tout le monde.

## <a name="working-with-pull-request-reviewers"></a>Utilisation des réviseurs de demande d’extraction

Voici les notions de base que vous devez savoir sur l’utilisation des réviseurs de demande d’extraction. 

- <b>Comprendre le rôle de réviseur de demande l’extraction. Le réviseur :</b>
  - Garantit la qualité du contenu
  - Empêche des régressions dans le référentiel
  - Fournit des commentaires avant la fusion

  Réviseurs de demande d’extraction sont dans un rôle de gestion de contenu. Le but principal n’est ne pas à simplement Fusionner tout ce qui est soumis aussi rapidement que possible. Attendez les commentaires qui vous demandera de mettre à jour, en particulier pour les articles de nouvelles et révisées fortement.

- <b>Planifiez avec votre réviseur de demande d’extraction :</b>
  - Pour les demandes d’extraction de haute priorité
  - Extraction des demandes pour les versions de dépassement/datée du
  - Demandes d’extraction que vous modifiez ou ajoutant un grand nombre de fichiers

- <b>Contrat SLA pour extraction demande de révision</b>

  Dans le référentiel privé, chaque fois que votre demande d’extraction saisit la file d’attente de requête extrait avec l’étiquette prêt-à-fusionner, l’équipe essaie de passer en revue la demande d’extraction dans les 12 heures de bureau (M-F, 8 h à 17 h) et d’envoyer vos commentaires ou de fusion si aucune évaluation n’est nécessaire. Ce contrat SLA s’applique à l’acte de révision de la PR, ne pas fusionner. PRs seront fusionnées qu’elles remplissent [les critères pour la fusion](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Vérifiez la file d’attente de la demande extraction fonctionne mieux pour tout le monde

Il existe deux réalités de base dans la file d’attente de relations publiques :

- Demandes d’extraction qui sont petites dans la portée et qui contiennent des modifications très similaires prennent moins de temps à examiner. 
- Demandes d’extraction qui sont importants dans la portée, ou qui contiennent des types différents, mixtes de modifications prennent plus de temps à examiner.

Vous pouvez rendre la file d’attente de la demande extraction fonctionnent mieux en suivant ces meilleures pratiques :

- Distincts mises à jour mineures aux articles existants à partir de nouveaux articles ou de réécritures principales. Travailler sur ces modifications dans les branches de travail distincts. 

- Lorsque vous supprimez des articles ou des images, ne mélangez pas les suppressions avec des nouveautés ou des mises à jour. Gérer le contenu de nouveaux changements dans une branche de travail séparée.

- Pour les versions ou du contenu de refactorisation, planifiez avec votre relecteur PR. Vous devrez peut-être aide son pour créer une branche de publication ou coordination fusion dont l’heure de la publication afin que le contenu est publié au bon moment.

- Si vous essayez de coordonner les mises à jour effectuées dans la mis en pension ACOM (ie, des modifications de navigation de gauche, les mappages de formation, les redirections ou pages de lancement) avec les modifications que vous apportez dans le référentiel de contenu-azure-pr, vous devez coordonner ce travail à l’avance avec votre relecteur PR. Sinon, vous risquez d’avoir un grand nombre de liens rompus.

## <a name="criteria-for-expedited-pull-requests"></a>Critères pour les demandes d’extraction accélérée

- Contactez azdocprs afin d’accélérer la PRs uniquement lorsque cela est absolument nécessaire. Vous pouvez demander expedited PR gestion de Zone de rouge, de confidentialité, juridique et les problèmes de sécurité ; pour des expériences client véritablement rompus ; et pour les escalades executive. 
- Contenu pour les versions de la fonction n’est pas qualifié pour une gestion accélérée - contenu de la version fonctionnalité nécessite une planification préalable ou il doit être géré par le biais de la file d’attente de priorité standard.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>En retard ? Soumettre PRs qui peuvent être acceptés automatiquement

Utiliser les règles d’automatisation PRMerger pour obtenir plus de votre quotidien PRs fusionnées automatiquement.

PRMerger peut accepter votre PR automatiquement, si :
* Il affecte les 10 fichiers ou moins.
* Il contient des validations de 15 ou moins.
* Moins de 20 % des modifications de texte.
* Sélecteurs/mélangeurs ne sont pas mis à jour.
* Aucun fichier est supprimés ou ajoutés.
* Aucune image n’est nouveaux, modifiés ou supprimés.

Si votre demande d’extraction ne répond pas à ces critères, l’étiquette « PRmerger ne pouvez pas fusionner » reçoit automatiquement afin que vous sachiez qu’il doit être révisé par un réviseur humain de PR.

### <a name="need-to-make-a-lot-of-little-changes"></a>Nécessité d’effectuer de nombreuses petites modifications ?

Prendre votre file d’attente à partir des règles d’automatisation PRMerger ci-dessus et effectuez les opérations suivantes :
* Soumettre des articles avec des changements d’éclairage dans une PR avec des fichiers de 10 ou moins.
* Créer une PR distinct pour les articles dans quelle modification d’images ou de sélecteurs. Cela nécessite de relecture humaine.
* Créer une PR distinct pour les articles nouveaux ou supprimés. Cela nécessite de relecture humaine.

## <a name="related"></a>Liées

- [Passez en revue les critères de qualité pour la demande d’extraction](contributor-guide-pr-criteria.md)

- [Extraction d’automation de commentaire de demande](contributor-guide-pull-request-comments.md)
