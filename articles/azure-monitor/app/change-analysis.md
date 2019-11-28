---
title: Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける | Microsoft Docs
description: Azure App Service のライブ サイトに関するアプリケーションの問題をトラブルシューティングするには、Azure Monitor でアプリケーション変更分析を使用します。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: ed297a1005f67a14db1da15aba2c47c98e83df9c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884982"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor でアプリケーション変更分析 (プレビュー) を使用する

ライブ サイトの問題や機能停止が発生した場合は、根本的な原因を迅速に特定することが重要です。 標準的な監視ソリューションでは、問題が警告される場合があります。 障害が発生しているコンポーネントが示される場合もあります。 ただし、この警告が障害の原因を直接的に説明しているとは限りません。 サイトは 5 分前には機能していたことがわかっています。そして今は機能していません。 この 5 分間でどのような変更があったのでしょうか。 これは、Azure Monitor でアプリケーション変更分析から回答が得られるように設計されている問題です。

変更分析は、[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) を基にして構築されており、Azure アプリケーションの変更に関する分析情報を提供し、可観測性を高めて MTTR (平均修理時間) を短縮します。

> [!IMPORTANT]
> 変更分析は現在プレビュー段階です。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 このバージョンは運用環境のワークロードにはお勧めできません。 一部の機能はサポート対象ではなく、機能が制限されることがあります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="overview"></a>概要

変更分析では、インフラストラクチャ レイヤーからアプリケーション デプロイにわたって、さまざまな種類の変更を検出します。 サブスクリプション内のリソースの変更を確認するサブスクリプションレベルの Azure リソース プロバイダーです。 変更分析は、問題を引き起こした可能性がある変更をユーザーが理解するために役立つ、さまざまな診断ツール向けのデータを提供します。

以下の図に、変更分析のアーキテクチャを示します。

![どのように変更分析で変更データが取得され、クライアント ツールに提供されるかを示すアーキテクチャ図](./media/change-analysis/overview.png)

現在、変更分析は App Service Web アプリの**問題の診断と解決**エクスペリエンスに統合されているほか、Azure portal のスタンドアロンのブレードで確認できます。
[変更分析] ブレードにアクセスする方法と、Web アプリ ポータル内で使用する方法については、それぞれこの記事で後述する「*Azure のすべてのリソースに対する変更内容を表示する*」セクションと「*Web アプリ機能の変更分析*」を参照してください。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager の追跡プロパティの変更

変更分析では、[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) が使用され、アプリケーションをホストしている Azure リソースが時間と共にどのように変更されたかを示す履歴の記録が提供されます。 マネージド ID、プラットフォーム OS のアップグレード、ホスト名などの追跡設定を検出できます。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager のプロキシ設定の変更
IP 構成ルール、SSL 設定、拡張機能のバージョンなどの設定は、まだ ARG では使用できないため、変更分析ではそれらの変更について確実にクエリを実行して計算し、アプリ内で何が変わったかについての詳細を提供します。 これらの情報は Azure Resource Graph ではまだ利用できませんが、まもなく利用できるようになります。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web アプリのデプロイと構成の変更 (ゲスト内の変更)

変更分析では、4 時間ごとにアプリケーションのデプロイと構成の状態がキャプチャされます。 たとえば、アプリケーション環境変数の変更を検出できます。 このツールで差分が計算され、変更点が提示されます。 Resource Manager の変更とは異なり、ツールでコード デプロイの変更情報をすぐに利用できない場合があります。 変更分析で最新の変更を表示するには、 **[Scan changes now]\(今すぐ変更をスキャン\)** を選択します。

![[Scan changes now]\(今すぐ変更をスキャン\) ボタンのスクリーンショット](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依存関係の変更

リソースの依存関係の変更も、Web アプリで問題を引き起こす場合があります。 たとえば、Web アプリから Redis キャッシュが呼び出される場合、Redis キャッシュ SKU が Web アプリのパフォーマンスに影響を与える可能性があります。 依存関係の変更を検出するために、変更分析では Web アプリの DNS レコードが確認されます。 このようにして、問題を引き起こす可能性のあるすべてのアプリ コンポーネントの変更が特定されます。
現在、次の依存関係がサポートされています。
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>有効化
Azure Resource Manager の追跡プロパティとプロキシ設定の変更データが利用できるようになるには、"Microsoft.ChangeAnalysis" リソース プロバイダーがサブスクリプションに登録されている必要があります。 Web アプリの問題の診断と解決に入る、または [変更分析] スタンドアロン ブレードを起動すると、このリソース プロバイダーが自動的に登録されます。 サブスクリプションに対するパフォーマンスとコストの実装はありません。
Web アプリのゲスト内の変更については、Web アプリ内でコード ファイルをスキャンするには別個の有効化が必要です。 詳細については、この記事の後半の「*問題の診断と解決ツールで変更分析を有効にする*」を参照してください。

## <a name="viewing-changes-for-all-resources-in-azure"></a>Azure のすべてのリソースに対する変更内容を表示する
Azure Monitor では、変更分析のためのスタンドアロン ブレードがあり、すべての変更内容が分析情報およびアプリケーション依存リソースと一緒に表示されます。

Azure portal の検索バーで変更分析を検索し、ブレードを起動します。

![Azure portal における変更分析の検索のスクリーンショット](./media/change-analysis/search-change-analysis.png)

リソース グループとリソースを選択して、変更内容の表示を開始します。

![Azure portal の変更分析ブレードのスクリーンショット](./media/change-analysis/change-analysis-standalone-blade.png)

分析情報およびアプリケーションをホストする関連した依存リソースを表示することもできます。 この表示は、開発者が問題のトラブルシューティングを行うために、アプリケーションを中心にして設計されています。

現在サポートされているリソースは次のとおりです。
- Virtual Machines
- 仮想マシン スケール セット
- Azure ネットワーク リソース
- ゲスト内のファイル追跡と環境変数の変更内容に関する Web アプリ

フィードバックについては、ブレードにあるフィードバックの送信用のボタンを使用するか、changeanalysisteam@microsoft.com まで電子メールを送信してください。

![変更分析ブレードのフィードバック ボタンのスクリーンショット](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Web アプリ機能の変更分析

Azure Monitor では、変更分析はセルフサービスの**問題の診断と解決**エクスペリエンスにも組み込まれています。 このエクスペリエンスには、App Service アプリケーションの **[概要]** ページからアクセスします。

![[概要] ボタンと [問題の診断と解決] ボタンのスクリーンショット](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>問題の診断と解決ツールで変更分析を有効にする

1. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択します。

    ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/availability-and-performance.png)

1. **[Application Changes]\(アプリケーションの変更\)** を選択します。 この機能は、 **[アプリケーションのクラッシュ]** でも使用できることに注意してください。

   ![[アプリケーションのクラッシュ] ボタンのスクリーンショット](./media/change-analysis/application-changes.png)

1. 変更分析を有効にするには、 **[今すぐ有効にする]** を選択します。

   ![[アプリケーションのクラッシュ] オプションのスクリーンショット](./media/change-analysis/enable-changeanalysis.png)

1. **変更分析**を有効にして **[保存]** を選択します。 ツールでは、すべての Web アプリが [App Service プラン] の下に表示されます。 プラン レベルのスイッチを使用して、プランの下にあるすべての Web アプリの変更分析をオンにできます。

    !["変更分析を有効にする" ユーザー インターフェイスのスクリーンショット](./media/change-analysis/change-analysis-on.png)


1. 変更分析にアクセスするには、 **[問題の診断と解決]**  >  **[Availability and Performance]\(可用性とパフォーマンス\)**  >  **[アプリケーションのクラッシュ]** の順に選択します。 変更の種類を時系列でまとめたグラフと、その変更の詳細が表示されます。 既定では、当面の問題を解決できるように、過去 24 時間の変更が表示されます。

     ![変更の差分ビューのスクリーンショット](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>大規模な変更分析を有効にする

サブスクリプションに多数の Web アプリが含まれている場合、Web アプリのレベルでサービスを有効にすることは非効率的です。 サブスクリプション内のすべての Web アプリを有効にするには、次のスクリプトを実行します。

前提条件:
* PowerShell Az モジュール。 「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)」の手順に従います

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



## <a name="next-steps"></a>次の手順

- [Azure App Service アプリ](azure-web-apps.md)の Application Insights を有効にします。
- [Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ](azure-vm-vmss-apps.md)の Application Insights を有効にします。
- [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) の詳細を参照してください。変更分析の強化に役立ちます。
