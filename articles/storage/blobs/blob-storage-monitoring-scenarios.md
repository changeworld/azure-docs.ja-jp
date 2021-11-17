---
title: Azure Blob Storage の監視に関するベスト プラクティス
description: メトリックとログを使用して Azure Blob Storage を監視する際のベスト プラクティスのガイドラインとその使用方法について説明します。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 07/30/2021
ms.custom: monitoring
ms.openlocfilehash: 222c78803e268cef95dc322952874f58346c2919
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180167"
---
# <a name="best-practices-for-monitoring-azure-blob-storage"></a>Azure Blob Storage の監視に関するベスト プラクティス

この記事では、一般的なストレージ監視シナリオのコレクションを取り上げ、それらを実現するためのベスト プラクティスのガイドラインを提供します。

## <a name="identify-storage-accounts-with-no-or-low-use"></a>使用率がゼロまたは低いストレージ アカウントを特定する

Storage Insights は、Azure Storage のメトリックとログの上にあるダッシュボードです。 Storage Insights を使用すると、すべてのアカウントのトランザクション ボリュームと使用済み容量を調べることができます。 その情報は、廃止するアカウントを決定するのに役立ちます。 Storage Insights を構成するには、「[Azure Monitor Storage 分析情報を使用したストレージ サービスの監視](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)」を参照してください。

### <a name="analyze-transaction-volume"></a>トランザクション ボリュームを分析する

[Azure Monitor の Storage Insights ビュー](../common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json#view-from-azure-monitor)から、 **[トランザクション]** 列を使用してアカウントを昇順に並べ替えます。 次の画像は、指定した期間にトランザクション ボリュームが少ないアカウントを示しています。

> [!div class="mx-imgBorder"]
> ![Storage Insights でのトランザクション ボリューム](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-volume.png)

これらのトランザクションの詳細については、アカウントのリンクをクリックしてください。 この例では、ほとんどの要求が Blob Storage サービスに対して行われています。

> [!div class="mx-imgBorder"]
> ![サービスの種類別トランザクション](./media/blob-storage-monitoring-scenarios/storage-insights-transactions-by-storage-type.png)

行われている要求の種類を判別するには、**API 名別トランザクション** グラフの詳細を調べます。

> [!div class="mx-imgBorder"]
> ![ストレージ トランザクション API](./media/blob-storage-monitoring-scenarios/storage-insights-transaction-apis.png)

この例では、要求はすべて、アカウント プロパティ情報に対するリスト操作または要求です。 読み取りおよび書き込みトランザクションはありません。 このことから、このアカウントがあまり使用されていないと想像できます。

### <a name="analyze-used-capacity"></a>使用済み容量を分析する

[Azure Monitor の Storage Insights ビュー](../common/storage-insights-overview.md#view-from-azure-monitor)の **[容量]** タブから、 **[アカウントの使用済み容量]** 列を使用して、アカウントを昇順に並べ替えます。 次の画像は、他のアカウントよりも容量ボリュームが少ないアカウントを示しています。

> [!div class="mx-imgBorder"]
> ![使用済みストレージ容量](./media/blob-storage-monitoring-scenarios/storage-insights-capacity-used.png)

この使用済み容量に関連する BLOB を調べるには、Storage Explorer を使用します。 BLOB の数が多い場合は、[BLOB インベントリ ポリシー](blob-inventory.md)を使用してレポートを生成することを検討してください。

## <a name="monitor-the-use-of-a-container"></a>コンテナーの使用を監視する

顧客のデータをコンテナーごとにパーティション分割すると、各顧客によって使用されている容量を監視できます。 Azure Storage の BLOB インベントリを使用すると、サイズ情報を含む BLOB のインベントリを取得できます。 次に、コンテナー レベルでサイズと数を集計できます。 例については、「[Azure Storage インベントリを使用してコンテナーあたりの BLOB の数と合計サイズを計算する](calculate-blob-count-size.md)」を参照してください。

また、ログに対してクエリを実行して、コンテナー レベルでトラフィックを評価することもできます。 Log Analytic クエリの記述について詳しくは、[Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md) に関するページを参照してください。 ストレージ ログ スキーマの詳細については、[Azure Blob Storage の監視データのリファレンス](monitor-blob-storage-reference.md#resource-logs-preview)に関するページを参照してください。

各コンテナーで読み取られたトランザクションの数と読み取られたバイト数を取得するクエリを次に示します。

```kusto
StorageBlobLogs
| where OperationName  == "GetBlob"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize ReadSize = sum(ResponseBodySize), ReadCount = count() by tostring(ContainerName)
```

次のクエリでは、同様のクエリを使用して、書き込み操作に関する情報を取得します。

```kusto
StorageBlobLogs
| where OperationName == "PutBlob" or
  OperationName == "PutBlock" or
  OperationName == "PutBlockList" or
  OperationName == "AppendBlock" or
  OperationName == "SnapshotBlob" or
  OperationName == "CopyBlob" or
  OperationName == "SetBlobTier"
| extend ContainerName = split(parse_url(Uri).Path, "/")[1]
| summarize WriteSize = sum(RequestBodySize), WriteCount = count() by tostring(ContainerName)
```

上記のクエリでは、複数の種類の操作を書き込み操作としてカウントできるため、複数の操作の名前が参照されています。 どの操作が読み取りおよび書き込み操作と見なされるかについては、「[Azure Blob Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」または「[Azure Data Lake Storage の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)」を参照してください。

## <a name="audit-account-activity"></a>アカウントのアクティビティを監査する

多くの場合、セキュリティとコンプライアンスのためにストレージ アカウントのアクティビティを監査する必要があります。 ストレージ アカウントの操作は、"*コントロール プレーン*" と "*データ プレーン*" の 2 つのカテゴリに分類されます。

コントロール プレーン操作は、ストレージ アカウントを作成したり既存のストレージ アカウントのプロパティを更新したりする Azure Resource Manager 要求です。 詳細については、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) に関するページを参照してください。

データ プレーン操作は、ストレージ サービス エンドポイントへの要求の結果として得られたストレージ アカウント内のデータに対する操作です。 たとえば、ストレージ アカウントに BLOB をアップロードしたり、ストレージ アカウントから BLOB をダウンロードしたりするときに、データ プレーン操作が実行されます。 詳細については、[Azure Storage API](/rest/api/storageservices/) に関するページを参照してください。

このセクションでは、コントロールおよびデータ プレーン操作の "いつ"、"誰が"、"何を"、"どのように" という情報を特定する方法を示します。

### <a name="auditing-control-plane-operations"></a>コントロール プレーン操作の監査

Resource Manager の操作は、[Azure アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)に取り込まれます。 アクティビティ ログを表示するには、Azure portal でストレージ アカウントを開き、 **[アクティビティ ログ]** を選択します。

> [!div class="mx-imgBorder"]
> ![アクティビティ ログ](./media/blob-storage-monitoring-scenarios/activity-log.png)

任意のログ エントリを開いて、アクティビティが記述されている JSON を表示します。 次の JSON では、コントロール プレーン操作の "いつ"、"何を"、"どのように" という情報が示されています。

> [!div class="mx-imgBorder"]
> ![アクティビティ ログ JSON](./media/blob-storage-monitoring-scenarios/activity-log-json.png)

"誰が" という情報の可用性は、コントロール プレーン操作の実行に使用された認証方法によって決まります。 認可が Azure AD セキュリティ プリンシパルによって実行された場合は、そのセキュリティ プリンシパルのオブジェクト識別子もこの JSON 出力に表示されます (例: `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`)。 メール アドレスや名前など、他の ID 関連情報は必ずしも表示されるとは限らないので、セキュリティ プリンシパルを一意に識別するには、オブジェクト識別子が常に最善の方法です。

そのセキュリティ プリンシパルのフレンドリ名を見つけるには、オブジェクト識別子の値を取得し、Azure portal の Azure AD ページでセキュリティ プリンシパルを検索します。 次のスクリーンショットは、Azure AD での検索結果を示しています。

> [!div class="mx-imgBorder"]
> ![Azure Active Directory を検索する](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>データ プレーン操作の監査

データ プレーン操作は、[Storage の Azure リソース ログ](monitor-blob-storage.md#analyzing-logs)に取り込まれます。 ネイティブ クエリ エクスペリエンスを得るために、ログを Log Analytics ワークスペースにエクスポートするように[診断設定を構成](monitor-blob-storage.md#send-logs-to-azure-log-analytics)できます。

ログ エントリの一覧から "いつ"、"誰が"、"何を"、"どのように" という情報を取得する Log Analytics クエリを次に示します。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

監査の "いつ" 部分については、`TimeGenerated` フィールドに、ログ エントリがいつ記録されたかが表示されます。

監査の "何を" 部分については、`Uri` フィールドに、項目が変更または読み取られたことが表示されます。

監査の "どのように" 部分については、`OperationName` フィールドに、実行された操作が表示されます。

監査の "誰が" 部分については、`AuthenticationType` に、要求を行うために使用された認証の種類が表示されます。 このフィールドには、アカウント キー、SAS トークン、または Azure Active Directory (Azure AD) 認証の使用など、Azure Storage でサポートされているすべての種類の認証を表示できます。

#### <a name="identifying-the-security-principal-used-to-authorize-a-request"></a>要求の承認に使用されるセキュリティ プリンシパルの特定

要求が Azure AD を使用して認証された場合、`RequesterObjectId` フィールドには、セキュリティ プリンシパルを識別する最も信頼性の高い方法が示されます。 そのセキュリティ プリンシパルのフレンドリ名を見つけるには、`RequesterObjectId` フィールドの値を取得し、Azure portal の Azure AD ページでセキュリティ プリンシパルを検索します。 次のスクリーンショットは、Azure AD での検索結果を示しています。

> [!div class="mx-imgBorder"]
> ![Azure Active Directory を検索する](./media/blob-storage-monitoring-scenarios/search-azure-active-directory.png)

場合によっては、ユーザー プリンシパル名 (*UPN*) がログに表示されることがあります。 たとえば、セキュリティ プリンシパルが Azure AD ユーザーの場合は、UPN が表示される可能性が高くなります。 ユーザー割り当てマネージド ID などの他の種類のセキュリティ プリンシパルの場合や、Azure AD テナント間認証などの特定のシナリオでは、UPN はログに表示されません。

このクエリでは、OAuth セキュリティ プリンシパルによって実行されたすべての読み取り操作が表示されます。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "GetBlob"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

共有キーと SAS 認証では、個々の ID を監査する手段は提供されません。 したがって、ID に基づく監査能力を向上させたい場合は、Azure AD に移行し、共有キーと SAS の認証を禁止することをお勧めします。 共有キーと SAS 認証を禁止する方法については、「[Azure Storage アカウントの共有キーによる承認を禁止する](../common/shared-key-authorization-prevent.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=portal)」を参照してください。 Azure AD の使用を開始するには、[Azure Active Directory を使用した BLOB へのアクセスの承認](authorize-access-azure-active-directory.md)に関するページを参照してください。

#### <a name="identifying-the-sas-token-used-to-authorize-a-request"></a>要求の承認に使用される SAS トークンの特定

SAS トークンを使用して承認された操作を照会できます。 たとえば、次のクエリは、SAS トークンを使用して承認されたすべての書き込み操作を返します。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutBlob"
  and AuthenticationType == "SAS"
| project TimeGenerated, AuthenticationType, AuthenticationHash, OperationName, Uri
```

セキュリティ上の理由から、SAS トークンはログには表示されません。 ただし、SAS トークンの SHA-256 ハッシュは、このクエリによって返される `AuthenticationHash` フィールドに表示されます。 

複数の SAS トークンを分散していて、どの SAS トークンが使用されているかを知る必要がある場合は、各 SAS トークンを SHA-256 ハッシュに変換し、ログに表示されるハッシュ値とそのハッシュを比較する必要があります。

最初に各 SAS トークン文字列をデコードします。 次の例では、PowerShell を使用して SAS トークン文字列をデコードします。

```powershell
[uri]::UnescapeDataString("<SAS token goes here>")
```

その後、その文字列を [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) PowerShell コマンドレットに渡すことができます。 サンプルについては、「[例 4: 文字列のハッシュを計算する](/powershell/module/microsoft.powershell.utility/get-filehash#example-4--compute-the-hash-of-a-string)」を参照してください。

または、デコードされた文字列を Azure Data Explorer を使用するクエリの一部として [hash_sha256()](/azure/data-explorer/kusto/query/sha256hashfunction) 関数に渡すこともできます。

SAS トークンには ID 情報は含まれません。 ユーザーまたは組織のアクティビティを追跡する 1 つの方法は、ユーザーまたは組織のさまざまな SAS トークン ハッシュへのマッ​​ピングを維持することです。

## <a name="optimize-cost-for-infrequent-queries"></a>頻度の低いクエリのコストを最適化する

Log Analytics にログをエクスポートすることで、豊富なネイティブ クエリ機能を利用できます。 ストレージ アカウントに大量のトランザクションがある場合、Log Analytics でログを使用するコストが高くなる可能性があります。 詳細については、[Azure Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)に関するページを参照してください。 ログに対してたまにしかクエリを実行しない場合 (コンプライアンス監査のためにログに対してクエリを実行するなど)、ログをストレージ アカウントにエクスポートしてから、ログ データに対してサーバーレス クエリ ソリューション (Azure Synapse など) を使用することによって、合計コストの削減を検討することができます。

Azure Synapse を使用すると、サーバーレス SQL プールを作成して、必要に応じてログ データに対してクエリを実行できます。 これによってコストを大幅に削減できる可能性があります。

1. ログをストレージ アカウントにエクスポートします。 詳細については、「[診断設定の作成](monitor-blob-storage.md#creating-a-diagnostic-setting)」を参照してください。

2. Synapse ワークスペースを作成して構成します。 詳細については、「[クイックスタート: Synapse ワークスペースを作成する](../../synapse-analytics/quickstart-create-workspace.md)」を参照してください。

2. ログに対してクエリを実行します。 詳細については、「[Azure Synapse Analytics でサーバーレス SQL プールを使用して JSON ファイルのクエリを実行する](../../synapse-analytics/sql/query-json-files.md)」を参照してください。

   次に例を示します。

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>関連項目

- [Azure Blob Storage の監視](monitor-blob-storage.md)。
- [Azure Blob Storage 監視データのリファレンス](monitor-blob-storage-reference.md)
- [チュートリアル: Azure Data Explorer と Azure Monitor で Kusto クエリを使用する](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。
- [Azure Monitor でログ クエリの使用を開始する](../../azure-monitor/logs/get-started-queries.md)。
