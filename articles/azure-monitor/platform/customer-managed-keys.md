---
title: Azure Monitor のカスタマー マネージド キーの構成
description: カスタマー マネージド キー (CMK) を、Azure Key Vault キーを使用して Log Analytics ワークスペースのデータを暗号化するように構成するための情報と手順。
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/24/2020
ms.openlocfilehash: b3e110766b2e131330f3108b7938e9e5e01e48a4
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208561"
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

過去 14 日間に取り込まれたデータも、効率的なクエリ エンジン操作のためにホットキャッシュ (SSD ベース) で保持されます。 このデータは、CMK の構成に関係なく Microsoft キーで暗号化されたままになりますが、2020 年の早い時期に SSD を CMK で暗号化できるように取り組んでいます。

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor での CMK の動作

Azure Monitor は、システム割り当てのマネージド ID を活用して Azure Key Vault にアクセス権を付与します。 システム割り当てのマネージド ID は、1 つの Azure リソースにのみ関連付けることができます。 Azure Monitor データ ストア (ADX クラスター) の ID はクラスター レベルでサポートされます。これは、CMK 機能が専用 ADX クラスターで配信されることを示します。 複数のワークスペースで CMK をサポートするために、新しい Log Analytics リソース (*クラスター*) は、Key Vault と Log Analytics のワークスペースの間の中間 ID 接続として実行されます。 この概念はシステムで割り当てられた ID 制約に準拠しており、ID は ADX クラスターと Log Analytics の*クラスター* リソースとの間で管理されますが、関連するすべてのワークスペースのデータは Key Vault キーで保護されます。 土台となる ADX クラスター ストレージは、*クラスター* リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。

![CMK の概要](media/customer-managed-keys/cmk-overview.png)
1.  お客様の Key Vault。
2.  Key Vault に対するアクセス許可を持つマネージド ID を持つお客様の Log Analytics クラスター リソース – ID は、データストア (ADX クラスター) レベルでサポートされます。
3.  Azure Monitor 専用の ADX クラスター。
4.  CMK 暗号化のためにクラスター リソースに関連付けられているお客様のワークスペース。

## <a name="encryption-keys-management"></a>暗号化キーの管理

ストレージ データの暗号化には、次の 3 種類のキーが関係します。

- **KEK** - Key Vault 内のキー暗号化キー (CMK)
- **AEK** - アカウント暗号化キー
- **DEK** - データ暗号化キー

次の規則が適用されます。

- ADX ストレージ アカウントは、すべてのストレージ アカウントに対して一意の暗号化キーを生成します。これは、AEK と呼ばれます。

- AEK は、ディスクに書き込まれた各データ ブロックの暗号化に使用されるキーである DEK を派生させるために使用されます。

- Key Vault でキーを構成し、*クラスター* リソースでそのキーを参照すると、Azure Storage は Azure Key Vault の KEK で AEK を折り返します。

- KEK が Key Vault から離れることはありません。また、HSM キーの場合はハードウェアから離れることはありません。

- Azure Storage は、*クラスター* リソースに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。

- 読み取り/書き込み操作の場合、Azure Storage は Azure Key Vault に要求を送信して、暗号化と暗号化解除の操作を実行するために AEK を折り返しおよび折り返しの解除を行います。

## <a name="cmk-provisioning-procedure"></a>CMK のプロビジョニング手順

Application Insights CMK の構成の場合、手順 3 と 6 については付録の内容に従ってください。

1. サブスクリプションのホワイトリスト登録 - これは、この初期アクセス機能に必要です
2. Azure Key Vault の作成とキーの格納
3. *クラスター* リソースを作成する
4. Azure Monitor のデータストア (ADX クラスター) のプロビジョニング
5. Key Vault へのアクセス許可を付与する
6. Log Analytics ワークスペースの関連付け

この手順は現時点で UI ではサポートされておらず、プロビジョニング プロセスは REST API を介して実行されます。

> [!IMPORTANT]
> すべての API 要求には、要求ヘッダーにベアラー認証トークンを含める必要があります。

次に例を示します。

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

ここで *eyJ0eXAiO...* は完全な認証トークンを表します。 

トークンは次のいずれかの方法を使用して取得できます。

1. [アプリの登録](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)方法を使用します。

2. Azure ポータルで次の操作を行います。
    1. 開発者ツール (F12) で Azure portal に移動します
    1. "batch? api-version" インスタンスのいずれかで、"要求ヘッダー" の下にある認証文字列を検索します。 これは "authorization: Bearer \<token\>" のように表示されます。 
    1. これを、次の例に従って API 呼び出しにコピーして追加します。

3. Azure REST ドキュメント サイトに移動します。 任意の API で [試してみる] を押し、ベアラー トークンをコピーします。

### <a name="subscription-whitelisting"></a>サブスクリプションのホワイトリスト登録

CMK の機能は初期アクセス機能です。 *クラスター* リソースを作成する予定のサブスクリプションは、Azure 製品グループによって事前にホワイトリストに登録されている必要があります。 連絡先を Microsoft に登録して、サブスクリプション ID を提供します。

> [!IMPORTANT]
> CMK 機能はリージョン別です。 Azure Key Vault、ストレージ アカウント、*クラスター* リソース、および関連付けられた Log Analytics のワークスペースは同じリージョンに存在している必要があります。ただし、サブスクリプションは異なっていてもかまいません。

### <a name="storing-encryption-key-kek"></a>暗号化キー (KEK) の格納

Azure Key Vault リソースを作成してから、データの暗号化に使用するキーを生成またはインポートします。

キーを保護し、Azure Monitor データへのアクセスを保護するには、Azure Key Vault を回復可能として構成する必要があります。

これらの設定は、CLI と PowerShell を使用して利用できます。
- [論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)を有効にする必要があります
- 論理的な削除の後でもシークレット/コンテナーの強制削除を防ぐには、[消去保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)を有効にする必要があります

### <a name="create-cluster-resource"></a>*クラスター* リソースを作成する

このリソースは、Key Vault とワークスペースの間の中間 ID 接続として使用されます。 サブスクリプションがホワイトリストに登録されたことを示すメッセージが表示されたら、ワークスペースが配置されているリージョンで Log Analytics の*クラスター* リソースを作成します。 Application Insights と Log Analytics には別々のクラスター リソースが必要です。 *クラスター* リソースの種類は、作成時に "clusterType" プロパティを 'LogAnalytics' または 'ApplicationInsights' のいずれかに設定することによって定義されます。 クラスター リソースの種類を変更することはできません。

Application Insights CMK の構成の場合、この手順については付録の内容に従ってください。

**作成**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
この ID は、作成時に "*クラスター*" リソースに割り当てられます。
"clusterType" の値は、Application Insights CMK の "ApplicationInsights" です。

**応答**

202 受理されました。 これは、非同期操作に対する標準の Resource Manager 応答です。

何らかの理由で (別の名前または clusterType を使用して作成する場合など) "*クラスター*" リソースを削除する場合は、次の REST API を使用します。

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Azure Monitor のデータストア (ADX クラスター) のプロビジョニング

この機能の初期アクセス期間中は、前の手順が完了した後に、製品チームによって ADX クラスターが手動でプロビジョニングされます。 Microsoft チャネルを使用して、"*クラスター*" リソースの詳細を提供します。 "*クラスター*" リソースの GET REST API からの JSON 応答をコピーします。

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

**応答**
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "properties": {
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

"principal-id" は、"*クラスター*" リソースのマネージド ID サービスによって生成される GUID です。

> [!IMPORTANT]
> "principal-id" の値は、次の手順で必要になるため、コピーして保持します。


### <a name="grant-key-vault-permissions"></a>Key Vault アクセス許可を付与する

> [!IMPORTANT]
> この手順は、Azure Monitor データストア (ADX クラスター) のプロビジョニングが満たされたことの確認を、Microsoft チャネルを通じて製品グループから受信した後に実行する必要があります。 このプロビジョニングの前に Key Vault アクセス ポリシーを更新すると、失敗する場合があります。

"*クラスター*" リソースにアクセス許可を付与する新しいアクセス ポリシーで Key Vault を更新します。 これらのアクセス許可は、データの暗号化のために下層の Azure Monitor ストレージによって使用されます。
Azure portal で Key Vault を開き、[アクセス ポリシー]、[+ アクセス ポリシーの追加] の順にクリックして、次の設定で新しいポリシーを作成します。

- [キーのアクセス許可]: [取得]、[キーを折り返す]、および [キーの折り返しを解除] の各アクセス許可を選択します。
- [プリンシパルの選択]: 前の手順の応答で返された principal-id 値を入力します。

![Key Vault アクセス許可の付与](media/customer-managed-keys/grant-key-vault-permissions.png)

*[取得]* アクセス許可は、キーを保護し、Azure Monitor データへのアクセスを保護するために、Key Vault が回復可能として構成されていることを確認するために必要です。

### <a name="update-cluster-resource-with-key-identifier-details"></a>キー識別子の詳細を使用してクラスター リソースを更新する

この手順は、Key Vault の今後のキー バージョンの更新に適用されます。 Key Vault の*キー識別子*の詳細で*クラスター* リソースを更新して、Azure Monitor Storage で新しいキー バージョンを使用できるようにします。 Azure Key Vault で現在のバージョンのキーを選択して、キー識別子の詳細を取得します。

![Key Vault アクセス許可を付与する](media/customer-managed-keys/key-identifier-8bit.png)

キー識別子の詳細を使用して*クラスター* リソース KeyVaultProperties を更新します。

**アップデート**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
   },
   "location":"<region-name>",
   "identity": { 
     "type": "systemAssigned" 
     }
}
```
"KeyVaultProperties" には Key Vault キー識別子の詳細が含まれています。

**応答**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
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

> [!NOTE]
> この手順は、**Azure Monitor データストア (ADX クラスター) のプロビジョニング**が満たされたことの確認を、Microsoft チャネルを通じて製品グループから受信した後に**のみ**実行してください。 この**プロビジョニング**の前にワークスペースを関連付けてデータを取り込むと、データは削除され、回復できなくなります。

Application Insights CMK の構成の場合、この手順については付録の内容に従ってください。

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
*clusterDefinitionId* は、前の手順の応答で指定された *clusterId* 値です。

**応答**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

関連付けの後、ワークスペースに送信されるデータは、マネージド キーで暗号化された状態で格納されます。

### <a name="workspace-association-verification"></a>ワークスペースの関連付けの検証
ワークスペースが *Custer* リソースに関連付けられているかどうかを確認するには、[Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get)応答を参照します。 関連付けられたワークスペースには、*クラスター* リソース ID を持つ 'clusterResourceId' プロパティがあります。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
```

**応答**

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
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

Azure Monitor Storage は常に 1 時間以内にキーのアクセス許可の変更に対応し、通常はすぐにストレージを使用できなくなります。 *クラスター* リソースに関連付けられているワークスペースに取り込まれたデータは削除され、クエリは失敗します。 キーが失効していて、ワークスペースが削除されていない限り、以前に取り込まれたデータは Azure Monitor Storage ではアクセスできません。 アクセスできないデータは、データ保持ポリシーによって管理され、リテンション期間に達すると削除されます。

ストレージは、Key Vault を定期的にポーリングして暗号化キーの折り返しを解除しようとし、アクセスされた後、データ インジェストとクエリが 30 分以内に再開されます。

## <a name="cmk-kek-rotation"></a>CMK (KEK) のローテーション

CMK のローテーションを行うには、新しい Azure Key Vault キー バージョンで*クラスター* リソースを明示的に更新する必要があります。 新しいキー バージョンで Azure Monitor を更新するには、「*キー識別子*の詳細で*クラスター* リソースを更新する」の手順に従います。

Key Vault でキーを更新し、*クラスター* リソース* の新しい*キー識別子*の詳細を更新しない場合、Azure Monitor Storage では以前のキーが引き続き使用されます。

## <a name="limitations-and-constraints"></a>制限と制約

- CMK 機能は ADX クラスター レベルでサポートされており、専用の Azure Monitor ADX クラスターが必要です

- サブスクリプションごとの*クラスター* リソースの最大数は 5 に制限されています

- ワークスペースへの*クラスター* リソースの関連付けは、ADX クラスターのプロビジョニングが満たされたことの確認を製品グループから受信した後にのみ実行してください。 このプロビジョニングの前に送信されたデータは削除され、回復できなくなります。

- CMK の暗号化は、CMK の構成後に新しく取り込まれたデータに適用されます。 CMK の構成より前に取り込まれたデータは、Microsoft キーで暗号化されたままになります。 データのクエリは構成の前後にシームレスに実行できます。

- ワークスペースが*クラスター* リソースに関連付けられた後、データはキーで暗号化され、Azure Key Vault で KEK を使用しないとアクセスできないため、*クラスター* リソースから関連付けを解除することはできません。

- Azure Key Vault は、回復可能として構成する必要があります。 これらのプロパティは既定では有効になっておらず、CLI と PowerShell を使用して構成する必要があります。

  - [論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)を有効にする必要があります
  - 論理的な削除の後でもシークレット/コンテナーの強制削除を防ぐには、[消去保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)を有効にする必要があります

- Application Insights と Log Analytics には別々の*クラスター* リソースが必要です。 *クラスター* リソースの種類は、作成時に "clusterType" プロパティを 'LogAnalytics' または 'ApplicationInsights' のいずれかに設定することによって定義されます。 *クラスター* リソースの種類を変更することはできません。

- *クラスター* リソースの別のリソース グループまたはサブスクリプションへの移動は、現時点ではサポートされていません。

- Azure Key Vault、*クラスター* リソースおよび関連付けられたワークスペースは、同じリージョンで同じ Azure Active Directory (Azure AD) テナント内に存在している必要がありますが、サブスクリプションは異なっていてもかまいません。

- *クラスター* リソースへのワークスペースの関連付けは、別の*クラスター* リソースに関連付けられている場合は失敗します

## <a name="troubleshooting-and-management"></a>トラブルシューティングと管理

- Key Vault の可用性
    - 通常の運用では、Storage のキャッシュの AEK は、折り返しを解除するために短時間の間、一時的に Key Vault に戻ります。
    
    - 一時的な接続エラー。 Storage は、キーが短時間キャッシュにとどまることができるようにすることで一時的なエラー (タイムアウト、接続エラー、DNS の問題) を処理し、これにより可用性の小さな中断を克服できます。 クエリ機能と取り込み機能は中断されることなく続行されます。
    
    - ライブ サイトが約 30 分で使用できなくなると、Storage アカウントが使用できなくなります。 クエリ機能は使用できず、データの損失を防ぐために、Microsoft キーを使用して数時間の間、取り込まれたデータがキャッシュされます。 Key Vault へのアクセスが復元されると、クエリが使用可能になり、一時的にキャッシュされたデータがデータストアに取り込まれ、CMK で暗号化されます。

- システム ID が*クラスター* リソースに割り当てられるまでアクセス ポリシーを定義できないため、*クラスター* リソースを作成して KeyVaultProperties をすぐに指定すると、操作が失敗する可能性があります。

- KeyVaultProperties で既存の*クラスター* リソースを更新し、Key Vault に 'Get' キーのアクセス ポリシーがない場合、操作は失敗します。

- ワークスペースに関連付けられている*クラスター* リソースを削除しようとすると、削除操作は失敗します。

- この API を使用して、リソース グループのすべての*クラスター* リソースを取得します。

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **応答**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
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

- 次の API 呼び出しを使用して、サブスクリプションのすべての*クラスター* リソースを取得します。

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **応答**
    
  'リソース グループの*クラスター* リソース' について、ただしサブスクリプション スコープ内のリソースと同じ応答です。
    
- この API 呼び出しを使用して*クラスター* リソースを削除します。*クラスター* リソースを削除する前に、関連付けられているすべてのワークスペースを削除する必要があります。

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **応答**

  200 OK


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

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**応答**

ID は、作成時に*クラスター* リソースに割り当てられます。

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"principle-id" は、マネージド ID サービスによって生成された GUID です。

> [!IMPORTANT]
> "principle-id" の値は、次の手順で必要になるため、コピーして保持します。

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>[コンポーネント - 作成または更新](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) API を使用してコンポーネントを*クラスター* リソースに関連付ける

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

**応答**

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
