<properties 
    pageTitle="Exécuter des scripts de formation Python machine | Microsoft Azure" 
    description="Sous-jacent de prise en charge des scripts Python dans Azure Machine Learning et les scénarios d’utilisation de base, les capacités et les limitations de principes de conception de plans." 
    keywords="machine de Python de formation, pandas, pandas de python, scripts de python, exécuter des scripts de python"
    services="machine-learning"
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Exécuter des scripts de formation de machine de Python dans le Studio d’apprentissage Machine Azure

Cette rubrique décrit les principes de la conception sous-jacente de la prise en charge actuelle des scripts Python dans l’apprentissage automatique de Azure. Les fonctions principales sont également présentées, y compris la prise en charge pour l’importation de code existant, exportation de visualisations et, enfin, certaines des limitations et des travaux en cours sont traitées.

[Python](https://www.python.org/) est un outil indispensable dans la poitrine d’outil de nombreux scientifiques de données. Il comprend :

-  une syntaxe concise et élégante, 
-  prise en charge de plusieurs plates-formes, 
-  un vaste ensemble de bibliothèques puissants, et 
-  outils de développement pour adultes. 

Python est utilisé dans toutes les phases du flux de travail en général utilisé dans la modélisation d’apprentissage machine, à partir de données d’acquisition et de traitement, à la construction de la fonctionnalité et formation de modèle, puis validation et déploiement des modèles. 

Studio de formation de Machine Azure prend en charge les scripts de Python incorporation dans différentes parties d’une machine de formation d’expérience et également de façon transparente en le publiant en tant que services web évolutifs et operationalized sur Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Principes de conception des scripts de Python dans l’apprentissage automatique
L’interface principale pour les Python dans Azure Machine Learning Studio est via l' [Exécution de Script de Python] [ execute-python-script] module illustré dans la Figure 1.

![Image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

La figure 1. Le module **d’Exécution de Script de Python** .

[Exécuter le Script Python] [ execute-python-script] module accepte jusqu'à trois entrées et produit jusqu'à deux sorties (voir ci-dessous), tout comme son analogique R, [Exécuter un Script R] [ execute-r-script] module. L’exécution de code Python est entré dans la zone de paramètre comme spéciale nommée fonction appelée point d’entrée `azureml_main`. Vous trouverez ici les principes de conception clé utilisés pour implémenter ce module :

1.  *Doit être idiomatiques pour les utilisateurs les Python.* La plupart des utilisateurs de Python facteur leur code en tant que fonctions à l’intérieur de modules, afin de placer un lot d’instructions exécutables dans un module de niveau supérieur est relativement rare. En conséquence, la zone de script a également une fonction spéciale nommée Python juste une séquence d’instructions. Les objets exposés dans la fonction sont des types de bibliothèque Python standard des trames de données [Pandas](http://pandas.pydata.org/) baies de [NumPy](http://www.numpy.org/) .
2.  *Doit avoir entre local haute fidélité et exécutions en nuage.* Le serveur principal utilisé pour exécuter le code Python est basé sur les [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, une distribution de Python scientifique multiplateforme largement utilisée. Il est fourni avec près de 200 des plus courants colis Python. Par conséquent, les scientifiques de données peuvent déboguer et qualifier leur code sur leur environnement d’Anaconda de formation compatibles Azure Machine locale. Puis utilisez les environnements de développement existant comme [IPython](http://ipython.org/) portable ou les [Python des outils de Visual Studio](http://aka.ms/ptvs) pour s’exécuter dans le cadre d’une expérience d’apprentissage automatique de Azure avec un niveau de confiance élevé. En outre, les `azureml_main` point d’entrée est une fonction de Python vanille et peut être créé sans code spécifique d’apprentissage automatique de Azure ou le Kit de développement logiciel installé.
3.  *Doit être modulable en toute transparence avec d’autres modules d’apprentissage automatique de Azure.* [Exécuter le Script Python] [ execute-python-script] module accepte, sous la forme d’entrées et de sorties, des groupes de données Azure Machine formation standard. L’infrastructure sous-jacente efficacement et en toute transparence établit une passerelle entre les exécutions d’apprentissage automatique de Azure et les Python (prise en charge des fonctionnalités telles que des valeurs manquantes). Python peut par conséquent être utilisé en conjonction avec des workflows existants de formation de Machine d’Azure, y compris ceux qui font appel R et SQLite. Un peut donc envisager des workflows qui :
  * Utilisez les Python et Pandas pour les données de prétraitement et d’élimination, 
  * flux de données à une transformation de SQL, jointure de plusieurs ensembles de données aux fonctionnalités de l’écran, 
  * apprentissage des modèles à l’aide de la collection complète d’algorithmes dans la formation de Machine d’Azure, et 
  * évaluer et post-traitement sur les résultats à l’aide de R.


## <a name="basic-usage-scenarios-in-machine-learning-for-python-scripts"></a>Scénarios d’utilisation de base dans l’apprentissage automatique pour les scripts de Python
Dans cette section, nous avons une enquête parmi les utilisations courantes de l' [Exécution de Script de Python] [ execute-python-script] module.
Comme mentionné précédemment, les entrées dans le module Python sont exposées sous forme de trames de données Pandas. Plus d’informations sur les Python Pandas et comment elle peut être utilisée pour manipuler les données efficacement et sont accessibles dans les *Python pour analyse de données* (o ' Reilly, 2012) par McKinney ouest. La fonction doit renvoyer une seule trame de données Pandas emballée à l’intérieur d’une [séquence](https://docs.python.org/2/c-api/sequence.html) de Python par exemple un tuple, une liste ou un tableau de NumPy. Le premier élément de cette séquence est ensuite retourné dans le premier port de sortie du module. Ce schéma est illustré à la Figure 2.

![sa stratégie partenaires3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

La figure 2. Mise en correspondance des voies pour les paramètres d’entrée et la valeur de retour au port de sortie.

Plus la sémantique de la façon dont les ports d’entrée sont mappées aux paramètres de le `azureml_main` fonction sont affichés dans le tableau 1 :

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Le tableau 1. Mise en correspondance des ports d’entrée pour les paramètres de la fonction.

Le mappage entre les ports d’entrée et les paramètres de la fonction est de position. Le premier port d’entrée connecté est mappé au premier paramètre de la fonction et la seconde entrée (si connecté) est mappée au deuxième paramètre de la fonction.

## <a name="translation-of-input-and-output-types"></a>Conversion de types d’entrée et de sortie
Comme expliqué plus haut, les jeux de données d’entrée dans l’apprentissage automatique de Azure sont converties en blocs dans Pandas et les blocs de données de sortie sont convertis vers Azure Machine formation de groupes de données. Les conversions suivantes sont exécutées :

1.  Les colonnes numériques et de chaîne sont convertis en tant que-est et les valeurs manquantes dans un groupe de données sont convertis en valeurs de « NA » dans les Pandas. La même conversion se produit dans l’autre sens (NA valeurs Pandas sont converties en valeurs manquantes dans l’apprentissage automatique de Azure).
2.  Azure Machine Learning ne prend pas en charge les vecteurs de index dans Pandas. Toutes les images de données d’entrée dans la fonction Python ont toujours un index numérique du 64 bits compris entre 0 et le nombre de lignes moins 1. 
3.  Apprentissage automatique des groupes de données Azure ne peut pas avoir de noms de colonne en double et les noms de colonne ne sont pas des chaînes. Si une trame de données de sortie contient les colonnes non numériques, l’infrastructure appelle `str` sur les noms de colonne. De même, les tous les noms de colonne en double sont déformés automatiquement afin que les noms sont uniques. Le suffixe (2) est ajouté pour le premier doublon, (3) pour la seconde etc. en double.

## <a name="operationalizing-python-scripts"></a>À mettre en application les scripts Python
Toute [Exécution de Script de Python] [ execute-python-script] modules utilisés dans une expérience de score sont appelées lors de la publication sous la forme d’un service web. Par exemple, la Figure 3 illustre une expérience de score qui contient le code pour évaluer une seule expression de Python. 

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

La figure 3. Service Web pour l’évaluation d’une expression de Python.

Un service web créé à partir de cette expérience prend comme entrée une expression Python (sous forme de chaîne), il envoie à l’interpréteur Python et renvoie une table contenant à la fois l’expression et le résultat calculé.

## <a name="importing-existing-python-script-modules"></a>Importation des modules de script Python
Un cas d’usage courant pour de nombreux scientifiques de données est d’incorporer des scripts Python existants dans les expériences d’apprentissage automatique de Azure. Au lieu de concaténation et Coller tout le code dans une zone de script unique, l' [Exécution de Script de Python] [ execute-python-script] module accepte un troisième port d’entrée à laquelle un fichier zip qui contient les Python de modules peut être connecté. Le fichier est décompressé puis par l’infrastructure d’exécution lors de l’exécution et le contenu est ajouté pour le chemin d’accès de la bibliothèque de l’interpréteur Python. Le `azureml_main` point d’entrée fonction puis importer ces modules directement.

Par exemple, considérez le fichier Hello.py contenant une simple fonction de « Hello, World ».

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

La figure 4. Fonction définie par l’utilisateur.

Ensuite, nous créons un fichier Hello.zip qui contient Hello.py :

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

La figure 5. Fichier ZIP contenant un code Python défini par l’utilisateur.

Ensuite, télécharger ce sous la forme d’un groupe de données dans un Studio de formation de Machine Azure. Créez et exécutez une expérience simple qui utilise le code Python dans le fichier Hello.zip en le reliant à la troisième port d’entrée du Script exécuter Python, comme illustré dans cette figure.

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

La figure 6. Exemple Expérimentez définis par l’utilisateur les Python code téléchargé sous la forme d’un fichier zip.

Le module de sortie montre que le fichier zip a été décompressée et la fonction `print_hello` a en effet été exécuté.
 
![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
La figure 7. Fonction définie par l’utilisateur en cours d’utilisation à l’intérieur de l' [Exécution de Script de Python] [ execute-python-script] module.

## <a name="working-with-visualizations"></a>Travail avec des visualisations
Les tracés créés à l’aide de MatplotLib qui peut être visualisé sur le navigateur peuvent être renvoyées par l' [Exécution de Script de Python][execute-python-script]. Mais les placettes ne sont pas redirigés automatiquement vers les images comme ils le sont lors de l’utilisation de R. Ainsi, l’utilisateur doit enregistrer explicitement les tracés vers des fichiers PNG s’ils doivent être renvoyés à formation de Machine Azure. 

Pour générer des images à partir de MatplotLib, vous devez entrer en concurrence la procédure suivante :

* utiliser le serveur principal « %{AGG/} » à partir de l’outil de rendu par défaut basée sur Qt 
* créer un nouvel objet figure 
* obtenir l’axe et générer toutes les placettes dedans 
* Enregistrer la figure dans un fichier PNG 

Ce processus est illustré à la Figure 8 ci-dessous qui crée une matrice de traçage en nuages de points à l’aide de la fonction scatter_matrix dans Pandas.
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

La figure 8. L’enregistrement MatplotLib chiffres aux images.



La figure 9 montre une expérience qui utilise le script illustré précédemment pour renvoyer trace via le second port de sortie.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png) 
     
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png) 

La figure 9. Visualisation des tracés générés à partir de code Python.

Il est possible de renvoyer plusieurs illustrations en les enregistrant dans différentes images, le runtime Azure Machine Learning sélectionne toutes les images et les concatène pour visualisation.


## <a name="advanced-examples"></a>Exemples avancés
L’environnement Anaconda installé dans Azure Machine d’apprentissage contient des packages courants tels que NumPy, SciPy et Scikits d’apprendre et il peuvent être utilisés efficacement pour diverses tâches de traitement de données dans un pipeline de formation de machine par défaut. Par exemple, les essais suivants et le script illustre l’utilisation de stagiaires ensemble dans Scikits-comment calculer les scores d’importance de fonctionnalité pour un groupe de données. Les scores puis peuvent servir à effectuer une sélection de fonctionnalité contrôlés avant leur alimentation dans un autre modèle d’apprentissage machine.

La fonction Python pour calculer les scores de l’importance et l’ordre les fonctionnalités selon qu’il est indiquée ci-dessous :

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

La figure 10. Fonction aux fonctions de rang par les scores.
 L’expérience suivante calcule et renvoie les résultats de l’importance des fonctionnalités dans le dataset « DIABÈTE indien de Pima » dans l’apprentissage automatique de Azure :

![image12](./media/machine-learning-execute-python-scripts/figure9a.png)
![image13](./media/machine-learning-execute-python-scripts/figure9b.png)    
    
La figure 11. Faites des essais aux fonctions de rang dans le dataset Pima indien diabète.

## <a name="limitations"></a>Limitations 
[Exécuter le Script Python] [ execute-python-script] actuellement présente les limitations suivantes :

1.  *Exécution de bac à sable.* Le runtime Python est actuellement mis en sandbox et, par conséquent, n’autorise pas l’accès au réseau ou au système de fichiers local de façon permanente. Tous les fichiers enregistrés localement sont isolés et supprimés une fois que le module se termine. Le code Python ne peut pas accéder à la plupart des répertoires sur l’ordinateur, qu’il s’exécute, l’exception est le répertoire courant et ses sous-répertoires.
2.  *Manque de développement sophistiquées et le débogage.* Actuellement, le module Python ne gère pas les fonctionnalités telles qu’intellisense et le débogage de l’IDE. En outre, si le module échoue au moment de l’exécution, la trace de pile complète Python est disponible, mais doit être affichée dans le journal de sortie pour le module. Actuellement, il est recommandé que vous développez et déboguez leurs scripts Python dans un environnement de IPython et ensuite importerez le code dans le module.
3.  *Sortie de cadre de données unique.* Le point d’entrée de Python n’est autorisé à retourner un bloc unique de données en tant que sortie. Il n’est pas actuellement possible de renvoyer des objets arbitraires de Python tels que les modèles formés directement à l’exécution de la formation de Machine Azure. Comme [l’Exécution de Script de R][execute-r-script], qui a la même limitation, il est toutefois possible dans de nombreux cas à marinade des objets dans un tableau d’octets et un retour puis qu’à l’intérieur d’un bloc de données.
4.  *Impossibilité de personnaliser l’installation de Python*. Actuellement, la seule manière d’ajouter des modules personnalisés de Python est via le mécanisme de fichier zip décrit précédemment. Alors que cela n’est possible pour les modules de petite taille, il est lourde pour les grands modules (notamment ceux avec des DLL natives) ou un grand nombre de modules. 


##<a name="conclusions"></a>Conclusions
[Exécuter le Script Python] [ execute-python-script] module permet un chercheur de données afin d’incorporer le code Python existant dans les workflows de machine d’hébergé sur le nuage d’apprentissage dans Azure Machine d’apprentissage et de façon transparente effectuent les dans le cadre d’un service web. Le module de script Python interagit naturellement à d’autres modules d’apprentissage d’ordinateur Azure et peut être utilisé pour une gamme de tâches de l’exploration de données pour le traitement préalable, pour l’extraction de fonctionnalité, d’évaluation et de traitement des résultats. Le back-end du runtime utilisé pour l’exécution est basé sur la Anaconda, et une distribution de Python bien testée et largement utilisée. Cela facilite pour vous à des ressources de code existantes intégrée dans le nuage.

Nous prévoyons de fournir des fonctionnalités supplémentaires à l' [Exécution de Script de Python] [ execute-python-script] module telles que la capacité à former et effectuent des modèles dans les Python et ajouter de mieux prendre en charge le développement et le débogage de code dans un Studio de formation de Machine Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez le [Centre de développement de Python](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
