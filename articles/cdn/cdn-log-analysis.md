---
title: "Azure CDN のログ分析 | Microsoft Docs"
description: "顧客は、Azure CDN のログ分析を有効にすることができます。"
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---

# <a name="diagnostics-logs-for-azure-cdn"></a>Azure CDN の診断ログ

アプリケーションの CDN を有効にした後、CDN の使用状況の監視、配信の正常性の確認、潜在的な問題のトラブルシューティングが必要になる場合があるかもしれません。 Azure CDN は、[コア分析](cdn-analyze-usage-patterns.md)によって、これらの機能を提供しています。

現在 Azure CDN ユーザーであり、Verizon の Standard/Premium プロファイルがあれば、Azure ポータルの [管理] オプションからアクセスできる補助ポータルにコア分析を表示することができます。 この新しい[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)機能では、コア分析を表示し、これを Azure ストレージ アカウント、Azure イベント ハブ、または [Log Analytics (OMS) ワークスペース](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)のうち 1 つまたは複数を含む保存先に保存することができます。 この機能は、Verizon (Standard/Premium) および Akamai (Standard) の CDN プロファイルに属しているすべての CDN エンドポイントで使用できます。

診断ログにより、基本的な使用メトリックを CDN エンドポイントからさまざまなソースにエクスポートして、それらをカスタマイズした方法で使用できるようになります。 たとえば、次のようなことができます。

- BLOB ストレージへのデータのエクスポート、CSV へのエクスポート、Excel でのグラフの生成。
- イベント ハブへのデータのエクスポートと、他の Azure サービスのデータとの関連付け。
- ログ分析へのデータのエクスポートと、自分の OMS ワークスペースでのデータ表示


![ポータル - 診断ログ](./media/cdn-diagnostics-log/OMS-workspace.png)

次のチュートリアルでは、コア分析データのスキーマ、この機能を有効にしてさまざまな宛先に配信するための手順、そしてその保存先からの使用方法について説明します。

## <a name="enable-logging-with-azure-portal"></a>Azure ポータルを使用したログの有効化

既定では、診断ログは**オフ**になっています。 次の手順に従って診断ログを有効にします。


[Azure ポータル](http://portal.azure.com)にサインインします。 ワークフローで CDN をまだ有効にしていない場合は、続行する前に [Azure CDN を有効にしてください](cdn-create-new-endpoint.md)。

1. ポータルの、**CDN プロファイル**に移動します。
2. CDN プロファイルを選択して、**診断ログ**を有効にする CDN エンドポイントを選択します。
    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. **[監視]** セクションの下の **[診断ログ]** に移動して、ステータスを**[オン]** に変更します。
    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. 目的のアーカイブ ターゲットを選択して設定します (ストレージ アカウント、イベント ハブ、Log Analytics)。 
    
    この例では、Azure ストレージを使用してログを保存するので、**[ストレージ アカウントへのアーカイブ]** を選択し、保持日数を選択して、**[ログ]** の下の **[CoreAnalytics]** をクリックします。 現在は **CoreAnalytics** のみを提供していますが、今後より多くの種類のログを提供していきます。 CoreAnalytics のスキーマ、集計、遅延の情報については下記を参照してください。 

    ![ポータル - 診断ログ](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  新しい診断構成を保存します。
    
    Verizon のログ データには 1 時間の遅延があり、エンドポイントへの伝達が完了してから表示が開始されるまでに最大で 2 時間かかります。
    Akamai のログ データには 24 時間の遅延があり、24 時間より前に作成されている場合には、表示が開始されるまでに最大で 2 時間かかります。 ログが作成されたばかりの場合は、表示が開始されるまでに最大で 25 時間かかることがあります。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

次の 2 つの例は、Azure PowerShell コマンドレットを使用して診断ログを有効にして取得する方法を示しています。

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>ストレージ アカウントでの診断ログの有効化

ストレージ アカウントで診断ログを有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## <a name="consuming-logs-from-storage"></a>ストレージからのログの使用
このセクションでは、CDN コア分析のスキーマと、これが Azure ストレージ アカウントの内部でどのように編成されているかについて説明し、ログを CSV ファイルにダウンロードするためのサンプル コードを提供します。

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラーの使用
Azure ストレージ アカウントからコア分析データにアクセスするには、最初にストレージ アカウントの内容にアクセスするためのツールが必要です。 市場にはいくつかのツールがありますが、お勧めするツールは Microsoft Azure ストレージ エクスプローラーです。 このツールは、[こちら](http://storageexplorer.com/)からダウンロードできます。 ソフトウェアをダウンロードしてインストールしたら、CDN 診断ログの保存先として構成したのと同じ Azure ストレージ アカウントを使用するように構成してください。

1.    **Microsoft Azure ストレージ エクスプローラー**を開きます
2.    ストレージ アカウントを見つけます
3.    このストレージ アカウントの下にある **“BLOB コンテナー”** ノードに移動して、ノードを展開します
4.    **“insights-logs-coreanalytics”** という名前のコンテナーを選択して、ダブルクリックします
5.    最初のレベルが **“resourceId=”** などで始まる結果が右側のウィンドウに表示されます。 **PT1H.json** というファイルが表示されるまでクリックを続けます。 パスの説明については、下記の注を参照してください。
6.    BLOB の **PT1H.json** はそれぞれ特定の CDN エンドポイントまたはそのカスタム ドメインの 1 時間分の分析ログを表します。
7.    この JSON ファイルの内容のスキーマについては、「コア分析ログのスキーマ」のセクションで説明しています


> [!NOTE]
> **BLOB パスの形式**
> 
> コア分析のログは 1 時間ごとに生成されます。 1 時間以内のすべてのデータが収集され、JSON ペイロードとして 1 つの Azure BLOB 内に格納されます。 階層構造がある場合は、この Azure BLOB へのパスが表示されます。 これは、ストレージ エクスプローラー ツールが '/' をディレクトリの区切り記号と見なし、利便性のために階層を表示するためです。 実際には、パス全体が BLOB 名を表します。 BLOB のこの名前は次の名前付け規則に従っています    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**フィールドの説明:**

|値|description|
|-------|---------|
|サブスクリプション ID    |Azure サブスクリプションの ID。 GUID 形式が使用されています。|
|リソース |グループ名。CDN のリソースが属するリソース グループの名前。|
|プロファイル名 |CDN プロファイルの名前|
|エンドポイント名 |CDN エンドポイントの名前|
|Year|    年の 4 桁表記 (例: 2017)|
|月|    月の 2 桁表記。 01=1 月、 12=12 月|
|日|    日付の 2 桁表記|
|PT1H.json|    分析データが格納されている実際の JSON ファイル|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>CSV ファイルへのコア分析データのエクスポート

コア分析にアクセスしやすくするため、ツールのサンプル コードを提供しています。これにより、JSON ファイルをコンマ区切り形式のフラット ファイルにダウンロードし、これを使用してグラフやその他の集計を簡単に作成できるようになります。

このツールを使用する方法を次に示します。

1.    GitHub のリンクを参照します: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    コードのダウンロード
3.    指示に従ってコンパイルして構成します
4.    ツールを実行します
5.    生成される CSV ファイルには単純なフラット階層の分析データが示されています。

## <a name="diagnostic-logs-types"></a>診断ログの種類

CDN の POP/エッジからわかるように、現在のところ、HTTP 応答の統計とエグレスの統計を示すメトリックを含むコア分析ログしか提供していません。 今後、ログの種類は追加される予定です。

### <a name="core-analytics-metrics-details"></a>コア分析メトリックの詳細
コア分析ログで使用可能なメトリックの一覧を以下に示します。 プロバイダーによっては使用できないメトリックがありますが、違いはほとんどありません。 以下の表は、そのメトリックがプロバイダーで使用できるかどうかも示しています。 メトリックを使用できるのは、トラフィックが存在する CDN エンドポイントに対してのみであることに注意してください。


|メトリック                     | Description   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |この期間中にヒットした要求の合計数| あり  |あり   |
| RequestCountHttpStatus2xx |2xx の HTTP コード (200、202 など) になった要求の合計数              | あり  |あり   |
| RequestCountHttpStatus3xx | 3xx の HTTP コード (300、302 など) になった要求の合計数              | あり  |あり   |
| RequestCountHttpStatus4xx |4xx の HTTP コード (400、404 など) になった要求の合計数               | あり   |あり   |
| RequestCountHttpStatus5xx | 5xx の HTTP コード (500、504 など) になった要求の合計数              | あり  |あり   |
| RequestCountHttpStatusOthers |  その他のすべての HTTP コード (2xx-5xx 以外) の数 | あり  |あり   |
| RequestCountHttpStatus200 | HTTP コード応答 200 になった要求の合計数              |いいえ   |あり   |
| RequestCountHttpStatus206 | HTTP コード応答 206 になった要求の合計数              |いいえ   |あり   |
| RequestCountHttpStatus302 | HTTP コード応答 302 になった要求の合計数              |いいえ   |あり   |
| RequestCountHttpStatus304 |  HTTP コード応答 304 になった要求の合計数             |いいえ   |あり   |
| RequestCountHttpStatus404 | HTTP コード応答 404 になった要求の合計数              |いいえ   |あり   |
| RequestCountCacheHit |キャッシュ ヒットが生じた要求の合計数。 これは、POP からクライアントに対して、アセットが直接処理されたことを意味します。               | あり  |いいえ   |
| RequestCountCacheMiss | キャッシュ ミスが生じた要求の合計数。 これは、クライアントに最も近い POP でアセットが見つからなかったため、配信元から取得されたことを意味します。              |あり   | いいえ  |
| RequestCountCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットに対する要求の合計数。              |あり   | いいえ  |
| RequestCountCacheUncacheable | アセットの Cache-Control および Expires ヘッダーによりキャッシュされなかったアセットへの要求の合計数。この場合、POP または HTTP クライアントではキャッシュすべきでないことを示します                |あり   |いいえ   |
| RequestCountCacheOthers | 上記に含まれないキャッシュの状態の要求の合計数。              |あり   | いいえ  |
| EgressTotal | 送信データ転送 (GB)              |あり   |あり   |
| EgressHttpStatus2xx | HTTP 状態コードが 2xx の応答の送信データ転送* (GB)            |あり   |いいえ   |
| EgressHttpStatus3xx | HTTP 状態コードが 3xx の応答の送信データ転送 (GB)              |あり   |いいえ   |
| EgressHttpStatus4xx | HTTP 状態コードが 4xx の応答の送信データ転送 (GB)               |あり   | いいえ  |
| EgressHttpStatus5xx | HTTP 状態コードが 5xx の応答の送信データ転送 (GB)               |あり   |  いいえ |
| EgressHttpStatusOthers | その他の HTTP 状態コードの応答の送信データ転送 (GB)                |あり   |いいえ   |
| EgressCacheHit |  CDN の POP/エッジで CDN キャッシュから直接配信された応答の送信データ転送    |あり   |  いいえ |
| EgressCacheMiss | 最も近い POP サーバーで見つからず、配信元サーバーから取得された応答の送信データ転送              |あり   |  いいえ |
| EgressCacheNoCache | エッジでのユーザーの構成が原因でキャッシュされなかったアセットの送信データ転送。                |あり   |いいえ   |
| EgressCacheUncacheable | アセットの Cache-Control および/または Expires ヘッダーによりキャッシュされなかったアセットの送信データ転送。この場合、POP または HTTP クライアントではキャッシュすべきでないことを示します                    |あり   | いいえ  |
| EgressCacheOthers |  その他のキャッシュ シナリオの送信データ転送。             |あり   | いいえ  |

*送信データ転送は、CDN の POP サーバーからクライアントに配信されたトラフィックを指します。


### <a name="schema-of-the-core-analytics-logs"></a>コア分析ログのスキーマ 

すべてのログが JSON 形式で格納され、各エントリには次のスキーマの文字列フィールドが含まれます。

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

ここで 'time' とは、統計情報を報告する時間の境界の開始時刻を表します。 メトリックが CDN プロバイダーによってサポートされていない場合、double または整数の値の代わりに null 値が設定されることに注意してください。 この null 値は、メトリックがないことを示し、0 の値とは異なります。 エンドポイントに構成されたドメインごとにこれらのメトリックのセットが 1 つあることにも注意してください。

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


