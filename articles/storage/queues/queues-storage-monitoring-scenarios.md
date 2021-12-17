---
title: Azure Queue Storage の監視に関するベスト プラクティス
description: メトリックとログを使用して Azure Queue Storage を監視する際のベスト プラクティスのガイドラインとその利用方法について説明します。
author: normesta
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.author: normesta
ms.date: 08/24/2021
ms.custom: monitoring
ms.openlocfilehash: e7344330740bebc41a49547c7602a852b5f7665f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868722"
---
# <a name="best-practices-for-monitoring-azure-queue-storage"></a>Azure Queue Storage の監視に関するベスト プラクティス

この記事では、一般的な Queue Storage 監視シナリオのコレクションを取り上げ、それらを実現するためのベスト プラクティスのガイドラインを提供します。  

## <a name="monitor-message-counts-in-each-queue"></a>各キューのメッセージ数を監視する 

`QueueMessageCount` メトリックを使用して、ストレージ アカウント内のすべてのキューのメッセージ数を監視できます。 このメトリックは毎日更新されます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用している場合は、次のようなコマンドを使用できます。  

```powershell
(Get-AzMetric -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/queueServices/default -MetricName "QueueMessageCount").data.Average
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用している場合は、次のようなコマンドを使用できます。 

```azurecli
az monitor metrics list --resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/ --metric "QueueMessageCount" --interval PT1H --query value[0].timeseries[0].data[0].average
```

---

メッセージ ボリュームを処理するためにワークロードを調整するかどうかを動的に決定する必要がある場合は、各キューのおおよそのメッセージ数に対してクエリを実行し、適切なアクションで応答できます。 [Get Queue Metadata](/rest/api/storageservices/get-queue-metadata) REST 操作を使用するか、サポートされている任意の BLOB ストレージ SDK を使用して、おおよそのメッセージ数を取得します。 

次の例では、Azure Storage .NET v12 ライブラリを使用して、おおよそのメッセージ数を取得します。

```csharp
        static async Task<string> RetrieveNextMessageAsync(QueueClient theQueue)
        {
            if (await theQueue.ExistsAsync())
            {
                QueueProperties properties = await theQueue.GetPropertiesAsync();

                if (properties.ApproximateMessagesCount > 0)
                {
                    QueueMessage[] retrievedMessage = await theQueue.ReceiveMessagesAsync(1);
                    string theMessage = retrievedMessage[0].MessageText;
                    await theQueue.DeleteMessageAsync(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
                    return theMessage;
                }

                return null;
            }

            return null;
        }
```

また、エンティティごとのメッセージをサポートする Service Bus の使用も検討してください。 詳細については、「[Azure Service Bus の監視データのリファレンス](../../service-bus-messaging/monitor-service-bus-reference.md)」を参照してください。 

## <a name="audit-account-activity"></a>アカウントのアクティビティを監査する

多くの場合、セキュリティとコンプライアンスのためにストレージ アカウントのアクティビティを監査する必要があります。 ストレージ アカウントの操作は、"*コントロール プレーン*" と "*データ プレーン*" の 2 つのカテゴリに分類されます。 

コントロール プレーン操作は、ストレージ アカウントを作成したり既存のストレージ アカウントのプロパティを更新したりする Azure Resource Manager 要求です。 詳細については、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) に関するページを参照してください。 

データ プレーン操作は、ストレージ サービス エンドポイントへの要求の結果として得られたストレージ アカウント内のデータに対する操作です。 たとえば、キューにメッセージを追加すると、データ プレーン操作が実行されます。 詳細については、[Azure Storage API](/rest/api/storageservices/) に関するページを参照してください。 

このセクションでは、コントロールおよびデータ プレーン操作の "いつ"、"誰が"、"何を"、"どのように" という情報を特定する方法を示します。

### <a name="auditing-control-plane-operations"></a>コントロール プレーン操作の監査

Resource Manager の操作は、[Azure アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)に取り込まれます。 アクティビティ ログを表示するには、Azure portal でストレージ アカウントを開き、 **[アクティビティ ログ]** を選択します。

> [!div class="mx-imgBorder"]
> ![アクティビティ ログ](./media/queues-storage-monitoring-scenarios/activity-log.png)


任意のログ エントリを開いて、アクティビティが記述されている JSON を表示します。 次の JSON では、コントロール プレーン操作の "いつ"、"何を"、"どのように" という情報が示されています。

> [!div class="mx-imgBorder"]
> ![アクティビティ ログ JSON](./media/queues-storage-monitoring-scenarios/activity-log-json.png)

"誰が" という情報の可用性は、コントロール プレーン操作の実行に使用された認証方法によって決まります。 認可が Azure AD セキュリティ プリンシパルによって実行された場合は、そのセキュリティ プリンシパルのオブジェクト識別子もこの JSON 出力に表示されます (例: `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`)。 メール アドレスや名前など、他の ID 関連情報は必ずしも表示されるとは限らないので、セキュリティ プリンシパルを一意に識別するには、オブジェクト識別子が常に最善の方法です。 

そのセキュリティ プリンシパルのフレンドリ名を見つけるには、オブジェクト識別子の値を取得し、Azure portal の Azure AD ページでセキュリティ プリンシパルを検索します。 次のスクリーンショットは、Azure AD での検索結果を示しています。

> [!div class="mx-imgBorder"]
> ![Azure Active Directory を検索する](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>データ プレーン操作の監査

データ プレーン操作は、[Storage の Azure リソース ログ](monitor-queue-storage.md#analyzing-logs)に取り込まれます。 ネイティブ クエリ エクスペリエンスを得るために、ログを Log Analytics ワークスペースにエクスポートするように[診断設定を構成](monitor-queue-storage.md#send-logs-to-azure-log-analytics)できます。 

ログ エントリの一覧から "いつ"、"誰が"、"何を"、"どのように" という情報を取得する Log Analytics クエリを次に示します。 

```kusto
StorageQueueLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

監査の "いつ" 部分については、`TimeGenerated` フィールドに、ログ エントリがいつ記録されたかが表示されます。 

監査の "何を" 部分については、`Uri` フィールドに、項目が変更または読み取られたことが表示されます。 

監査の "どのように" 部分については、`OperationName` フィールドに、実行された操作が表示されます。 

監査の "誰が" 部分については、`AuthenticationType` に、要求を行うために使用された認証の種類が表示されます。 このフィールドには、アカウント キー、SAS トークン、または Azure Active Directory (Azure AD) 認証の使用など、Azure Storage でサポートされているすべての種類の認証を表示できます。 

要求が Azure AD を使用して認証された場合、`RequesterObjectId` フィールドには、セキュリティ プリンシパルを識別する最も信頼性の高い方法が示されます。 そのセキュリティ プリンシパルのフレンドリ名を見つけるには、`RequesterObjectId` フィールドの値を取得し、Azure portal の Azure AD ページでセキュリティ プリンシパルを検索します。 次のスクリーンショットは、Azure AD での検索結果を示しています。

> [!div class="mx-imgBorder"]
> ![Azure Active Directory を検索する - 2](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

場合によっては、ユーザー プリンシパル名 (*UPN*) がログに表示されることがあります。 たとえば、セキュリティ プリンシパルが Azure AD ユーザーの場合は、UPN が表示される可能性が高くなります。 ユーザー割り当てマネージド ID などの他の種類のセキュリティ プリンシパルの場合や、Azure AD テナント間認証などの特定のシナリオでは、UPN はログに表示されません。 

このクエリでは、OAuth セキュリティ プリンシパルによって実行されたすべての書き込み操作が表示されます。

```kusto
StorageQueueLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutMessage"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

共有キーと SAS 認証では、個々の ID を監査する手段は提供されません。 したがって、ID に基づく監査能力を向上させたい場合は、Azure AD に移行し、共有キーと SAS の認証を禁止することをお勧めします。 共有キーと SAS 認証を禁止する方法については、「[Azure Storage アカウントの共有キーによる承認を禁止する](../common/shared-key-authorization-prevent.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json&tabs=portal)」を参照してください。 Azure AD の使用を開始するには、[Azure Active Directory を使用した BLOB へのアクセスの承認](authorize-access-azure-active-directory.md)に関するページを参照してください。

## <a name="optimize-cost-for-infrequent-queries"></a>頻度の低いクエリのコストを最適化する

Log Analytics にログをエクスポートすることで、豊富なネイティブ クエリ機能を利用できます。 ストレージ アカウントに大量のトランザクションがある場合、Log Analytics でログを使用するコストが高くなる可能性があります。 [Azure Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)に関するページを参照してください。 ログに対してたまにしかクエリを実行しない場合 (コンプライアンス監査のためにログに対してクエリを実行するなど)、ログをストレージ アカウントにエクスポートしてから、ログ データに対してサーバーレス クエリ ソリューション (Azure Synapse など) を使用することによって、合計コストの削減を検討することができます。

Azure Synapse を使用すると、サーバーレス SQL プールを作成して、必要に応じてログ データに対してクエリを実行できます。 これによってコストを大幅に削減できる可能性があります。 

1. ログをストレージ アカウントにエクスポートします。 「[診断設定の作成](monitor-queue-storage.md#creating-a-diagnostic-setting)」を参照してください。

2. Synapse ワークスペースを作成して構成します。 「[クイックスタート: Synapse ワークスペースを作成する](../../synapse-analytics/quickstart-create-workspace.md)」を参照してください。

2. ログに対してクエリを実行します。 「[Azure Synapse Analytics でサーバーレス SQL プールを使用して JSON ファイルのクエリを実行する](../../synapse-analytics/sql/query-json-files.md)」を参照してください。

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

- [Azure Queue Storage の監視](monitor-queue-storage.md)。
- [Azure Queue Storage 監視データのリファレンス](monitor-queue-storage-reference.md)
- [チュートリアル: Azure Data Explorer と Azure Monitor で Kusto クエリを使用する](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。
- [Azure Monitor でログ クエリの使用を開始する](../../azure-monitor/logs/get-started-queries.md)。

  

