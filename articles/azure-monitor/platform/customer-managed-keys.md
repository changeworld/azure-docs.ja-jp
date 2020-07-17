---
title: Azure Monitor のカスタマー マネージド キーの構成
description: カスタマー マネージド キー (CMK) を、Azure Key Vault キーを使用して Log Analytics ワークスペースのデータを暗号化するように構成するための情報と手順。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758812"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor のカスタマー マネージド キーの構成 

この記事では、Log Analytics のワークスペースと Application Insights コンポーネントに対して、カスタマー マネージド キー (CMK) を構成するための背景情報と手順について説明します。 構成が完了すると、ワークスペースまたはコンポーネントに送信されるすべてのデータが Azure Key Vault キーで暗号化されます。

構成の前に「[制限と制約](#limitations-and-constraints)」を確認することをお勧めします。

## <a name="disclaimers"></a>免責事項

- Azure Monitor CMK は、初期アクセス機能であり、登録されたサブスクリプションに対して有効になっています。

- この記事で説明されている CMK デプロイは、初期アクセス機能ですが、実稼働品質で提供されており、そのいうものとしてサポートされています。

- CMK 機能は、Azure Data Explorer (ADX) クラスターであり、1 日に 1 TB 以上を送信するお客様に適した、専用のデータストア クラスターで提供されます。 

- CMK の価格モデルは現時点では利用できず、この記事では説明しません。 専用 ADX クラスターの価格モデルはカレンダー年度 (CY) の 2020 年の第 2 四半期に予定されており、既存の CMK デプロイに適用される予定です。

- この記事では、Log Analytics ワークスペースの CMK 構成について説明します。 この記事では Application Insights コンポーネント用の CMK にも対応していますが、付録には相違点が記載されています。

> [!NOTE]
> Log Analytics と Application Insights は、同じデータストア プラットフォームとクエリ エンジンを使用しています。
> Application Insights を Log Analytics に統合することによって、これら 2 つのストアをまとめて、Azure Monitor に 1 つの統合ログ ストアを作成します。 この変更は、2020 年の第 2 四半期に予定されています。 その後、Application Insights データ用に CMK をデプロイする必要がない場合は、統合が完了するまで待っていただくことをお勧めします。このようなデプロイは統合によって中断され、Log Analytics ワークスペースへの移行後に CMK の再構成が必要になるためです。 少なくとも 1 日に 1TB がクラスター レベルで適用され、第 2 四半期に統合が完了するまで、Application Insights と Log Analytics には別々のクラスターが必要です。

## <a name="customer-managed-key-cmk-overview"></a>カスタマー マネージド キー (CMK) の概要

[保存時の暗号化](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)は、組織の一般的なプライバシーおよびセキュリティ要件です。 保存時の暗号化は Azure で完全に管理できますが、暗号化または暗号化キーを厳密に管理するさまざまなオプションが提供されています。

Azure Monitor データ ストアは、Azure Storage に格納されている間、Azure で管理されるキーを使用してすべてのデータを保存時に暗号化します。 Azure Monitor には、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) に格納されている独自のキーを使用したデータ暗号化のオプションも用意されています。これには、システムによって割り当てられた[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 認証を使用してアクセスします。 このキーは、[ソフトウェアまたはハードウェアの HSM で保護](https://docs.microsoft.com/azure/key-vault/key-vault-overview)できます。
Azure Monitor の暗号化の使用は、[Azure Storage の暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)と同じように動作します。

折り返し操作と折り返しの解除操作のために Azure Monitor Storage が Key Vault にアクセスする頻度は、6 ～ 60 秒です。 Azure Monitor Storage は、常に 1 時間以内にキーのアクセス許可の変更に対応します。

過去 14 日間に取り込まれたデータも、効率的なクエリ エンジン操作のためにホットキャッシュ (SSD ベース) で保持されます。 このデータは、CMK の構成に関係なく Microsoft キーで暗号化されたままになりますが、2020 年上半期に SSD を CMK で暗号化できるように取り組んでいます。

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor での CMK の動作

Azure Monitor は、システム割り当てのマネージド ID を活用して Azure Key Vault にアクセス権を付与します。 システム割り当てのマネージド ID は、1 つの Azure リソースにのみ関連付けることができます。 Azure Monitor データ ストア (ADX クラスター) の ID はクラスター レベルでサポートされます。これは、CMK 機能が専用 ADX クラスターで配信されることを示します。 複数のワークスペースで CMK をサポートするために、新しい Log Analytics リソース (*クラスター*) は、Key Vault と Log Analytics のワークスペースの間の中間 ID 接続として実行されます。 この概念はシステムで割り当てられた ID 制約に準拠しており、ID は ADX クラスターと Log Analytics の*クラスター* リソースとの間で管理されますが、関連するすべてのワークスペースのデータは Key Vault キーで保護されます。 土台となる ADX クラスター ストレージは、*クラスター* リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。

![CMK の概要](media/customer-managed-keys/cmk-overview-8bit.png)
1.    お客様の Key Vault。
2.    Key Vault に対するアクセス許可を持つマネージド ID を持つお客様の Log Analytics *クラスター* リソース – ID は、データストア (ADX クラスター) レベルでサポートされます。
3.    Azure Monitor 専用の ADX クラスター。
4.    CMK 暗号化のために*クラスター* リソースに関連付けられているお客様のワークスペース。

## <a name="encryption-keys-management"></a>暗号化キーの管理

ストレージ データの暗号化には、次の 3 種類のキーが関係します。

- **KEK** - キー暗号化キー (CMK)
- **AEK** - アカウント暗号化キー
- **DEK** - データ暗号化キー

次の規則が適用されます。

- ADX ストレージ アカウントは、すべてのストレージ アカウントに対して一意の暗号化キーを生成します。これは、AEK と呼ばれます。

- AEK は、ディスクに書き込まれた各データ ブロックの暗号化に使用されるキーである DEK を派生させるために使用されます。

- Key Vault でキーを構成し、それを*クラスター* リソースで参照すると、Azure Storage から Azure Key Vault に要求が送信され、AEK をラップおよびアンラップしてデータの暗号化および復号化操作を実行します。

- KEK が Key Vault から離れることはありません。また、HSM キーの場合はハードウェアから離れることはありません。

- Azure Storage は、*クラスター* リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。

## <a name="cmk-provisioning-procedure"></a>CMK のプロビジョニング手順

Application Insights CMK の構成の場合、手順 3 と 6 については付録の内容に従ってください。

1. サブスクリプションのホワイトリスト登録 - これは、この初期アクセス機能に必要です
2. Azure Key Vault の作成とキーの格納
3. *クラスター* リソースの作成
4. Azure Monitor のデータストア (ADX クラスター) のプロビジョニング
5. Key Vault へのアクセス許可の付与
6. Log Analytics ワークスペースの関連付け

この手順は現時点で UI ではサポートされておらず、プロビジョニング プロセスは REST API を介して実行されます。

> [!IMPORTANT]
> すべての API 要求には、要求ヘッダーにベアラー認証トークンを含める必要があります。

次に例を示します。

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

ここで *eyJ0eXAiO...* は完全な認証トークンを表します。 

トークンは次のいずれかの方法を使用して取得できます。

1. [アプリの登録](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法を使用します。
2. Azure ポータルで次の操作を行います。
    1. "開発者ツール" (F12) の使用中に Azure portal に移動します。
    1. "batch? api-version" インスタンスのいずれかで、"要求ヘッダー" の下にある認証文字列を検索します。 これは "authorization: Bearer eyJ0eXAiO...." のようになります。 
    1. これを、次の例に従って API 呼び出しにコピーして追加します。
3. Azure REST ドキュメント サイトに移動します。 任意の API で [試してみる] を押し、ベアラー トークンをコピーします。

### <a name="asynchronous-operations-and-status-check"></a>非同期操作と状態のチェック

この構成手順の一部の操作は迅速に完了できないため、非同期的に実行されます。 非同期操作の応答では、最初に HTTP 状態コード 200 (OK) が返され、受け入れられたときに *Azure-AsyncOperation* プロパティを含むヘッダーが返されます。
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

*Azure-AsyncOperation* ヘッダー値に GET 要求を送信することにより、非同期操作の状態を確認できます。
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

この応答には、操作とその*状態*に関する情報が含まれています。 これは、次のいずれかになります。

操作が進行中です
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

操作が完了しました
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

操作に失敗しました
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="subscription-whitelisting"></a>サブスクリプションのホワイトリスト登録

CMK の機能は初期アクセス機能です。 *クラスター* リソースを作成する予定のサブスクリプションは、Azure 製品グループによって事前にホワイトリストに登録されている必要があります。 連絡先を Microsoft に登録して、サブスクリプション ID を提供します。

> [!IMPORTANT]
> CMK 機能はリージョン別です。 Azure Key Vault、*クラスター* リソース、および関連付けられた Log Analytics のワークスペースは同じリージョンに存在している必要があります。ただし、サブスクリプションは異なっていてもかまいません。

### <a name="storing-encryption-key-kek"></a>暗号化キー (KEK) の格納

Azure Key Vault を作成するか既存のものを使用して、データの暗号化に使用するキーを生成またはインポートします。 キーを保護し、Azure Monitor のデータへのアクセスを保護するには、Azure Key Vault を回復可能として構成する必要があります。 この構成は Key Vault のプロパティで確認できます。 *[論理的な削除]* と *[Purge protection]\(消去保護\)* の両方を有効にしてください。

![論理的な削除と消去保護の設定](media/customer-managed-keys/soft-purge-protection.png)

これらの設定は、CLI と PowerShell を使用して利用できます。
- [論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [[Purge protection]\(消去保護\)](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) は、論理的な削除の後もシークレットやコンテナーを強制削除から保護します

### <a name="create-cluster-resource"></a>*クラスター* リソースを作成する

このリソースは、Key Vault と Log Analytics ワークスペースの間の中間 ID 接続として使用されます。 サブスクリプションがホワイトリストに登録されたことを示すメッセージが表示されたら、ワークスペースが配置されているリージョンで Log Analytics の*クラスター* リソースを作成します。 Application Insights と Log Analytics には別々の種類の*クラスター* リソースが必要です。 *クラスター* リソースの種類は、作成時に *clusterType* プロパティを *[LogAnalytics]* または *[ApplicationInsights]* のいずれかに設定することによって定義されます。 クラスター リソースの種類を後から変更することはできません。

Application Insights CMK の構成の場合は、付録の内容に従ってください。

*クラスター* リソースを作成するときに、容量予約レベル (sku) を指定する必要があります。 容量予約レベルは1 日あたり 1,000 ～ 2,000 GB の範囲で指定でき、後から 100 刻みで更新できます。 一日あたり 2,000 GB を超える容量予約レベルが必要な場合は、Microsoft の担当者に有効化を依頼してください。 現在、このプロパティは請求には影響しません。専用クラスターの価格モデルが導入された段階で、既存の CMK デプロイに請求が適用されます。

**作成**

この Resource Manager の要求は非同期操作です。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
この ID は、作成時に "*クラスター*" リソースに割り当てられます。

**Response**

200 OK とヘッダー。
この機能の早期アクセス期間中は、ADX クラスターは手動でプロビジョニングされます。 土台となる ADX クラスターのプロビジョニングには時間がかかりますが、次の 2 つの方法でプロビジョニングの状態を確認できます。
1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. *クラスター* リソースに GET 要求を送信し、*provisioningState* 値を確認します。 プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure Monitor のデータストア (ADX クラスター) のプロビジョニング

この機能の初期アクセス期間中は、前の手順が完了した後に、製品チームによって ADX クラスターが手動でプロビジョニングされます。 この手順を実行するには、Microsoft の担当者に連絡して、*クラスター* リソースの応答をお伝えください。 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> これらの詳細は次の手順で必要になるため、応答をコピーして保存してください。

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
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

"principalId" GUID は、*クラスター* リソースのマネージド ID サービスによって生成されます。

### <a name="grant-key-vault-permissions"></a>Key Vault アクセス許可を付与する

*クラスター* リソースにアクセス許可を付与する新しいアクセス ポリシーで Key Vault を更新します。 これらのアクセス許可は、データの暗号化のために下層の Azure Monitor ストレージによって使用されます。 Azure portal で Key Vault を開き、[アクセス ポリシー]、[+ アクセス ポリシーの追加] の順にクリックして、次の設定でポリシーを作成します。

- [キーのアクセス許可]: [取得]、[キーを折り返す]、および [キーの折り返しを解除] の各アクセス許可を選択します。
- [プリンシパルの選択]: 前の手順の応答で返された principal-id 値を入力します。

![Key Vault アクセス許可の付与](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

*[取得]* アクセス許可は、キーを保護し、Azure Monitor データへのアクセスを保護するために、Key Vault が回復可能として構成されていることを確認するために必要です。

### <a name="update-cluster-resource-with-key-identifier-details"></a>キー識別子の詳細を使用してクラスター リソースを更新する

この手順は、Key Vault の初期および将来のキー バージョンの更新時に実行されます。 データの暗号化に使用されるキーのバージョンについて Azure Monitor ストレージに通知します。 更新すると、新しいキーを使用してストレージ キー (AEK) のラップとラップ解除が行われます。

Key Vault の*キー識別子*の詳細で*クラスター* リソースを更新するには、Azure Key Vault で現在のバージョンのキーを選択して、キー識別子の詳細を取得します。

![Key Vault アクセス許可を付与する](media/customer-managed-keys/key-identifier-8bit.png)

キー識別子の詳細を使用して*クラスター* リソース KeyVaultProperties を更新します。

**アップデート**

この Resource Manager 要求は、容量の値を更新する場合は同期操作ですが、キー識別子の詳細を更新する場合は非同期操作です。

> [!Warning]
> *クラスター* リソースの更新で、*identity*、*sku*、*KeyVaultProperties*、*location* を含む完全な本体を指定する必要があります。 *KeyVaultProperties* の詳細がないと、キー識別子が*クラスター* リソースから削除され、[キーが失効](#cmk-kek-revocation)します。

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>"
}
```
"KeyVaultProperties" には Key Vault キー識別子の詳細が含まれています。

**Response**

200 OK とヘッダー。
キー識別子の伝播が完了するまで数分かかります。 更新の状態を確認するには、次の 2 つの方法があります。
1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. *クラスター* リソースに GET 要求を送信し、*KeyVaultProperties* 値を確認します。 最近更新されたキー識別子の詳細が、応答で返されます。

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>*クラスター* リソースへのワークスペースの関連付け
Application Insights CMK の構成の場合、この手順については付録の内容に従ってください。

この操作を実行するには、ワークスペースと "*クラスター*" リソースの両方に対して、以下のアクションが含まれる "書き込み" アクセス許可を保持している必要があります。

- ワークスペースの場合:Microsoft.OperationalInsights/workspaces/write
- "*クラスター*" リソースの場合:Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> この手順は、ADX クラスターのプロビジョニング後にのみ実行してください。 このプロビジョニングの前にワークスペースを関連付けてデータを取り込むと、取り込まれたデータは削除され、回復できなくなります。

**ワークスペースを関連付ける**

この Resource Manager の要求は非同期操作です。

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

**Response**

200 OK とヘッダー。
取り込まれたデータは、関連付け操作の後、マネージド キーで暗号化された状態で格納されます。これが完了するには最大 90 分かかることがあります。 ワークスペースの関連付けの状態は、次の 2 つの方法で確認できます。
1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) 要求を送信し、応答を観察します。関連付けられたワークスペースでは "features" の下に clusterResourceId が含まれます。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Response**

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
    "retentionInDays": days,
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

## <a name="cmk-kek-revocation"></a>CMK (KEK) の失効

Key Vault でキーを無効にするか、*クラスター* リソースのアクセス ポリシーを削除することによって、データへのアクセスを失効させることができます。 Azure Monitor Storage は常に 1 時間以内にキーのアクセス許可の変更に対応し、通常はすぐにストレージを使用できなくなります。 *クラスター* リソースに関連付けられているワークスペースに取り込まれたデータは削除され、クエリは失敗します。 *クラスター* リソースおよびワークスペースが削除されていない限り、以前に取り込まれたデータは Azure Monitor Storage ではアクセスできません。 アクセスできないデータは、データ保持ポリシーによって管理され、リテンション期間に達すると削除されます。

ストレージは、Key Vault を定期的にポーリングして暗号化キーの折り返しを解除しようとし、アクセスされた後、データ インジェストとクエリが 30 分以内に再開されます。

## <a name="cmk-kek-rotation"></a>CMK (KEK) のローテーション

CMK のローテーションを行うには、Azure Key Vault の新しいキー バージョンで*クラスター* リソースを明示的に更新する必要があります。 新しいキー バージョンで Azure Monitor を更新するには、「キー識別子の詳細で*クラスター* リソースを更新する」の手順に従います。 Key Vault でキー バージョンを更新し、*クラスター* リソースの新しいキー識別子の詳細を更新しない場合、Azure Monitor Storage では以前のキーが引き続き使用されます。
AEK は新しいキー暗号化キー (KEK) バージョンによって暗号化されるようになりますが、すべてのデータはアカウント暗号化キー (AEK) によって暗号化されたままであるため、キー ローテーション操作後も、ローテーションの前と後に取り込まれたデータを含め、すべてのデータにアクセスできます。

## <a name="limitations-and-constraints"></a>制限と制約

- CMK 機能は ADX クラスター レベルでサポートされており、1 日あたり 1 TB 以上を送信する必要がある専用の Azure Monitor ADX クラスターが必要です。

- サブスクリプションごとの*クラスター* リソースの最大数は 2 に制限されています

- ワークスペースへの "*クラスター*" リソースの関連付けは、ADX クラスターのプロビジョニングが完了したことを確認した後にのみ実行してください。 プロビジョニングの完了前にワークスペースに送信されたデータは削除され、復旧できなくなります。

- CMK の暗号化は、CMK の構成後に新しく取り込まれたデータに適用されます。 CMK の構成より前に取り込まれたデータは、Microsoft キーで暗号化されたままになります。 取り込まれたデータのクエリは CMK 構成の前後にシームレスに実行できます。

- 特定のワークスペースで CMK が不要であると判断した場合は、"*クラスター*" リソースからワークスペースの関連付けを解除できます。 関連付け解除操作後に新たに取り込まれたデータは、"*クラスター*" リソースに関連付けられる前と同じように、共有 Log Analytics ストレージに格納されます。 "*クラスター*" リソースがプロビジョニングされ、有効な Key Vault キーで構成されている場合は、関連付け解除の前および後に取り込まれたデータに対してシームレスにクエリを実行できます。

- Azure Key Vault は、回復可能として構成する必要があります。 これらのプロパティは既定では有効になっておらず、CLI または PowerShell を使用して構成する必要があります。

  - [論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)を有効にする必要があります
  - 論理的な削除の後でもシークレット/コンテナーの強制削除を防ぐには、[消去保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)を有効にする必要があります

- Application Insights と Log Analytics には別々の*クラスター* リソースが必要です。 *クラスター* リソースの種類は、作成時に "clusterType" プロパティを 'LogAnalytics' または 'ApplicationInsights' のいずれかに設定することによって定義されます。 *クラスター* リソースの種類を変更することはできません。

- *クラスター* リソースの別のリソース グループまたはサブスクリプションへの移動は、現時点ではサポートされていません。

- Azure Key Vault、*クラスター* リソースおよび関連付けられたワークスペースは、同じリージョンで同じ Azure Active Directory (Azure AD) テナント内に存在している必要がありますが、サブスクリプションは異なっていてもかまいません。

- *クラスター* リソースへのワークスペースの関連付けは、別の*クラスター* リソースに関連付けられている場合は失敗します

## <a name="troubleshooting-and-management"></a>トラブルシューティングと管理

- Key Vault の可用性
    - 通常の運用では、Storage は一時的に AEK をキャッシュし、定期的に Key Vault に戻ってラップを解除します。
    
    - 一時的な接続エラー -- Storage は、キーが短時間キャッシュにとどまることができるようにすることで一時的なエラー (タイムアウト、接続エラー、DNS の問題) を処理し、これにより可用性の小さな中断を克服できます。 クエリ機能と取り込み機能は中断されることなく続行されます。
    
    - ライブ サイト -- 約 30 分で使用できなくなると、Storage アカウントが使用できなくなります。 クエリ機能は使用できず、データの損失を防ぐために、Microsoft キーを使用して数時間の間、取り込まれたデータがキャッシュされます。 Key Vault へのアクセスが復元されると、クエリが使用可能になり、一時的にキャッシュされたデータがデータストアに取り込まれ、CMK で暗号化されます。

- システム ID が*クラスター* リソースに割り当てられるまでアクセス ポリシーを定義できないため、*クラスター* リソースを作成して KeyVaultProperties をすぐに指定すると、操作が失敗する可能性があります。

- KeyVaultProperties で既存の*クラスター* リソースを更新し、Key Vault に 'Get' キーのアクセス ポリシーがない場合、操作は失敗します。

- ワークスペースに関連付けられている*クラスター* リソースを削除しようとすると、削除操作は失敗します。

- "*クラスター*" リソースを作成するときに競合エラーが発生した場合は、過去 14 日以内に "*クラスター*" リソースを削除し、それが論理的な削除の期間内にある可能性があります。 *クラスター* リソース名は、論理的な削除の期間中は予約されたままであり、その名前で新しいクラスターを作成することはできません。 この名前は、論理的な削除の期間の後、"*クラスター*" リソースが完全に削除されたときに解放されます。

- リソース グループのすべての*クラスター* リソースを取得します。

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Response**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- サブスクリプションのすべての*クラスター* リソースを取得します。

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  'リソース グループの*クラスター* リソース' について、ただしサブスクリプション スコープ内のリソースと同じ応答です。

- "*クラスター*" リソースの "*容量予約*" の更新 -- 関連付けられているワークスペースのデータ ボリュームが変化し、課金を考慮して容量予約レベルを更新する場合は、["*クラスター*" リソースの更新](#update-cluster-resource-with-key-identifier-details)の手順に従い、新しい容量の値を指定します。 容量予約レベルは、1 日あたり 1,000 から 2,000 GB の範囲で、100 刻みで指定できます。 1 日あたり 2,000 GB を超えるレベルの場合は、Microsoft の担当者に有効化を依頼してください。

- "*クラスター*" リソースの削除 -- 不注意による削除か意図的な削除かにかかわらず、14 日以内であればデータを含む "*クラスター*" リソースを復旧できるように、論理的な削除操作が実行されます。 *クラスター* リソース名は、論理的な削除の期間中は予約されたままであり、その名前で新しいクラスターを作成することはできません。 論理的な削除の期間後は、"*クラスター*" リソース名が解放され、"*クラスター*" リソースとデータは完全に削除されて、復旧できなくなります。 関連付けられているすべてのワークスペースは、削除操作時に "*クラスター*" リソースから関連付けを解除されます。 新たに取り込まれたデータは、共有 Log Analytics ストレージに格納され、Microsoft キーで暗号化されます。 ワークスペースの関連付け解除操作は非同期です。

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK

- "*クラスター*" リソースとデータの復旧 -- 過去 14 日以内に削除された "*クラスター*" リソースは、論理的な削除状態であり、復旧できます。 これは、現在、製品グループごとに手動で実行されます。 復旧要求には、Microsoft チャネルを使用します。

## <a name="appendix"></a>付録

Application Insights のカスタマー マネージド キー (CMK) もサポートされていますが、Application insights コンポーネント用の CMK のデプロイを計画する際には、次の変更を考慮する必要があります。

Log Analytics と Application Insights は、同じデータストア プラットフォームとクエリ エンジンを使用しています。 Application Insights を Log Analytics に統合することによって、これら 2 つのストアをまとめて、2020 年度第 2 四半期までに Azure Monitor に 1 つの統合ログ ストアを作成します。
2020. この変更によって、Application insights のデータが Log Analytics ワークスペースに移動し、ワークスペースで CMK を構成している間にクエリや分析情報などの機能強化が加えられ、Application Insights データにも適用されます。

> [!NOTE]
> 統合の前に Application Insights データ用に CMK をデプロイする必要がない場合は、Application Insights CMK を使用して待機することをお勧めします。このようなデプロイは統合によって中断され、Log Analytics ワークスペースへの移行後に CMK の再構成が必要になるためです。 少なくとも 1 日に 1TB がクラスター レベルで適用され、第 2 四半期に統合が完了するまで、Application Insights と Log Analytics には別々のクラスターが必要です。

## <a name="application-insights-cmk-configuration"></a>Application Insights CMK の構成

Application Insights CMK の構成は、次の手順を除き、制約とトラブルシューティングを含み、この記事で説明されているプロセスと同じです。

- *クラスター* リソースを作成する
- コンポーネントを*クラスター* リソースに関連付ける

CMK を Application Insights 用に構成する場合は、上記の手順ではなく、次の手順を使用します。

### <a name="create-a-cluster-resource"></a>*クラスター* リソースを作成する

このリソースは、Key Vault とコンポーネントの間の中間 ID 接続として使用されます。 サブスクリプションがホワイトリストに登録されたことを示すメッセージが表示されたら、コンポーネントが配置されているリージョンで Log Analytics の*クラスター* リソースを作成します。 *クラスター* リソースの種類は、作成時に *clusterType* プロパティを *[LogAnalytics]* または *[ApplicationInsights]* のいずれかに設定することによって定義されます。 これは Application Insights CMK の *ApplicationInsights* である必要があります。 *clusterType* 設定は、構成後に変更することはできません。

**作成**

この Resource Manager の要求は非同期操作です。

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Response**

200 OK とヘッダー。
この機能の早期アクセス期間中は、ADX クラスターは手動でプロビジョニングされます。 土台となる ADX クラスターのプロビジョニングには時間がかかりますが、次の 2 つの方法でプロビジョニングの状態を確認できます。
1. 応答から Azure-AsyncOperation URL 値をコピーし、[非同期操作と状態のチェック](#asynchronous-operations-and-status-check)に従います。
2. *クラスター* リソースに GET 要求を送信し、*provisioningState* 値を確認します。 プロビジョニング中は *ProvisioningAccount*、完了時は *Succeeded* になります。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>[コンポーネント - 作成または更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API を使用してコンポーネントを*クラスター* リソースに関連付ける

この操作を実行するには、コンポーネントと*クラスター* リソースの両方において、以下のアクションが含まれる "書き込み" アクセス許可を保持している必要があります。

- コンポーネントの場合:Microsoft.Insights/component/write
- "*クラスター*" リソースの場合:Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> この手順は、ADX クラスターのプロビジョニング後にのみ実行してください。 このプロビジョニングの前にコンポーネントを関連付けてデータを取り込むと、取り込まれたデータは削除され、回復できなくなります。
> ADX クラスターがプロビジョニングされていることを確認するには、*クラスター* リソースの Get REST API を実行し、*provisioningState* の値が *Succeeded* であることを確認します。

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> 応答は次の手順で必要になるため、コピーして保持してください。

**コンポーネントを関連付ける**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" は、前の手順からの応答で指定された "clusterId" 値です。
"kind" の例は "web" です。

**Response**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" は、このコンポーネントに関連付けられている *クラスター* リソース ID です。

関連付けの後、コンポーネントに送信されるデータは、マネージド キーで暗号化された状態で格納されます。
