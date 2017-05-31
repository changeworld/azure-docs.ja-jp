---
title: "Azure CLI 2.0 で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス 2.0 を使用して Data Lake Analytics アカウントを作成し、U-SQL を使用して Data Lake Analytics ジョブを作成し、ジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25278e621155e7c90b5bb870d08793d285f4edff
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>チュートリアル: Azure CLI 2.0 を使用して Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure CLI 2.0 を使用して、Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) で Data Lake Analytics ジョブを定義し、Data Lake Analtyics アカウントにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

このチュートリアルでは、タブ区切り値 (TSV) ファイルを読み取り、コンマ区切り値 (CSV) ファイルに変換するジョブを開発します。 サポートされている他のツールを使用する同じチュートリアルを読み進めるには、このセクションの上部にあるドロップダウン リストを使用してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure CLI 2.0**。 「 [Azure CLI のインストールと構成](https://docs.microsoft.com/cli/azure/install-azure-cli)」をご覧ください。

## <a name="log-in-to-azure"></a>Azure へのログイン

Azure サブスクリプションにログインするには:

```azurecli
az login
```

URL に移動し、認証コードを入力するように求められます。  次に、指示に従って、資格情報を入力します。

ログイン後は、login コマンドでサブスクリプションの一覧が表示されます。

特定のサブスクリプションを使用するには:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Data Lake Analytics アカウントを作成するには、以下の項目を指定する必要があります。

* **Azure リソース グループ**。 Data Lake Analytics アカウントは、Azure リソース グループ内に作成する必要があります。 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用すると、アプリケーション内の複数リソースを 1 つのグループとして操作できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。  
  
    サブスクリプションの既存のリソース グループを一覧表示するには:
  
    ```azurecli
    az group list 
    ```

    新しいリソース グループを作成するには:
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Data Lake Analytics アカウント名**。 各 Data Lake Analytics アカウントには、名前があります。
* **場所**。 Data Lake Analytics をサポートしているいずれかの Azure データ センターを使用します。
* **既定の Data Lake Store アカウント**: 各 Data Lake Analytics アカウントには既定の Data Lake Store アカウントがあります。
  
    既存の Data Lake Store アカウントを一覧表示するには:

    ```azurecli
    az dls account list
    ```
  
    新しい Data Lake Store アカウントを作成するには:
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Data Lake Analytics アカウントを作成するには、次の構文を使用します。

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

アカウントを作成した後、以下のコマンドを使用して、アカウントを一覧表示し、アカウントの詳細を表示することができます。

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Data Lake Store にデータをアップロードする
このチュートリアルでは、いくつかの検索ログを処理します。  検索ログは、Data Lake Store または Azure Blob Storage に格納できます。 

Azure Portal には、検索ログ ファイルを含むサンプル データ ファイルを既定の Data Lake Store アカウントにコピーするためのユーザー インターフェイスが用意されています。 「 [ソース データの準備](data-lake-analytics-get-started-portal.md#prepare-source-data) 」を参照して、データを既定の Data Lake Store アカウントにアップロードします。

CLI 2.0 を使用してファイルをアップロードするには、以下のコマンドを使用します。

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics は、Azure BLOB ストレージにもアクセスできます。  Azure BLOB ストレージへのデータのアップロードについては、「 [Azure ストレージでの Azure CLI の使用](../storage/storage-azure-cli.md)」を参照してください。

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブを送信する
Data Lake Analtyics ジョブは U-SQL 言語で記述されます。 U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関する記事および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

**Data Lake Analytics ジョブ スクリプトを作成するには**

次の U-SQL スクリプトでテキスト ファイルを作成し、ワークステーションにテキスト ファイルを保存します。
  
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
  
この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。 

ソース ファイルを別の場所にコピーしない限り、2 つのパスを変更しないでください。  存在しない場合、Data Lake Analytics は出力フォルダーを作成します。

既定の Data Lake Store アカウントに保存されたファイルの相対パスを使用する方が簡単です。 絶対パスを使用することもできます。  次に例を示します。

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。  リンクされた Azure ストレージ アカウントに格納されているファイルの構文は以下のとおりです。

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > パブリック BLOB またはパブリック コンテナーのアクセス許可を持つ Azure BLOB コンテナーは、現在サポートされていません。      
  > 
  > 

**ジョブを送信するには**

ジョブを送信するには、次の構文を使用します。

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

次に例を示します。

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**ジョブを一覧表示し、ジョブの詳細を表示するには**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**ジョブを取り消すには**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>ジョブの結果を取得する

ジョブが完了したら、以下のコマンドを使用し、出力ファイルを一覧表示して、ファイルをダウンロードできます。

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

For example:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>次のステップ

* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* Data Lake Analytics CLI 2.0 のリファレンス ドキュメントについては、「[Data Lake Analytics - az dla](https://docs.microsoft.com/cli/azure/dla)」を参照してください。
* Data Lake Store CLI 2.0 のリファレンス ドキュメントについては、「[Data Lake Store - az dls](https://docs.microsoft.com/cli/azure/dls)」を参照してください。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。


