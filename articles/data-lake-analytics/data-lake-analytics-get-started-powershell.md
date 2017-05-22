---
title: "Azure PowerShell で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Azure PowerShell を使用して Data Lake Analytics アカウントを作成し、U-SQL で Data Lake Analytics ジョブを作成して、ジョブを送信します。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>チュートリアル: Azure PowerShell で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell を使って Azure Data Lake Analytics アカウントを作成し、U-SQL ジョブを送信して実行する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の条件を満たしておく必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell を実行できるワークステーション**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

## <a name="preparing-for-the-tutorial"></a>チュートリアルの準備
Data Lake Analytics アカウントを作成するには、最初に次の事柄を定義する必要があります。

* **Azure リソース グループ**: Data Lake Analytics アカウントは、Azure リソース グループ内に作成する必要があります。
* **Data Lake Analytics アカウント名**: Data Lake Analytics アカウント名には小文字と数字のみを含める必要があります。
* **場所**: Data Lake Analytics をサポートするいずれかの Azure データ センター。
* **既定の Data Lake Store アカウント**: 各 Data Lake Analytics アカウントには既定の Data Lake Store アカウントがあります。 これらのアカウントは同じ場所に存在する必要があります。

このチュートリアルの PowerShell スニペットでは、以下の変数を使って各情報を格納します。

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

まだ使用するリソース グループがない場合は、新たに作成します。 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

すべての Data Lake Analytics アカウントに、ログの保存を目的とした既定の Data Lake Store アカウントが必要です。 既にあるアカウントを再利用するか、新しいアカウントを作成してください。 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

リソース グループと Data Lake Store アカウントが用意できたら、Data Lake Analytics アカウントを作成します。

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントに関する情報を取得する

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL ジョブの送信

次の U-SQL スクリプトを含んだテキスト ファイルを作成します。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

このスクリプトを送信します。

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>U-SQL ジョブの監視

アカウントに存在するすべてのジョブを一覧表示します。 出力結果には、現在実行されているジョブと最近完了したジョブが含まれます。

```
Get-AdlJob -Account $adla
```

特定のジョブの状態を取得します。

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

ジョブが完了するまで Get-AdlAnalyticsJob を何度も呼び出す代わりに、Wait-AdlJob コマンドレットを使用することができます。

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

ジョブの完了後、フォルダー内のファイルを一覧表示して、出力ファイルが存在するかどうかを確認します。

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

ファイルの存在を確認します。

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>ファイルのアップロードとダウンロード

U-SQL スクリプトの出力をダウンロードします。

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


U-SQL スクリプトへの入力として使用するファイルをアップロードします。

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>関連項目
* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。

