<properties
    pageTitle="Unité Roll un didacticiel de balle"
    description="Étapes pour créer l’unité classique restaurer un jeu de balle qui est requis pour tous les didacticiels de l’unité Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Créer une unité restaurer un jeu de balle

Ce didacticiel décrit les étapes principales pour [rouleau d’unité un didacticiel de ROTULE](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)légèrement modifié. Cette partie de l’exemple se compose d’un objet sphérique 'lecteur' qui est contrôlé par l’utilisateur de l’application et le but du jeu est à « collecter » des objets pouvant être collectés par collision l’objet lecteur avec ces objets, pouvant être collectés. Cela suppose une connaissance avec l’environnement de l’éditeur de l’unité de base. Si vous rencontrez un problème puis reportez-vous au didacticiel complet. 

### <a name="setting-up-the-game"></a>Paramétrage du jeu
Les étapes ci-dessous sont dans le [didacticiel de l’unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Ouvrez **l’Éditeur de l’unité** et cliquez sur **Nouveau**. 
    
    ![][51] 
    
2. Fournissez un **nom de projet** & **emplacement**, sélectionnez **3D** , puis cliquez sur **créer un projet**.
    
    ![][52]

3. Enregistrer la scène par défaut créée uniquement dans le cadre du nouveau projet comme avec le nom **MiniGame** dans un nouvel ** \_scènes** dossier sous dossier de **ressources** :
    
    ![][53]

4. Créer un **objet 3D -> plan** comme le champ de jeu et de renommer cet objet de plan au **sol**

    ![][1]

5. Réinitialiser le composant de transformation de cet objet **au sol** afin qu’il soit à l’origine. 

    ![][3]

6. Décochez la case **Afficher la grille** à partir du **menu de trucs** pour l’objet **au sol** .

    ![][4]

7. Le composant de **l’échelle** de l’objet **au sol** de mettre à jour [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Ajouter un nouvel **objet 3D -> sphère** au projet et renommer cet objet sphère en tant que **lecteur**. 

    ![][6]

9. Sélectionnez l’objet de **lecteur** , cliquez sur **Rétablir la transformation** similaire à l’objet de plan. 

10. Mise à jour **Transformation -> Position -> coordonnée Y** composant de lecteur Y par 0,5.  

    ![][7]

11. Créer un nouveau dossier appelé **documents** dans le projet où vous allez créer la matière à la couleur du lecteur. 

12. Créer un nouveau **matériau** appelée **arrière-plan** dans ce dossier. 

    ![][8]

13. Mettre à jour la couleur de la matière par la mise à jour de la propriété **Albedo** de celui-ci. Vous pouvez sélectionner les valeurs RVB de [0,32,64]. 

    ![][9]

14. Faites glisser ce matériau dans la vue de la scène pour appliquer une couleur à l’objet **au sol** . 

    ![][10]

17. Enfin mettre à jour le **Transformation -> Rotation -> Y** à 60 sur l’objet de lumière directionnelle pour plus de clarté. 

    ![][12]

### <a name="moving-the-player"></a>Déplacement du lecteur
Les étapes ci-dessous sont dans le [didacticiel de l’unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Ajouter un composant de **RigidBody** à l’objet de **lecteur** . 

    ![][13]

2. Créer un nouveau dossier appelé **Scripts** dans le projet. 

3. Cliquez sur **Ajouter composant -> Nouveau Script -> Script C#**. Le nom **PlayerController**et cliquez sur **créer et ajouter**. Cela créer et associer un script à l’objet de lecteur.  

    ![][14]

5. Déplacer ce script dans le dossier **Scripts** dans le projet. 

6. Ouvrez le script de modification dans l’éditeur de script favori, mettre à jour le code de script avec le code suivant et enregistrez-le. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Notez que le script ci-dessus utilise une propriété de **vitesse** . Dans l’éditeur de l’unité, vous devez mettre à jour la propriété de la vitesse à 10.  

    ![][15]

9. Cliquez sur **lire** dans l’éditeur de l’unité. Maintenant vous devriez pouvoir contrôler la balle à l’aide du clavier et il doit les faire pivoter et déplacer. 

### <a name="moving-the-camera"></a>Déplacement de la caméra
Les étapes ci-dessous sont dans le [didacticiel de l’unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) et attachera à la **Caméra de la Main** à l’objet de **lecteur** . 

1. Mise à jour de la **Transform.Position** pour x = 0, Y = 10.5, Z =-10.  
2. Mise à jour de la **Transform.Rotation** pour x = 45, Y = 0, Z = 0.  

    ![][16]

2. Ajouter un script appelé **CameraController** à la **MainCamera** et la déplacer dans le dossier Scripts. 

    ![][17]

3. Ouvrez le script de modification et ajoutez le code suivant dans celui-ci :

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. Dans l’environnement de l’unité - faites glisser la variable de lecteur dans la fente de lecteur de l’objet principal Camera afin que les deux soient associés à un autre. 

    ![][18]

6. Désormais si vous appuyez sur Play dans l’éditeur de l’unité et faire pivoter l’objet lecteur puis vous verrez la caméra suit le mouvement.  

### <a name="setting-up-the-play-area"></a>Configuration de la zone de lecture
Les étapes ci-dessous sont dans le [didacticiel de l’unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Nous allons créer des panneaux entourant le sol afin que l’objet lecteur ne déplacer hors de la zone de lecture de son mouvement. 

1. Cliquez sur **Créer -> créer un élément vide -> jeu objet** et nommez-le **murs**

    ![][19]

2. Sous cet objet Walls - créer un nouveau **Cube -> l’objet 3D** et nommez-le « Murs ouest ». 

    ![][20]

3. Mettre à jour la **Transformation -> Position** et **Transformation -> échelle** de cet objet de la paroi de l’ouest. 

    ![][21]

4. Dupliquer la paroi ouest pour créer une **paroi d’Extrême-Orient** avec la position de la transformation de mise à jour et l’échelle. 

    ![][22]

5. Dupliquer la paroi orientale pour créer une **paroi du Nord** avec la position de la transformation de mise à jour et l’échelle. 

    ![][23]

6. Dupliquer la paroi du Nord et créez une **paroi du Sud** avec la position de la transformation de mise à jour et l’échelle. 

    ![][24]

### <a name="creating-collectible-objects"></a>Création d’objets pouvant être collectés
Les étapes ci-dessous sont dans le [didacticiel de l’unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Nous allons créer certains objets aspect attrayants qui forment l’ensemble des objets pouvant être collectés qui a besoin de l’objet lecteur à collecter en collision avec eux. 

1. Créer un nouvel **objet de Cube 3D** et collecte le nom. 

2. Ajuster la **Transformation -> Rotation** & **Transformation -> échelle** de l’objet de remise en mains propres. 

    ![][25]

3. Créer et joindre un **Nouveau Script C#** appelé **rotation** à l’objet de remise en mains propres. Assurez-vous de placer le script dans le dossier Scripts. 

    ![][26]

4. Ouvrir ce script pour modifier et mettre à jour pour être le suivant : 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. Le mode de jeu dans l’éditeur de l’unité et votre diaporama objet capture atteint désormais être rotation sur son axe.

6. Créer un nouveau dossier appelé **Prefabs** 

    ![][27]

7. Faites glisser l’objet de la **collecte** et le placer dans le dossier Prefabs.

    ![][28]

8. Créer un nouvel **objet de jeu vide** appelée **collectes**. Rétablir sa position d’origine et puis faites glisser l’objet collecte sous l’objet de ce jeu.  

    ![][29]

9. Dupliquer l’objet de la **remise en mains propres** et la répartir sur l’objet de **sol** autour de l’objet **lecteur** en mettant à jour les valeurs **X et Z de Transform.Position** manière appropriée. 

    ![][30]

10. Créer un **nouveau matériau** appelé **collecte** et mettre à jour pour être rouge dans la couleur par la mise à jour de la **propriété du Albedo** similaire à ce que nous avons fait de mise à jour de l’objet au sol. 

    ![][31]

11. Appliquer le matériau à tous les objets de prise 4.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>Collecter les objets de collecte
Les étapes ci-dessous sont dans le [didacticiel de l’unité](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Nous va mettre à jour le lecteur afin qu’il soit en mesure de « collecter » les objets collecte en collision avec les. 

1. Ouvrez le script **PlayerController** associée à l’objet de lecteur pour le modifier et le mettre à jour à la suivante :  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Créer une nouvelle **balise** appelée **Pick Up** (elle doit correspondre à ce qui est dans le script)  

    ![][33]
    
    ![][34]

3. Appliquer cette **balise** à l’objet de la remise en mains propres Prefab. 

    ![][35]

4. Activer la case à cocher de **IsTrigger** pour l’objet PREFABRIQUE polyvalent.

    ![][36]

5. Ajoutez un corps rigide à l’objet Prefab de remise en mains propres. Pour optimiser les performances nous mettent à jour le collider statique que nous avons utilisé un collider dynamique. 

    ![][37]
  
6. Enfin, vérifiez la propriété **IsKinematic** de l’objet prefab. 

    ![][38]

7. D’accès **lire** dans l’éditeur de l’unité et vous pourrez jouer à ce jeu de **reporter une balle** en déplaçant l’objet lecteur à l’aide des touches de votre clavier pour l’entrée de sens. 

### <a name="updating-the-game-for-mobile-play"></a>Mise à jour de la partie pour le jeu mobile
Les sections précédentes conclu le didacticiel de base à partir de l’unité. Maintenant, nous allons modifier le jeu pour le rendre portable convivial. Notez que nous avons utilisé l’entrée au clavier pour le jeu jusqu'à présent pour le test. Maintenant nous allons modifier afin que nous pouvons contrôler le lecteur en utilisant le mouvement du téléphone c'est-à-dire à l’aide d’accéléromètre comme entrée. 

Ouvrez le script **PlayerController** pour l’édition et mise à jour de la méthode **FixedUpdate** pour le mouvement de l’accéléromètre permet de déplacer l’objet lecteur. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Ce didacticiel conclut la création d’un jeu base avec Unity et vous pouvez le déployer sur un périphérique de votre choix pour le jeu. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
