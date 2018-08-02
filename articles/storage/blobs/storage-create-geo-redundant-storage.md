---
title: Azure でアプリケーション データを高可用にする | Microsoft Docs
description: 読み取りアクセス geo 冗長ストレージを使用してアプリケーション データを高可用にする
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 03/26/2018
ms.author: tamram
ms.custom: mvc
ms.component: blobs
ms.openlocfilehash: 7abd251751613224d062da5578e9c91a525599c9
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399034"
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Azure Storage を使用してアプリケーション データを高可用にする

このチュートリアルは、Azure でアプリケーション データに高い可用性を確保する方法について紹介するシリーズの第 1 部です。 完了すると、BLOB を[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントにアップロードし、取得するコンソール アプリケーションが作成されます。 RA-GRS は、プライマリ リージョンからセカンダリ リージョンにトランザクションをレプリケートすることによって機能します。 このレプリケーション プロセスにより、セカンダリ リージョンのデータの結果整合性が保証されます。 このアプリケーションで[ブレーカー](/azure/architecture/patterns/circuit-breaker) パターンを使用して、接続先のエンドポイントを判断します。 エラーをシミュレートすると、アプリケーションはセカンダリ エンドポイントに切り替えます。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * サンプルのダウンロード
> * 接続文字列の設定
> * コンソール アプリケーションを実行する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* 次のワークロードを使って、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールします。
  - **Azure 開発**

  ![Azure 開発 ([Web & Cloud]\(Web とクラウド\) 以下)](media/storage-create-geo-redundant-storage/workloads.png)

* (省略可能) [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードしてインストールします。
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* [Python](https://www.python.org/downloads/) のインストール
* [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) をダウンロードしてインストールします。
* (省略可能) [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードしてインストールします。

# <a name="java-tabjava"></a>[Java] (#tab/java)

* [Maven](http://maven.apache.org/download.cgi) をインストールして、コマンド ラインから使用するように構成します
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) をインストールして構成します

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を用意します。

次の手順で、読み取りアクセス geo 冗長ストレージ アカウントを作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** ボタンを選択します。

2. **[新規]** ページの **[ストレージ]** を選択し、**[おすすめ]** の **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。
3. 下図のように、ストレージ アカウント フォームに次の情報を入力し、**[作成]** を選択します。

   | Setting       | 推奨値 | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **名前** | mystorageaccount | ストレージ アカウント用の一意の値 |
   | **デプロイ モデル** | リソース マネージャー  | Resource Manager には最新の機能が含まれています。|
   | **アカウントの種類** | StorageV2 | アカウントの種類の詳細については、「[ストレージ アカウントの種類](../common/storage-introduction.md#types-of-storage-accounts)」を参照してください |
   | **パフォーマンス** | 標準 | このサンプル シナリオでは、標準で十分です。 |
   | **レプリケーション**| 読み取りアクセス geo 冗長ストレージ (RA-GRS) | サンプルが動作するには、この設定が必要です。 |
   |**安全な転送が必須** | Disabled| このシナリオでは、安全な転送は必要ありません。 |
   |**サブスクリプション** | 該当するサブスクリプション |サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   |**ResourceGroup** | myResourceGroup |有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   |**場所** | 米国東部 | 場所を選択します。 |

![ストレージ アカウントの作成](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>サンプルのダウンロード

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)し、storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトにはコンソール アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)し、storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトには基本的な Python アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-tabjava"></a>[Java] (#tab/java)
[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-java-ha-ra-grs)し、storage-java-ragrs.zip ファイルを抽出します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトには基本的な Java アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>接続文字列の設定

アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 アプリケーションを実行しているローカル コンピューター上の環境変数内にこの接続文字列を格納することをお勧めします。 環境変数を作成するオペレーティング システムに応じて、以下の例のいずれかに従います。

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 プライマリ キーまたはセカンダリ キーの**接続文字列**をコピーします。 オペレーティング システムに基づいて次のコマンドのいずれかを実行して、\<yourconnectionstring\> を実際の接続文字列に置き換えます。 このコマンドで、環境変数をローカル コンピューターに保存します。 Windows では、環境変数は、使用している**コマンド プロンプト**またはシェルを再度読み込むまで使用できません。 次のサンプルの **\<storageConnectionString\>** を置き換えます。

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>コンソール アプリケーションを実行する

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
Visual Studio で **F5** キーを押すか **[スタート]** を選択してアプリケーションのデバッグを開始します。 構成に応じて、不足している NuGet パッケージが Visual Studio で自動的に復元されます。詳細については、[パッケージの復元によるパッケージのインストールと再インストール](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)に関するセクションを参照してください。

コンソール ウィンドウが起動し、アプリケーションの実行が開始されます。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`Program.cs` ファイルの `RunCircuitBreakerAsync` タスクは、[DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。 そのダウンロードの前に、[OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) が定義されています。 操作コンテキストにイベント ハンドラーが定義され、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。

# <a name="python-tabpython"></a>[Python] (#tab/python) 
ターミナルまたはコマンド プロンプトでアプリケーションを実行するには、**circuitbreaker.py** ディレクトリに移動して「`python circuitbreaker.py`」と入力します。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`circuitbreaker.py` ファイルの `run_circuit_breaker` メソッドは、[get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。 

ストレージ オブジェクトの再試行関数は、線形的な再試行ポリシーに設定されています。 この再試行関数によって、要求を再試行するかどうかが判断され、要求が再試行されるまでの秒数が指定されます。 プライマリへの初回要求が失敗したときにセカンダリに対して要求を再試行する場合は、**retry\_to\_secondary** の値を true に設定してください。 サンプル アプリケーションのカスタム再試行ポリシーは、ストレージ オブジェクトの `retry_callback` 関数で定義されます。
 
ダウンロードの前に、サービス オブジェクトの [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) と [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 関数が定義されています。 これらの関数に定義されているイベント ハンドラーが、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。  

# <a name="java-tabjava"></a>[Java] (#tab/java)
アプリケーションは、ダウンロード済みアプリケーション フォルダーをスコープとするターミナルまたはコマンド プロンプトを開くことによって実行できます。 そこで、「`mvn compile exec:java`」と入力してアプリケーションを実行します。 すると、アプリケーションは、**HelloWorld.png** 画像をディレクトリからストレージ アカウントにアップロードした後、画像がセカンダリ RA-GRS エンドポイントにレプリケートされたかどうかを確認します。 チェックが完了すると、アプリケーションは、ダウンロード元のエンドポイントを報告しながら、画像のダウンロードを繰り返し開始します。

ストレージ オブジェクトの再試行関数は、線形的な再試行ポリシーを使用するように設定されます。 この再試行関数によって、要求を再試行するかどうかが判断され、各再試行の間に待機する秒数が指定されます。 **BlobRequestOptions** の **LocationMode** プロパティは、**PRIMARY\_THEN\_SECONDARY** に設定されます。 これにより、アプリケーションは、**HelloWorld.png** をダウンロードするときにプライマリ ロケーションに到達できない場合に自動的にセカンダリ ロケーションに切り替えることができます。

---

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`OperationContextRetrying` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) は `SecondaryOnly` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
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

`OperationContextRequestCompleted` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) を `PrimaryThenSecondary` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。
 
```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`retry_callback` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) は `SECONDARY` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>要求が完了したイベント ハンドラー

`response_callback` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) を `PRIMARY` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-tabjava"></a>[Java] (#tab/java)

Java では、**BlobRequestOptions** の **LocationMode** プロパティが **PRIMARY\_THEN\_SECONDARY** に設定されている場合にコールバック ハンドラーを定義する必要はありません。 これにより、アプリケーションは、**HelloWorld.png** をダウンロードするときにプライマリ ロケーションに到達できない場合に自動的にセカンダリ ロケーションに切り替えることができます。

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```
---


## <a name="next-steps"></a>次の手順

シリーズの第 1 部では、次のように RA-GRS ストレージ アカウントでアプリケーションを高可用にする方法について説明しました。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * サンプルのダウンロード
> * 接続文字列の設定
> * コンソール アプリケーションを実行する

シリーズの第 2 部に進んで、エラーをシミュレートし、アプリケーションがセカンダリ RA-GRS エンドポイントを使用するように強制する方法について学んでください。

> [!div class="nextstepaction"]
> [プライマリ ストレージ エンドポイントへの接続時のエラーをシミュレートする](storage-simulate-failure-ragrs-account-app.md)
