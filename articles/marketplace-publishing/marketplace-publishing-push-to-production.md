<properties
   pageTitle="Déployer votre offre sur le marché d’Azure | Microsoft Azure"
   description="Découvrez et passez en revue les instructions pour le déploiement de votre offre : image de machine virtuelle, service de développeur, service de données, etc., sur le marché d’Azure."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Déployer votre offre sur le marché d’Azure
Lorsque vous êtes satisfait de votre offre (autrement dit, vous avez testé les scénarios clients, marketing de contenu, etc.) et vous êtes prêt à lancer, **Pousser vers la production** de l’onglet **Publier** .  

1. Les quatre étapes dans la procédure pas à pas de page de la publication portail doit être terminée et vert. Pour les offres de la Machine virtuelle, assurez-vous que les instructions suivantes sont suivies.

    ![dessin][img-pubportal-walkthru-checked]

2. Sélectionnez l’onglet **Publier** dans la liste sur le côté gauche.

    ![dessin][img-pubportal-menu-publish]

3. Cliquez sur le bouton **Soumettre pour approbation à pousser vers la production**. Une fois que la demande est faite, l’équipe de réception exécute une révision finale, et ensuite votre offre sera disponible sur le marché d’Azure.

    ![dessin][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] Dans le cas d’ordinateurs virtuels, lorsque vous cliquez sur le bouton approbation de la demande à envoyer à la production, les étapes suivantes sont effectuées derrière la scène. Vous ne pourrez pas afficher la progression de chaque étape sous l’onglet Publier dans la publication portal. Vous devez vérifier cette page à intervalles réguliers (jusqu'à ce que l’état affiche « Vente ») pour les informations sur l’échec nécessitant une correction à partir de votre fin.

> - Dans un premier temps votre demande de production accède à l’équipe de certification qui valide le disque dur virtuel. Toutefois, si vous mettez à jour votre offre déjà répertorié et que la demande a marketing uniquement les modifications, puis la certification étape est ignorée.
> - À l’étape suivante, la demande sont fournis à l’équipe de validation de contenu chargés de vérifier le contenu de commercialisation de l’offre.
> - Si les étapes ci-dessus réussissent, l’offre est approuvée dans la production. À ce stade, l’état devient « énumérés » dans le portail de publication. Toutefois, ce statut de « Vente » n’implique pas que le processus est terminé. Les étapes suivantes doivent être terminée avant que l’offre soit disponible sur le marché d’Azure.
> - Une fois que l’offre est approuvée en production dans l’étape ci-dessus, la réplication de l’offre démarrer sur tous les centres de données Azure. En général, il prend 24-48hours pour la réplication mais peut prendre jusqu'à une semaine selon la taille du disque dur virtuel. Toutefois, si vous mettez à jour votre offre déjà répertorié et il a marketing uniquement des modifications, la réplication est plus rapide.
> - Une fois la réplication terminée, puis l’offre sera disponible sur le marché d’Azure.

> Vous pouvez toujours supprimer l’offre alors qu’il est dans un état de **brouillon** (jamais **Pousser vers la mise en attente** ou **envoyé à la production**). Dans l’onglet **historique** , cliquez sur le bouton **Annuler le brouillon** en bas de la page pour supprimer un brouillon.


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Liste de contrôle de production pour toutes les offres de Machine virtuelle

- Vérifiez que vous êtes un partenaire certifié de Microsoft Azure
- Sous l’onglet références, l’option « Masquer cette référence à partir du marché, car il doit toujours être achetée via un modèle de solution » doit être marqué comme Oui uniquement si la référence est une partie d’un modèle de Solution. Dans tous les autres cas, cette option doit toujours être marquée comme étant non.
- N’oubliez pas : Vous ne modifiez pas le paramètre de visibilité de référence une fois le point de stock est répertorié. Nous ne supportent pas cette fonctionnalité.
- Assurez-vous que les logos de respectent les directives de logo Azure Marketplace indiquées ci-dessous.
- Description de l’offre et de la référence SKU ne doivent pas être identiques.
- Titre de références SKU et offrent Long résumé ne doivent pas être identiques.
- Titre de référence et récapitulatif des offres ne doit pas être le même.
- Titres de référence ne doit pas être identiques à une offre avec plusieurs points de stock.

**Instructions de logo Azure Marketplace**

- La conception Azure a une palette de couleurs simple. Limiter le nombre de principal et couleurs secondaires sur votre logo.
- Les couleurs de thème du portail Azure sont blancs et noirs. Évitez, par conséquent, à l’aide de ces couleurs comme couleur d’arrière-plan de votre logo. Utiliser des couleurs qui rendent vos logos mis en évidence dans le portail Azure. Nous vous recommandons les couleurs primaires simples. Si vous utilisez un arrière-plan transparent, puis assurez-vous que le logo/texte n’est pas blanc ou noir.
- N’utilisez pas un arrière-plan dégradé sur le logo.
- Évitez de placer du texte, même votre société ou le nom de la marque, sur le logo.
- L’aspect de votre logo doit être « plat » et évitez les dégradés.
- Le logo ne doit pas être étiré.

**Obtenir des conseils supplémentaires pour le logo du héros :**

- Le logo héros est facultatif. L’éditeur peut choisir ne pas à télécharger un logo héros. **Toutefois une fois téléchargé l’icône héros ne peut pas être supprimé à partir de la publication portal. À ce stade, le partenaire doit suivre les instructions Azure Marketplace pour les autres icônes héros que l’offre n’est pas approuvée en production.**
- Nom d’affichage de l’éditeur, titre de référence et l’offre long résumé sont affichés dans la couleur de police blanche. Par conséquent, vous devez éviter conservant toute couleur de la lumière dans l’arrière-plan de l’icône du héros. Noir, arrière-plan blanc et transparent n’est pas autorisée pour les icônes de Hero.
- L’éditeur afficher le nom, le titre de référence, l’offre long résumé et le bouton Créer sont incorporés par programme à l’intérieur du logo héros, une fois que l’offre est répertorié. Par conséquent, vous devez entrer tout texte pendant que vous concevez le logo héros. Simplement laisser un espace vide sur la droite, car le texte (c'est-à-dire Publisher nom complet, titre de référence, l’offre long résumé) est inclus par programme par nous là-bas. L’espace vide pour le texte doit être 415 x 100 à droite (et elle est compensée par 370px à partir de la gauche).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Liste de contrôle de production supplémentaires pour les machines virtuelles déjà répertoriés offre

- Vérifiez s’il existe déjà une offre portant le même nom d’offre de votre société. Si Oui, vous devez ajouter une nouvelle version de la référence SKU de l’offre existante au lieu de créer une nouvelle offre en double.
- Disque de données ne doit pas changer entre deux versions de la même référence.
- L’Azure Marketplace ne gère pas la modification de prix des listés références car il influence la facturation des clients existants. Assurez-vous que vous ne modifiez pas la tarification des références répertoriées dans les régions où la référence est disponible. Toutefois, vous pouvez ajouter de nouvelles références SKU ou ajouter de nouvelles zones à un point de stock existant.


## <a name="next-steps"></a>Étapes suivantes
Une fois que l’offre est publié, tester les scénarios de client pour vérifier que tous les contrats et les fonctionnalités fonctionnent correctement dans l’environnement de production comme testés et validés dans l’environnement intermédiaire.

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
