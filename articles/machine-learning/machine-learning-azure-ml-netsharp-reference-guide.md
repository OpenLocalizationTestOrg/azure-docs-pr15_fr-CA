<properties 
    pageTitle="Guide du langage de spécification de réseaux neuronaux Net # | Microsoft Azure" 
    description="Syntaxe de la neural Net # réseaux langage de spécification, ainsi que des exemples de création d’un modèle de réseau neuronal personnalisé dans Microsoft Azure ML est à l’aide de Net#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guide de langage de spécification de réseau neuronal Net # pour l’apprentissage de Machine Azure

## <a name="overview"></a>Vue d’ensemble
NET # est un langage de développé par Microsoft, qui est utilisée pour définir des architectures de réseau neuronal pour modules de réseau neuronal dans l’apprentissage d’automatique Microsoft Azure. Dans cet article, vous apprendrez à :  

-   Concepts de base relatifs aux réseaux neuronaux
-   Comment définir les principaux composants et les exigences de réseau neuronal
-   La syntaxe et les mots clés du langage de spécification Net #
-   Parmi les réseaux neuronaux personnalisés créés à l’aide de Net# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Bases des réseaux neuronaux
Une structure de réseau neuronal se compose des ***nœuds*** qui sont organisés en ***couches***et pondérée de ***connexions*** (ou ***bords***) entre les nœuds. Les connexions sont directionnelles et chaque connexion dispose d’un nœud ***source*** et un nœud de ***destination*** .  

Chaque ***couche capable d’apprentissage*** (un masquée ou une couche de sortie) a un ou plusieurs ***lots de connexion***. Une offre groupée de connexion se compose d’un calque source et une spécification des connexions à partir du calque source. Toutes les connexions d’un lot donné partagent le même ***calque source*** et le même ***calque de destination***. Dans Net #, une offre groupée de connexion est considérée comme appartenant au calque de destination de l’offre groupée.  
 
NET # prend en charge les différents types de lots, qui vous permet de personnaliser les entrées de la façon dont sont mis en correspondance avec les calques masqués et mis en correspondance avec les sorties de connexion.   

L’offre groupée standard ou par défaut est une **offre complète**, dans laquelle chaque nœud dans le calque source est connecté à tous les nœuds dans le calque de destination.  

En outre, Net # prend en charge les quatre types d’offres de connexion avancées suivantes :  

-   **Offres groupées de filtré**. L’utilisateur peut définir un prédicat en utilisant les emplacements de nœud calque source et le nœud de calque de destination. Les nœuds sont connectés à chaque fois que le prédicat a la valeur True.
-   **Offres groupées à convolution**. L’utilisateur peut définir des petits cercles de nœuds dans le calque source. Chaque nœud dans le calque de destination est connecté à un club de nœuds dans le calque source.
-   **Mise en pool de bottes** et les **bottes de normalisation de réponse**. Ils sont semblables à des offres groupées à convolution dans la mesure où l’utilisateur définit des petits cercles de nœuds dans le calque source. La différence est que les pondérations de bords dans ces offres ne sont pas capable d’apprentissage. Au lieu de cela, une fonction prédéfinie est appliquée pour les valeurs de nœud source pour déterminer la valeur de nœud de destination.  

À l’aide de Net # pour définir la structure d’un réseau neuronal rend possible de définir des structures complexes tels que les réseaux neuronaux approfondie ou de structures de dimensions arbitraires, qui sont connues pour améliorer la formation sur des données comme image, audio ou vidéo.  

## <a name="supported-customizations"></a>Personnalisations prises en charge
L’architecture des modèles de réseau neuronal que vous créez dans l’apprentissage automatique de Azure peut être largement personnalisé à l’aide de Net #. Vous pouvez :  

-   Créer des calques masqués et contrôler le nombre de nœuds de chaque couche.
-   Permet de spécifier comment les couches doivent être connectés entre eux.
-   Définir des structures de connectivité spéciaux, tels que les structures et partage des offres groupées de poids.
-   Spécifier les différentes fonctions d’activation.  

Pour plus de détails de la syntaxe de langage de spécification, consultez [Spécification de la Structure](#Structure-specifications).  
 
Pour obtenir des exemples de définition des réseaux neuronaux pour un ordinateur commun apprentissage des tâches, à partir de simplex complexe, consultez les [exemples](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Exigences générales
-   Il faut couche de seule sortie, au moins une couche d’entrée et zéro, une ou plusieurs couches cachées. 
-   Chaque couche possède un nombre fixe de nœuds, conceptuellement disposées dans un tableau rectangulaire de dimensions arbitraires. 
-   Les couches d’entrée ont pas formés de paramètres associés et représentent le point où les données de l’instance entre le réseau. 
-   Couches capable d’apprentissage (les calques masqués et de sortie) sont associés à des paramètres formés, appelés poids et biais. 
-   Les nœuds source et de destination doivent être dans des couches séparées. 
-   Les connexions doivent être acycliques ; en d’autres termes, il ne peut pas être une chaîne de connexions pointe vers le nœud d’origine.
-   La couche de sortie ne peut pas être une source d’une offre groupée de connexion.  

## <a name="structure-specifications"></a>Spécifications de la structure
Une spécification de structure de réseau neuronal est composée de trois sections : la **déclaration de constante**, la **déclaration de la couche**, la **déclaration de connexion**. Il existe également une section facultative **partager la déclaration** . Les sections peuvent être spécifiées dans n’importe quel ordre.  

## <a name="constant-declaration"></a>Déclaration de constante 
Une déclaration de constante est facultative. Il fournit un moyen de définir les valeurs utilisées ailleurs dans la définition du réseau neuronal. L’instruction de déclaration se compose d’un identificateur suivi d’un signe égal et une expression de valeur.   

Par exemple, l’instruction suivante définit une constante **x**:  


    Const X = 28;  

Pour définir simultanément les deux constantes ou plus, mettez les valeurs et les noms d’identificateurs entre accolades et séparés par des points-virgules. Par exemple :  

    Const { X = 28; Y = 4; }  

La partie droite de chaque expression d’affectation peut être un entier, un nombre réel, une valeur booléenne (True ou False) ou une expression mathématique. Par exemple :  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Déclaration de couche
La déclaration de la couche est requise. Il définit la taille et la source du calque, y compris ses attributs et les offres groupées de connexion. L’instruction de déclaration commence par le nom de la couche (entrée, masqués ou de sortie), suivi par les dimensions du calque (un tuple d’entiers positifs). Par exemple :  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   Le produit des dimensions est le nombre de nœuds de la couche. Dans cet exemple, il y a deux dimensions [5,20], qui signifie qu’il existe 100 nœuds dans la couche.
-   Les couches peuvent être déclarés dans n’importe quel ordre, à une exception près : si plus d’une couche d’entrée est définie, l’ordre dans lequel elles sont déclarées doit correspondre à l’ordre des fonctions dans les données d’entrée.  


Pour spécifier que le nombre de nœuds dans un calque détermine automatiquement, utilisez le mot clé **auto** . Le mot clé **auto** a des effets différents, en fonction de la couche :  

-   Dans une déclaration de la couche d’entrée, le nombre de nœuds est le nombre de fonctionnalités dans les données d’entrée.
-   Dans une déclaration de la couche cachée, le nombre de nœuds est le nombre qui est spécifié par la valeur du paramètre nombre **de nœuds masqués**. 
-   Dans une déclaration de couche de sortie, le nombre de nœuds est 2 pour la classification de deux classes, 1 pour la régression et égale au nombre de nœuds de sortie pour le classement multiclass.   

Par exemple, la définition suivante de réseau permet la taille de tous les calques pour être déterminée automatiquement :  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Une déclaration de calque d’un calque capable d’apprentissage (les calques masqués ou sorties) peut éventuellement inclure la fonction de sortie (également appelée une fonction d’activation), qui est par défaut **sigmoïde** pour les modèles de classification et **linéaire** pour les modèles de régression. (Même si vous utilisez la valeur par défaut, vous pouvez indiquez explicitement la fonction d’activation, si vous le souhaitez pour plus de clarté.)

Les fonctions de sortie suivantes sont prises en charge :  

-   sigmoïde
-   linéaire
-   Softmax
-   rlinear
-   carré
-   SQRT
-   srlinear
-   ABS
-   TANH 
-   brlinear  

Par exemple, la déclaration suivante utilise la fonction **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Déclaration de connexion
Immédiatement après la définition de la couche capable d’apprentissage, vous devez déclarer les connexions entre les couches que vous avez défini. La déclaration de groupe de connexion démarre avec le mot clé **à partir de**, suivi du nom de la couche d’origine de l’offre et le type d’ensemble de connexion pour créer.   

Actuellement, les cinq types d’offres de connexion sont prises en charge :  

-   Offres **complètes** , indiqués par le mot clé **toutes les**
-   Offres **filtré** , indiqués par le mot clé **où**, suivi d’une expression de prédicat
-   Offres **Convolutional** , indiqués par le mot clé **convolve**, suivi par les attributs de convolution
-   Offres groupées de **pooling** , indiquées par le mots-clés **max pool** ou **signifie le pool**
-   Offres groupées de **normalisation de la réponse** , indiquées par le mot clé **normales de réponse**      

## <a name="full-bundles"></a>Offres complètes  

Un ensemble complet de connexion comporte une connexion à partir de chaque nœud dans le calque source à chaque nœud dans le calque de destination. Il s’agit du type de connexion réseau par défaut.  

## <a name="filtered-bundles"></a>Offres groupées filtrées
Une connexion filtrée offre groupée inclut une bien prédicat, du point de vue syntaxique, exprimée comme une expression lambda de C#. L’exemple suivant définit deux offres filtrées :  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   Dans le prédicat pour _ByRow_, **s** est un paramètre représentant un index dans le tableau rectangulaire de nœuds de la couche d’entrée, _Pixels_, et **d** est un paramètre représentant un index dans le tableau des nœuds de la couche cachée, _ByRow_. Le type de **s** et **d** est un tuple d’entiers de longueur deux. Du point de vue conceptuel, les plages de **s** sur toutes les paires de nombres entiers avec _0 < = s [0] < 10_ et _0 < = s[1] < 20_, et les plages de **d** sur toutes les paires d’entiers, avec _0 < = d [0] < 10_ et _0 < = d[1] < 12_. 
-   Sur le côté droit de l’expression de prédicat, il existe une condition. Dans cet exemple, pour chaque valeur de **s** et **d** telles que la condition est True, il existe une arête à partir du nœud de couche source vers le nœud de calque de destination. Par conséquent, cette expression de filtre indique que l’offre groupée inclut une connexion à partir du nœud défini par **s** pour le nœud défini par **d** dans tous les cas où s [0] est égal à d [0].  

Si vous le souhaitez, vous pouvez spécifier un ensemble de poids d’un ensemble filtré. La valeur de l’attribut de **poids** doit être un tuple de valeurs à virgule flottante dont la longueur correspond au nombre de connexions définies par l’offre groupée. Par défaut, les poids sont générés au hasard.  

Les valeurs de poids sont regroupées par l’index de nœud de destination. Autrement dit, si le premier nœud de destination est connecté aux nœuds de source K, _K_ premiers éléments du tuple **poids** sont les poids pour le premier nœud de destination, dans l’ordre d’index source. Il en va de même pour les nœuds restants de la destination.  

Il est possible de spécifier un poids directement en tant que valeurs de constante. Par exemple, si vous avez précédemment appris les poids, vous pouvez les spécifier en tant que constantes à l’aide de cette syntaxe :

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Offres groupées à convolution
Lorsque les données d’apprentissage ont une structure homogène, connexions à convolution sont couramment utilisées pour apprendre les fonctionnalités de base de données. Par exemple, dans l’image, les données audio ou vidéo, dimensionnalité spatiale ou temporelle peuvent être relativement uniformes.  

Offres groupées à convolution utilisent des **noyaux** rectangulaire qui sont glissé à travers les dimensions. Pour l’essentiel, chaque noyau définit un ensemble de poids appliqué dans des Clubs locaux, appelés **applications de noyau**. Chaque application du noyau correspond à un nœud dans le calque source, qui est désignée sous le **nœud central**. Les poids d’un noyau sont partagés entre plusieurs connexions. Dans une offre groupée à convolution, chaque noyau est rectangulaire et toutes les applications de noyau ont la même taille.  

Offres groupées à convolution prennent en charge les attributs suivants :

**InputShape** définit la dimensionnalité du calque source pour les besoins de ce groupe à convolution. La valeur doit être un tuple d’entiers positifs. Le produit des entiers doit être égal au nombre de nœuds dans le calque source, mais dans le cas contraire, il n’a pas besoin de correspondant à la dimensionnalité déclarée pour le calque source. La longueur de ce tuple devient la valeur **arité** pour l’offre groupée à convolution. (En général une arité fait référence au nombre d’arguments ou d’opérandes possibles d’une fonction.)  

Pour définir la forme et les emplacements des noyaux, utilisez les attributs **KernelShape**, **Stride**, **remplissage**, **LowerPad**et **UpperPad**:   

-   **KernelShape**: (obligatoire) définit la dimensionnalité de chaque noyau pour l’offre groupée à convolution. La valeur doit être un tuple d’entiers positifs avec une longueur égale à l’arité de l’offre. Chaque composant de ce tuple doit être ne peut excéder le composant correspondant de **InputShape**. 
-   **STRIDE**: (facultatif) définit les tailles étape décalée de la convolution (taille de l’étape pour chaque dimension), qui est la distance entre les nœuds central. La valeur doit être un tuple d’entiers positifs avec une longueur qui correspond à l’arité de l’offre. Chaque composant de ce tuple doit être ne peut excéder le composant correspondant de **KernelShape**. La valeur par défaut est un tuple avec tous les composants égales à un. 
-   **Partage**: (facultatif) définit le poids de partage pour chaque dimension de la convolution. La valeur peut être une valeur booléenne unique ou un tuple de valeurs booléennes avec une longueur qui correspond à l’arité de l’offre. Une valeur booléenne unique est étendue à un tuple de la longueur correcte avec tous les composants sont égales à la valeur spécifiée. La valeur par défaut est un tuple qui se compose de toutes les valeurs True. 
-   **MapCount**: (facultatif) définit le numéro de fonctionnalité est mappé pour l’offre groupée à convolution. La valeur peut être un entier positif unique ou un tuple d’entiers positifs avec une longueur qui correspond à l’arité de l’offre. Une seule valeur entière est étendue à un tuple de la longueur correcte avec les premiers composants égales à la valeur spécifiée et tous les composants restants est égal à un. La valeur par défaut est 1. Le nombre total de cartes de fonctionnalité est le produit des composants du tuple. La factorisation de ce nombre total entre les composants détermine la manière dont les valeurs de mappage de fonction sont regroupés dans les nœuds de destination. 
-   **Poids**: (facultatif) définit les poids initiales pour l’offre groupée. La valeur doit être un tuple de valeurs à virgule flottante dont la longueur est le nombre de noyaux fois le nombre de poids par le noyau, tel que défini plus loin dans cet article. Les poids par défaut sont générés de façon aléatoire.  

Il existe deux jeux de propriétés qui contrôlent les marges intérieures, les propriétés en cours qui s’excluent mutuellement :

-   **Remplissage**: (facultatif) détermine si l’entrée doit être remplie à l’aide d’un **modèle de remplissage par défaut**. La valeur peut être une valeur booléenne unique, ou il peut être un tuple de valeurs booléennes avec une longueur qui correspond à l’arité de l’offre. Une valeur booléenne unique est étendue à un tuple de la longueur correcte avec tous les composants sont égales à la valeur spécifiée. Si la valeur d’une dimension a la valeur True, la source est logiquement remplie dans cette dimension avec la valeur zéro de cellules pour prendre en charge des applications de noyau supplémentaires, tels que les nœuds centrale des première et la dernières noyaux de cette dimension sont les premier et dernier noeud de cette dimension dans le calque source. Par conséquent, le nombre de nœuds « factices » dans chaque dimension est déterminé automatiquement, pour s’adapter exactement à _(InputShape [d] - 1) / Stride [d] + 1_ noyaux dans le calque source rembourré. Si la valeur d’une dimension a la valeur False, les noyaux définies de sorte que le nombre de nœuds de chaque côté qui ne sont pas indiqués est identique (à une différence de 1). La valeur par défaut de cet attribut est un tuple avec tous les composants égales à False.
-   **UpperPad** et **LowerPad**: (facultatif) fournir mieux contrôler la quantité de remplissage à utiliser. **Important :** Ces attributs peuvent être définis uniquement si la propriété **Padding** ci-dessus n’est ***pas*** défini. Les valeurs doivent être des valeurs entières de tuples dont les longueurs sont l’arité de l’offre. Lorsque ces attributs sont spécifiés, les nœuds « factices » sont ajoutés aux extrémités inférieures et supérieures de chaque dimension du calque d’entrée. Le nombre de nœuds ajoutés aux extrémités inférieures et supérieures de chaque dimension est déterminé respectivement par **LowerPad**[i] et **UpperPad**[i]. Pour vous assurer que les noyaux correspondent uniquement aux nœuds de « real » et non à des nœuds « factices », les conditions suivantes doivent être remplies :
    -   Chaque composant de **LowerPad** doit être strictement inférieure à KernelShape [d] / 2. 
    -   Chaque composant de **UpperPad** doit pas être supérieure à KernelShape [d] / 2. 
    -   La valeur par défaut de ces attributs est un tuple avec tous les composants égales à 0. 

Le paramètre **remplissage** = true autorise autant de remplissage comme est nécessaire pour maintenir le « centre » du noyau à l’intérieur de la « réel » d’entrée. Cela modifie l’écriture mathématique un peu pour le calcul de la taille de sortie. En règle générale, la taille de sortie _D_ est calculée en tant que _D = (I - K) / S + 1_, où _I_ est la taille de l’entrée, _K_ est la taille du noyau, _S_ est la largeur de numérisation, et _/_ est une division d’entier (arrondi vers zéro). Si vous définissez UpperPad = [1, 1], la taille de l’entrée _I_ est effectivement 29 et par conséquent _D = (29-5) / 2 + 1 = 13_. Toutefois, lorsque **de remplissage** = true, essentiellement __ J’obtient augmenté par _K - 1_; Par conséquent, _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. En spécifiant des valeurs pour **UpperPad** et **LowerPad** , vous obtenez beaucoup plus de contrôle sur la marge intérieure que si vous venez de définir **Padding** = true.

Pour plus d’informations sur les réseaux à convolution et leurs applications, voir les articles suivants :  

-   [http://deeplearning.NET/Tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.Microsoft.com/pubs/68920/icdar03.PDF](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://People.csail.MIT.edu/jvb/Papers/cnn_tutorial.PDF](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Le regroupement de tresses
Un **lot de regroupement** s’applique géométrie similaire à connectivité à convolution, mais elle utilise des fonctions prédéfinies à des valeurs de nœud source pour obtenir la valeur de nœud de destination. Par conséquent, regroupement de tresses n’ont aucun état capable d’apprentissage (poids ou biais). Regroupement de tresses prend en charge tous les attributs à convolution, à l’exception de **partage**, les **MapCount**et les **poids**.  

En règle générale, les noyaux synthétisées par unités de regroupement adjacentes ne se chevauchent pas. Si la largeur de numérisation [d] est égal à KernelShape [d] dans chaque dimension, la couche obtenue est la locale couche regroupement traditionnelle, ce qui est généralement employée dans des RESEAUX NEURONAUX CONVOLUTIONNELS. Chaque nœud de destination calcule la valeur maximale ou la moyenne des activités de son noyau dans le calque source.  

L’exemple suivant illustre un lot de regroupement : 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   L’arité de l’offre groupée est 3 (la longueur de tuples, **InputShape**, **KernelShape**et **Stride**). 
-   Le nombre de nœuds dans le calque source est _5 *24* 24 = 2 880_. 
-   Il s’agit d’une couche de regroupement locale traditionnelle car **KernelShape** et **Stride** sont égaux. 
-   Le nombre de nœuds dans le calque de destination est _5 *12* 12 = 1 440_.  
    
Pour plus d’informations sur la mise en pool des calques, voir les articles suivants :  

-   [http://www.cs.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Point 3.4)
-   [http://cs.NYU.edu/~koray/publis/lecun-iscas-10.PDF](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://cs.NYU.edu/~koray/publis/jarrett-iccv-09.PDF](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Offres groupées de normalisation de réponse
**Normalisation de la réponse** est un schéma de normalisation local qui a été introduit par Geoffrey Hinton, et al., dans le livre de [Classiﬁcation de ImageNet avec une profondeur de RESEAUX NEURONAUX CONVOLUTIONNELS](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalisation de la réponse est utilisée pour faciliter la généralisation dans les réseaux neuronaux. Lorsque déclenche un neurone à un niveau très élevé de l’activation, une couche de normalisation de réponse local supprime le niveau d’activation entourent des neurones de la. Cela est fait à l’aide de trois paramètres (***α***, ***β***et ***k***) et une structure à convolution (ou forme de cercle). Chaque neurone de la couche de destination ***y*** correspond à un neurone ***x*** dans le calque source. Le niveau d’activation de ***y*** est donné par la formule suivante, où ***f*** est le niveau d’activation d’un neurone et ***Nx*** est le noyau (ou l’ensemble qui contient les neurones dans le voisinage de ***x***), tel que défini par la structure à convolution suivante :  

![][1]  

Offres groupées de normalisation de réponse prend en charge tous les attributs à convolution, à l’exception de **partage**, les **MapCount**et les **poids**.  
 
-   Si le noyau contient les neurones dans le même plan que ***x***, le jeu de normalisation est appelé **même normalisation de mappage**. Pour définir le même normalisation de carte, la première coordonnée dans **InputShape** doit avoir la valeur 1.
-   Si le noyau contient des neurones dans la même position spatiale sous la forme ***x***, mais les neurones dans les autres cartes, le jeu de normalisation est appelé **sur la normalisation des cartes**. Ce type de normalisation de la réponse implémente un formulaire d’inhibition latérale inspiré par le type de neurones réels, création de concurrence pour les niveaux d’activation du grand entre les sorties de neurone calculées sur des cartes différentes. Pour définir cette option sur les cartes de normalisation, la première coordonnée doit être un entier supérieur à 1 et ne peut excéder le nombre de cartes, et le reste des coordonnées doit avoir la valeur 1.  

Offres groupées de normalisation de réponse s’appliquant à une fonction prédéfinie pour les valeurs de nœud source pour déterminer la valeur de nœud de destination, ils n’ont aucun état capable d’apprentissage (poids ou biais).   

**Alerte**: les nœuds dans le calque de destination correspondent aux neurones qui sont les nœuds central du cerneau. Par exemple, si KernelShape [d] est impair, puis _KernelShape [d] / 2_ correspondant au nœud noyau central. Si _KernelShape [d]_ est la même, le nœud central est au _KernelShape [d] / 2-1_. Par conséquent, si les **marges intérieures**[d] est False, la première et la dernière _KernelShape [d] / 2_ nœuds n’ont pas de nœuds correspondants dans le calque de destination. Pour éviter cette situation, définissez **marge intérieure** en tant que [true,..., true, true].  

En plus des quatre attributs décrits plus haut, bottes de normalisation de réponse prennent également en charge les attributs suivants :  

-   **Alpha**: (obligatoire) indique une valeur à virgule flottante qui correspond à ***α*** dans la formule précédente. 
-   **Bêta**: (obligatoire) indique une valeur à virgule flottante qui correspond à ***β*** dans la formule précédente. 
-   **Décalage**: (facultatif) spécifie une valeur à virgule flottante qui correspond à ***k*** de la formule précédente. 1 par défaut.  

L’exemple suivant définit un groupe de normalisation de réponse à l’aide de ces attributs :  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   Le calque source inclut cinq cartes, chaque dimension unede de 12 x 12, total de nœuds de 1440. 
-   La valeur **KernelShape** indique qu’il s’agit d’un même calque de normalisation de mappage, où le voisinage est un rectangle de 3 x 3. 
-   La valeur par défaut de **remplissage** a la valeur False, donc le calque de destination a seulement 10 nœuds dans chaque dimension. Pour inclure un nœud dans le calque de destination qui correspond à tous les nœuds dans le calque source, ajouter le remplissage = [true, true, true] ; et modifiez la taille de RN1 à [5, 12 et 12].  

## <a name="share-declaration"></a>Déclaration de partage 
NET # éventuellement prend en charge la définition de plusieurs bundles avec poids partagés. Les poids de tous deux offres peuvent être partagées si leurs structures sont identiques. La syntaxe suivante définit des offres groupées avec poids partagés :  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

Par exemple, la déclaration suivante de partage spécifie les noms de calque, indiquant que les poids et les biais doivent être partagées :  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   Les fonctionnalités d’entrée sont partitionnées en deux couches d’entrée tailles égales. 
-   Les calques masqués calculent des fonctions de niveau supérieures sur les deux couches d’entrée. 
-   La déclaration de partage spécifie que _H1_ et _H2_ doivent être calculé de la même façon à partir de leurs entrées respectives.  
 
Sinon, cela peut être spécifiée avec deux déclarations de partage séparées comme suit :  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Vous pouvez utiliser la forme abrégée uniquement lorsque les couches contiennent un lot unique. En règle générale, le partage est possible uniquement lorsque la structure appropriée est identique, c'est-à-dire qu’ils ont la même taille, même géométrie à convolution et ainsi de suite.  

## <a name="examples-of-net-usage"></a>Exemples d’utilisation de Net #
Cette section fournit des exemples de la façon dont vous pouvez utiliser Net # pour ajouter les calques masqués, définir la façon dont les calques masqués interagissent avec d’autres calques et construire des réseaux à convolution.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Définir un réseau neuronal personnalisé simple : exemple de « Hello World »
Cet exemple simple montre comment créer un modèle de réseau neuronal qui a une seule couche cachée.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

L’exemple illustre certaines des commandes de base comme suit :  

-   La première ligne définit la couche d’entrée (nommée _données_). Lorsque vous utilisez le mot clé **auto** , le réseau neuronal inclut automatiquement toutes les colonnes de fonctionnalité dans les exemples d’entrée. 
-   La deuxième ligne crée la couche masquée. Le nom _H_ est affecté au calque masqué, qui dispose de 200 nœuds. Cette couche est entièrement connectée à la couche d’entrée.
-   La troisième ligne définit la couche de sortie (nommée _O_), qui contient 10 postes de sortie. Si le réseau neuronal est utilisé pour le classement, il existe un nœud sortie par classe. Le mot clé **sigmoïde** indique que la fonction de sortie est appliquée à la couche de sortie.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Définir plusieurs calques masqués : exemple de vision d’ordinateur
L’exemple suivant montre comment définir un réseau neuronal légèrement plus complexe, avec plusieurs calques masqués personnalisés.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Cet exemple illustre plusieurs fonctionnalités du langage de spécification de réseaux neuronaux :  

-   La structure a deux couches d’entrée, les _métadonnées_et les _Pixels_ .
-   La couche de _Pixels_ est une couche de source pour les deux groupes de connexion avec des couches de destination, _ByRow_ et _ByCol_.
-   Les couches _rassembler_ et le _résultat_ sont des couches de destination en bottes de connexion multiples.
-   La couche de sortie, le _résultat_est un calque de destination dans les deux offres de connexion ; l’une avec le deuxième niveau masqué (collecte) sous la forme d’un calque de destination et l’autre avec le calque d’entrée (métadonnées) sous la forme d’un calque de destination.
-   Les calques masqués, _ByRow_ et _ByCol_, spécifiez connectivité filtrée à l’aide d’expressions de prédicat. Plus précisément, le nœud de _ByRow_ à [x, y] est connecté aux nœuds dans les _Pixels_ dont la première coordonnée d’index égale à la première coordonnée du nœud, x. De même, le nœud de _ByCol à [x, y] est connecté aux nœuds dans les _Pixels_ qui ont le deuxième index de coordonnées dans une des coordonnées de deuxième du nœud, y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Définissez un réseau à convolution de classification multiclass : exemple de reconnaissance de chiffre
La définition du réseau suivant est conçue pour reconnaître des nombres, et il illustre des techniques avancées pour la personnalisation d’un réseau neuronal.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   La structure a une seule couche d’entrée, _Image_.
-   Le mot clé **convolve** indique que les calques nommés _Conv1_ et _Conv2_ sont les couches à convolution. Chacune de ces déclarations de couche est suivi d’une liste d’attributs de la convolution.
-   Le filet une troisième masque du calque, _Hid3_, qui est entièrement connecté au second calque masqué, _Conv2_.
-   La couche de sortie, de _chiffres_, est connectée uniquement à la troisième couche masquée, _Hid3_. Le mot clé **tous** indique que la couche de sortie est entièrement connectée à _Hid3_.
-   L’arité de la convolution est trois (la longueur de tuples, **InputShape**, **KernelShape**, **Stride**et **partage**). 
-   Le nombre de poids par noyau est _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. Ou 26 * 50 = 1300_.
-   Vous pouvez calculer les nœuds de chaque calque masqué comme suit :
    -   **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   Le nombre total de nœuds peut être calculé à l’aide de la dimensionnalité déclarée du calque, [50, 5, 5], comme suit : _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Dans la mesure où **partage**[d] a la valeur False uniquement pour _d == 0_, le nombre de noyaux est _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Accusés de réception

La langue pour la personnalisation de l’architecture des réseaux neuronaux Net # a été développée à Microsoft par Shon Katzenberger (architecte, apprentissage automatique) et Alexey Kamenev (ingénieur logiciel, Microsoft Research). Elle est utilisée en interne pour machine de formation projets et applications allant de la détection d’images à analytique du texte. Pour plus d’informations, reportez-vous à la section [Neuronal filets dans Azure ML - Introduction à Net #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
