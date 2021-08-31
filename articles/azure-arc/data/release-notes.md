---
title: Azure Arc 対応データ サービス - リリース ノート
description: 最新のリリース ノート
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: conceptual
ms.openlocfilehash: ed916e16392911dd92a98f3199cbb1c422ed2f55
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861477"
---
# <a name="release-notes---azure-arc-enabled-data-services"></a>リリース ノート - Azure Arc 対応データ サービス

この記事では、Azure Arc 対応データ サービスで最近リリースされた、または強化された機能と機能強化について説明します。

## <a name="july-2021"></a>2021 年 7 月

このリリースは、2021 年 7 月 30 日に公開されています。

このリリースでは、間接接続モードでの Azure Arc 対応 SQL Managed Instance [General Purpose サービス レベル](service-tiers.md)の一般提供が発表されています。

   > [!NOTE]
   > さらに、このリリースでは、次の Azure Arc 対応サービスがプレビューで提供されます。 
   > - 直接接続モードでの SQL Managed Instance
   > - SQL Managed Instance [Business Critical サービス レベル](service-tiers.md)
   > - PostgreSQL Hyperscale

### <a name="breaking-changes"></a>重大な変更

#### <a name="tools"></a>ツール

次のツールをご使用ください。
- [Azure Data Studio の Insider ビルド](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)。
- [Azure (`az`) CLI 向け `arcdata` 拡張機能](install-arcdata-extension.md)。 


#### <a name="data-controller"></a>データ コントローラー

- `--azure-subscription` という名前の `az arcdata dc create` パラメーターが、標準 `--subscription` パラメーターを使用するように変更されました。
- AKS HCI へのデプロイには、特別なストレージ クラス構成が必要です。 詳細については、「[ストレージの構成 (Azure Stack HCI と AKS-HCI)](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci)」を参照してください。
- データをエクスポートするときに SSL 以外の接続を許可するための新しい要件があります。 対話型プロンプトを非表示にするように環境変数を設定します。

### <a name="whats-new"></a>新着情報

#### <a name="data-controller"></a>データ コントローラー

- 直接接続モードはプレビュー段階です。 

- このリリースでは、直接接続モード (プレビュー) は次の Azure リージョンでのみ使用できます。
   - 米国中部
   - 米国東部
   - 米国東部 2
   - 米国西部 2
   - 英国南部
   - 西ヨーロッパ
   - 北ヨーロッパ
   - オーストラリア東部
   - 東南アジア
   - 韓国中部
   - フランス中部

- 現時点では、Grafana 管理エクスペリエンスを使用して、追加の基本認証ユーザーを Grafana に追加できます。 Grafana の .ini ファイルを変更することによる Grafana のカスタマイズは、サポートされていません。

- 現在、ElasticSearch と Kibana の構成を変更することは、Kibana の管理エクスペリエンスで可能な範囲を超えてはサポートされていません。 サポートされているのは、単一ユーザーでの基本認証のみです。
    
- Azure portal のカスタム メトリックはプレビュー段階です。

- コマンド `az arcdata dc export` を使用して使用状況および課金情報、メトリック、ログをエクスポートするには、今のところ SSL 検証をバイパスする必要があります。  SSL 検証をバイパスするように求めるメッセージが表示されます。または、プロンプトを表示しないように環境変数 `AZDATA_VERIFY_SSL=no` を設定できます。  現在、データ コントローラー エクスポート API に対して SSL 証明書を構成する方法はありません。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- 自動バックアップとポイントインタイム リストアはプレビュー段階です。
- Azure Arc 対応 SQL マネージド インスタンス内の既存のデータベースから、同じインスタンス内の新しいデータベースへのポイントインタイム リストアをサポートします。
- 現在の datetime が UTC 形式でポイントインタイムとして指定されている場合、最新の有効な復元時刻に解決され、有効な最後のトランザクションまで特定のデータベースが復元されます。
- データベースは、トランザクションが実行された任意の時点に復元できます。
- Azure Arc 対応 SQL マネージド インスタンスに対して特定の回復ポイント目標を設定するには、SQL マネージド インスタンスの CRD を編集して `recoveryPointObjectiveInSeconds` プロパティを設定します。 サポートされる値は 300 から 600 までです。
- 自動バックアップを無効にするには、SQL インスタンスの CRD を編集して `recoveryPointObjectiveInSeconds` プロパティを 0 に設定します。

### <a name="known-issues"></a>既知の問題

#### <a name="platform"></a>プラットフォーム

- Azure portal を使用して、直接接続モードのクラスター上にデータ コントローラー、SQL マネージド インスタンス、または PostgreSQL Hyperscale サーバー グループを作成できます。 直接接続モードのデプロイは、他の Azure Arc 対応データ サービス ツールではサポートされていません。 具体的には、このリリース中は、次のツールのいずれかを使用して直接接続モードでデータ コントローラーをデプロイすることはできません。
   - Azure Data Studio
   - Kubernetes ネイティブ ツール (`kubectl`)
   - Azure CLI 用 `arcdata` 拡張機能 (`az`)

   「[Azure portal から Azure Arc データ コントローラーを作成する - 直接接続モード](create-data-controller-direct-azure-portal.md)」では、ポータルでデータ コントローラーを作成する方法について説明しています。

- 引き続き `kubectl` を使用して Kubernetes クラスター上にリソースを直接作成することはできますが、直接接続モードを使用している場合、Azure portal には反映されません。

- 直接接続モードで、`az arcdata dc upload` を使用して使用状況、メトリック、およびログをアップロードすることは、仕様によって現在ブロックされています。 使用状況は自動的にアップロードされます。 間接接続モードで作成されたデータ コントローラーのアップロードは引き続き機能します。
- `–proxy-cert <path-t-cert-file>` 経由でプロキシを使用する場合、直接接続モードでの使用状況データの自動アップロードは成功しません。
- Azure Arc 対応 SQL Managed Instance および Azure Arc 対応 PostgreSQL Hyperscale は、GB18030 認定ではありません。
- 現時点では、Kubernetes クラスターごとに 1 つの Azure Arc データ コントローラーのみがサポートされています。

#### <a name="data-controller"></a>データ コントローラー

- Azure Arc データ コントローラーが Azure portal から削除されたとき、この Arc データ コントローラーにデプロイされた Azure Arc 対応 SQL マネージド インスタンスがある場合は、削除をブロックするための検証が行われます。 現在、この検証は、Azure Arc データ コントローラーの [概要] ページから削除が実行された場合にのみ適用されます。 

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- 7 月 30 日のリリースでは、バックアップと復元の操作は機能しなくなりました。 これは一時的な制限です。 バックアップまたは復元を行う必要がある場合、当面は 2021 年 6 月のリリースを使用してください。 これは今後のリリースで修正される予定です。

- `pg_cron` 拡張機能を同時に有効にし、構成することはできません。 これには、2 つのコマンドを使用する必要があります。 1 つは有効化のコマンドで、もう 1 つは構成するコマンドです。 次に例を示します。

   1. 拡張機能の有効化:

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. サーバー グループを再起動します。

   1. 拡張機能の構成:

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   再起動が完了する前に 2 つ目のコマンドを実行すると、失敗します。 そのような場合は、しばらく待ってから、2 つ目のコマンドをもう一度実行してください。

- サーバー グループの構成を編集して追加の拡張機能を有効にするときに `--extensions` パラメーターに無効な値を渡すと、有効な拡張機能の一覧がサーバー グループの作成時の設定に誤ってリセットされ、ユーザーは追加の拡張機能を作成できなくなります。 そのような場合に使用できる唯一の回避策は、サーバー グループを削除して再デプロイすることです。

- 現時点では、ポイントインタイム リストアは NFS ストレージではサポートされていません。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

##### <a name="cant-see-resources-in-portal"></a>ポータルでリソースを表示できない

- 6 月のリリースで作成された Azure Arc 対応 SQL Managed Instance リソースは、ポータルに表示されません。 リソース グループのリスト ビューから SQL Managed Instance リソースを削除してください。 カスタムの場所リソースを最初に削除することが必要な場合があります。

##### <a name="point-in-time-restorepitr-supportability-and-limitations"></a>ポイントインタイム リストア (PITR) のサポートと制限事項:
    
-  1 つの Azure Arc 対応 SQL マネージド インスタンスから、別の Azure Arc 対応 SQL マネージド インスタンスへの復元は、サポートされていません。  データベースは、バックアップが作成されたのと同じ Arc 対応 SQL Managed Instance にのみ復元できます。
-  ポイントインタイム リストアの目的のためには、データベースの名前変更は現在サポートされていません。
-  現時点では、ポイントインタイム リストアに許可される時間枠に関する情報を提供する CLI コマンドや API はありません。 データベースが作成された日時以降の妥当な期間内の時間を指定することができ、タイムスタンプが有効な場合は復元が機能します。 タイムスタンプが有効でない場合は、許可される期間がエラー メッセージを介して示されます。
-  TDE 対応データベースの復元はサポートされていません。
-  現在、削除されたデータベースは復元できません。

#####   <a name="automated-backups"></a>自動バックアップ

-  データベースの名前を変更すると、このデータベースの自動バックアップが停止します。
-  データ保持期間は適用されません。 使用可能な領域がある限り、すべてのバックアップが保持されます。 
-  単純復旧モデルのユーザー データベースはバックアップされません。
-  データベースの作成および削除に対する干渉を防ぐために、システム データベース `model` はバックアップされません。 管理操作が実行されているときは DB がロックされます。 
-  現時点では、`master` および `msdb` システム データベースだけがバックアップされます。 完全バックアップのみが 12 時間ごとに実行されます。
-  バックアップできるのは `ONLINE` ユーザー データベースのみです。
-  既定の回復ポイントの目標 (RPO): 5 分。 現在のリリースでは変更できません。
-  バックアップは無期限に保持されます。 領域を回復するには、手動でバックアップを削除します。

##### <a name="other-limitations"></a>その他の制限事項
- トランザクション レプリケーションは現在サポートされていません。
- ログ配布は現在ブロックされています。
- SQL Server 認証のみがサポートされています。

## <a name="june-2021"></a>2021 年 6 月

このプレビュー リリースは、2021 年 7 月 13 日に公開されています。

### <a name="breaking-changes"></a>重大な変更

#### <a name="new-deployment-templates"></a>新しいデプロイ テンプレート

- データ コントローラー、ブートストラップ、マネージド インスタンス、および SQL マネージド インスタンス用の Kubernetes ネイティブ デプロイ テンプレートが変更されています。 ご使用の .yaml テンプレートを更新してください。 [サンプルの yaml ファイル](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### <a name="new-azure-cli-extension-for-data-controller-and-azure-arc-enabled-sql-managed-instance"></a>データ コントローラーおよび Azure Arc 対応 SQL Managed Instance 用の新しい Azure CLI 拡張機能

このリリースでは、Azure CLI に `arcdata` 拡張機能が導入されています。 この拡張機能を追加するには、次のコマンドを実行します。

```azurecli
az extension add --name arcdata
```

この拡張機能により、データ コントローラーと SQL マネージド インスタンスおよび PostgreSQL Hyperscale リソースのコマンドライン操作がサポートされます。

データ コントローラーのスクリプトを更新するには、`azdata arc dc...` を `az arcdata dc...` に置き換えます。

マネージド インスタンスのスクリプトを更新するには、`azdata arc sql mi...` を `az sql mi-arc...` に置き換えます。

Azure Arc 対応 PostgreSQL Hyperscale の場合は、`azdata arc sql postgres...` を `az postgres arc-server...` に置き換えます。

`azdata` コマンドにこれまで存在していたパラメーターに加えて、Azure CLI 拡張機能 `arcdata` の同じコマンドには、`--k8s-namespace` や `--use-k8s` などのいくつかの新しいパラメーターが必須になりました。 `--use-k8s` パラメーターは、コマンドを Kubernetes API と ARM API のどちらに送る必要があるのかを区別するために使用されます。 現在のところ、Arc 対応データ サービス用の Azure CLI コマンドはすべて、Kubernetes API のみをターゲットにしています。

パラメーター名の短い形式 (たとえば `--core-limit` を `-cl` とするなど) の一部は、削除または変更されています。 新しいパラメーターの短い名前または長い名前を使用してください。

`azdata arc dc export` コマンドは機能しなくなりました。 代わりに `az arcdata dc export` を使用してください

#### <a name="required-property-infrastructure"></a>必須のプロパティ: `infrastructure`

`infrastructure` プロパティは、データ コントローラーをデプロイするときに必須の新しいプロパティです。 このプロパティ値を指定することを考慮して、ご使用の yaml ファイル、azdata または az スクリプト、および ARM テンプレートを調整してください。 使用できる値は、`alibaba`、`aws`、`azure`、`gpc`、`onpremises`、`other` です。

#### <a name="kibana-login"></a>Kibana ログイン

OpenDistro セキュリティ パックが削除されました。 Kibana へのログインは、汎用的なブラウザーのユーザー名とパスワードのプロンプトを使用して実行されます。 追加の認証および承認オプションを構成する方法について後ほど詳しく説明します。

#### <a name="crd-version-bump-to-v1beta1"></a>CRD バージョン `v1beta1` への更新

このリリースでは、すべての CRD のバージョンが `v1alpha1` から `v1beta1` に更新されています。 2021 年 6 月のリリースより前に Arc 対応データ サービスのバージョンをデプロイした場合は、アンインストール プロセスの一環としてすべての CRD を必ず削除してください。 2021 年 6 月のリリースでデプロイされた新しい CRD には、バージョンとして v1beta1 が与えられます。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

自動バックアップ サービスが利用可能になり、既定で有効になっています。 バックアップ ボリュームの空き領域を注意深く監視してください。

### <a name="whats-new"></a>新着情報

このリリースでは、Azure Arc 対応データ サービス用の CLI 拡張機能 `az` が導入されています。 詳細については、上記の「[互換性に影響する変更点](#breaking-change)」を参照してください。

#### <a name="platform"></a>プラットフォーム

#### <a name="data-controller"></a>データ コントローラー

- Azure portal から直接接続モードでデータ コントローラーをデプロイするユーザー エクスペリエンスが効率化されました。 Kubernetes クラスターが Arc 対応になると、Arc データ コントローラー作成ウィザードを使用して、ポータルから 1 回の操作でデータ コントローラーを完全にデプロイできます。 このようにデプロイすると、カスタムの場所と Arc 対応データ サービス拡張機能 (ブートストラップ) も作成されます。 カスタムの場所や拡張機能を事前に作成し、それらを使用するようにデータ コントローラーのデプロイを構成することもできます。
- 新しい `Infrastructure` プロパティは、Arc データ コントローラーをデプロイするときに必須のプロパティです。 このプロパティは課金の目的のために必要になります。 詳細は一般提供時に示されます。
- Azure portal でのデータ コントローラーのユーザー エクスペリエンスに関する、さまざまな操作性の向上。たとえば、Kubernetes クラスターでのデプロイ プロセスの途中にあるリソースのデプロイ状態をより正確に把握できます。
- データ コントローラーによってログが (必要に応じて) 自動的にアップロードされ、メトリックも直接接続モードで Azure にアップロードされるようになりました。
- 監視スタック (メトリックとログ データベースまたはダッシュボード) は、独自のカスタム リソース定義 (CRD) `monitors.arcdata.microsoft.com` にパッケージ化されるようになりました。 このカスタム リソースが作成されると、監視スタック ポッドが作成されます。 削除されると、監視スタック ポッドが削除されます。 データ コントローラーが作成されると、監視カスタム リソースが自動的に作成されます。
- 直接接続モード (プレビュー) でサポートされる新しいリージョン: 米国東部 2、米国西部 2、米国中南部、英国南部、フランス中部、東南アジア、オーストラリア東部。
- 概要ブレードのカスタムの場所リソース グラフに、デプロイされている Arc 対応データ サービス リソースが表示されます。
- データ コントローラーの診断と解決策が Azure portal に追加されました。
- Arc 関連のすべてのカスタム リソースに新しい `Observed Generation` プロパティが追加されました。
- 資格情報マネージャー サービスが追加され、データ コントローラーによって管理されているすべてのサービスへの証明書の自動配布が処理されます。

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- Azure Arc PostgreSQL Hyperscale で NFS ストレージがサポートされています。
- Azure Arc PostgreSQL Hyperscale デプロイで、nodeSelector、nodeAffinity、およびアンチアフィニティを使用したノード割り当て戦略に対して Kubernetes ポッドがサポートされます。
- PostgreSQL Hyperscale サーバー グループをデプロイする場合、または Azure Data Studio と Azure portal からデプロイした後に、ロール (コーディネーターまたは worker) ごとにコンピューティング パラメーター (仮想コアとメモリ) を構成できます。
- Azure portal から、PostgreSQL Hyperscale サーバー グループに作成された PostgreSQL 拡張機能の一覧を表示できるようになりました。
- Azure portal から、Azure に直接接続されているデータ コントローラー上の Arc 対応 PostgreSQL Hyperscale グループを削除できます。


#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

-  自動バックアップが有効になりました。
-  `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` カスタム リソース定義 (CRD) に基づいて新しいカスタム リソースを作成することで、同じ SQL インスタンス上の新しいデータベースとしてデータベース バックアップを復元できるようになりました。 詳細についてはドキュメントを参照してください。 データベースを復元するためのコマンド ライン インターフェイス (`azdata` または `az`)、Azure portal、Azure Data Studio エクスペリエンスはまだありません。
-  このリリースに含まれる SQL エンジンのバイナリのバージョンは、Azure SQL Managed Instance (Azure の PaaS) にグローバルにデプロイされている最新のバイナリに合わせて調整されています。 この調整により、Azure SQL Managed Instance PaaS と Azure Arc 対応 Azure SQL Managed Instance の間でバックアップと復元が可能になります。 互換性の詳細については、後で説明します。
-  Azure portal から直接接続モードで Azure Arc SQL マネージド インスタンスを削除できるようになりました。
-  価格レベル (`GeneralPurpose`、`BusinessCritical`)、ライセンスの種類 (`LicenseIncluded`)、`BasePrice` (AHB 価格に使用)、`developer` を使用するように SQL Managed Instance を構成できるようになりました。 Azure Arc 対応 SQL Managed Instance を使用した場合、一般提供日 (公式発表で 2021 年 7 月 30 日の予定) まで、およびサービスの一般提供バージョンにアップグレードするまで、料金は発生しません。
-  Azure Data Studio の `arcdata` 拡張機能には、SQL マネージド インスタンスをデプロイおよび編集するために構成できる追加のパラメーターが加えられています (エージェントの有効化と無効化、管理者ログイン シークレット、注釈、ラベル、サービス注釈、サービス ラベル、SSL/TLS 構成設定、照合順序、言語、トレース フラグなど)。
-  `azdata` またはカスタム リソース タスクで分散型可用性グループを設定するための新しいコマンド。 これらのコマンドはプレビューの初期段階にあるため、ドキュメントは間もなく提供される予定です。

   > [!NOTE]
   > これらのコマンドは、`az arcdata` 拡張機能に移行されます。

-  `azdata arc dc export` は非推奨とされます。 これは、Azure CLI 用 `arcdata` 拡張機能 (`az`) の `az arcdata dc export` によって置き換えられます。 データのエクスポートに異なる方法が使用されます。データ コントローラー API への直接接続は行われなくなくなりました。 代わりに、`exporttasks.tasks.arcdata.microsoft.com` カスタム リソース定義 (CRD) に基づいてエクスポート タスクが作成されます。 作成されたカスタム リソースのエクスポート タスクにより、ダウンロード可能なパッケージを生成するようにワークフローが処理されます。 Azure CLI の場合、このタスクが完了するまで待機し、タスクのカスタム リソース ステータスからセキュリティで保護された URL を取得してパッケージをダウンロードします。
-  NFS ベースのストレージ クラスの使用をサポート。
- Azure portal に Arc SQL Managed Instance の診断とソリューションが追加されました

## <a name="may-2021"></a>2021 年 5 月

このプレビュー リリースは、2021 年 6 月 2 日に公開されています。

この記事で紹介しているテクノロジはプレビュー機能であり、「[Microsoft Azure プレビューの追加利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に従うことを条件として提供されます。

### <a name="breaking-change"></a>互換性に影響する変更点

- Kubernetes ネイティブ デプロイ テンプレートが変更されました。 .Yml テンプレートを更新してください。
    - データ コントローラー、ブートラップ、SQL マネージド インスタンスのテンプレートを更新しました: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - PostgreSQL Hyperscale のテンプレートを更新しました: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### <a name="whats-new"></a>新機能

#### <a name="platform"></a>プラットフォーム

- Azure portal から、データ コントローラー、SQL マネージド インスタンス、および PostgreSQL Hyperscale サーバー グループを作成および削除します。
- Azure Arc データ サービスを削除するときにポータル アクションを検証します。 たとえば、データ コントローラーを使用してデプロイされた SQL マネージド インスタンスがある場合に、そのデータ コントローラーを削除しようとすると、ポータルによって警告が表示されます。
- Azure portal を使用して、Arc 対応のデータ コントローラーをデプロイするときに、カスタム設定をサポートするカスタム構成プロファイルを作成します。
- 必要に応じて、直接接続モードでログを Azure Log Analytics ワークスペースに自動的にアップロードします。

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

このリリースでは次の特徴と機能が導入されます。

- データ コントローラーが直接接続モード用に構成されている場合は、Azure portal から Azure Arc PostgreSQL Hyperscale を削除します。
- Azure portal で、Azure Database for PostgreSQL デプロイ ページから Azure Arc 対応 PostgreSQL Hyperscale をデプロイします。 「[Azure Database for PostgreSQL デプロイ オプションを選択する - Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)」を参照してください。
- Azure portal から Azure Arc 対応 PostgreSQL Hyperscale をデプロイするときに、ストレージ クラスと Postgres 拡張機能を指定します。
- Azure Arc 対応 PostgreSQL Hyperscale で ワーカー ノードの数を減らします。 この操作 (ワーカー ノードの数を増やすスケールアウトに対して、スケールインと呼ばれる) は `azdata` コマンド ラインから実行できます。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- Arc 対応 SQL Managed Instance の新しい [Azure CLI 拡張機能](/cli/azure/azure-cli-extensions-overview)にも `az sql mi-arc <command>` と同じコマンドがあります。 Arc 対応 SQL Managed Instance のコマンドはすべて、`az sql mi-arc` にあります。 Arc 関連の `azdata` コマンドはすべて非推奨となり、今後のリリースで Azure CLI に移行します。

   拡張機能の追加するには:

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Transact-SQL を使用するフェールオーバーを手動でトリガーします。 次の 2 つのコマンドを順に実行します。

   1. プライマリ レプリカのエンドポイント接続で、次のコマンドを実行します。

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. セカンダリ レプリカのエンドポイント接続で、次のコマンドを実行します。

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- `BACKUP` 設定を使用しない場合、Transact-SQL の `COPY_ONLY` コマンドはブロックされます。 これで、ポイントインタイム リストアの機能がサポートされます。

## <a name="april-2021"></a>2021 年 4 月

このプレビュー リリースは、2021 年 4 月 29 日に公開されています。

### <a name="whats-new"></a>新機能

このセクションでは、このリリースで導入または有効化された新機能について説明します。

#### <a name="platform"></a>プラットフォーム

- 直接接続クラスターによって、テレメトリ情報が自動的に Azure にアップロードされます。

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

- Azure Arc 対応 PostgreSQL Hyperscale が直接接続モードでサポートされるようになりました。 これで、Azure portal の Azure マーケット プレースから、Azure Arc 対応 PostgreSQL Hyperscale をデプロイできるようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale には、カラム型テーブル ストレージを特長とする Citus 10.0 拡張機能が付属しています
- Azure Arc 対応 PostgreSQL Hyperscale で、完全なユーザーおよびロール管理がサポートされるようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale で、`Tdigest` および `pg_partman` を含む追加の拡張機能がサポートされるようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale では、サーバー グループ内の PostgreSQL インスタンスのロールごとに、仮想コアおよびメモリ設定の構成をサポートするようになりました。
- Azure Arc 対応 PostgreSQL Hyperscale では、サーバー グループ内の PostgreSQL インスタンスのロールごとに、データベース エンジンおよびサーバー設定の構成をサポートするようになりました。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- データベースを 3 つのレプリカを持つ SQL Managed Instance に復元すると、それが可用性グループに自動的に追加されます。
- 3 つのレプリカと共にデプロイされた SQL Managed Instance で、読み取り専用のセカンダリ エンドポイントに接続します。 読み取り専用のセカンダリ接続エンドポイントを表示するには、`azdata arc sql endpoint list` を使用します。

## <a name="march-2021"></a>2021 年 3 月

2021 年 3 月のリリースは、2021 年 4 月 5 日に初めて導入され、リリースの最終ステージは 2021 年 4 月 9 日に完了しました。

Azure Data CLI (`azdata`) バージョン番号: 20.3.2。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

### <a name="data-controller"></a>データ コントローラー

- Azure Arc 対応データ サービスのデータ コントローラーをポータルから直接接続モードでデプロイします。 「[データ コントローラーのデプロイ - 直接接続モード - 前提条件](create-data-controller-direct-prerequisites.md)」から始めます。

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

PostgreSQL 用のカスタム リソース定義 (CRD) は、どちらも 1 つの CRD に統合されています。 次の表を参照してください。

|Release |CRD |
|-----|-----|
|2021 年 2 月以前| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|2021 年 3 月以降 | postgresqls.arcdata.microsoft.com

前のインストールをクリーンアップするときに、以前の CRD を削除します。 「[以前のインストールからのクリーンアップ](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)」を参照してください。

### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance

- Azure portal から直接接続モードで SQL マネージド インスタンスを作成できるようになりました。

- データベースを 3 つのレプリカを持つ SQL Managed Instance に復元できるようになりました。これは、可用性グループに自動的に追加されます。

- 3 つのレプリカと共にデプロイされた SQL Managed Instance で、読み取り専用のセカンダリ エンドポイントに接続できるようになりました。 読み取り専用のセカンダリ接続エンドポイントを表示するには、`azdata arc sql endpoint list` を使用します。

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号: 20.3.1。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

追加の更新プログラムには以下が含まれます。

- Azure Arc 対応 SQL Managed Instance
   - Always On 可用性グループによる高可用性

- Azure Arc 対応 PostgreSQL Hyperscale Azure Data Studio:
   - 概要ページに、ノードごとに項目別にされたサーバー グループの状態が表示されます
   - 新しいプロパティ ページにサーバー グループに関する詳細が表示されます
   - **[ノード パラメーター]** ページから Postgres エンジン パラメーターを構成します

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号:20.3.0。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

追加の更新プログラムには以下が含まれます。
- 17 の新しい言語で使用できるローカライズされたポータル
- Kube ネイティブの .yaml ファイルに対する軽微な変更
- 新しいバージョンの Grafana と Kibana
- Azure Data Studio のノートブックで azdata を使用する場合の Python 環境に関する問題が解決されました
- pg_audit 拡張機能を PostgreSQL Hyperscale で使用できるようになりました
- PostgreSQL Hyperscale データベースの完全復元を実行する場合、バックアップ ID は不要になりました
- サーバー グループを構成する PostgreSQL インスタンスごとに状態 (正常性状態) が報告されます

   以前のリリースでは、状態はサーバー グループ レベルで集計され、PostgreSQL ノード レベルでは項目別にまとめられませんでした。

- PostgreSQL のデプロイでは、create コマンドに指定されたボリューム サイズのパラメーターが受け入れられます
- サーバー グループを編集するときに、エンジンのバージョン パラメーターが受け入れられるようになりました
- Azure Arc 対応 PostgreSQL Hyperscale のポッドの名前付け規則が変更されました

    現在は `ServergroupName{c, w}-n` の形式になっています。 たとえば、3 つのノード (1 つのコーディネーター ノードと 2 つのワーカー ノード) を持つサーバー グループは次のように表されます。
   - `Postgres01c-0` (コーディネーター ノード)
   - `Postgres01w-0` (ワーカー ノード)
   - `Postgres01w-1` (ワーカー ノード)

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新機能

Azure データ CLI (`azdata`) バージョン番号:20.2.5。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

Azure Data CLI (`azdata`) で `azdata arc sql endpoint list` および `azdata arc postgres endpoint list` コマンドを使用すると、SQL Managed Instance および PostgreSQL Hyperscale の各エンドポイントが表示されます。

Azure Data Studio を使用して、SQL Managed Instance リソース (CPU コアとメモリ) の要求および制限を編集できます。

Azure Arc 対応 PostgreSQL Hyperscale では、PostgreSQL のバージョン 11 と 12 の両方に対して、完全バックアップの復元に加えて、ポイントインタイム リストアがサポートされるようになりました。 ポイントインタイム リストア機能を使用すると、復元する特定の日付と時刻を指定できます。

Azure Arc 対応 PostgreSQL Hyperscale のポッドの名前付け規則が変更されました。 現在は、ServergroupName{r, s}-_n_ の形式になっています。 たとえば、3 つのノード (1 つのコーディネーター ノードと 2 つのワーカー ノード) を持つサーバー グループは次のように表されます。
- `postgres02r-0` (コーディネーター ノード)
- `postgres02s-0` (ワーカー ノード)
- `postgres02s-1` (ワーカー ノード)

### <a name="breaking-change"></a>互換性に影響する変更点

#### <a name="new-resource-provider"></a>新しいリソース プロバイダー

このリリースでは、`Microsoft.AzureArcData` という名称の更新された[リソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)が導入されました。 この機能を使用するには、事前にこのリソース プロバイダーを登録する必要があります。

リソース プロバイダーを登録するには、次のようにします。

1. Azure portal で、 **[サブスクリプション]** を選択します
2. サブスクリプションの選択
3. **[設定]** で、 **[リソース プロバイダー]** を選択します
4. `Microsoft.AzureArcData` を検索し、 **[登録]** を選択します

詳細な手順については [「Azure リソースプロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。 また、この変更により、Azure portal にアップロードした既存のすべての Azure リソースが削除されます。 このリソース プロバイダーを使用するには、データ コントローラーを更新し、最新の `azdata` CLI を使用する必要があります。

### <a name="platform-release-notes"></a>プラットフォームのリリース ノート

#### <a name="direct-connectivity-mode"></a>直接接続モード

このリリースでは、直接接続モードが導入されます。 直接接続モードでは、データ コントローラーによって使用状況の情報を Azure に自動的にアップロードできます。 使用状況のアップロードの一環として、Arc データ コントローラー リソースがポータルに自動的に作成されます (`azdata` アップロードによって既に作成されていない場合)。

データ コントローラーを作成するときに、直接接続を指定できます。 次の例では、直接接続モード (`connectivity-mode direct`) を使用して `arc` という名前のデータ コントローラーを `az arcdata dc create` によって作成します。 例を実行する前に、`<subscription id>` をお使いのサブスクリプション ID に置き換えてください。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>2020 年 10 月

Azure データ CLI (`azdata`) バージョン番号:20.2.3。 `azdata` は、「[Azure Data CLI (`azdata`) のインストール](/sql/azdata/install/deploy-install-azdata)」に従ってインストールできます。

### <a name="breaking-changes"></a>重大な変更

このリリースでは、次のような破壊的変更が導入されました。

* PostgreSQL カスタム リソース定義 (CRD) で、用語 `shards` が `workers` に名称変更されています。 この用語 (`workers`) は、コマンド ライン パラメーター名と一致します。

* `azdata arc postgres server delete` では、postgres インスタンスを削除する前に確認を求めるメッセージが表示されます。 プロンプトをスキップするには `--force` を使用します。

### <a name="additional-changes"></a>追加の変更

* `--volume-claim mounts` という新しい省略可能パラメーターが `azdata arc postgres server create` に追加されました。 値は、ボリューム要求マウントのコンマ区切りの一覧です。 ボリューム要求マウントは、ボリュームの種類と PVC 名のペアです。 現在サポートされているボリュームの種類は `backup` のみです。 PostgreSQL では、ボリュームの種類が `backup` の場合、PVC は `/mnt/db-backups` にマウントされます。 これにより、PostgresSQL インスタンス間でバックアップを共有でき、1 つの PostgresSQL インスタンスのバックアップを別のインスタンスで復元できるようになります。

* PostgresSQL カスタム リソース定義の新しい短い名前:
  * `pg11`
  * `pg12`
* テレメトリのアップロードでは、次のいずれかがユーザーに提供されます。
   * Azure にアップロードされたポイントの数、または
   * Azure にデータが読み込まれていない場合は、再試行するためのプロンプト。
* `az arcdata dc debug copy-logs` では、`/var/opt/controller/log` フォルダーからの読み取りと、Linux での PostgreSQL エンジン ログの収集も行われるようになりました。
*   PostgreSQL Hyperscale を使用してバックアップを作成および復元するときに、作業インジケーターを表示します。
* `azdata arc postrgres backup list` にバックアップ サイズの情報が含まれるようになりました。
* SQL Managed Instance 管理者名プロパティが Azure portal の概要ブレードの右側の列に追加されました。
* Azure Data Studio では、PostgreSQL Hyperscale のワーカー ノードの数、仮想コア、メモリ設定の構成がサポートされます。
* プレビューで、Postgres バージョン 11 と 12 のバックアップ/復元がサポートされます。

## <a name="september-2020"></a>2020 年 9 月

Azure Arc 対応データ サービスは、パブリック プレビュー用にリリースされています。 Arc 対応データ サービスを使用すると、どこからでもデータ サービスを管理できます。

- SQL Managed Instance
- PostgreSQL Hyperscale

手順については「[Azure Arc 対応データ サービスとは](overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> **試してみたい場合**
> AKS、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。

- [クライアント ツールをインストールする](install-client-tools.md)
- [Azure Arc データ コントローラーを作成する](create-data-controller.md) (先にクライアント ツールをインストールする必要があります)
- [Azure Arc で Azure SQL マネージド インスタンスを作成する](create-sql-managed-instance.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure Arc に Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) (先に Azure Arc データ コントローラーを作成する必要があります)
- [Azure サービスのリソース プロバイダー](../../azure-resource-manager/management/azure-services-resource-providers.md)
