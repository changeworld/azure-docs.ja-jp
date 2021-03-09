---
title: Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける | Microsoft Docs
description: Azure App Service のライブ サイトに関するアプリケーションの問題をトラブルシューティングするには、Azure Monitor でアプリケーション変更分析を使用します。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 0f541df091733c081c77e41ebff4d0d0d93dca96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573921"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor でアプリケーション変更分析 (プレビュー) を使用する

ライブ サイトの問題や機能停止が発生した場合は、根本的な原因を迅速に特定することが重要です。 標準的な監視ソリューションでは、問題が警告される場合があります。 障害が発生しているコンポーネントが示される場合もあります。 ただし、この警告が障害の原因を直接的に説明しているとは限りません。 サイトは 5 分前には機能していたことがわかっています。そして今は機能していません。 この 5 分間でどのような変更があったのでしょうか。 これは、Azure Monitor でアプリケーション変更分析から回答が得られるように設計されている問題です。

変更分析は、[Azure Resource Graph](../../governance/resource-graph/overview.md) を基にして構築されており、Azure アプリケーションの変更に関する分析情報を提供し、可観測性を高めて MTTR (平均修理時間) を短縮します。

> [!IMPORTANT]
> 変更分析は現在プレビュー段階です。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 このバージョンは運用環境のワークロードにはお勧めできません。 一部の機能はサポート対象ではなく、機能が制限されることがあります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="overview"></a>概要

変更分析では、インフラストラクチャ レイヤーからアプリケーション デプロイにわたって、さまざまな種類の変更を検出します。 サブスクリプション内のリソースの変更を確認するサブスクリプションレベルの Azure リソース プロバイダーです。 変更分析は、問題を引き起こした可能性がある変更をユーザーが理解するために役立つ、さまざまな診断ツール向けのデータを提供します。

以下の図に、変更分析のアーキテクチャを示します。

![どのように変更分析で変更データが取得され、クライアント ツールに提供されるかを示すアーキテクチャ図](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>サポートされているリソースの種類

アプリケーション変更分析サービスでは、次のような一般的なリソースを含む、すべての Azure リソースの種類でのリソース プロパティ レベルの変更がサポートされます。
- 仮想マシン
- 仮想マシン スケール セット
- App Service
- Azure Kubernetes Service
- Azure 関数
- ネットワーク リソース:ネットワーク セキュリティ グループ、Virtual Network、Application Gateway など。
- データ サービス:Storage、SQL、Redis Cache、Cosmos DB など。

## <a name="data-sources"></a>データ ソース

アプリケーション変更分析では、Azure Resource Manager の追跡プロパティ、プロキシ設定された構成、および Web アプリのゲスト内の変更に対するクエリが実行されます。 さらに、サービスはリソース依存関係の変更を追跡して、アプリケーションの診断と監視をエンド ツー エンドで行います。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager の追跡プロパティの変更

変更分析では、[Azure Resource Graph](../../governance/resource-graph/overview.md) が使用され、アプリケーションをホストしている Azure リソースが時間と共にどのように変更されたかを示す履歴の記録が提供されます。 マネージド ID、プラットフォーム OS のアップグレード、ホスト名などの追跡設定を検出できます。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager のプロキシ設定の変更

IP 構成ルール、TLS 設定、拡張機能のバージョンなどの設定は、まだ Azure Resource Graph では使用できないため、変更分析ではそれらの変更について確実にクエリを実行して計算し、アプリ内で何が変わったかについての詳細を提供します。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web アプリのデプロイと構成の変更 (ゲスト内の変更)

変更分析では、4 時間ごとにアプリケーションのデプロイと構成の状態がキャプチャされます。 たとえば、アプリケーション環境変数の変更を検出できます。 このツールで差分が計算され、変更点が提示されます。 Resource Manager の変更とは異なり、ツールでコード デプロイの変更情報をすぐに利用できない場合があります。 変更分析で最新の変更を表示するには、 **[Refresh]\(最新の情報に更新\)** を選択します。

![[Scan changes now]\(今すぐ変更をスキャン\) ボタンのスクリーンショット](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依存関係の変更

リソースの依存関係の変更も、リソースで問題を引き起こす場合があります。 たとえば、Web アプリから Redis キャッシュが呼び出される場合、Redis キャッシュ SKU が Web アプリのパフォーマンスに影響を与える可能性があります。 別の例として、仮想マシンのネットワーク セキュリティ グループでポート 22 が閉じられた場合は、接続エラーが発生します。

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Web アプリに関する問題の診断と解決のナビゲーター (プレビュー)

依存関係の変更を検出するために、変更分析では Web アプリの DNS レコードが確認されます。 このようにして、問題を引き起こす可能性のあるすべてのアプリ コンポーネントの変更が特定されます。
現在、次の依存関係は **Web アプリに関する問題の診断と解決 | ナビゲーター (プレビュー)** でサポートされています。

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>関連リソース

アプリケーション変更分析により、関連リソースが検出されます。 一般的な例として、仮想マシンに関連するネットワーク セキュリティ グループ、Virtual Network、Application Gateway および Load Balancer があります。
通常、ネットワーク リソースは、それを使用するリソースと同じリソース グループに自動的にプロビジョニングされるため、リソース グループによる変更をフィルター処理すると、仮想マシンおよび関連するネットワーク リソースのすべての変更が表示されます。

![ネットワークの変更のスクリーンショット](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>アプリケーション変更分析サービスの有効化

アプリケーション変更分析サービスは、前述のデータ ソースから変更データを計算して集計します。 これには、ユーザーがすべてのリソースの変更参照したり、トラブルシューティングや監視のコンテキストに関係する変更を特定したりするための一連の分析機能が用意されています。
Azure Resource Manager の追跡プロパティとプロキシ設定の変更データが利用できるようになるには、"Microsoft.ChangeAnalysis" リソース プロバイダーがサブスクリプションに登録されている必要があります。 Web アプリの問題の診断と解決ツールに入る、または [変更分析] スタンドアロン タブを起動すると、このリソース プロバイダーが自動的に登録されます。
Web アプリのゲスト内の変更については、Web アプリ内でコード ファイルをスキャンするには別個の有効化が必要です。 詳細については、この記事の後半の[問題の診断と解決ツールの変更分析](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool)に関するセクションを参照してください。

## <a name="cost"></a>コスト
アプリケーション変更分析は無料のサービスであり、これが有効になっているサブスクリプションに対する課金コストは発生しません。 また、Azure リソース プロパティの変更をスキャンしても、サービスのパフォーマンスに影響はありません。 Web アプリのゲスト内のファイル変更に対して変更分析を有効にすると (あるいは、問題の診断と解決ツールを有効にすると)、Web アプリのパフォーマンスに影響が出ますが、それは無視しても構わない程度であり、また、課金コストは発生しません。

## <a name="visualizations-for-application-change-analysis"></a>アプリケーション変更分析の視覚化

### <a name="standalone-ui"></a>スタンドアロン UI

Azure Monitor では、変更分析のためのスタンドアロン ウィンドウがあり、アプリケーションの依存関係とリソースに関する分析情報の変更を表示できます。

Azure portal の検索バーで変更分析を検索し、エクスペリエンスを起動します。

![Azure portal における変更分析の検索のスクリーンショット](./media/change-analysis/search-change-analysis.png)

選択したサブスクリプションのすべてのリソースの、過去 24 時間の変更内容が表示されます。 ページ読み込みのパフォーマンスを最適化するために、サービスでは一度に 10 個のリソースが表示されます。 次のページをクリックして、その他のリソースを表示します。 この制限については、削除するよう取り組んでいる最中です。

![Azure portal の変更分析ブレードのスクリーンショット](./media/change-analysis/change-analysis-standalone-blade.png)

リソースをクリックすると、そのすべての変更が表示されます。 必要に応じて、変更をドリルダウンして JSON 形式の変更の詳細と分析情報を表示します。

![変更の詳細のスクリーンショット](./media/change-analysis/change-details.png)

フィードバックについては、ブレードにあるフィードバックの送信用のボタンを使用するか、changeanalysisteam@microsoft.com まで電子メールを送信してください。

![変更分析ブレードのフィードバック ボタンのスクリーンショット](./media/change-analysis/change-analysis-feedback.png)

#### <a name="multiple-subscription-support"></a>複数のサブスクリプションのサポート
UI では、リソースの変更を表示する複数のサブスクリプションの選択がサポートされています。 次のようにサブスクリプション フィルターを使用します。

![複数のサブスクリプションの選択をサポートするサブスクリプション フィルターのスクリーンショット](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Web アプリに関する問題の診断と解決

Azure Monitor では、変更分析はセルフサービスの **問題の診断と解決** エクスペリエンスにも組み込まれています。 このエクスペリエンスには、App Service アプリケーションの **[概要]** ページからアクセスします。

![[概要] ボタンと [問題の診断と解決] ボタンのスクリーンショット](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>問題の診断と解決ツールのアプリケーション変更分析

アプリケーション変更分析は、Web アプリの問題の診断と解決ツールに含まれているスタンドアロン検出機能です。 また、 **[アプリケーションのクラッシュ]** と **[Web App Down detectors]\(Web アプリのダウン検出\)** にも含まれています。 [問題の診断と解決] ツールを開くと、**Microsoft.ChangeAnalysis** リソース プロバイダーが自動的に登録されます。 次の手順に従って、ゲストでの Web アプリの変更追跡を有効にします。

1. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択します。

    ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/availability-and-performance.png)

2. **[Application Changes]\(アプリケーションの変更\)** を選択します。 この機能は、 **[アプリケーションのクラッシュ]** でも使用できます。

   ![[アプリケーションのクラッシュ] ボタンのスクリーンショット](./media/change-analysis/application-changes.png)

3. このリンクから、Web アプリにスコープ指定された Application Change Aalysis UI につながります。 Web アプリのゲスト内の変更追跡が有効になっていない場合、バナーに従って、ファイルとアプリの設定の変更を取得します。

   ![[アプリケーションのクラッシュ] オプションのスクリーンショット](./media/change-analysis/enable-changeanalysis.png)

4. **変更分析** を有効にして **[保存]** を選択します。 ツールでは、すべての Web アプリが [App Service プラン] の下に表示されます。 プラン レベルのスイッチを使用して、プランの下にあるすべての Web アプリの変更分析をオンにできます。

    !["変更分析を有効にする" ユーザー インターフェイスのスクリーンショット](./media/change-analysis/change-analysis-on.png)

5. 変更データは、 **[Web アプリのダウン]** および **[アプリケーションのクラッシュ]** 検出機能の選択でも使用でき ます。 変更の種類を時系列でまとめたグラフと、その変更の詳細が表示されます。 既定では、当面の問題を解決できるように、過去 24 時間の変更が表示されます。

     ![変更の差分ビューのスクリーンショット](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>仮想マシンの問題の診断と解決

仮想マシンの問題の診断と解決ツールにアクセスします。  **[トラブルシューティング ツール]** に移動し、ページの下を参照して **[最近の変更の分析]** を選択し、仮想マシンの変更内容を確認します。

![VM の問題の診断と解決のスクリーンショット](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![トラブルシューティング ツールの変更アナライザー](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>アクティビティ ログ変更履歴
アクティビティ ログの[変更履歴の表示](../essentials/activity-log.md#view-change-history)機能では、アプリケーション変更分析サービス バックエンドを呼び出して、操作に関連付けられている変更を取得します。 **変更履歴** では、[Azure Resource Graph](../../governance/resource-graph/overview.md) が直接呼び出されていましたが、バックエンドが交換され、アプリケーション変更分析が呼び出されるようになったため、返される変更に、[Azure リソースグラフ](../../governance/resource-graph/overview.md)からのリソースレベルの変更、 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) からのリソース プロパティ、App Services Web アプリなどの PaaS サービスからのゲスト内の変更が含まれるようになります。 アプリケーション変更分析サービスで、ユーザーのサブスクリプションの変更をスキャンできるようにするには、リソース プロバイダーを登録する必要があります。 **[変更履歴]** タブに初めて入ると、ツールによって **Microsoft.ChangeAnalysis** リソース プロバイダーの登録が自動的に開始されます。 登録後、**Azure Resource Graph** からの変更がすぐに利用できるようになり、過去 14 日間が対象になります。 ほかのソースからの変更は、サブスクリプションのオンボードから 4 時間後に使用できるようになります。

![アクティビティ ログ変更履歴の統合](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>VM Insights の統合
[VM Insights](../vm/vminsights-overview.md) を有効にしているユーザーは、CPU やメモリなどのメトリック グラフでスパイクを発生させた可能性がある仮想マシンの変更内容を表示して、その原因を考えることができます。 変更データは、VM Insights のサイド ナビゲーションバーに統合されています。 ユーザーは、VM で変更が発生したかどうかを表示し、 **[変更の調査]** をクリックして、アプリケーション変更分析スタンドアロン UI で変更の詳細を表示できます。

[![VM insights の統合](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)


## <a name="enable-change-analysis-at-scale"></a>大規模な変更分析を有効にする

サブスクリプションに多数の Web アプリが含まれている場合、Web アプリのレベルでサービスを有効にすることは非効率的です。 サブスクリプション内のすべての Web アプリを有効にするには、次のスクリプトを実行します。

前提条件:

- PowerShell Az モジュール。 「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」の手順に従います

次のスクリプトを実行します。

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="next-steps"></a>次のステップ

- [変更分析での視覚化](change-analysis-visualizations.md)について学習する
- [変更分析の問題をトラブルシューティングする](change-analysis-troubleshoot.md)方法について学習する
- [Azure App Service アプリ](azure-web-apps.md)の Application Insights を有効にします。
- [Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ](azure-vm-vmss-apps.md)の Application Insights を有効にします。
