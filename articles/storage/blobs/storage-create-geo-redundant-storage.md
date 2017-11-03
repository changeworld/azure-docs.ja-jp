---
title: "Azure でアプリケーション データを高可用にする | Microsoft Docs"
description: "読み取りアクセス geo 冗長ストレージを使用してアプリケーション データを高可用にする"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 547ca7843f53bd11fdb922af8e0ae77e38f813d9
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Azure Storage を使用してアプリケーション データを高可用にする

このチュートリアルは、シリーズの第 1 部です。 このチュートリアルでは、Azure でアプリケーション データを高可用にする方法について説明します。 完了すると、BLOB を[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントにアップロードし、取得するコンソール アプリケーションが作成されます。 RA-GRS は、プライマリ リージョンからセカンダリ リージョンにトランザクションをレプリケートすることによって機能します。 このレプリケーション プロセスにより、セカンダリ リージョンのデータの結果整合性が保証されます。 このアプリケーションで[ブレーカー](/azure/architecture/patterns/circuit-breaker.md) パターンを使用して、接続先のエンドポイントを判断します。 エラーをシミュレートすると、アプリケーションはセカンダリ エンドポイントに切り替えます。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * サンプルのダウンロード
> * 接続文字列の設定
> * コンソール アプリケーションを実行する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* 次のワークロードを使って、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールします。
  - **Azure 開発**

  ![Azure 開発 ([Web & Cloud]\(Web とクラウド\) 以下)](media/storage-create-geo-redundant-storage/workloads.png)

* [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードしてインストールする

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を用意します。

次の手順で、読み取りアクセス geo 冗長ストレージ アカウントを作成します。

1. Azure Portal の左上にある **[新規]** ボタンを選択します。

2. **[新規]** ページの **[ストレージ]** を選択し、**[おすすめ]** の **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。
3. 下図のように、ストレージ アカウント フォームに次の情報を入力し、**[作成]** を選択します。

   | 設定       | 推奨値 | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **名前** | mystorageaccount | ストレージ アカウント用の一意の値 |
   | **デプロイ モデル** | リソース マネージャー  | Resource Manager には最新の機能が含まれています。  |
   | **アカウントの種類** | 汎用 | アカウントの種類の詳細については、「[ストレージ アカウントの種類](../common/storage-introduction.md#types-of-storage-accounts)」を参照してください |
   | **パフォーマンス** | 標準 | このサンプル シナリオでは、標準で十分です。 |
   | **レプリケーション**| 読み取りアクセス geo 冗長ストレージ (RA-GRS) | サンプルが動作するには、この設定が必要です。 |
   |**安全な転送が必須** | 無効| このシナリオでは、安全な転送は必要ありません。 |
   |**サブスクリプション** | 該当するサブスクリプション |サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   |**ResourceGroup** | myResourceGroup |有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   |**場所** | 米国東部 | 場所を選択します。 |

![ストレージ アカウントの作成](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>サンプルのダウンロード

[サンプル プロジェクトをダウンロードします](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)。

storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。
サンプル プロジェクトにはコンソール アプリケーションが含まれています。

## <a name="set-the-connection-string"></a>接続文字列の設定

Visual Studio で *storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs* コンソール アプリケーションを開きます。

**App.config** ファイルの **appSettings** ノードで、_StorageConnectionString_ の値を実際のストレージ アカウントの接続文字列で置き換えます。 この値を取得するには、Azure Portal のストレージ アカウントで **[設定]** の **[アクセス キー]** を選択します。 プライマリ キーまたはセカンダリ キーの**接続文字列**をコピーし、**App.config** ファイルに貼り付けます。 完了したら、**[保存]** を選択してファイルを保存します。

![アプリの構成ファイル](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>コンソール アプリケーションを実行する

Visual Studio で **F5** キーを押すか **[スタート]** を選択してアプリケーションのデバッグを開始します。 構成に応じて、不足している NuGet パッケージが Visual Studio で自動的に復元されます。詳細については、[パッケージの復元によるパッケージのインストールと再インストール](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)に関するセクションを参照してください。 

コンソール ウィンドウが起動し、アプリケーションの実行が開始されます。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`Program.cs` ファイルの `RunCircuitBreakerAsync` タスクは、[DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。 ダウンロード前に、[OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) は定義されています。 操作コンテキストにイベント ハンドラーが定義され、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`Operation_context_Retrying` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) は `SecondaryOnly` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。

```csharp
private static void Operation_context_Retrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>要求が完了したイベント ハンドラー

`Operation_context_RequestCompleted` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) を `PrimaryThenSecondary` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

```csharp
private static void Operation_context_RequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

シリーズの第 1 部では、次のように RA-GRS ストレージ アカウントでアプリケーションを高可用にする方法について説明しました。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * サンプルのダウンロード
> * 接続文字列の設定
> * コンソール アプリケーションを実行する

シリーズの第 2 部に進んで、エラーをシミュレートし、アプリケーションがセカンダリ RA-GRS エンドポイントを使用するように強制する方法について学んでください。

> [!div class="nextstepaction"]
> [プライマリ ストレージ エンドポイントへの接続時のエラーをシミュレートする](storage-simulate-failure-ragrs-account-app.md)
