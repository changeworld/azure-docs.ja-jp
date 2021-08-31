---
title: データ収集とレポート | Azure Arc 対応データ サービス
description: Arc 対応データ サービスによって Microsoft に送信されるデータの種類について説明します。
author: dnethi
ms.author: dinethi
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
ms.date: 07/30/2021
ms.custom: template-concept
ms.openlocfilehash: 189021997362da8508d2e60c23cc3acb1238ca55
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728027"
---
# <a name="azure-arc-data-services-data-collection-and-reporting"></a>Azure Arc データ サービスのデータ収集とレポート

この記事では、Azure Arc 対応データ サービスによって Microsoft に送信されるデータについて説明します。 


## <a name="related-products"></a>関連製品

Azure Arc 対応 データ サービスでは、次の製品の一部またはすべてを使用する場合があります。

- SQL MI – Azure Arc 
- PostgreSQL Hyperscale – Azure Arc
- Azure Data Studio

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- Azure CLI (az)
- Azure Data CLI (`azdata`) 

## <a name="directly-connected"></a>直接接続

クラスターが Azure に直接接続するように構成されている場合、一部のデータは自動的に Microsoft に送信されます。 

次の表では、データの種類、送信方法、要件について説明します。  

|データ カテゴリ|送信されるデータ|送信方法|必須かどうか
|:----|:----|:----|:----|
|オペレーショナル データ|メトリックとログ|自動 (実行されるよう構成されている場合)|いいえ
課金およびインベントリ データ|インスタンス数などのインベントリや、消費された仮想コアの数などの使用状況|自動 |はい
診断|トラブルシューティングのための診断情報|手動でエクスポートされ、Microsoft サポートに提供される|トラブルシューティングの範囲でのみとし、標準の[プライバシー ポリシー](https://privacy.microsoft.com/privacystatement)に従う
カスタマー エクスペリエンス向上プログラム (CEIP)|[CEIP の概要](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|自動 (許可されている場合)|いいえ

## <a name="indirectly-connected"></a>間接接続

Azure に直接接続するように構成されていないクラスターでは、オペレーショナル データや課金およびインベントリ データが自動的に Microsoft に送信されることはありません。 Microsoft にデータを送信するには、エクスポートを構成する必要があります。 

次の表では、データの種類、送信方法、要件について説明します。  

|データ カテゴリ|送信されるデータ|送信方法|必須かどうか
|:----|:----|:----|:----|
|オペレーショナル データ|メトリックとログ|手動|いいえ
課金およびインベントリ データ|インスタンス数などのインベントリや、消費された仮想コアの数などの使用状況|マニュアル |はい
診断|トラブルシューティングのための診断情報|手動でエクスポートされ、Microsoft サポートに提供される|トラブルシューティングの範囲でのみとし、標準の[プライバシー ポリシー](https://privacy.microsoft.com/privacystatement)に従う
カスタマー エクスペリエンス向上プログラム (CEIP)|[CEIP の概要](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)|自動 (許可されている場合)|いいえ

## <a name="detailed-description-of-data"></a>データの詳しい説明

このセクションでは、Azure Arc 対応データ サービスによって Microsoft に送信される際に含まれる情報の詳細について説明します。

### <a name="operational-data"></a>業務データ

オペレーショナル データは、すべてのデータベース インスタンスと、Arc 対応データ サービス プラットフォーム自体について収集されます。 次の 2 種類のオペレーショナル データがあります。 

- メトリック - パフォーマンスと容量に関連するメトリックで、Arc 対応データ サービスの一部として提供される Influx DB に収集されます。 これらのメトリックは、提供される Grafana ダッシュボードで確認できます。 

- ログ - エラー、警告、情報イベントなどのすべてのコンポーネントによって出力されるログが、Arc 対応データ サービスの一部として提供される Elasticsearch データベースに収集されます。 ログは、提供される Kibana ダッシュボードで確認できます。 

ローカルに保存されているオペレーショナル データには、Grafana または Kibana で表示するための管理特権が組み込まれている必要があります。 

オペレーショナル データは、エクスポートやアップロード (間接接続モード) を選択するか、データを Azure Monitor や Log Analytics に自動的に送信 (直接接続モード) しない限り、環境からなくなることはありません。 データは、制御する Log Analytics ワークスペースに移動します。 

データが Azure Monitor または Log Analytics に送信される場合は、Log Analytics ワークスペースが存在する Azure リージョンまたはデータセンターを選択できます。 その後、他の場所から表示またはコピーするためのアクセスを制御できます。 

### <a name="billing-and-inventory-data"></a>課金およびインベントリ データ 

課金データは、課金される使用状況を追跡するために使用されます。 このデータはサービスの実行に不可欠であり、すべてのモードで手動または自動で送信する必要があります。 

すべてのデータベース インスタンスとデータ コントローラー自体が、Azure Resource Manager の Azure リソースとして Azure に反映されます。 

次の 3 つのリソースの種類があります。 

- Arc 対応 SQL Managed Instance 
- Arc 対応 PostgreSQL Hyperscale サーバー グループ 
- Azure Arc 対応サーバーでの SQL Server 
- データ コントローラー 

次のセクションでは、各種類のリソースについて収集および格納されるプロパティ、型、および説明を示します。 

### <a name="sql-server-on-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーでの SQL Server 
- SQL Server のエディション。 
   - `string: Edition` 
- コンテナー リソース (Azure Arc for Servers) のリソース ID。 
   - `string: ContainerResourceId` 
- リソースが作成された時刻。 
   - `string: CreateTime` 
- SQL Server インスタンスで使用される論理プロセッサの数。
   - `string: VCore` 
- クラウドの接続状態。 
   - `string: Status` 
- SQL Server の更新レベル。 
   - `string: PatchLevel` 
- SQL Server の照合順序。 
   - `string: Collation`
- SQL Server の現在のバージョン。
   - `string: CurrentVersion`
- SQL Server のインスタンス名。 
   - `string: InstanceName`
- SQL Server で使用される動的 TCP ポート。 
   - `string: TcpDynamicPorts`
- SQL Server で使用される静的 TCP ポート。
   - `string: TcpStaticPorts` 
- SQL Server の製品 ID。
   - `string: ProductId`
- SQL Server のプロビジョニング状態。
   - `string: ProvisioningState`

### <a name="data-controller"></a>データ コントローラー 

- 場所情報
   - `public OnPremiseProperty OnPremiseProperty` 
- 未加工の Kubernetes 情報 (`kubectl get datacontroller`) 
   - `object: K8sRaw` 
- オンプレミス クラスターから最後にアップロードされた日付。
   - `System.DateTime: LastUploadedDate` 
- データ コントローラーの状態
   - `string: ProvisioningState` 

### <a name="postgresql-hyperscale-server-group"></a>PostgreSQL Hyperscale サーバー グループ 

- データ コントローラー ID
   - `string: DataControllerId`
- インスタンス管理者名
   - `string: Admin`
- 基本認証用のユーザー名とパスワード
   - `public: BasicLoginInformation BasicLoginInformation` - 未加工の Kubernetes 情報 (`kubectl get postgres12`) 
   - `object: K8sRaw` - オンプレミス クラスターから最後にアップロードされた日付。 
   - `System.DateTime: LastUploadedDate` 
- グループのプロビジョニング状態
   - `string: ProvisioningState` 

### <a name="sql-managed-instance"></a>SQL Managed Instance 

- マネージド インスタンス ID
   - `public string: DataControllerId` 
- インスタンス管理者のユーザー名 
   - `string: Admin` 
- インスタンスの開始時刻 
   - `string: StartTime`
- インスタンスの終了時刻 
   - `string: EndTime` 
- 未加工の Kubernetes 情報 (`kubectl get sqlmi`) 
   - `object: K8sRaw` 
- 基本認証用のユーザー名とパスワード。 
   - `public: BasicLoginInformation BasicLoginInformation`
- オンプレミス クラスターから最後にアップロードされた日付。 
   - `public: System.DateTime LastUploadedDate` 
- SQL マネージド インスタンスのプロビジョニング状態
   - `public string: ProvisioningState` 

### <a name="examples"></a>例

サブスクリプションに Azure リソースを作成するために Azure に送信されるリソース インベントリ データの JSON ドキュメントの例。 

```json
{ 

        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711", 

        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373", 

        "instanceName": "sqlInstance001", 

        "instanceNamespace": "arc", 

        "instanceType": "<resource>", 

        "location": "eastus", 

        "resourceGroupName": "production-resources", 

        "subscriptionId": "<subscription_id>", 

        "isDeleted": false, 

        "externalEndpoint": "32.191.39.83:1433", 

        "vCores": "2", 

        "createTimestamp": "05/29/2020 23:13:17", 

        "updateTimestamp": "05/29/2020 23:13:17" 

    } 
```

 

課金データは、特定のインスタンスの開始時間 (「作成」) と終了時間 (「削除」)、および特定のインスタンスで使用可能なコア数 (「コア制限」) の変更が発生したときの開始と時間をキャプチャします。 

```json
{ 

          "requestType": "usageUpload", 

          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d", 

          "name": "DataControllerTestName", 

          "subscriptionId": "<subscription_id>", 

          "resourceGroup": "production-resources", 

          "location": "eastus", 

          "uploadRequest": { 

            "exportType": "usages", 

            "dataTimestamp": "2020-06-17T22:32:24Z", 

            "data": "[{\"name\":\"sqlInstance001\", 

                       \"namespace\":\"arc\", 

                       \"type\":\"<resource type>\", 

                       \"eventSequence\":1,  

                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\", 

                       \"startTime\":\"2020-06-17T19:11:47.7533333\", 

                       \"endTime\":\"2020-06-17T19:59:00\", 

                       \"quantity\":1, 

                       \"id\":\"<subscription_id>\"}]", 

           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK" 

```

### <a name="diagnostic-data"></a>診断データ

サポート状況によっては、データベース インスタンスのログ、Kubernetes ログ、およびその他の診断ログを提供するように求められる場合があります。 サポート チームは、アップロード先として安全な場所を提供します。 動的管理ビュー (DMV) では、診断データを提供することもできます。 使用される DMV またはクエリには、データベース スキーマ メタデータの詳細が含まれている場合がありますが、通常は顧客データは含まれません。 診断データには、パスワード、クラスター IP、または個人を特定できるデータは含まれません。 これらは消去され、可能な場合はログが匿名で保存されます。 これらは自動的には送信されないため、管理者は手動でアップロードする必要があります。 

|フィールド名  |Notes  |
|---------|---------|
|エラー ログ |エラーをキャプチャするログ ファイルには、顧客または個人データが含まれている場合がありますが (下記を参照)、ユーザーによって制限および共有されます |
|DMV      |動的管理ビューにはクエリとクエリ プランを含めることができますが、ユーザーによって制限および共有されます     |
|ビュー    |ビューには顧客データを含めることができますが、ユーザーによってのみ制限および共有されます     |
|クラッシュ ダンプ – 顧客データ | 最大 30 日間のクラッシュ ダンプの保有には、アクセス制御データが含まれる場合があります <br/><br/> 統計オブジェクト、行内のデータ値、クエリ テキストが顧客のクラッシュ ダンプに含まれる場合があります    |
|クラッシュ ダンプ – 個人データ | マシン、ログイン/ユーザー名、電子メール、場所情報、顧客 ID には、ユーザーの同意を含める必要があります  |

## <a name="next-steps"></a>次のステップ
[使用状況データを Azure Monitor にアップロードする](upload-usage-data.md)
