---
title: 'チュートリアル: IntelliJ を使用した HDInsight での Spark の Scala Maven アプリケーションの作成 | Microsoft Docs'
description: Scala で記述され、ビルド システムとして Apache Maven を使用し、IntelliJ IDEA によって提供される Scala 用の既存の Maven アーキタイプを使用する Spark アプリケーションを作成します。
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: c72f513c7134c556afa5fa5d0b94c17b1142be54
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>チュートリアル: IntelliJ を使用した HDInsight での Spark の Scala Maven アプリケーションの作成

このチュートリアルでは、Maven と IntelliJ IDEA を利用し、Scala で記述された Spark アプリケーションを作成する方法について説明します。 この記事では、ビルド システムとして Apache Maven を使用し、IntelliJ IDEA で提供されている Scala 用の既存の Maven アーキタイプから始めます。  IntelliJ IDEA での Scala アプリケーションには次の手順があります。

* ビルド システムとして Maven を使用します。
* プロジェクト オブジェクト モデル (POM) ファイルを更新して、Spark モジュールの依存関係を解決します。
* Scala でアプリケーションを作成します。
* HDInsight Spark クラスターに送信できる jar ファイルを生成します。
* Livy を使用して Spark クラスターでアプリケーションを実行します。

> [!NOTE]
> HDInsight には、アプリケーションを作成して Linux の HDInsight Spark クラスターに送信するプロセスを容易にする IntelliJ IDEA プラグイン ツールも用意されています。 詳細については、 [IntelliJ IDEA 用の HDInsight Tools プラグインを使用した Spark Scala アプリケーションの作成と送信](apache-spark-intellij-tool-plugin.md)に関するページをご覧ください。
> 

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * IntelliJ を使用して Scala Maven アプリケーションを開発する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。


## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
* Oracle Java Development kit。 [ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
* Java IDE。 この記事では、IntelliJ IDEA 18.1.1 を使用します。 [ここ](https://www.jetbrains.com/idea/download/)からインストールできます。

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA 用の Scala プラグインをインストールする
Scala プラグインをインストールするには、次の手順を使用します。

1. IntelliJ IDEA を開きます。
2. ようこそ画面で、**[Configure] (構成)** を選択し、**[Plugins] (プラグイン)** を選択します。
   
    ![Scala プラグインの有効化](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
3. 左下隅にある **[Install JetBrains plugin] (JetBrains プラグインのインストール)** を選択します。 
4. **[Browse JetBrains Plugins] (JetBrains プラグインの参照)** ダイアログ ボックスで、**Scala** を検索して、**[Install] (インストール)** を選択します。
   
    ![Scala プラグインのインストール](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
5. プラグインが正常にインストールされたら、IDE を再起動する必要があります。

## <a name="create-a-standalone-scala-project"></a>スタンドアロンの Scala プロジェクトを作成する
1. IntelliJ IDEA を開きます。
2. **[File] (ファイル)** メニューの **[New] (新規) > [Project] (プロジェクト)** を選択して新しいプロジェクトを作成します。
3. 新しいプロジェクト ダイアログ ボックスで、次の選択を行います。
   
    ![Maven プロジェクトの作成](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * プロジェクトの種類として、 **[Maven]** を選択します。
   * **[Project SDK (プロジェクトのSDK)]** を指定します。 **New (新規)** をクリックし、Java のインストール ディレクトリに移動します。通常は、`C:\Program Files\Java\jdk1.8.0_66` です。
   * **[Create from archetype (アーキタイプからの作成)]** オプションを選択します。
   * アーキタイプの一覧から、 **org.scala-tools.archetypes:scala-archetype-simple**を選択します。 このアーキタイプによって、正しいディレクトリ構造が作成され、Scala プログラムを作成するのに必要な既定の依存関係がダウンロードされます。
4. **[次へ]** を選択します。
5. **[GroupId]**、**[ArtifactId]**、および **[Version]** に関連する値を指定します。 このチュートリアルでは、次の値を使用しています。

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
6. **[次へ]** を選択します。
7. 設定を確認してから、**[Next] (次へ)** を選択します。
8. プロジェクト名と場所を確認し、**[Finish] (完了)** をクリックします。
9. 左ウィンドウで **[src] > [test] > [scala] > [com] > [microsoft] > [spark] > [example]** を選択し、**[MySpec]** を右クリックして、**[Delete] (削除)** を選択します。 このファイルはアプリケーションに必要ありません。
  
10. 以降の手順では、pom.xml を更新して、Spark Scala アプリケーションの依存関係を定義します。 これらの依存関係が自動的にダウンロードされ解決されるように、適宜、Maven を構成する必要があります。
   
    ![自動ダウンロードのための Maven の構成](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. **[File] (ファイル)** メニューの **[Settings] (設定)** を選択します。
   2. **[Settings (設定)]** ダイアログ ボックスで、**[Build, Execution, Deployment (ビルド、実行、デプロイ)]** > **[Build Tools (構築ツール)]** > **[Maven]** > **[Importing (インポート)]** の順に移動します。
   3. **[Import Maven projects automaticallyMaven (Maven プロジェクトを自動的にインポートする)]** オプションを選択します。
   4. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。
11. 左ウィンドウで **[src] > [main] > [scala] > [com] > [com.microsoft.spark.example]** を選択し、**[App]** をダブルクリックして、App.scala を開きます。

12. 既存のサンプル コードを次のコードに置き換え、変更を保存します。 このコードでは、HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、6 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
13. 左ウィンドウで **pom.xml** をダブルクリックします。
   
   1. `<project>\<properties>` に、以下のセグメントを追加します。
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. `<project>\<dependencies>` に、以下のセグメントを追加します。
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Pom.xml に変更内容を保存します。
10. jar ファイルを作成します。 IntelliJ IDEA では、JAR をプロジェクトのアーティファクトとして作成できます。 次の手順に従います。
    
    1. **[File]\(ファイル\)** メニューの **[Project Structure]\(プロジェクトの構造\)** を選択します。
    2. **[Project Structure] (プロジェクトの構造)** ダイアログ ボックスで、**[Artifacts] (アーティファクト)** を選択し、プラス記号を選択します。 ポップアップ ダイアログ ボックスで、**[JAR]** をクリックし、**[From modules with dependencie] (依存関係を持つモジュールから)** を選択します。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. **[Create JAR from Modules] (モジュールから JAR を作成)** ダイアログ ボックスで、**[Main Class] (メイン クラス)** の省略記号 (![省略記号](./media/apache-spark-create-standalone-application/ellipsis.png)) を選択します。
    4. **[Select Main Class] (メインクラスの選択)** ダイアログ ボックスで、既定で表示されるクラスを選択し、**[OK]** を選択します。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. **[Create JAR from Modules] (モジュールから JAR を作成)** ダイアログ ボックスで、**[extract to the target JAR] (ターゲット JAR に抽出する)** オプションが選択されていることを確認し、**[OK]** をクリックします。  これにより、すべての依存関係を持つ 1 つの JAR が作成されます。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. [Output Layout (出力レイアウト)] タブに、Maven プロジェクトの一部として取り込まれたすべての jar が一覧表示されます。 Scala アプリケーションと直接的な依存関係がないものについては、選択し削除できます。 ここで作成するアプリケーションの場合は、最後の 1 つ (**SparkSimpleApp compile output**) を除き、あとはすべて削除することができます。 削除する jar を選択し、 **[Delete] (削除)** アイコンをクリックします。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        **[Include in project build] (プロジェクト ビルドに含める)** ボックスが選択されていることを確認します。それにより、プロジェクトがビルドまたは更新されるたびに jar が確実に作成されます。 **[Apply] (適用)**、**[OK]** の順に選択します。
    7. **[Build] (ビルド)** メニューの **[Build Artifacts] (ビルド アーティファクト)** を選択して jar を作成します。 出力 jar が **\out\artifacts** の下に作成されます。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Spark クラスターでアプリケーションを実行する
クラスターでアプリケーションを実行するには、次のアプローチを使用できます。

* **Azure Storage BLOB にアプリケーション jar をコピーします** 。 コピーには、[**AzCopy**](../../storage/common/storage-use-azcopy.md) コマンドライン ユーティリティを使用できます。 他にも、データのアップロードに使用できるクライアントが多数あります。 詳細については、「 [HDInsight での Hadoop ジョブ用データのアップロード](../hdinsight-upload-data.md)」を参照してください。
* **アプリケーション ジョブをLivy を使用してリモートで送信** します。 HDInsight の Spark クラスターには、Spark ジョブをリモートで送信するための REST エンドポイントを公開する Livy が含まれています。 詳細については、「[Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (HDInsight の Spark クラスターで Livy を使用して Spark ジョブをリモートで送信する)](apache-spark-livy-rest-interface.md)」を参照してください。

## <a name="next-step"></a>次のステップ

この記事では、Spark Scala アプリケーションの作成方法について説明しました。 Livy を使用して HDInsight Spark クラスター上でこのアプリケーションを実行する方法を確認するには、次の記事に進みます。

> [!div class="nextstepaction"]
>[Livy を使用して Spark クラスターでジョブをリモートで実行する](./apache-spark-livy-rest-interface.md)

