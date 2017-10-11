---
title: "Azure PowerShell を使用して Azure Data Lake Analytics の概要 |Microsoft ドキュメント"
description: "Azure PowerShell を使用して、Data Lake Analytics アカウントを作成し、U-SQL を使用して、Data Lake Analytics ジョブを作成して、ジョブを送信します。 "
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
ms.openlocfilehash: 4f73e27c733edae658d1ea3bdabe48076328279b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell を使用して Azure Data Lake Analytics の概要します。
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell を使用して、Azure Data Lake Analytics アカウントを作成および送信し、U SQL ジョブを実行する方法を説明します。 Data Lake Analytics の詳細については、次を参照してください。 [Azure Data Lake Analytics 概要](data-lake-analytics-overview.md)です。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の情報が必要です。

* **Azure Data Lake Analytics アカウント**です。 参照してください[Data Lake Analytics の概要](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-get-started-portal)です。
* **Azure PowerShell を使用してワークステーション**です。 参照してください[をインストールして、Azure PowerShell を構成する方法](/powershell/azure/overview)です。

## <a name="log-in-to-azure"></a>Azure にログイン

このチュートリアルでは、Azure PowerShell を使用に慣れて既に前提としています。 具体的には、Azure にログインする方法を理解する必要があります。 参照してください、 [Azure PowerShell の入門](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps)ヘルプが必要な場合です。

サブスクリプションの名前でログインします。

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

サブスクリプション名の代わりにログインするサブスクリプション id を使用することもできます。

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

成功した場合、このコマンドの出力は、次のテキストのようになります。

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>チュートリアルの準備

このチュートリアルでは PowerShell スニペットは、この情報を格納するのにこれらの変数を使用します。

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントに関する情報を取得します。

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U SQL ジョブを送信します。

U-SQL スクリプトを保持する PowerShell 変数を作成します。

```
$script = @"
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

"@
```

スクリプトを送信します。

```
$job = Submit-AdlJob -AccountName $adla –Script $script
```

または、スクリプト ファイルとして保存し、次のコマンドで送信します。

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -AccountName $adla –ScriptPath $filename
```


特定のジョブの状態を取得します。 ジョブの完了が表示されるまで、このコマンドレットを使用して保持します。

```
$job = Get-AdlJob -AccountName $adla -JobId $job.JobId
```

ジョブが終了するまでに Get AdlAnalyticsJob を繰り返し呼び出す代わりに、待機 AdlJob コマンドレットを使用することができます。

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

出力ファイルをダウンロードします。

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>関連項目
* その他のツールを使用して同じチュートリアルを表示するには、ページの上部のタブのセレクターをクリックします。
* U-SQL については、次を参照してください。 [Azure データ Lake Analytics U-SQL 言語の概要](data-lake-analytics-u-sql-get-started.md)です。
* 管理タスクを参照してください。[管理 Azure Data Lake Analytics Azure ポータルを使用して](data-lake-analytics-manage-use-portal.md)です。
