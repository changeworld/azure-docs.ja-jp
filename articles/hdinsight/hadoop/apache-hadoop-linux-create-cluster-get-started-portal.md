---
title: 'クイック スタート: Azure portal を使用して Azure HDInsight で Hadoop と Hive を使用する | Microsoft Docs'
description: Azure portal を使用して HDInsight クラスターを作成する方法および Hive を使用してデータを照会する方法について説明します。
keywords: Hadoop の概要, Hadoop Linux, Hadoop クイックスタート, Hive の概要, Hive クイックスタート
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: nitinme
ms.openlocfilehash: dd803fed4bc6a25904b2ea00a5b21af606ad7153
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097898"
---
# <a name="quickstart-get-started-with-hadoop-and-hive-in-azure-hdinsight-using-the-azure-portal"></a>クイック スタート: Azure portal を使用して Azure HDInsight で Hadoop と Hive を使用する

この記事では、Azure portal を使用して HDInsight で [Hadoop](http://hadoop.apache.org/) クラスターを作成し、HDInsight で Hive ジョブを実行する方法について説明します。 Hadoop ジョブのほとんどはバッチ ジョブです。 クラスターを作成し、いくつかのジョブを実行して、クラスターを削除します。 この記事では、3 つのすべてのタスクを実行します。

このクイック スタートでは、Azure portal を使用して HDInsight Hadoop クラスターを作成します。 また、[Azure Resource Manager テンプレート](apache-hadoop-linux-tutorial-get-started.md)を使用して、クラスターを作成することもできます。

現在、HDInsight には [7 種類のクラスター](./apache-hadoop-introduction.md#cluster-types-in-hdinsight)が用意されています。 クラスターの種類はそれぞれ異なるコンポーネント セットをサポートしていますが、 Hive は全種類のクラスターでサポートされています。 HDInsight でサポートされているコンポーネントの一覧については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](../hdinsight-component-versioning.md)」をご覧ください。  

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-hadoop-cluster"></a>Hadoop クラスターの作成

このセクションでは、Azure portal を使用して HDInsight で Hadoop クラスターを作成します。 

1. [Azure portal](https://portal.azure.com) にログインします。

1. Azure portal で、**[リソースの作成]** > **[データ + 分析]** > **[HDInsight]** の順に選択します。 

    ![Azure portal の Databricks](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-hdinsight-on-portal.png "Azure portal の Databricks")

2. **[HDInsight]** > **[簡易作成]** > **[基本]** で、次のスクリーンショットに示されているように値を指定します。

    ![HDInsight Linux の使用。クラスターの基本的な値の指定](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-provide-basic-values.png "HDInsight クラスターを作成するための基本的な値の指定")

    次の値を入力または選択します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**クラスター名**     | Hadoop クラスターの名前を入力します。 HDInsight のすべてのクラスターでは同じ DNS 名前空間が共有されるため、この名前は一意である必要があります。 この名前は、文字、数字、ハイフンを含む最大 59 文字で構成できます。 名前の先頭と末尾の文字をハイフンにすることはできません。 |
    |**サブスクリプション**     |  Azure サブスクリプションを選択します。 |
    |**クラスターの種類**     | ここでは、スキップします。 この手順説明の次の手順で入力します。|
    |**クラスター ログイン ユーザー名とパスワード**     | 既定のログイン名は **admin** です。パスワードは 10 文字以上で、数字、大文字、小文字、英数字以外の文字 (' " ` を除く\) が少なくとも 1 つずつ含まれる必要があります。 "Pass@word1" などのよく使われるパスワードを**指定していない**ことを確認してください。|
    |**SSH ユーザー名** | 既定のユーザー名は **sshuser** です。  SSH ユーザー名に別の名前を指定できます。 |
    | **クラスター ログインと同じパスワードを使用します** | クラスター ログイン ユーザーに指定したのと同じパスワードを SSH ユーザーに使用する場合は、このチェック ボックスをオンにします。|
    |**リソース グループ**     | リソース グループを作成するか、既存のリソース グループを選択します。  リソース グループとは、Azure コンポーネントのコンテナーです。  この場合、リソース グループには、HDInsight クラスターおよび依存する Azure ストレージ アカウントが含まれています。 |
    |**場所**     | クラスターを作成する Azure の場所を選択します。  パフォーマンスを向上させるため、お近くの場所を選択してください。 |
        
3. **クラスターの種類**を選択し、次のスクリーンショットに示されているように入力します。

    ![HDInsight Linux の使用。クラスターの基本的な値の指定](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-provide-cluster-type.png "HDInsight クラスターを作成するための基本的な値の指定")

    次の値を選択します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**クラスターの種類**     | **[Hadoop]** を選択します |
    |**オペレーティング システム**     |  Azure サブスクリプションを選択します。 |
    |**バージョン**     | **[Hadoop 2.7.3 (HDI 3.6)]** を選択します。|

    **[選択]** をクリックし、**[次へ]** をクリックします。

4. **[ストレージ]** タブで、次のスクリーンショットに示されているように入力します。

    ![HDInsight Linux の使用。クラスター ストレージの値の指定](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-select-storage.png "HDInsight クラスターを作成するためのストレージの値の指定")

    次の値を選択します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**プライマリ ストレージの種類**     | この記事では、**[Azure Storage]** を選択して、Azure Storage Blob を既定のストレージ アカウントとして使用します。 Azure Data Lake Storage を既定のストレージとして使用することもできます。 |
    |**メソッドの選択**     |  この記事では、**[個人用サブスクリプション]** を選択して、Azure サブスクリプションのストレージ アカウントを使用します。 他のサブスクリプションのストレージ アカウントを使用するには、**[アクセス キー]** を選択し、そのアカウントのアクセス キーを指定します。 |
    |**Create a new storage account \(新しいストレージ アカウントの作成\)**     | ストレージ アカウントの名前を入力します。|

    他の値はすべて既定値のままにし、**[次へ]** を選択します。

5. **[概要]** タブで、前の手順で選択した値を確認します。

    ![HDInsight Linux の使用。クラスターの概要](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-portal-summary.png "HDInsight Linux の使用。クラスターの概要")
      
4. **[作成]** を選択します。 ポータルのダッシュボードに、**[Submitting deployment for HDInsight]\(HDInsight にデプロイを送信しています\)** という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。

    ![HDInsight Linux の使用。リソース グループ](./media/apache-hadoop-linux-create-cluster-get-started-portal/deployment-progress-tile.png "Azure HDInsight クラスター リソース グループ")

4. クラスターが作成されると、Azure portal にクラスターの概要ページが表示されます。
   
    ![HDInsight Linux の使用。クラスター設定](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "HDInsight クラスターのプロパティ")    
    
    各クラスターには、[Azure Storage のアカウント](../hdinsight-hadoop-use-blob-storage.md)または [Azure Data Lake のアカウント](../hdinsight-hadoop-use-data-lake-store.md)との依存関係があります。 これを、既定のストレージ アカウントと呼びます。 HDInsight クラスターとその既定のストレージ アカウントは、同じ Azure リージョンに配置されている必要があります。 クラスターを削除しても、ストレージ アカウントは削除されません。

    > [!NOTE]
    > その他のクラスター作成方法と、このチュートリアルで使用するプロパティについて詳しくは、[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。       
    > 
    >

## <a name="run-hive-queries"></a>Hive クエリの実行

[Apache Hive](hdinsight-use-hive.md) は、HDInsight で使用される最も一般的なコンポーネントです。 HDInsight で Hive ジョブを実行する方法は多数存在します。 このチュートリアルでは、ポータルから Ambari Hive ビューを使用します。 Hive ジョブを送信する他の方法については、[HDInsight での Hive の使用](hdinsight-use-hive.md)に関するページをご覧ください。

1. Ambari を開くには、前のスクリーンショットから、**[クラスター ダッシュボード]** を選択します。  ブラウザーで **https://&lt;クラスター名>.azurehdinsight.net** に移動することもできます。&lt;クラスター名> は、前のセクションで作成したクラスターです。

    ![HDInsight Linux の使用。クラスター ダッシュボード](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux の使用。クラスター ダッシュボード")

2. クラスターの作成時に指定した Hadoop ユーザー名とパスワードを入力します。 既定のユーザー名は **admin**です。

3. 次のスクリーンショットのように **[Hive ビュー]** を開きます。
   
    ![Ambari ビューの選択](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "HDInsight Hive ビューアー メニュー")

4. **[QUERY]\(クエリ\)** タブで、次の HiveQL ステートメントをワークシートに貼り付けます。
   
        SHOW TABLES;

    ![HDInsight Hive ビュー](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "HDInsight Hive ビューのクエリ エディター")
   
   > [!NOTE]
   > Hive では、セミコロンが必要です。       
   > 
   > 

5. **[Execute]\(実行\)** を選択します。 **[QUERY]\(クエリ\)** タブの下に **[RESULTS]\(結果\)** タブが表示され、ジョブについての情報が表示されます。 
   
    クエリが完了すると、**[QUERY]\(クエリ\)** タブに操作の結果が表示されます。 **hivesampletable** という名前のテーブルが 1 つ表示されます。 このサンプルの Hive テーブルにはすべての HDInsight クラスターが付属します。
   
    ![HDInsight Hive ビュー](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "HDInsight Hive ビューのクエリ エディター")

6. 手順 4. と手順 5 を繰り返し、次のクエリを実行します。
   
        SELECT * FROM hivesampletable;
   
7. クエリの結果を保存することもできます。 右側のメニュー ボタンを選択し、結果を CSV ファイルとしてダウンロードするか、クラスターに関連付けられているストレージ アカウントに保存するかを指定します。

    ![Hive クエリの結果を保存](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Hive クエリの結果を保存")

Hive ジョブが完了したら、[結果を Azure SQL データベースまたは SQL Server データベースにエクスポート](apache-hadoop-use-sqoop-mac-linux.md)できます。[Excel を利用して結果を視覚化](apache-hadoop-connect-excel-power-query.md)することもできます。 HDInsight で Hive を使用する方法について詳しくは、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)」をご覧ください。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](../hdinsight-administer-use-portal-linux.md#create-clusters)」をご覧ください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターが使用されていないときは安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 

> [!NOTE]
> *すぐに*次のチュートリアルに進み、HDInsight で Hadoop を使用して ETL 操作を実行する方法を学習する場合は、クラスターを実行したままにしておいてかまいません。 そのチュートリアルでは Hadoop クラスターを再度作成する必要があるからです。 ただし、すぐに次のチュートリアルに進まない場合は、クラスターを今すぐ削除する必要があります。
> 
>  

**クラスターと既定のストレージ アカウントを削除するには**

1. ブラウザーの Azure portal を開いているタブに戻ります。 [クラスターの概要] ページが表示されているはずです。 クラスターのみを削除し、既定のストレージ アカウントを保持する場合は、**[削除]** を選択します。

    ![HDInsight におけるクラスターの削除](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight クラスターの削除")

2. クラスターと既定のストレージ アカウントを削除する場合は、(上のスクリーンショットで強調表示されている) リソース グループ名を選択して、リソース グループのページを開きます。

3. **[リソース グループの削除]** を選択して、クラスターと既定のストレージ アカウントが含まれたリソース グループを削除します。 リソース グループを削除するとストレージ アカウントも削除されます。 ストレージ アカウントを残しておく場合は、クラスターのみを削除してください。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Resource Manager テンプレートを利用して Linux ベースの HDInsight クラスターを作成する方法と基本的な Hive クエリを実行する方法について説明しました。 次の記事では、HDInsight で Hadoop を使用して抽出、変換、読み込み (ETL) 操作を実行する方法を学習します。

> [!div class="nextstepaction"]
>[HDInsight での Apache Hive を使用したデータの抽出、変換、読み込み](../hdinsight-analyze-flight-delay-data-linux.md)

実際のデータを使用する準備が整っていて、HDInsight のデータの格納方法や HDInsight にデータを取り込む方法を確認する場合は、以下の記事をご覧ください。

* HDInsight で Azure Storage を使用する方法について詳しくは、[HDInsight での Azure Storage の使用](../hdinsight-hadoop-use-blob-storage.md)に関するページをご覧ください。
* Data Lake Storage で HDInsight クラスターを作成する方法について詳しくは、「[クイック スタート: HDInsight のクラスターを設定する](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。
* データを HDInsight にアップロードする方法について詳しくは、[データを HDInsight にアップロードする方法](../hdinsight-upload-data.md)に関する記事をご覧ください。

HDInsight でデータを分析する方法について詳しくは、次の記事をご覧ください。

* Visual Studio から Hive クエリを実行する方法など、HDInsight で Hive を使用する方法について詳しくは、[HDInsight での Hive の使用](hdinsight-use-hive.md)に関する記事をご覧ください。
* データの変換に使用される言語 Pig について詳しくは、[HDInsight での Pig の使用](hdinsight-use-pig.md)に関する記事をご覧ください。
* Hadoop 上のデータを処理するプログラムを作成する方法の 1 つである MapReduce について詳しくは、[HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)に関する記事をご覧ください。
* HDInsight Tools for Visual Studio を使用して HDInsight 上のデータを分析する方法について詳しくは、 [HDInsight Hadoop Tools for Visual Studio の使用開始](apache-hadoop-visual-studio-tools-get-started.md)に関する記事をご覧ください。


HDInsight クラスターの作成または管理について詳しくは、以下の記事をご覧ください。

* Linux ベースの HDInsight クラスターを管理する方法について詳しくは、「[Ambari を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」をご覧ください。
* HDInsight クラスターの作成時に選択できるオプションについて詳しくは、「[HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


