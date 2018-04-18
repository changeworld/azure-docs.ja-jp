---
title: Azure 診断ログ | Microsoft Docs
description: 顧客は、Azure CDN のログ分析を有効にすることができます。
services: cdn
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: 9c61fe7c62f0718d390509d3b0ff3327bd193f43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="azure-diagnostic-logs"></a>Azure 診断ログ

Azure 診断ログでコア分析を確認し、1 つまたは複数の宛先に保存することができます。

 - Azure ストレージ アカウント
 - Azure Event Hubs
 - [Log Analytics ワークスペース](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
この機能は、Verizon (Standard/Premium) および Akamai (Standard) の CDN プロファイルに属しているすべての CDN エンドポイントで使用できます。 

Azure 診断ログにより、基本的な使用メトリックを CDN エンドポイントからさまざまなソースにエクスポートして、それらをカスタマイズした方法で使用できるようになります。 たとえば、次の種類のデータのエクスポートを実行できます。

- BLOB ストレージへのデータのエクスポート、CSV へのエクスポート、Excel でのグラフの生成。
- イベント ハブへのデータのエクスポートと、他の Azure サービスのデータとの関連付け。
- ログ分析へのデータのエクスポートと、自分の Log Analytics ワークスペースでのデータ表示

次の図は、データの一般的な CDN コア分析ビューを示しています。

![ポータル - 診断ログ](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*図 1 - CDN コア分析ビュー*

診断ログの詳細については、[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)に関するページを参照してください。

## <a name="enable-logging-with-azure-portal"></a>Azure ポータルを使用したログの有効化

CDN コア分析を使用してログ記録を有効にするには、次の手順に従います。

[Azure ポータル](http://portal.azure.com)にサインインします。 ワークフローで CDN をまだ有効にしていない場合は、続行する前に [Azure CDN を有効にしてください](cdn-create-new-endpoint.md)。

1. ポータルの、**CDN プロファイル**に移動します。
2. CDN プロファイルを選択して、**診断ログ**を有効にする CDN エンドポイントを選択します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. **[監視]** セクションで **[診断ログ]** を選択します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Azure Storage でログ記録を有効化する
    
1. Azure Storage を使用してログを保存するには、**[ストレージ アカウントへのアーカイブ]** を選択し、**[CoreAnalytics]** を選択し、**[リテンション期間 (日数)]** で保持日数を選択します。 リテンション期間を 0 にすると、ログが無期限に保存されます。 
2. 設定の名前を入力し、**[ストレージ アカウント]** をクリックします。 ストレージ アカウントを選択したら、**[保存]** をクリックします。

![ポータル - 診断ログ](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*図 2 - Azure Storage のログ記録*

### <a name="logging-with-log-analytics"></a>Log Analytics によるログ記録

Log Analytics を使用してログを保存するには、次の手順に従います。

1. **[診断ログ]** ブレードで、**[Log Analytics への送信]** を選択します。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. **[構成]** をクリックしてログ分析のログ記録を構成します。 [OMS ワークスペース] ダイアログ ボックスで、前のワークスペースを選択するか、新しいワークスペースを作成します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. **[新しいワークスペースの作成]** をクリックします。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/07_Create-new.png)

4. 新しい Log Analytics ワークスペース名を入力します。 Log Analytics ワークスペースには一意の名前を付ける必要があります。名前には、英字、数字、およびハイフンのみを使用できます。スペースとアンダースコアは使用できません。 
5. 次に、既存のサブスクリプション、リソース グループ (新規または既存)、場所、および価格レベルを選択します。 また、この構成をダッシュボードにピン留めするオプションもあります。 **[OK]** をクリックして、構成を完了します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  ワークスペースを作成した後は、[診断ログ] ウィンドウに戻ります。 新しいログ分析ワークスペースの名前を確認します。

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    ログ分析の構成を設定したら、**[CoreAnalytics]** が選択されていることを確認します。

6. **[Save]** をクリックします。

7. 新しい Log Analytics ワークスペースを確認するには、Azure Portal のダッシュボードに移動し、Log Analytics ワークスペースの名前をクリックします。 Log Analytics ワークスペースを表示するには、[OMS Portal] タイルをクリックします。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    これで、Log Analytics ワークスペースがデータをログ記録する準備ができました。 そのデータを使用するためには、[Log Analytics ソリューション](#consuming-diagnostics-logs-from-a-log-analytics-workspace)を使用する必要があります。これについては、この記事の中で後で説明します。

ログ データの遅延の詳細については、「[Log data delays](#log-data-delays)」(ログ データの遅延) を参照してください。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

次の例は、Azure PowerShell コマンドレットを使用して診断ログを有効にする方法を示しています。

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>ストレージ アカウントでの診断ログの有効化

まずログインしてサブスクリプションを選択します。

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


ストレージ アカウントで診断ログを有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Log Analytics ワークスペースで診断ログを有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Storage からの診断ログの使用
このセクションでは、CDN コア分析のスキーマと、これが Microsoft Azure Storage アカウントの内部でどのように編成されているかについて説明し、ログを CSV ファイルにダウンロードするためのサンプル コードを提供します。

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラーの使用
Azure ストレージ アカウントからコア分析データにアクセスするには、最初にストレージ アカウントの内容にアクセスするためのツールが必要です。 市場にはいくつかのツールがありますが、お勧めするツールは Microsoft Azure ストレージ エクスプローラーです。 ツールをダウンロードするには、「[Azure Storage Explorer](http://storageexplorer.com/)」を参照してください。 ソフトウェアをダウンロードしてインストールしたら、CDN 診断ログの保存先として構成したのと同じ Microsoft Azure Storage アカウントを使用するように構成します。

1.  **Microsoft Azure ストレージ エクスプローラー**を開きます
2.  ストレージ アカウントを見つけます
3.  このストレージ アカウントの下にある **“BLOB コンテナー”** ノードに移動して、ノードを展開します
4.  **“insights-logs-coreanalytics”** という名前のコンテナーを選択してダブルクリックします。
5.  最初のレベルが **“resourceId=”** などで始まる結果が右側のウィンドウに表示されます。 **PT1H.json** というファイルが表示されるまでクリックを続けます。 パスの説明については、次のメモを参照してください。
6.  BLOB の **PT1H.json** はそれぞれ特定の CDN エンドポイントまたはそのカスタム ドメインの 1 時間分の分析ログを表します。
7.  この JSON ファイルの内容のスキーマについては、「コア分析ログのスキーマ」のセクションで説明しています


> [!NOTE]
> **BLOB パスの形式**
> 
> コア分析ログは、1 時間ごとに生成され、そのデータは 1 つの Azure BLOB 内に JSON ペイロードとして収集され、格納されます。 Storage Explorer ツールでは '/' がディレクトリの区切り記号と解釈され、階層表示されるため、Azure BLOB のパスは、階層構造が存在するように表示され、BLOB 名が表示されます。 BLOB のこの名前は次の名前付け規則に従っています 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**フィールドの説明:**

|value|説明|
|-------|---------|
|サブスクリプション ID    |GUID 形式の Azure サブスクリプションの ID。|
|リソース |グループ名。CDN のリソースが属するリソース グループの名前|
|プロファイル名 |CDN プロファイルの名前|
|エンドポイント名 |CDN エンドポイントの名前|
|年|  年の 4 桁表記 (例: 2017)|
|月| 月の 2 桁表記。 01 = 1 月 ... 12 = 12 月|
|日|   日付の 2 桁表記|
|PT1H.json| 分析データが格納されている実際の JSON ファイル|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>CSV ファイルへのコア分析データのエクスポート

コア分析に簡単にアクセスできるように、ツールのサンプル コードが用意されています。 このツールを使用すると、JSON ファイルをコンマ区切り形式のフラット ファイルにダウンロードし、それを使用してグラフやその他の集計を簡単に作成できます。

このツールを使用する方法を次に示します。

1.  次の GitHub リンクを参照してください: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  コードをダウンロードします。
3.  指示に従ってコンパイルして構成します。
4.  ツールを実行します。
5.  生成される CSV ファイルには単純なフラット階層の分析データが示されています。

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Log Analytics ワークスペースからの診断ログの使用
Log Analytics は Azure のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持します。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。 

Log Analytics を使用するには、Azure Log Analytics ワークスペースに対する[ログ記録を有効にする](#enable-logging-with-azure-storage)必要があります。これについては、この記事の前半で説明しています。

### <a name="using-the-log-analytics-workspace"></a>Log Analytics ワークスペースの使用

 次の図は、リポジトリの入力と出力のアーキテクチャを示しています。

![Log Analytics ワークスペース](./media/cdn-diagnostics-log/12_Repo-overview.png)

*図 3 - Log Analytics リポジトリ*

管理ソリューションを使用して、さまざまな方法でデータを表示できます。 管理ソリューションは、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)から取得できます。

Azure Marketplace から各ソリューションの下にある **[Get it now (今すぐ入手する)]** リンクをクリックして管理ソリューションをインストールすることができます。

### <a name="adding-a-log-analytics-cdn-management-solution"></a>Log Analytics CDN 管理ソリューションの追加

管理ソリューションを追加するには、次の手順に従います。

1.   まだ Azure ポータルにサインインしていない場合は Azure サブスクリプションを使用してサインインし、ダッシュボードに移動します。
    ![Azure ダッシュボード](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. **[新規]** ブレードの **[Marketplace]** で、**[監視 + 管理]** を選択します。

    ![マーケットプレース](./media/cdn-diagnostics-log/14_Marketplace.png)

3. **[監視 + 管理]** ブレードで、**[すべて表示]** をクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/15_See-all.png)

4.  検索ボックスで CDN を検索します。

    ![すべて表示](./media/cdn-diagnostics-log/16_Search-for.png)

5.  **[Azure CDN Core Analytics]** を選択します。 

    ![すべて表示](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  **[作成]** をクリックすると、新しい Log Analytics ワークスペースを作成するか既存のものを使用するかを質問されます。 

    ![すべて表示](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  前に作成したワークスペースを選択します。 次に、オートメーション アカウントを追加する必要があります。

    ![すべて表示](./media/cdn-diagnostics-log/19_Add-automation.png)

8. 次の画面は、入力する必要があるオートメーション アカウント フォームを示しています。 

    ![すべて表示](./media/cdn-diagnostics-log/20_Automation.png)

9. オートメーション アカウントを作成した後、ソリューションを追加できます。 **[作成]** ボタンをクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/21_Ready.png)

10. これで、ソリューションがワークスペースに追加されます。 Azure Portal のダッシュボードに戻ります。

    ![すべて表示](./media/cdn-diagnostics-log/22_Dashboard.png)

    ワークスペースに移動するために作成した Log Analytics ワークスペースをクリックします。 

11. 新しいソリューションを確認するには、**[OMS Portal]** タイルをクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/23_workspace.png)

12. ポータルは次の画面のようになります。

    ![すべて表示](./media/cdn-diagnostics-log/24_OMS-solution.png)

    データのさまざまなビューを表示するには、いずれかのタイルをクリックします。

    ![すべて表示](./media/cdn-diagnostics-log/25_Interior-view.png)

    左右にスクロールすることで、データの個々のビューを表すその他のタイルを確認できます。 

    いずれかのタイルをクリックすると、データの詳細が表示されます。

     ![すべて表示](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>プランと価格レベル

[こちら](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)で管理ソリューションのプランと価格レベルを確認できます。

### <a name="customizing-views"></a>ビューのカスタマイズ

**ビュー デザイナー**を使用して、データのビューをカスタマイズできます。 デザインを開始するには、Log Analytics ワークスペースに移動し、**[ビュー デザイナー]** タイルをクリックします。

![ビュー デザイナー](./media/cdn-diagnostics-log/27_Designer.png)

グラフの種類をドラッグ アンド ドロップし、分析するデータの詳細を入力できます。

![ビュー デザイナー](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>ログ データの遅延

Verizon ログ データの遅延 | Akamai ログ データの遅延
--- | ---
Verizon のログ データには 1 時間の遅延があり、エンドポイントへの伝達が完了してから表示が開始されるまでに最大で 2 時間かかります。 | Akamai のログ データには 24 時間の遅延があります。24 時間より前に作成されている場合には、表示が開始されるまでに最大で 2 時間かかります。 ログが最近作成された場合は、表示が開始されるまでに最大で 25 時間かかることがあります。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN コア分析での診断ログの種類

現在のところ、CDN の POP/エッジから見た HTTP 応答の統計とエグレスの統計を示すメトリックを含むコア分析ログのみ提供されています。

### <a name="core-analytics-metrics-details"></a>コア分析メトリックの詳細
コア分析ログで使用可能なメトリックの一覧を次の表に示します。 プロバイダーによっては使用できないメトリックがありますが、違いはほとんどありません。 以下の表は、そのメトリックがプロバイダーで使用できるかどうかも示しています。 メトリックを使用できるのは、トラフィックが存在する CDN エンドポイントに対してのみであることに注意してください。


|メトリック                     | [説明]   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |この期間中にヒットした要求の合計数| [はい]  |[はい]   |
| RequestCountHttpStatus2xx |2xx の HTTP コード (200、202 など) になった要求の合計数              | [はい]  |[はい]   |
| RequestCountHttpStatus3xx | 3xx の HTTP コード (300、302 など) になった要求の合計数              | [はい]  |[はい]   |
| RequestCountHttpStatus4xx |4xx の HTTP コード (400、404 など) になった要求の合計数               | [はい]   |[はい]   |
| RequestCountHttpStatus5xx | 5xx の HTTP コード (500、504 など) になった要求の合計数              | [はい]  |[はい]   |
| RequestCountHttpStatusOthers |  その他のすべての HTTP コード (2xx-5xx 以外) の数 | [はい]  |[はい]   |
| RequestCountHttpStatus200 | HTTP コード応答 200 になった要求の合計数              |いいえ    |[はい]   |
| RequestCountHttpStatus206 | HTTP コード応答 206 になった要求の合計数              |いいえ    |[はい]   |
| RequestCountHttpStatus302 | HTTP コード応答 302 になった要求の合計数              |いいえ    |[はい]   |
| RequestCountHttpStatus304 |  HTTP コード応答 304 になった要求の合計数             |いいえ    |[はい]   |
| RequestCountHttpStatus404 | HTTP コード応答 404 になった要求の合計数              |いいえ    |[はい]   |
| RequestCountCacheHit |キャッシュ ヒットが生じた要求の合計数。 POP からクライアントに対して、アセットが直接処理されました。               | [はい]  |いいえ    |
| RequestCountCacheMiss | キャッシュ ミスが生じた要求の合計数。 これは、クライアントに最も近い POP でアセットが見つからなかったため、配信元から取得されたことを意味します。              |[はい]   | いいえ   |
| RequestCountCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットに対する要求の合計数。              |[はい]   | いいえ   |
| RequestCountCacheUncacheable | アセットの Cache-Control および Expires ヘッダーによりキャッシュされなかったアセットへの要求の合計数。この場合、POP または HTTP クライアントではキャッシュすべきでないことを示します                |[はい]   |いいえ    |
| RequestCountCacheOthers | 上記に含まれないキャッシュの状態の要求の合計数。              |[はい]   | いいえ   |
| EgressTotal | 送信データ転送 (GB)              |[はい]   |[はい]   |
| EgressHttpStatus2xx | HTTP 状態コードが 2xx の応答の送信データ転送* (GB)            |[はい]   |いいえ    |
| EgressHttpStatus3xx | HTTP 状態コードが 3xx の応答の送信データ転送 (GB)              |[はい]   |いいえ    |
| EgressHttpStatus4xx | HTTP 状態コードが 4xx の応答の送信データ転送 (GB)               |[はい]   | いいえ   |
| EgressHttpStatus5xx | HTTP 状態コードが 5xx の応答の送信データ転送 (GB)               |[はい]   |  いいえ  |
| EgressHttpStatusOthers | その他の HTTP 状態コードの応答の送信データ転送 (GB)                |[はい]   |いいえ    |
| EgressCacheHit |  CDN の POP/エッジで CDN キャッシュから直接配信された応答の送信データ転送  |[はい]   |  いいえ  |
| EgressCacheMiss | 最も近い POP サーバーで見つからず、配信元サーバーから取得された応答の送信データ転送              |[はい]   |  いいえ  |
| EgressCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットの送信データ転送。                |[はい]   |いいえ    |
| EgressCacheUncacheable | アセットの Cache-Control および/または Expires ヘッダーによりキャッシュされなかったアセットの送信データ転送。 POP または HTTP クライアントではキャッシュすべきでないことを示します。                   |[はい]   | いいえ   |
| EgressCacheOthers |  その他のキャッシュ シナリオの送信データ転送。             |[はい]   | いいえ   |

* 送信データ転送は、CDN の POP サーバーからクライアントに配信されたトラフィックを指します。


### <a name="schema-of-the-core-analytics-logs"></a>コア分析ログのスキーマ 

すべてのログが JSON 形式で格納され、各エントリには次のスキーマに従う文字列フィールドが含まれます。

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

ここで 'time' とは、統計情報を報告する時間の境界の開始時刻を表します。 メトリックが CDN プロバイダーによってサポートされていない場合、double または整数の値の代わりに null 値が設定されます。 この null 値は、メトリックがないことを示し、0 の値とは異なります。 エンドポイントに構成されたドメインごとにこれらのメトリックのセットが 1 つあります。

以下にプロパティの例を示します。

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>その他のリソース

* [Azure 診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Azure CDN の補助ポータルを使用したコア分析](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







