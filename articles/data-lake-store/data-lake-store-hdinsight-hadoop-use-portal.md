---
title: "ポータルを使用して Azure Data Lake Store で HDInsight クラスターを作成する | Microsoft Docs"
description: "Azure ポータルを使用して、Azure Data Lake Store で HDInsight クラスターを作成し、使用します"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3a9a4df2d260e8eebd8621b22efdb2927e9f5ecf
ms.openlocfilehash: a4fb47f9f517d66cf0ff9fde039d7bfd8edc29eb


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure Portal を使用して、Azure Data Lake Store にアクセスできる HDInsight クラスターを作成する方法について説明します。 サポートされている種類のクラスターでは、Data Lake Store を既定のストレージまたは追加のストレージ アカウントとして使用します。 Data Lake Store を追加のストレージとして使用した場合、クラスターの既定のストレージ アカウントは Azure Storage BLOB (WASB) のままであり、クラスター関連のファイル (ログなど) はその既定のストレージに書き込まれますが、一方で処理対象のデータは Data Lake Store アカウントに格納することができます。 Data Lake Store を追加のストレージ アカウントとして使用しても、クラスターからストレージに対する読み取り/書き込みのパフォーマンスや機能は何も変化しません。

重要な考慮事項をいくつか以下に示します。

* Data Lake Store にアクセスできる HDInsight クラスターを既定のストレージとして作成するオプションは、HDInsight バージョン 3.5 で使用できます。

* Data Lake Store にアクセスできる HDInsight クラスターを追加のストレージとして作成するオプションは、HDInsight バージョン 3.2、3.4、3.5 で使用できます。

* HBase クラスター (Windows および Linux) の場合、Data Lake Store は、既定のストレージとしても追加のストレージとしても**使用できません**。

* Storm クラスター (Windows および Linux) の場合、Data Lake Store は、Storm トポロジからデータを書き込むために使用できます。 Data Lake Store は、Storm トポロジから読み取ることができる、参照データを格納するために使用することもできます。 詳細については、「 [Storm トポロジで Data Lake Store を使用する](#use-data-lake-store-in-a-storm-topology)」を参照してください。


## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Store アカウント**。 「 [Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

* **Azure Active Directory Service のプリンシパル**。 このチュートリアルの手順では、Azure AD でサービス プリンシパルを作成する方法を説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 Azure AD 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    **Azure AD 管理者でない場合**は、サービス プリンシパルの作成に必要な手順を実行することはできません。 その場合は、Data Lake Store で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[Create a service principal with certificate](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」 (証明書でサービス プリンシパルを作成する) で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。

## <a name="do-you-learn-faster-with-videos"></a>ビデオで速習する
以下のビデオをご覧になり、Data Lake Store にアクセスできる HDInsight クラスターをプロビジョニングする方法を理解してください。

* [Azure ポータルを使用して、Data Lake Store を使用する HDInsight クラスターを作成する](https://mix.office.com/watch/l93xri2yhtp2)
* クラスターがセットアップされたら、 [Hive および Pig スクリプトを使用して Data Lake Store のデータにアクセス](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Azure Data Lake Store にアクセスできる HDInsight クラスターを作成する
このセクションでは、Data Lake Store を追加のストレージとして使用する HDInsight Hadoop クラスターを作成します。 このリリースでは、Hadoop クラスターの場合、Data Lake Store はクラスターの追加のストレージとしてのみ使用できます。 既定のストレージは、Azure Storage BLOB (WASB) のままです。 そのため、クラスターに必要なストレージ アカウントとストレージ コンテナーを最初に作成します。

1. 新しい [Azure ポータル](https://portal.azure.com)にサインオンします。

2. 「 [HDInsight で Hadoop クラスターを作成する](../hdinsight/hdinsight-provision-clusters.md) 」の手順に従って、HDInsight クラスターのプロビジョニングを開始します。

3. **[データ ソース]** ブレードで、既定のストレージとして Azure Storage (WASB) と Data Lake Store のどちらを使用するか指定します。 既定のストレージとして Azure Data Lake Store を使用する場合は、次の手順までスキップしてください。 

    既定のストレージとして Azure Storage BLOB を使用する場合は、**[Primary Storage Type (プライマリ ストレージの種類)]** で **[Azure Storage]** をクリックします。 ストレージ アカウントおよびストレージ コンテナーの詳細を指定し、**[場所]** を **[米国東部 2]** に指定して、**[クラスター AAD ID]** をクリックします。
    
    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Add service principal to HDInsight cluster")


4. 既定のストレージとして Azure Data Lake Store を使用する場合は、**[Primary Storage Type (プライマリ ストレージの種類)]** で **[Data Lake Store]** をクリックします。 既にある Data Lake Store アカウントを選択してクラスターに固有のファイルが格納されるルート フォルダーのパスを指定し (下記の注を参照)、**[場所]** を **[米国東部 2]** に指定して、**[クラスター AAD ID]** をクリックします。 このオプションは、HDInsight 3.5 クラスターでのみ使用できます。 HDInsight 3.5 クラスター内では、このオプションを HBase の種類のクラスターに使用することはできません。

    以下の画面キャプチャでは、ルート フォルダーのパスが /clusters/myhdiadlcluster ですが、ここで **myhdiadlcluster** は作成するクラスターの名前です。 このような場合は、**/clusters** フォルダーが既に Data Lake Store アカウント内に存在することを確認してください。 **myhdiadlcluster** フォルダーは、クラスターの作成時に作成されます。 同様に、ルートのパスを /hdinsight/clusters/data/myhdiadlcluter に設定した場合は、**/hdinsight/clusters/data/** が Data Lake Store アカウント内に既に存在することを確認する必要があります。
        
    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Add service principal to HDInsight cluster")

5. **[クラスター AAD ID]** ブレードで、既存のサービス プリンシパルを選択するか、または新しいサービス プリンシパルを作成することができます。 既存のサービス プリンシパルを使用する場合は、次の手順までスキップしてください。

    新しいサービス プリンシパルを作成する場合は、**[クラスター AAD ID]** ブレードで **[新規作成]**、**[サービス プリンシパル]** の順にクリックし、**[サービス プリンシパルの作成]** ブレードで、新しいサービス プリンシパルを作成するための値を指定します。 その一環として、証明書と Azure Active Directory アプリケーションも作成されます。 **[作成]**をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Add service principal to HDInsight cluster")

    **[証明書のダウンロード]** をクリックして、作成したサービス プリンシパルに関連付けられている証明書をダウンロードすることもできます。 これは、HDInsight クラスターを他にも作成しているが、後でその同じサービス プリンシパルを使用したい場合に役立ちます。 **[選択]**をクリックします。

6. 既存のサービス プリンシパルを使用する場合は、**[クラスター AAD ID]** ブレードで、**[既存のものを使用]**、**[サービス プリンシパル]** の順にクリックし、**[サービス プリンシパルの選択]** ブレードで既存のサービス プリンシパルを探します。 サービス プリンシパルの名前をクリックし、 **[選択]**をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Add service principal to HDInsight cluster")

    **[クラスター AAD ID]** ブレードで、選択したサービス プリンシパルに関連付けられている証明書 (.pfx) をアップロードし、証明書のパスワードを指定します。

6. **[クラスター AAD ID]** ブレードで、**[ADLS アクセスを管理する]** をクリックします。 次のウィンドウでは、既定で **[ファイル アクセス許可の選択]** が既に選択されており、サブスクリプション内のすべての Data Lake Store アカウントが一覧表示されています。 クラスターに関連付ける Data Lake Store アカウントをクリックし、そのアカウントのファイルとフォルダーを一覧表示します。 その後、ファイルまたはフォルダー レベルでアクセス許可を割り当てることができます。 アカウントのルート レベルでアクセス許可を関連付ける場合は、アカウント名の横にあるチェック ボックスをオンにします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Add service principal to HDInsight cluster")

    > [!NOTE]
    > Data Lake Store アカウントをクラスターの既定のストレージとして使用している場合は、**必ず** Data Lake Store アカウントのルート レベルでサービス プリンシパルにアクセス許可を割り当ててください。

7. アカウント内のファイルまたはフォルダーのアクセス許可を割り当てる場合は、次のウィンドウでファイルまたはフォルダーを表示する Data Lake Store アカウントを選択します。 ファイルまたはフォルダーを選択し、それについて割り当てるアクセス許可 ([読み取り]/[書き込み]/[実行]) を選択して、そのアクセス許可が子項目にも再帰的に適用されるかどうかを指定し、**[選択]** をクリックします。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Add service principal to HDInsight cluster")

8. 次の画面で **[実行]** をクリックし、選択したアカウント、ファイル、フォルダーでの Azure Active Directory サービス プリンシパルのアクセス許可を割り当てます。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Add service principal to HDInsight cluster")

9. アクセス許可が正常に割り当てられたら、**[クラスター AAD ID]** ブレードに戻るまで、すべてのブレードで **[完了]** をクリックします。

4. **[クラスター AAD ID]** で **[選択]** をクリックし、[HDInsight での Hadoop クラスターの作成](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)に関するページの説明に従ってクラスターの作成に進みます。

10. クラスターがプロビジョニングされたら、サービス プリンシパルが HDInsight クラスターに関連付けられていることを確認できます。 そのためには、クラスター ブレードで **[クラスター AAD ID]** をクリックし、関連付けられているサービス プリンシパルを確認します。

    ![HDInsight クラスターにサービス プリンシパルを追加する](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="show-me-some-examples"></a>いくつかの例

Data Lake Store をストレージとして使用するクラスターのプロビジョニングが完了したら、HDInsight クラスターを使用して Data Lake Store に格納されているデータを分析する方法について、以下に示すいくつかの例をご覧ください。

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store"></a>Data Lake Store に格納されているデータに対して Hive クエリを実行する

Hive クエリを実行する場合は、Ambari ポータルで提供されている Hive ビュー インターフェイスを使用できます。 Ambari Hive ビューの使用方法については、「[HDInsight での Hive View と Hadoop の使用](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)」を参照してください。 Data Lake Store 内のデータを操作する場合は、いくつか変更を行う必要があります。

* 上で作成したクラスターを例にとると、データへのパスは `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file` となります。 Data Lake Store アカウントに格納されているサンプル データからテーブルを作成する Hive クエリは、次のようになります。

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

上記のクエリで、

* `adl://hdiadlstorage.azuredatalakestore.net` は Data Lake Store アカウントのルートです。
* `/clusters/myhdiadlcluster` はクラスターの作成時に指定したクラスター データのルートです。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` はクエリで使用するサンプル ファイルの場所です。


### <a name="use-data-lake-store-with-spark-cluster"></a>Spark クラスターで Data Lake Store を使用する
Spark クラスターを使用すると、Data Lake Store に格納されているデータで Spark ジョブを実行できます。 この手順については、「[Use HDInsight Spark cluster to analyze data in Data Lake Store (HDInsight Spark クラスターを使用して Data Lake Store のデータを分析する)](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)」を参照してください。


### <a name="use-data-lake-store-in-a-storm-topology"></a>Storm トポロジで Data Lake Store を使用する
Data Lake Store を使用して、Storm トポロジからデータを書き込むことができます。 このシナリオを実現する方法については、「 [HDInsight で Apache Storm によって Azure Data Lake Store を使用する](../hdinsight/hdinsight-storm-write-data-lake-store.md)」をご覧ください。

## <a name="see-also"></a>関連項目
* [Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx



<!--HONumber=Nov16_HO4-->


