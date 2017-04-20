<properties
   pageTitle="À l’aide d’émulateur Express pour exécuter et déboguer un service cloud sur un ordinateur local | Microsoft Azure"
   description="À l’aide d’émulateur Express pour exécuter et déboguer un service cloud sur un ordinateur local"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>À l’aide d’émulateur Express pour exécuter et déboguer un service cloud sur un ordinateur local

En utilisant l’émulateur Express, vous pouvez tester et déboguer un service en nuage sans exécuter Visual Studio en tant qu’administrateur. Vous pouvez définir vos paramètres de projet à utiliser l’émulateur Express ou l’émulateur complet, selon les besoins de votre service cloud. Pour plus d’informations sur l’émulateur complète, consultez [exécution d’une Application Azure dans l’émulateur de calcul](./storage/storage-use-emulator.md). Émulateur Express a été pour la première fois dans Azure SDK 2.1 et à 2.3 du SDK Azure, il est l’émulateur par défaut.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>À l’aide d’émulateur Express dans l’IDE de Visual Studio

Lorsque vous créez un nouveau projet dans Azure SDK 2.3 ou version ultérieure, émulateur Express est déjà sélectionné. Pour les projets existants qui ont été créés avec une version antérieure du Kit de développement, procédez comme suit pour sélectionner Express de l’émulateur.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Pour configurer un projet pour utiliser l’émulateur Express

1. Dans le menu contextuel pour le projet d’Azure, choisissez **Propriétés**et cliquez sur l’onglet **Web** .

1. Sous **Serveur de développement Local**, cliquez sur le bouton **d’option utiliser IIS Express** . Émulateur Express n’est pas compatible avec le serveur Web IIS.

1. Dans l' **émulateur**, cliquez sur la case d’option **Utiliser émulateur Express** .

    ![Émulateur Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Lancer l’émulateur Express à l’invite de commande

À l’invite de commande, vous pouvez lancer la version expresse de l’émulateur de calcul Azure, csrun.exe, à l’aide de l’option /useemulatorexpress.

## <a name="limitations"></a>Limitations

Avant d’utiliser Express de l’émulateur, vous devez être conscient de certaines limitations :

- Émulateur Express n’est pas compatible avec le serveur Web IIS.

- Votre service cloud peut contenir plusieurs rôles, mais chaque rôle est limité à une seule instance.

- Impossible d’accéder aux numéros de port inférieur à 1000. Par exemple, si vous utilisez un fournisseur d’authentification qui utilise normalement un port inférieur à 1000, vous devez modifier cette valeur à un numéro de port qui est supérieure à 1000.

- Toutes les limitations qui s’appliquent à l’émulateur de calcul Azure s’appliquent également à l’émulateur Express. Par exemple, ne peut pas avoir plus de 50 instances de rôle par le déploiement. Consultez [exécution d’une Application Azure dans l’émulateur de calcul](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Étapes suivantes

[Débogage des Services en nuage](https://msdn.microsoft.com/library/azure/ee405479.aspx)
