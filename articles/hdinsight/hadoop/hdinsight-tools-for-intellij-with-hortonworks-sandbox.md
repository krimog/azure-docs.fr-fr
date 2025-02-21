---
title: Utiliser Azure Toolkit for IntelliJ avec Hortonworks Sandbox
description: Découvrez comment utiliser HDInsight Tools dans le Kit de ressources Azure pour IntelliJ avec Hortonworks Sandbox.
keywords: outils Hadoop, requête hive, intellij, hortonworks sandbox, kit de ressources azure pour intellij
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 783d7fc8bc26ce2c715c774e63ecf60c5b75a439
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076254"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox

Découvrez comment utiliser HDInsight Tools pour IntelliJ pour développer des applications Apache Scala et tester les applications sur [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) en cours d’exécution sur votre ordinateur. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) est un environnement de développement Java intégré (IDE) pour développer des logiciels. Après avoir développé et testé vos applications sur Hortonworks Sandbox, vous pouvez les déplacer vers [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Hortonworks Data Platform (HDP) 2.4 on Hortonworks Sandbox s’exécutant sur votre ordinateur local. Pour configurer HDP, consultez [Bien démarrer dans l’écosystème Apache Hadoop avec un bac à sable (sandbox) Hadoop sur une machine virtuelle](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > HDInsight Tools pour IntelliJ a seulement été testé avec HDP 2.4. Pour obtenir HDP 2.4, développez **Hortonworks Sandbox Archive** sur le [site de téléchargement Hortonworks Sandbox](https://hortonworks.com/downloads/#sandbox).

- [Kit de développeur Java (JDK) version 1.8 ou ultérieure](https://aka.ms/azure-jdks). Le Kit de ressources Azure pour IntelliJ nécessite le JDK.

- [Édition communautaire d’IntelliJ IDEA](https://www.jetbrains.com/idea/download) avec les plug-ins [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) et [Kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij). HDInsight Tools pour IntelliJ est disponible dans le cadre du kit de ressources Azure pour IntelliJ. 

Pour installer les plug-ins :

  1. Ouvrez IntelliJ IDEA.
  2. Sur la page d’**accueil**, sélectionnez **Configurer**, puis **Plug-ins**.
  3. Dans le coin inférieur gauche, sélectionnez **Installer le plug-in JetBrains**.
  4. Utilisez la fonction de recherche pour rechercher **Scala**, puis sélectionnez **Installer**.
  5. Pour terminer l’installation, sélectionnez **Redémarrer IntelliJ IDEA**.
  6. Répétez les étapes 4 et 5 pour installer le **kit de ressources Azure pour IntelliJ**. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Créer une application Apache Spark Scala

Dans cette section, vous créez un exemple de projet Scala à l’aide d’IntelliJ IDEA. Dans la section suivante, vous lizr IntelliJ IDEA à Hortonworks Sandbox (émulateur) avant d’envoyer le projet.

1. Ouvrez IntelliJ IDEA sur votre ordinateur. Dans la boîte de dialogue **New Project** (Nouveau projet), suivez ces étapes :

   1. Sélectionnez **HDInsight** > **Spark sur HDInsight (Scala)** .
   2. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

      * **Maven** : pour la prise en charge de l’assistant de création de projets Scala.
      * **SBT**: pour gérer les dépendances et la génération du projet Scala

   ![IntelliJ - Créer un projet Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Sélectionnez **Suivant**.
3. Dans la boîte de dialogue suivante **New Project** (Nouveau projet), effectuez ces étapes :

   1. Dans la zone **Nom du projet**, entrez un nom de projet.
   2. Dans la zone **Emplacement du projet**, entrez un emplacement de projet.
   3. En regard de la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Nouveau**, **JDK**, puis spécifiez le dossier Java JDK version 1.7 ou ultérieure. Sélectionnez **Java 1.8** pour le cluster Spark 2.x. Sélectionnez **Java 1.7** pour le cluster Spark 1.x. L’emplacement par défaut est C:\Program Files\Java\jdk1.8.x_xxx.
   4. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le SDK Spark et le SDK Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x**. La version utilisée dans cet exemple est Spark 1.6.2 (Scala 2.10.5). Assurez-vous que vous utilisez le référentiel marqué **Scala 2.10.x**. N’utilisez pas le référentiel marqué Scala 2.11.x.
    
      ![Créer les propriétés du projet IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Sélectionnez **Terminer**.
5. Si la vue **Projet** n’est pas déjà ouverte, appuyez sur **Alt+1** pour l’ouvrir.
6. Dans l’**Explorateur de projets**, développez le projet, puis sélectionnez **src**.
7. Cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis sélectionnez **Scala class** (Classe Scala).
8. Dans la zone **Nom** , entrez un nom. Dans la zone **Type**, sélectionnez **Objet**. Ensuite, sélectionnez **OK**.

    ![Boîte de dialogue Créer une classe Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Dans le fichier.scala, collez le code suivant :

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Dans le menu **Générer**, sélectionnez **Générer un projet**. Vérifiez que la compilation s’est correctement effectuée.


## <a name="link-to-the-hortonworks-sandbox"></a>Lien vers Hortonworks Sandbox

Vous devez disposer d’une application IntelliJ existante avant de pouvoir établir un lien vers un Hortonworks Sandbox (émulateur).

Pour établir un lien vers un émulateur :

1. Ouvrez le projet dans IntelliJ.
2. Dans le menu **Affichage**, sélectionnez **Fenêtres d’outil**, puis **Azure Explorer**.
3. Développez **Azure**, cliquez avec le bouton droit sur **HDInsight**, puis sélectionnez **Lier un émulateur**.
4. Dans la fenêtre **Link A New Emulator** (Lier un nouvel émulateur), saisissez le mot de passe que vous avez configuré pour le compte racine du Hortonworks Sandbox, puis entrez des valeurs semblables à celles de la capture d’écran ci-dessous. Ensuite, sélectionnez **OK**. 

   ![Boîte de dialogue Link a New Emulator (Lier un nouvel émulateur)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Sélectionnez **Oui** pour configurer l’émulateur.

Lorsqu’il est connecté avec succès, l’émulateur (Hortonworks Sandbox) est répertorié sous le nœud HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Envoyer l’application Spark Scala au Hortonworks Sandbox

Après avoir lié IntelliJ IDEA à l’émulateur, vous pouvez envoyer votre projet.

Pour envoyer un projet à un émulateur :

1. Dans l’**Explorateur de projets**, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).
2. Effectuez ensuite les tâches suivantes :

    1. Dans la liste déroulante **Cluster Spark (Linux uniquement)** , sélectionnez votre Hortonworks Sandbox local.
    2. Dans la zone **Nom de la classe principale**, sélectionnez ou entrez le nom de la classe principale. Dans le cas de cet article, il s’agit de **GroupByTest**.

3. Sélectionnez **Envoyer**. Les journaux d’activité d’envoi de travaux sont affichés dans la fenêtre d’outil d’envoi Spark.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Utiliser HDInsight Tools dans Azure Toolkit for IntelliJ afin de créer des applications Apache Spark pour un cluster Linux HDInsight Spark](../spark/apache-spark-intellij-tool-plugin.md).

- Pour en savoir plus sur HDInsight Tools for IntelliJ, regardez la vidéo de [présentation de HDInsight Tools for IntelliJ pour le développement d’Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Découvrez comment [déboguer des applications Apache Spark à distance sur un cluster HDInsight avec Azure Toolkit for IntelliJ via SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Découvrez comment [utiliser HDInsight Tools dans Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance sur un cluster Linux HDInsight Spark](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Découvrez comment [utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Pour en savoir plus sur HDInsight Tools pour Eclipse, regardez la vidéo [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Utiliser HDInsight Tools pour Eclipse afin de créer des applications Spark).
