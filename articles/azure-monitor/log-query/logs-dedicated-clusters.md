---
title: Azure Monitor ログ専用クラスター
description: 1 日に 1 TB を超える監視データを取り込むお客様は、共有クラスターではなく専用クラスターを使用できます
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: d2446e866c0e12d50a0759373682f4f62bc4bba0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512224"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor ログ専用クラスター

Azure Monitor ログ専用クラスターは、Azure Monitor ログのお客様向けの高度な機能を有効にするデプロイ オプションです。 専用クラスターを使用しているお客様は、これらのクラスターでホストされるワークスペースを選択できます。

専用クラスターを必要とする機能は次のとおりです。

- **[カスタマー マネージド キー](../platform/customer-managed-keys.md)** - お客様によって指定、管理されるキーを利用してクラスター データを暗号化します。
- **[Lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview)** - お客様は、Microsoft サポート エンジニアのデータ アクセス要求を制御できます。
- **[二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 この場合は、追加の暗号化レイヤーによって引き続きデータが保護されます。
- **[マルチワークスペース](../log-query/cross-workspace-query.md)** - お客様が運用に複数のワークスペースを使用している場合、専用クラスターの使用をお勧めします。 すべてのワークスペースが同じクラスター上にある場合は、クロスワークスペース クエリの実行速度が速くなります。 割り当てられた容量予約レベルですべてのクラスターのインジェストが考慮されるため、専用クラスターを使用する方がコスト効率が高くなる可能性もあります。その一部が小さく、容量予約の割引の対象になっていない場合でも、すべてのワークスペースに適用されます。

専用クラスターでは、1 日あたり少なくとも 1 TB のデータ インジェストの容量を使用してコミットを行う必要があります。 専用クラスターへの移行は簡単です。 データの損失やサービスの中断はありません。 

> [!IMPORTANT]
> 運用環境デプロイでは、専用クラスターが承認され、完全にサポートされます。 ただし、一時的な容量の制約があるため、この機能を使用するには事前登録が必要です。 連絡先を Microsoft に登録して、サブスクリプション ID を提供します。

## <a name="management"></a>管理 

専用クラスターは、Azure Monitor ログ クラスターを表す Azure リソースを介して管理されます。 すべての操作は、PowerShell または REST API を使用して、このリソースに対して行われます。

クラスターを作成したら、そのクラスターを構成し、ワークスペースにリンクすることができます。 ワークスペースがクラスターにリンクされている場合、ワークスペースに送信された新しいデータはクラスターに存在します。 クラスターにリンクできるのは、クラスターと同じリージョンにあるワークスペースだけです。 クラスターからワークスペースのリンクを解除できますが、いくつかの制限があります。 これらの制限事項の詳細についてもこの記事に記載されています。 

専用クラスターに取り込まれたデータは、2 回暗号化されます。Microsoft のマネージド キーまたは[カスタマー マネージド キー](../platform/customer-managed-keys.md)を使用してサービス レベルで一度暗号化され、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用してインフラストラクチャ レベルで一度暗号化されます。 [二重暗号化](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 この場合は、追加の暗号化レイヤーによって引き続きデータが保護されます。 専用クラスターを使用すると、[ロックボックス](../platform/customer-managed-keys.md#customer-lockbox-preview) コントロールを使用してデータを保護することもできます。

クラスター レベルでのすべての操作には、クラスターに対する `Microsoft.OperationalInsights/clusters/write` アクション権限が必要です。 このアクセス許可を付与するには、`*/write` アクションを含む所有者または共同作成者、または `Microsoft.OperationalInsights/*` アクションを含む Log Analytics 共同作成者ロールを使用します。 Log Analytics のアクセス許可の詳細については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](../platform/manage-access.md)」を参照してください。 


## <a name="cluster-pricing-model"></a>クラスターの価格モデル

Log Analytics 専用クラスターには、1000 GB/日以上の容量予約価格モデルを使用します。 予約レベルを超える使用量は、従量課金制で請求されます。  容量予約の価格情報については、[Azure Monitor の価格に関するページ]( https://azure.microsoft.com/pricing/details/monitor/)を参照してください。  

クラスター容量予約レベルは、`Sku` の下にある `Capacity` パラメーターを使用して、Azure Resource Manager でプログラムによって構成します。 `Capacity` は GB 単位で指定し、1000 GB/日以上の値を 100 GB/日の単位で設定できます。

クラスターでの使用については、2 つの課金モードがあります。 これらは、ご自分のクラスターを構成するときに、`billingType` パラメーターで指定できます。 

1. **クラスター**: この場合 (既定)、取り込まれたデータに対してクラスター レベルで課金されます。 クラスターに関連付けられている各ワークスペースが取り込んだデータ量が集計され、クラスターの日次請求が計算されます。 

2. **ワークスペース**: ご使用のクラスターの容量予約コストは、クラスター内のワークスペースに比例します (各ワークスペースに対する [Azure Security Center](../../security-center/index.yml) からのノードごとの割り当てを考慮した後)。

ワークスペースで従来のノードごとの価格レベルが使用されている場合、クラスターにリンクされると、クラスターの容量予約に対するデータ インジェストに基づいて課金され、ノードごとには課金されなくなります。 Azure Security Center からのノードごとのデータ割り当ては引き続き適用されます。

Log Analytics 専用クラスターの課金の詳細については、[こちら]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)をご覧ください。


## <a name="creating-a-cluster"></a>クラスターを作成する

最初にクラスター リソースを作成して、専用クラスターの作成を開始します。

次のプロパティを指定する必要があります。

- **ClusterName**:管理目的で使用されます。 この名前はユーザーには公開されません。
- **ResourceGroupName**:すべての Azure リソースと同様に、クラスターはリソース グループに属します。 クラスターは通常、組織内の多くのチームによって共有されるため、中央の IT リソース グループを使用することをお勧めします。 設計に関するその他の考慮事項については、「[Azure Monitor ログのデプロイの設計](../platform/design-logs-deployment.md)」を参照してください。
- **[場所]** :クラスターは特定の Azure リージョンにあります。 このクラスターにリンクできるのは、このリージョンにあるワークスペースだけです。
- **SkuCapacity**:"*クラスター*" リソースを作成するときに、"*容量予約*" レベル (sku) を指定する必要があります。 "*容量予約*" レベルは、1 日あたり 1,000 GB から 3,000 GB の範囲で指定できます。 必要に応じて、後から 100 刻みで更新できます。 1 日あたり 3,000 GB を超える容量予約レベルが必要な場合は、LAIngestionRate@microsoft.com までお問い合わせください。 クラスターのコストの詳細については、[Log Analytics クラスターのコストの管理](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)に関するページを参照してください。

"*クラスター*" リソースを作成した後、*sku*、*keyVaultProperties、*billingType* などの追加のプロパティを編集できます。 詳細については、以下を参照してください。

> [!WARNING]
> クラスターの作成によって、リソース割り当てとプロビジョニングがトリガーされます。 この操作が完了するまでに最大 1 時間かかります。 非同期的に実行することをお勧めします。

クラスターを作成するユーザー アカウントには、Azure リソースの作成に関する標準的なアクセス許可 (`Microsoft.Resources/deployments/*`) およびクラスターの書き込みアクセス許可 `(Microsoft.OperationalInsights/clusters/write)` が必要です。

### <a name="create"></a>作成 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Response*

200 OK とヘッダーである必要があります。

### <a name="check-provisioning-status"></a>プロビジョニング状態の確認

Log Analytics クラスターのプロビジョニングは、完了するまでに時間がかかります。 プロビジョニングの状態を確認するには、いくつかの方法があります。

- リソース グループ名を指定して Get-AzOperationalInsightsCluster PowerShell コマンドを実行し、ProvisioningState プロパティを確認します。 この値は、プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- 応答から Azure-AsyncOperation URL 値をコピーし、非同期操作と状態のチェックに従います。

- *クラスター* リソースに GET 要求を送信し、*provisioningState* 値を確認します。 この値は、プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Response**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

*principalId* GUID は、"*クラスター*" リソースのマネージド ID サービスによって生成されます。

## <a name="change-cluster-properties"></a>クラスターのプロパティを変更する

"*クラスター*" リソースを作成し、完全にプロビジョニングされたら、PowerShell または REST API を使用して、クラスター レベルで追加のプロパティを編集できます。 クラスターの作成時に使用できるプロパティ以外の追加のプロパティは、クラスターがプロビジョニングされた後でのみ設定できます。

- **keyVaultProperties**:[Azure Monitor のカスタマー マネージド キー](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure)をプロビジョニングするために使用する Azure Key Vault を構成するために使用します。 これには次のパラメーターが含まれています。*KeyVaultUri*、*KeyName*、*KeyVersion*。 
- **billingType** - *billingType* プロパティによって、"*クラスター*" リソースとそのデータの課金の帰属が決まります。
  - **クラスター** (既定) - クラスターの容量予約コストは、"*クラスター*" リソースに帰属します。
  - **ワークスペース** - クラスターの容量予約コストは、クラスター内のワークスペースに比例的に帰属します。その日に取り込まれた合計データが容量予約を下回る場合に使用量の一部が "*クラスター*" リソースに課金されます。 クラスターの価格モデルの詳細については、[Log Analytics 専用クラスター](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)に関するページを参照してください。 

> [!NOTE]
> *billingType* プロパティは、PowerShell ではサポートされていません。
> クラスター プロパティの更新は非同期的に実行され、完了するまでに時間がかかることがあります。

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> PATCH を使用して、*クラスター* リソース *sku*、*keyVaultProperties* または *billingType* を更新できます。

次に例を示します。 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" には Key Vault キー識別子の詳細が含まれています。

*Response*

200 OK とヘッダー

### <a name="check-cluster-update-status"></a>クラスターの更新状態を確認する

キー識別子の伝播が完了するまで数分かかります。 更新の状態を確認するには、次の 2 つの方法があります。

- 応答から Azure-AsyncOperation URL 値をコピーし、非同期操作と状態のチェックに従います。 

   OR

- *クラスター* リソースに GET 要求を送信し、*KeyVaultProperties* 値を確認します。 最近更新されたキー識別子の詳細が、応答で返されます。

   キー識別子の更新が完了すると、*クラスター* リソースの GET 要求に対する応答は次のようになります。

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principle-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "keyVaultProperties": {
         "keyVaultUri": "https://key-vault-name.vault.azure.net",
         "kyName": "key-name",
         "keyVersion": "current-version"
         },
        "provisioningState": "Succeeded",
       "billingType": "cluster",
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
  }
  ```

## <a name="link-a-workspace-to-the-cluster"></a>ワークスペースをクラスターにリンクする

ワークスペースが専用クラスターにリンクされている場合、ワークスペースに取り込まれた新しいデータは新しいクラスターにルーティングされ、既存のデータは既存のクラスターに残ります。 専用クラスターがカスタマー マネージド キー (CMK) を使用して暗号化されている場合は、新しいデータのみがキーで暗号化されます。 この違いはユーザーから分離されています。ユーザーは、システムがバックエンドでクラスター間クエリを実行している間、通常どおりにワークスペースを照会するだけです。

クラスターは最大 100 のワークスペースにリンクできます。 リンクされたワークスペースは、クラスターと同じリージョンにあります。 システムのバックエンドを保護し、データの断片化を避けるために、ワークスペースを 1 か月に 3 回以上クラスターにリンクすることはできません。

リンク操作を実行するには、ワークスペースと "*クラスター*" リソースの両方に対する次の "書き込み" アクセス許可を持っている必要があります。

- ワークスペース:*Microsoft.OperationalInsights/workspaces/write*
- "*クラスター*" リソース:*Microsoft.OperationalInsights/clusters/write*

請求の側面を除き、リンクされたワークスペースは、データ保有期間などの独自の設定を保持します。
ワークスペースとクラスターは、異なるサブスクリプションに配置できます。 Azure Lighthouse を使用して両方のテナントを 1 つのテナントにマップすると、ワークスペースとクラスターが異なるテナントに配置される可能性があります。

クラスターの操作と同様に、ワークスペースのリンクは、Log Analytics クラスターのプロビジョニングが完了した後にのみ実行できます。

> [!WARNING]
> ワークスペースをクラスターにリンクするには、複数のバックエンド コンポーネントを同期して、キャッシュのハイドレーションを確保する必要があります。 この操作が完了するまでに最大 2 時間かかる場合があります。 非同期的に実行することをお勧めします。


**PowerShell**

次の PowerShell コマンドを使用して、クラスターにリンクします。

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

次の REST 呼び出しを使用して、クラスターにリンクします。

*Send*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response*

200 OK とヘッダー。

### <a name="using-customer-managed-keys-with-linking"></a>カスタマー マネージド キーをリンクと共に使用する

カスタマー マネージド キーを使用する場合、取り込まれたデータは、関連付け操作の後、マネージド キーで暗号化された状態で格納されます。これが完了するには最大 90 分かかることがあります。 

ワークスペースの関連付けの状態は、次の 2 つの方法で確認できます。

- 応答から Azure-AsyncOperation URL 値をコピーし、非同期操作と状態のチェックに従います。

- [Workspaces - Get](/rest/api/loganalytics/workspaces/get) 要求を送信し、応答を確認します。 関連付けられているワークスペースの "features" の下に clusterResourceId があります。

送信要求は次のようになります。

*Send*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Response*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>専用クラスターからワークスペースのリンクを解除する

クラスターからワークスペースのリンクを解除することができます。 クラスターからワークスペースのリンクを解除すると、このワークスペースに関連付けられた新しいデータは専用クラスターに送信されません。 また、ワークスペースの課金はクラスター経由で行われなくなります。 リンクを解除したワークスペースの古いデータがクラスターに残っている可能性があります。 このデータがカスタマー マネージド キー (CMK) を使用して暗号化されている場合、Key Vault シークレットは保持されます。 この変更は Log Analytics ユーザーから分離されています。 ユーザーはワークスペースに通常どおりクエリを実行できます。 システムは必要に応じてバックエンドでクラスター間クエリを実行し、ユーザーには何も通知しません。  

> [!WARNING] 
> ワークスペースごとにリンク操作は 1 か月に 2 回という制限があります。 リンク解除アクションは考慮に時間をかけて計画してください。 

## <a name="delete-a-dedicated-cluster"></a>専用クラスターを削除する

専用クラスター リソースは削除できます。 クラスターを削除する前に、すべてのワークスペースのリンクを解除する必要があります。 この操作を実行するには、"*クラスター*" リソースに対する "書き込み" アクセス許可が必要です。 

クラスター リソースが削除されると、物理クラスターは消去と削除のプロセスに入ります。 クラスターを削除すると、クラスターに格納されていたすべてのデータが削除されます。 このデータは、過去にクラスターにリンクされたワークスペースのものである可能性があります。

過去 14 日以内に削除された "*クラスター*" リソースは、論理的な削除状態であり、そのデータで復旧できます。 "*クラスター*" リソースの削除によって、すべてのワークスペースが "*クラスター*" リソースから関連付け解除されているため、回復後にワークスペースを再関連付けする必要があります。 ユーザーが復旧操作を実行することはできません。Microsoft チャネルまたはサポートに問い合わせて、復旧要求を行ってください。

削除後 14 日間クラスター リソース名は予約され、他のリソースでは使用できません。

**PowerShell**

次の PowerShell コマンドを使用して、クラスターを削除します。

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

次の REST 呼び出しを使用して、クラスターを削除します。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK



## <a name="next-steps"></a>次の手順

- [Log Analytics 専用クラスターの課金](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)について学習します
- [Log Analytics ワークスペースの適切な設計](../platform/design-logs-deployment.md)について学習します
