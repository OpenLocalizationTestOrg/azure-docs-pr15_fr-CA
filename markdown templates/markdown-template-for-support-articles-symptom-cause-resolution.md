<properties
   pageTitle="Titre de la page qui s’affiche dans les résultats de recherche et onglet navigateur"
   description="Description de l’article qui sera affichée sur les pages d’accueil et dans la plupart des résultats de la recherche"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""
   tags="comma-separates-additional-tags-if-required"/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="title-maximum-120-characters-target-the-primary-keyword"></a>Titre (Maximum 120 caractères, cible, le mot clé primary)

_Utiliser des mots clés secondaires de 2-3 dans la description._

_Vous avez le choix entre les exclusions suivantes en fonction de votre scénario. Si votre article est indépendant du modèle de déploiement, ignorer._

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../learn-about-deployment-models-both-include.md)]

## <a name="summary-optional-especially-when-the-article-is-short"></a>Résumé (facultatif, en particulier lorsque l’article est court)

- _Décrivez brièvement le problème._
- _La section Résumé est un bon emplacement pour utiliser des mots-clés différents de ceux figurant dans le titre, mais assurez-vous de ne pas rendre très longue. Les phrases doivent également de flux et être faciles à comprendre._
- _La liste des exceptions (facultatives) - les scénarios pertinents qui ne sont pas traitées dans cet article. Par exemple, « scénarios de Linux/systèmes d’exploitation ne sont pas abordées dans cet article »._

_S’il s’agit d’un article sur la rubrique facturation, inclure la note suivante (la note ci-dessous est légèrement différente de celui du bas de cet article) :_
> [AZURE.NOTE] Si vous avez besoin d’obtenir de l’aide à tout moment dans cet article, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir votre problème résolu rapidement.

_S’il n’est pas un article de la facturation, utilisez la référence suivante :_
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptôme

- _Quelles actions serait l’utilisateur être essaie d’effectuer ?_
- _Ce qui a échoué ?_
- _Les systèmes et logiciels de l’utilisateur ont été utilisiez ?_
- _Quel message d’erreur messages a été indiqués ?_
- _Inclure la capture d’écran si possible._

## <a name="cause"></a>Cause

- _Ce qui provoque ce problème._

## <a name="solution"></a>Solution

- _Ajouter des captures d’écran si possible._
- _S’il existe plusieurs solutions, les placer dans l’ordre de la complexité et fournir des instructions sur la façon de faire votre choix parmi les._

| <em>Version 1 : Votre article est indépendant du modèle de déploiement</em> | <em>Version 2 : Étapes pour le Gestionnaire de ressources et classique sont en grande partie les mêmes</em> | <em>Version 3 : Étapes pour le Gestionnaire de ressources et classique sont essentiellement différentes. <br />Dans ce cas, utilisez la <a href="https://github.com/Azure/azure-content-pr/blob/master/contributor-guide/custom-markdown-extensions.md#simple-selectors">technique de sélecteurs simples dans Github</a>. <br />Remarque : Articles de machine virtuelle pour les exceptions de ARM et vous ne devez pas utiliser le sélecteur de ARM/classique.</em> |
|:------------------------------------------------------|:-----------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <p><h3>Solution 1</h3><em>(la plus simple et la plus efficace)</em></p><ol><li>[Étape 1]</li><li>[Étape 2]</li></ol><p><h3>Solution 2</h3><em>(moins simple ou efficace)</em></p><ol><li>[Étape 1]</li><li>[Étape 2]</li></ol><br /><br /><br /><br /><br /><br /><br /><br /> | <p><h3>Solution 1</h3><em>(la plus simple et la plus efficace)</em></p><ol><li>[Étape 1]</li><li>Si vous utilisez le modèle de déploiement classique, [cela].<br />Si vous utilisez le modèle de déploiement Gestionnaire de ressources, [cela].</li><li>[Étape 3]</li></ol><p><h3>Solution 2</h3><em>(moins simple ou efficace)</em></p><ol><li>[Étape 1]</li><li>Si vous utilisez le modèle de déploiement classique, [cela].<br />Si vous utilisez le modèle de déploiement Gestionnaire de ressources, [cela].</li><li>[Étape 3]</li></ol> | <img src="media/markdown-template-for-support-articles-symptom-cause-resolution/rm-classic.png" alt="ARM-Classic"><p><h3>Solution 1</h3><em>(la plus simple et la plus efficace)</em></p><ol><li>[Étape 1]</li><li>[Étape 2]</li></ol><p><h3>Solution 2</h3><em>(moins simple ou efficace)</em></p><ol><li>[Étape 1]</li><li>[Étape 2]</li></ol><br /><br /><br /><br /> |

## <a name="next-steps"></a>Étapes suivantes
_Inclure cette section s’il y a BETON-1 3, les étapes très pertinents que doivent effectuer les utilisateurs. Supprimer si aucune étape suivante. Ce n’est pas un emplacement dans une liste de liens. Si vous incluez des liens vers les étapes suivantes, veillez à inclure le texte pour expliquer pourquoi les étapes suivantes sont pertinentes / important._

_S’il s’agit d’un article sur la rubrique facturation, inclure la note suivante (la note ci-dessous est légèrement différente de celle au début de cet article) :_
> [AZURE.NOTE] Si vous en avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir votre problème résolu rapidement.
