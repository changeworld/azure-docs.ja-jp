---
title: Azure Monitor アプリケーションの変更の分析 - Azure Monitor アプリケーションの変更の分析を使用して、ライブ サイトの問題/障害に影響する可能性がある変更を検出する | Microsoft Docs
description: Azure Monitor アプリケーションの変更の分析を使用した Azure App Service でのライブ サイトの問題のトラブルシューティング
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226315"
---
# <a name="application-change-analysis-public-preview"></a>アプリケーションの変更分析 (パブリック プレビュー)

ライブ サイトの問題/障害が発生した場合は、根本原因をすばやく特定することが重要です。 標準の監視ソリューションは、問題があることをすばやく識別し、多くの場合、障害が発生しているコンポーネントを識別するのにも役立ちます。 しかし、これが障害が発生している原因を即座に究明することに必ずしもつながらない場合があります。 5 分前に動作していたサイトが、今は停止しています。 この 5 分間でどのような変更があったのでしょうか。 新機能の Azure Monitor アプリケーションの変更の分析は、この質問に対する答えを提供するために設計されています。 変更分析は、[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) アプリケーションを基にして構築されており、Azure アプリケーションの変更に関する分析情報を提供し、可観測性を高めて MTTR (平均修理時間) を短縮します。

> [!IMPORTANT]
> Azure Monitor アプリケーションの変更の分析は現在、パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="overview-of-change-analysis-service"></a>変更分析サービスの概要
変更分析サービスでは、インフラストラクチャ レイヤーからアプリケーション デプロイにわたって、さまざまな種類の変更を検出します。 サブスクリプション レベルの Azure リソース プロバイダーであり、問題を引き起こした可能性がある要因をユーザーが把握できるように、サブスクリプションでのリソースの変更を調査して、さまざまな診断ツールにデータを提供します。

以下の図に、変更分析サービスのアーキテクチャを示します。![変更分析サービスによって変更データを取得して、クライアント ツールにデータを提供する方法を示したアーキテクチャの図](./media/change-analysis/overview.png)

ツールは現在、App Services Web アプリの問題の診断と解決エクスペリエンスに統合されています。 有効にして Web アプリに対して行われた変更を表示する方法については、"*App Services Web アプリに対する変更分析サービス*" セクションをご覧ください。

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager デプロイの変更
変更分析ツールである [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) を活用すると、アプリケーションをホストしている Azure リソースが時間と共にどのように変更されたかを示す履歴の記録が提供されます。 たとえば、Web アプリに追加されたタグがある場合、変更分析ツールにその変更が反映されます。
この情報は、`Microsoft.ChangeAnalysis` リソース プロバイダーが Azure サブスクリプションにオンボードされている限り、常に利用できます。

### <a name="web-application-deployment-and-configuration-changes"></a>Web アプリケーションのデプロイと構成の変更
変更分析ツールでは、アプリケーションのデプロイと構成の状態を 4 時間ごとにキャプチャし、差異を計算して変更内容を示します。 このような変更の例には、アプリケーション環境変数の変更、IP コンフィグレーション ルールの変更、マネージド サービス ID の変更、SSL 設定の変更などが含まれます。
Resource Manager の変更とは異なり、この種類の変更情報はツール内ですぐには利用可能にならない場合があります。 最新の変更を表示するには、ツール内の [Scan changes now]\(変更を今すぐスキャン\) ボタンを使用します。

![App Service Web アプリに変更分析の統合を利用した、問題の診断と解決ツールにある [Scan for changes now]\(変更を今すぐスキャン\) ボタンのスクリーンショット](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依存関係の変更
依存関係リソースも、問題の原因になる場合があります。 たとえば、Web アプリが Redis キャッシュの呼び出しを行った場合、Web アプリのパフォーマンスは Redis キャッシュの SKU から影響を受ける可能性があります。 Web アプリの DNS レコードを調べることで、変更分析サービスでは、問題を引き起こした可能性があるアプリの全コンポーネントでの変更を特定するために、依存関係の変更情報も提示します。


## <a name="change-analysis-service-for-app-services-web-app"></a>App Services Web アプリに対する変更分析サービス

Azure Monitor アプリケーションの変更の分析は現在、セルフ サービスの**問題の診断と解決**エクスペリエンスに組み込まれており、ご利用の Azure App Service の **[概要]** セクションからアクセスできます。

![[概要] ボタンと [問題の診断と解決] ボタンが赤いボックスで囲まれた、Azure App Service の概要ページのスクリーンショット](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>問題の診断と解決ツールで変更分析を有効にする

1. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択します。

    ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/availability-and-performance.png)

2. **[アプリケーションのクラッシュ]** タイルをクリックします。

   ![[アプリケーションのクラッシュ] タイルが表示されたスクリーンショット](./media/change-analysis/application-crashes-tile.png)

3. **[Change Analysis]\(変更の分析\)** を有効にするには、 **[今すぐ有効にする]** を選択します。

   ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/application-crashes.png)

4. 変更の分析の機能をフル活用するため、 **[Change Analysis]\(変更の分析\)** 、 **[Scan for code changes]\(コード変更のスキャン\)** 、および **[Always On]** を **[オン]** に設定して、 **[保存]** を選択します。

    ![変更の分析を有効にする Azure App Service のユーザー インターフェイスのスクリーンショット](./media/change-analysis/change-analysis-on.png)

    **[Change Analysis]\(変更の分析\)** を有効にすると、リソース レベルの変更を検出できるようになります。 **[Scan for code changes]\(コード変更のスキャン\)** を有効にすると、デプロイ ファイルとサイト構成の変更も確認できます。 **[Always On]** を有効にすると、変更のスキャンのパフォーマンスを最適化できますが、課金の観点からは追加のコストが発生する可能性があります。

5.  すべてを有効にして、 **[問題の診断と解決]**  >  **[Availability and Performance]\(可用性とパフォーマンス\)**  >  **[アプリケーションのクラッシュ]** の順に選択すると、変更の分析のエクスペリエンスにアクセスできるようになります。 発生した変更の種類とそれらの変更の詳細が、時間の経過に沿ってグラフにまとめられます。

     ![変更の差分ビューのスクリーンショット](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>変更分析サービスを一括で有効にする
サブスクリプションに多数の Web アプリがある場合、Web アプリ レベルごとにサービスを有効化するのは非効率です。 代わりに使用できるオンボード手順を、以下に示します。

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>サブスクリプション用の変更分析リソース プロバイダーを登録する

1. 変更分析のプレビュー機能フラグを登録します

    この機能はプレビュー段階にあるため、お使いのサブスクリプションに表示されるように、最初に機能フラグを登録する必要があります。
    - [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を開きます。

    ![Azure Cloud Shell の変更のスクリーンショット](./media/change-analysis/cloud-shell.png)

    - シェルの種類を PowerShell に変更します。

    ![Azure Cloud Shell の変更のスクリーンショット](./media/change-analysis/choose-powershell.png)

    - 次の PowerShell コマンドを実行します。

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. サブスクリプション用の変更分析リソース プロバイダーを登録します

    - サブスクリプションに移動し、変更サービスをオンボードするサブスクリプションを選択してから、リソース プロバイダーをクリックします。

        ![サブスクリプション ブレードから変更分析 RP を登録するスクリーンショット](./media/change-analysis/register-rp.png)

    - *[Microsoft.ChangeAnalysis]* を選択して、ページの上部にある *[登録]* をクリックします。

    - リソース プロバイダーがオンボードされたら、下の "*変更分析情報をフェッチできない*" 場合の手順に従って、Web アプリに非表示のタグを設定し、Web アプリ上でデプロイ レベルの変更検出を有効にします。

3. 上記の手順 2 の代わりに、PowerShell スクリプトを利用してリソース プロバイダーを登録してもかまいません。

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. PowerShell を使用して Web アプリに非表示のタグを設定するには、次のコマンドを実行します。

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> 非表示タグを追加してから、最初のビューを変更できるようになるまで、最初は場合によっては最大 4 時間待機する必要があります。 これは、スキャンのパフォーマンスに与える影響を制限しながら、Web アプリをスキャンするために変更の分析サービスが使用している頻度が 4 時間だからです。

## <a name="next-steps"></a>次の手順

- Azure Monitor の [Application Insights 機能を有効にすることで](azure-web-apps.md) Azure App Services の監視を改善する。
- Azure Monitor アプリケーションの変更の分析の活用に役立つ [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) の理解を深める。
