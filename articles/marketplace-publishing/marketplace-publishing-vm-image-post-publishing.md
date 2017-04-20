<properties
   pageTitle="Gestion de votre image de machine virtuelle sur Azure Marketplace | Microsoft Azure"
   description="Guide détaillé sur la gestion de votre image de machine virtuelle sur Azure Marketplace après la publication initiale."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guide de post-production pour les offres de machine virtuelle sur le marché d’Azure

Cet article explique comment vous pouvez mettre à jour une offre de la Machine virtuelle direct sur le marché d’Azure. Aussi, il vous guide dans le processus d’ajout d’un ou de plusieurs nouvelles références SKU à l’offre existante et supprimer une Machine virtuelle offre direct ou une référence à partir de Azure Marketplace.

Une fois une offre/point de stock est transféré dans le [Portail Azure](http://portal.azure.com), vous ne pouvez pas modifier les champs ci-dessous :

- **Offre identificateur :** [Portail de publication -> Machines virtuelles -> sélectionnez votre offre -> onglet Images de machine virtuelle -> offrent un identificateur]
- **Identificateur de référence :** [Portail de publication -> Machines virtuelles -> sélectionnez votre offre -> références SKU de l’onglet -> Ajouter une référence SKU]
- **Publisher Namespace :** [Portail de publication -> Machines virtuelles -> procédure pas à pas, onglet -> Tell nous concernant votre société (trouvé dans « Étape 2 inscrire votre société ») -> Publisher Namespace -> Namespace]

Une fois que l’offre/point de stock est répertorié dans l' [Azure Marketplace](http://azure.microsoft.com/marketplace), vous ne pouvez pas modifier les champs ci-dessous :

- **Offre identificateur :** [Portail de publication -> Machines virtuelles -> sélectionnez votre offre -> onglet Images de machine virtuelle -> offrent un identificateur]
- **Identificateur de référence :** [Portail de publication -> Machines virtuelles -> sélectionnez votre offre -> références SKU de l’onglet -> Ajouter une référence SKU]
- **Publisher Namespace :** [Portail de publication -> Machines virtuelles -> procédure pas à pas, onglet -> savoir nous sur votre société (trouvé sous étape 2 Enregistrer) Publisher Namespace -> Namespace]
- **Ports** [Portail de publication -> Machines virtuelles -> sélectionnez votre offre -> onglet Images de machine virtuelle -> Ouvrir les Ports]
- **Modification de SKU(s) de liste de prix**
- **Modification du modèle de liste SKU(s) de facturation**
- **Suppression de zones de liste SKU(s) de facturation**
- **Modifier le nombre de disques de données de liste SKU(s)**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. comment mettre à jour les détails techniques d’un point de stock

Vous pouvez ajouter une nouvelle version de la référence SKU de la liste et le publier à nouveau votre offre en suivant les étapes indiquées ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **IMAGES de la machine virtuelle** .
4. Dans la section **références SKU** de l’onglet **IMAGES de la machine virtuelle** , recherchez la référence que vous souhaitez mettre à jour.
5. Après cela, ajoutez un nouveau numéro de version de la référence SKU et cliquez sur le bouton **« + »** . La nouvelle version doit être X.Y.Z format où X, Y et Z sont des nombres entiers. Modifications de la version doivent uniquement être incrémentielles.
6. Dans la zone **URL du disque dur virtuel du système d’exploitation** , ajouter la signature d’accès partagé créé pour le système d’exploitation VHD et enregistrer les modifications.

    >[AZURE.IMPORTANT] Vous ne pouvez pas incrémentation/décrémentation le nombre de disques de données d’un point de stock répertorié. Vous devez créer une nouvelle référence SKU dans ce cas. Reportez-vous à la section [3. Comment faire pour ajouter une nouvelle référence SKU sous une liste offre](#3-how-to-add-a-new-sku-under-a-live-offer) pour obtenir des instructions détaillées.

7. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. comment mettre à jour les détails techniques d’une offre ou d’une référence SKU

Vous pouvez mettre à jour les techniques (marketing, juridique, support, catégories) les détails de votre offre direct ou de référence sur le marché d’Azure.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 mise à jour de la description de l’offre et des logos

Vous pouvez mettre à jour les détails de l’offre et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton de **l’Anglais (États-Unis)** .
5. Dans la barre latérale de gauche, cliquez sur l’onglet **Détails** . Sous la section *DESCRIPTION* de l’onglet **Détails** vous pouvez mettre à jour le titre de l’offre, offrent le résumé, offrent résumé long et enregistrer les modifications.

    >[AZURE.NOTE] Veuillez prendre en charge les éléments suivants lorsque vous mettez à jour les détails de la référence SKU.
    **N’entrez pas de texte en double sous la description de l’offre et de la description de la référence SKU. N’entrez pas de texte en double sous le titre de référence et l’offre long résumé. N’entrez pas de texte en double sous le titre de référence et le résumé de l’offre.**

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. Dans la section des *LOGOS* de l’onglet **Détails** , vous pouvez mettre à jour les logos. Toutefois, vous assurer que les logos de suivent les [instructions d’Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : contenu marketing de fournir le marché -> Détails -> Azure Marketplace Logo instructions).

    >[AZURE.NOTE] Icône de héros est facultative. Vous pouvez choisir ne pas à charger une icône héros. Cependant, une fois que l’icône du héros est téléchargé, puis rien n’est prévu pour le supprimer de la publication portal. Dans ce cas, vous devez suivre les [instructions d’icône héros](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : contenu marketing de fournir le marché -> Détails -> conseils supplémentaires pour la bannière du logo héros).

7. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
8. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Mise à jour de la description de la référence SKU

Vous pouvez mettre à jour les détails de la référence SKU et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connexion d’accès à la [publication de portail](https://publish.windowsazure.com)
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton de **l’Anglais (États-Unis)** .
5. Dans la barre latérale de gauche, cliquez sur l’onglet **PLANS** . Dans la section *références SKU* de l’onglet **PLANS** , vous pouvez mettre à jour le titre de la SKU, les SKU de résumé et les informations de description de référence SKU et enregistrer les modifications.

    >[AZURE.NOTE] Veuillez prendre en charge les éléments suivants lorsque vous mettez à jour les détails de la référence SKU. **N’entrez pas de texte en double sous la description de l’offre et de la description de la référence SKU. N’entrez pas de texte en double sous le titre des références SKU et l’offre long résumé. N’entrez pas de texte en double sous le titre de référence et le résumé de l’offre.**

6. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce lien
7. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 modifier les liens existants ou ajouter de nouveaux liens

Vous pouvez modifier les liens existants ou ajouter de nouveaux liens et republiez votre offre en suivant les étapes ci-dessous :

1. Connexion d’accès à la [publication de portail](https://publish.windowsazure.com)
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton de **l’Anglais (États-Unis)** .
5. Dans la barre latérale de gauche, cliquez sur l’onglet **liens** .
6. Si vous souhaitez ajouter un nouveau lien, puis sous les *liens* de la section cliquez sur le bouton **Ajouter un lien** . La boîte de dialogue *« Ajouter un lien »* s’ouvre. Dans cette boîte de dialogue, vous pouvez ajouter le titre du lien et URL de champs et enregistrer les modifications. Vous pouvez entrer n’importe quel lien qui contient des informations qui peuvent aider les clients.
7. Si vous souhaitez mettre à jour ou supprimer un lien existant, puis sélectionnez le lien approprié et cliquez sur le bouton Modifier ou sur le bouton Supprimer en conséquence.

    >[AZURE.NOTE] Vérifiez que les liens que vous avez saisies dans cette section fonctionnent correctement, comme ces liens sont-elles validées au cours de votre processus de demande de production.

8. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 modifier une image d’exemple existante ou ajouter une nouvelle image de l’exemple

Vous pouvez modifier un exemples d’images existantes ou ajouter de nouvelles images d’échantillon et republiez votre offre en suivant les étapes ci-dessous :

>[AZURE.NOTE] Un seul exemple d’image s’affiche dans le [https://portal.azure.com](https://portal.azure.com).

1. Connexion d’accès à la [publication de portail](https://publish.windowsazure.com)
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton de **l’Anglais (États-Unis)** .
5. Dans la barre latérale de gauche, cliquez sur l’onglet **d’Exemples d’IMAGES** .
6. Si vous souhaitez ajouter une nouvelle image de l’exemple, cliquez sur le bouton **Télécharger une nouvelle IMAGE** dans la section des *Exemples d’Images* et puis enregistrez les modifications.

    >[AZURE.NOTE] Y compris un exemple d’image sont une étape facultative.

7. Si vous souhaitez mettre à jour ou supprimer une image existante de l’exemple, puis recherchez l’image appropriée et puis cliquez sur le bouton **Remplacer l’IMAGE** ou le bouton Supprimer en conséquence.

8. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour obtenir des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 mise à jour de contenu légal

Vous pouvez mettre à jour le contenu juridique et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connexion d’accès à la [publication de portail](https://publish.windowsazure.com)
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton de **l’Anglais (États-Unis)** .
5. Dans la barre latérale de gauche, cliquez sur l’onglet **juridique** . Sous la section *juridique* , vous pouvez mettre à jour vos stratégies/conditions d’utilisation. Entrez ou collez les stratégies/termes dans la zone de texte de *Conditions d’utilisation* et enregistrer les modifications.
6. La limite de caractères pour les conditions d’utilisation est de 1 000 000 caractères.
7. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 mettre à jour les informations de support

Vous pouvez mettre à jour les informations de support technique et le publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connexion d’accès à la [publication de portail](https://publish.windowsazure.com)
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **prise en charge** .
4. Sous la section *Ingénierie de Contact* de l’onglet **prise en charge** , vous pouvez mettre à jour les détails du contact. Ces détails sont utilisés pour les communications internes entre le partenaire et par Microsoft.
5. Dans la section *Prise en charge de client* de l’onglet **prise en charge** vous pouvez mettre à jour les informations de contact de Support tels que **nom, courrier électronique, téléphone** et **URL du support technique**. Ces détails sont utilisés pour les communications internes entre le partenaire et par Microsoft.

    >[AZURE.NOTE] Si vous souhaitez fournir uniquement le support par courrier électronique, fournir un numéro de téléphone fictifs sous la section **Service clientèle** . Dans ce cas, votre adresse de courriel fournie sera utilisé à la place.

6. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
7. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 mise à jour de les catégories

Vous pouvez mettre à jour la section catégories pour votre offre et publier à nouveau votre offre en suivant les étapes ci-dessous :

1. Connexion d’accès à la [publication de portail](https://publish.windowsazure.com)
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **catégories** .
4. Dans la section *catégories* , vous pouvez mettre à jour les catégories de votre offre et enregistrer les modifications. Vous pouvez sélectionner jusqu'à cinq catégories de la galerie d’Azure Marketplace.
5. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
6. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. comment ajouter une nouvelle référence SKU sous une offre de la liste

Vous pouvez ajouter une nouvelle référence SKU sous votre offre direct en suivant les étapes indiquées ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet de **SKU** . Après ce clic sur le bouton **Ajouter une référence**.  Une boîte de dialogue s’ouvre. Permet d’entrer un identificateur de référence en minuscules. Cochez la case de mettre vos propres de facturation model(BYOL) si vous souhaitez publier le nouvelles références SKU avec le modèle de facturation BYOL. Dans le cas contraire, désactivez la case à cocher pour BYOL. Après ce clic sur la marque de graduation dans la boîte de dialogue pour créer une nouvelle référence SKU. Si vous ne pas opter pour le modèle de facturation BYOL pour le nouvelles références SKU, puis le modèle de facturation est automatiquement fixé à toutes les heures pour le nouvelles références SKU. Si vous souhaitez activer la version d’évaluation gratuite de 30 jours pour le modèle de facturation horaire, puis cliquez sur l’option « Mois » pour « est une version d’évaluation gratuite disponible ? ». Dans le cas contraire, sélectionnez « NO évaluation ». [Remarque : l’option « est une version d’évaluation gratuite disponible ? » ne s’affiche que si vous avez sélectionné BYOL pas dans la boîte de dialogue lors de la création de la nouvel SKU.]

    >[AZURE.IMPORTANT] L’option « Masquer cette référence à partir du marché, car il doit toujours être achetée via un modèle de solution » doit être marqué comme « Oui » uniquement si vous sont approuvées pour la publication d’une modèle d’offre sur le marché d’Azure. Dans le cas contraire, cette option doit toujours être signalée comme « Non ».

4. Maintenant à partir de la barre latérale de gauche, cliquez sur l’onglet **IMAGES de la machine virtuelle** et découvrez le nouvelles références SKU que vous avez créée.
5. Pour configurer le nouvelles références SKU, reportez-vous à l’étape 5 de ce [lien](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) pour obtenir des instructions.
6. Pour ajouter le matériel de marketing pour le nouvelles références SKU, reportez-vous à la section Étape 1 : contenu marketing de fournir le marché -> Détails -> point de numéros 2 à 5 de ce [lien](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Pour ajouter les informations de tarification pour le nouvelles références SKU, reportez-vous à la section 2.1. Définir le prix de votre machine virtuelle de ce [lien](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Après avoir apporté les modifications, accédez à l’onglet **Publier** et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
9. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. comment modifier le nombre de disques de données pour un point de stock répertorié

Vous ne pouvez pas incrémentation/décrémentation le nombre de disques de données d’un point de stock répertorié. Vous devez créer une nouvelle référence SKU dans ce cas. Reportez-vous à la section [3. Comment faire pour ajouter une nouvelle référence SKU sous une offre live](#3-how-to-add-a-new-sku-under-a-live-offer) pour obtenir des instructions détaillées.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. comment supprimer une offre de la liste à partir de Azure Marketplace

Il existe différents aspects qui doivent être pris en compte dans le cas d’une demande pour supprimer une offre live. Veuillez suivre les étapes ci-dessous pour obtenir des conseils de l’équipe de support pour supprimer une liste offre le marché Azure :

1.  Déclencher un ticket de support à l’aide de ce [lien](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Sélectionnez le type de problème en tant que **« Gestion des offres »** et sélectionnez la catégorie comme **« Modification une offre ou un SKU déjà en production »**
3.  Soumettre la demande

L’équipe de support vous guidera dans le processus de suppression/SKU de l’offre.

>[AZURE.NOTE] Vous pouvez toujours supprimer l’offre alors qu’il est dans un état de brouillon (c'est-à-dire, pas en PRODUCTION ou intermédiaire) en cliquant sur le bouton **Annuler le brouillon** sous l’onglet **historique** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. comment supprimer une référence SKU répertoriée à partir de Azure Marketplace

Vous pouvez supprimer une référence SKU répertoriée à partir de Azure Marketplace en suivant les étapes indiquées ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans le volet gauche, cliquez sur l’onglet **références** .
4. Sélectionnez la référence que vous voulez supprimer et cliquez sur le bouton Supprimer sur cette version de Windows.
5. Une fois terminé, accédez à l’onglet Publier dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier à nouveau l’offre sur le marché d’Azure.
6. Une fois le nouveau publiée l’offre sur le marché d’Azure, le point de stock sera supprimé le Azure Marketplace et le portail Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. comment supprimer la version actuelle d’un SKU répertorié à partir de Azure Marketplace

Vous pouvez supprimer la version actuelle d’un SKU répertorié à partir de Azure Marketplace en suivant les étapes indiquées ci-dessous. Une fois le processus terminé, la référence sera restaurée à sa version précédente.

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2.  Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3.  Dans le volet de gauche, cliquez sur l’onglet **IMAGES de la machine virtuelle** .
4.  Sélectionnez la référence dont vous souhaitez supprimer et cliquez sur le bouton Supprimer cette version la version en cours.
5.  Une fois terminé, accédez à l’onglet **Publier** dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier à nouveau l’offre sur le marché d’Azure.
6.  Une fois le nouveau publiée l’offre sur le marché d’Azure, la version actuelle de la référence SKU de la liste sera supprimée le Azure Marketplace et le portail Azure. Le point de stock sera restaurée à sa version précédente.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. comment restaurer la liste des prix aux valeurs de production
J’ai changé la tarification d’une référence SKU répertoriée (ou j’ai supprimé les régions de facturation d’un SKU répertorié). Dans la mesure où il n’est pas pris en charge sur le marché d’Azure, je souhaite annuler mes modifications pour les valeurs de production. Comment atteindre qui ?

Veuillez suivre les étapes indiquées ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **tarification** .
4. Sous l’onglet tarification, sélectionnez une région dont vous souhaitez réinitialiser la tarification.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. En cas de points de stock avec le modèle de facturation horaire, réinitialiser les prix de tous les noyaux dans la production pour la zone sélectionnée. Pour les points de stock avec le modèle de facturation BYOL, rendre le SKU disponibles dans la région en activant la case à cocher par rapport à la référence dans la section disponibilité de référence EXTERNALLY-LICENSED (BYOL) (voir la capture d’écran ci-dessous).

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Maintenant, cliquez sur le bouton **AUTOPRICE autres marchés basés sur les prix en États-Unis**.

    >[AZURE.NOTE] L’étiquette du bouton peut-être être différent selon la région que vous avez sélectionnés. Dans la mesure où nous avons sélectionné les États-Unis lors de la création de ce document, donc le bouton est intitulé « Autres marchés basées sur les prix aux États-Unis de prix automatique » dans la capture d’écran ci-dessous.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. L’Assistant de prix automatique s’ouvre. La première page affiche la sélection pour le marché de base. Rendre votre section et passer à la page suivante en cliquant sur le bouton **« -> »** .

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Option permettant de sélectionner les cœurs et les plans s’affichera sur la page 2. Sélectionnez les plans de votre choix et les brins, cliquez sur « -> » bouton.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Page 3 affiche les marchés ou les régions. Cliquez sur le bouton Activer/désactiver tous pour sélectionner toutes les zones ou de vérifier manuellement les cases de la région. Cliquez sur le bouton « -> » pour passer à la page suivante.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Page 4 affiche le taux de change. Cliquez sur le bouton Terminer pour terminer la procédure. L’Assistant permet de réinitialiser la tarification en fonction de votre sélection.

11. Maintenant, accédez à l’onglet tarification et cliquez sur le bouton « Affichage résumé et modifications ».
Sélectionnez « Brouillon » dans la section « Version de la vue » et la « Production » de section de « Comparer avec » (voir la capture d’écran ci-dessous). Si vous ne voyez aucune différence de prix, elle implique de tarification a été rétablie les valeurs de production avec succès.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Après avoir apporté les modifications, accédez à l’onglet Publier et cliquez sur le bouton **PUSH pour la mise en attente**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, consultez ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
13. Une fois que vous avez testé votre offre dans la phase intermédiaire, accédez à l’onglet Publier dans la publication portail et cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. comment rétablir le modèle de facturation pour les valeurs de production
J’ai modifié le modèle de facturation d’un SKU répertorié. Dans la mesure où il n’est pas pris en charge sur le marché d’Azure, je souhaite annuler mes modifications pour les valeurs de production. Comment atteindre qui ?

Veuillez suivre les étapes ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **références** .
4. Cliquez sur le bouton Modifier pour revenir au modèle de facturation. Une fenêtre s’ouvre. Activez ou désactivez la case à cocher **« de facturation et de gestion de licences est effectuée en externe à partir d’Azure (alias amener votre propre licence) »** en conséquence.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Une fois, reportez-vous à la réponse de la question 8 dans ce document pour revenir en arrière la tarification.
6. Une fois qu’accédez à l’onglet **Publier** dans la publication portail et la diffusion de l’offre à la zone de transit pour le tester. Vous en avez terminé avec l’offre de test, puis cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. comment restaurer le paramètre de visibilité d’un SKU répertorié pour la valeur de la production

Veuillez suivre les étapes ci-dessous :

1. Ouverture de session sur le [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **ordinateurs virtuels** et sélectionnez votre offre.
3. Dans la barre latérale de gauche, cliquez sur l’onglet **références** .
4. Sélectionnez votre SKU et rétablir les paramètres de visibilité de la référence à la valeur de la production.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Vous avez terminé les modifications, puis cliquez sur le bouton **Demander approbation à pousser à la PRODUCTION** pour publier de nouveau votre offre sur le marché d’Azure.

## <a name="see-also"></a>Voir aussi
- [Mise en route : Comment publier une offre sur le marché d’Azure](marketplace-publishing-getting-started.md)
- [Comprendre les idées de vendeur reporting](marketplace-publishing-report-seller-insights.md)
- [Présentation des rapports de paiement](marketplace-publishing-report-payout.md)
- [Comment faire pour modifier les incentives pour les revendeurs de votre fournisseur de solutions de Cloud](marketplace-publishing-csp-incentive.md)
- [Résolution des problèmes de publication courants sur le marché](marketplace-publishing-support-common-issues.md)
- [Obtenir de l’assistance en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)
- [Création d’une image de machine virtuelle sur site](marketplace-publishing-vm-image-creation-on-premise.md)
- [Créer un ordinateur virtuel exécutant Windows dans le portail d’aperçu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
