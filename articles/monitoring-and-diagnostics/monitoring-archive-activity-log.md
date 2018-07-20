---
title: Azure アクティビティ ログのアーカイブ
description: ストレージ アカウントの長期保持に関する Azure アクティビティ ログをアーカイブします。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: a519cd242b88916d1a11df47c0b7450594848ef5
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920551"
---
# <a name="archive-the-azure-activity-log"></a>Azure アクティビティ ログのアーカイブ
この記事では、Azure Portal、PowerShell コマンドレット、またはクロス プラットフォーム CLI を使用して、ストレージ アカウントで [**Azure アクティビティ ログ**](monitoring-overview-activity-logs.md)をアーカイブする方法について説明します。 このオプションは、監査、静的分析、またはバックアップに対して (保持ポリシーを完全に制御して) 90 日よりも長いアクティビティ ログを保持する場合に便利です。 90 日以下でイベントを保持する必要があるだけの場合は、ストレージ アカウントにアーカイブを設定する必要はありません。アーカイブを有効にしなければ、アクティビティ ログのイベントは Azure プラットフォームに90 日間保持されるためです。

> [!WARNING]
> ストレージ アカウント内のログ データの形式は、2018 年 11 月 1 日より JSON Lines に変更されます。 [この記事では、この変更による影響と、新しい形式に対応するツールに更新する方法について説明します。](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>前提条件
開始する前に、アクティビティ ログのアーカイブ先の[ストレージ アカウントを作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)する必要があります。 既存のストレージ アカウントを使用しないことを強くお勧めします。既存のストレージ アカウントには、監視データへのアクセスをさらに制御するために保存されている他の非監視データがあります。 ただし、診断ログとメトリックもストレージ アカウントにアーカイブする場合は、中央の場所にすべての監視データを保持するために、アクティビティ ログのそのストレージ アカウントも使用するのが適切であることがあります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、ストレージ アカウントは、ログを出力するのと同じサブスクリプションに属している必要はありません。

> [!NOTE]
>  現在、セキュリティで保護された仮想ネットワークの背後にあるストレージ アカウントにデータをアーカイブすることはできません。

## <a name="log-profile"></a>ログ プロファイル
以下の方法のいずれかを使用して、アクティビティ ログをアーカイブするには、サブスクリプションに **ログ プロファイル** を設定します。 ログ プロファイルは、保存またはストリーミングされたイベントの種類と、ストレージ アカウントまたはイベント ハブの出力の種類を定義します。 また、ストレージ アカウントに格納されたイベントの保持ポリシー (保持する日数) も定義します。 保持ポリシーが 0 に設定されている場合は、イベントが無制限に保存されます。 それ以外の場合は、1 ～ 2,147, 483,647 の範囲の任意の値に設定できます。 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。 削除プロセスは午前 0 時 (UTC) に開始されますが、ストレージ アカウントからのログの削除には最大で 24 時間かかる可能性があるので注意してください。 [ログ プロファイルの詳細については、こちらを参照してください](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)。 

## <a name="archive-the-activity-log-using-the-portal"></a>ポータルを使用したアクティビティ ログのアーカイブ
1. ポータルで、左側のナビゲーションの **[アクティビティ ログ]** リンクをクリックします。 アクティビティ ログのリンクが表示されない場合は、最初に **[すべてのサービス]** リンクをクリックします。
   
    ![[アクティビティ ログ] ブレードに移動します。](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. ブレードの上部にある、 **[エクスポート]** をクリックします。
   
    ![[エクスポート] ボタンをクリックします。](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. 表示されるブレードで、 **[Export to a storage account (ストレージ アカウントへのエクスポート)]** チェック ボックスをオンにし、ストレージ アカウントを選択します。
   
    ![ストレージ アカウントを設定します。](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. スライダーまたはテキスト ボックスを使用して、アクティビティ ログのイベントをストレージ アカウント内で保持する必要がある日数 (0 から 365) を定義します。 データがストレージ アカウントに無期限に保持されるようにする場合は、この数を 0 に設定します。 365 以上の日数を入力する必要がある場合は、以下に示す PowerShell または CLI による方式を使用します。
5. **[Save]** をクリックします。

## <a name="archive-the-activity-log-via-powershell"></a>PowerShell を使用したアクティビティ ログのアーカイブ

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| StorageAccountId |[はい] |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| Locations |[はい] |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 `(Get-AzureRmLocation).Location` を使って、サブスクリプションのすべてのリージョンの一覧を見ることができます。 |
| RetentionInDays |いいえ  |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が 0 の場合、ログは無期限に (いつまでも) 保存されます。 |
| カテゴリ |いいえ  |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。  指定しないと、すべての可能な値と見なされます |

## <a name="archive-the-activity-log-via-cli"></a>CLI を使用したアクティビティ ログのアーカイブ

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| name |[はい] |ログ プロファイルの名前。 |
| storage-account-id |[はい] |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| locations |[はい] |アクティビティ ログ イベントを収集するリージョンのスペース区切りリスト。 `az account list-locations --query [].name` を使って、サブスクリプションのすべてのリージョンの一覧を見ることができます。 |
| days |[はい] |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が 0 の場合、ログは無期限に (いつまでも) 保存されます。  0 の場合は、enabled パラメーターを true に設定する必要があります。 |
|enabled | [はい] |True または False。  アイテム保持ポリシーを有効または無効にするために使います。  True の場合は、days パラメーターを 0 より大きい値にする必要があります。
| categories |[はい] |収集するイベント カテゴリのスペース区切りリスト。 指定できる値は、Write、Delete、Action です。 |

## <a name="storage-schema-of-the-activity-log"></a>アクティビティ ログのストレージ スキーマ
アーカイブの設定後、アクティビティ ログ イベントが発生するとすぐに、ストレージ コンテナーは、ストレージ アカウントに作成されます。 コンテナー内の BLOB は、次に示すようにアクティビティ ログおよび診断ログで同じ名前付け規則に従います。

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、BLOB の名前は次のようになります。

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

各 PT1H.json BLOB には、BLOB の URL で指定された時間内に発生したイベントの JSON BLOB が含まれます (例: h = 12)。 現在の時間内にイベントが発生すると、PT1H.json ファイルにイベントが追加されます。 分の値 (m = 00) は常に 00 です。アクティビティ ログ イベントが個々の BLOB に 1 時間ごとに分類されるためです。

PT1H.json ファイル内では、各イベントは、この形式に従って “レコード” 配列で保存されます。

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| 要素名 | 説明 |
| --- | --- |
| time |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| resourceId |影響を受けるリソースのリソース ID。 |
| operationName |操作の名前。 |
| category |アクションのカテゴリ。例:  Write、Read、Action |
| resultType |結果の種類。例:  Success、Failure、Start |
| resultSignature |リソースの種類によって異なります。 |
| durationMs |操作時間 (ミリ秒) |
| callerIpAddress |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの IP アドレス。 |
| correlationId |通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| identity |承認および要求を記述する JSON BLOB。 |
| authorization |イベントの RBAC プロパティの BLOB。 通常は、"action"、"role"、"scope" の各プロパティが含まれます。 |
| level |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| location |発生した場所のリージョン (またはグローバル)。 |
| properties |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |

> [!NOTE]
> プロパティとそのプロパティの使用方法については、リソースによって異なることがあります。
> 
> 

## <a name="next-steps"></a>次の手順
* [分析のための BLOB のダウンロード](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [アクティビティ ログの Event Hubs へのストリーム](monitoring-stream-activity-logs-event-hubs.md)
* [詳細については、アクティビティ ログに関するセクションをご覧ください](monitoring-overview-activity-logs.md)

