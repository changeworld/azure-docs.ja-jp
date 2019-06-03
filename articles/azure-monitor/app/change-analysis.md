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
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415854"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure Monitor アプリケーションの変更の分析 (パブリック プレビュー)

ライブ サイトの問題/障害が発生した場合は、根本原因をすばやく特定することが重要です。 標準の監視ソリューションは、問題があることをすばやく識別し、多くの場合、障害が発生しているコンポーネントを識別するのにも役立ちます。 しかし、これが障害が発生している原因を即座に究明することに必ずしもつながらない場合があります。 5 分前に動作していたサイトが、今は停止しています。 この 5 分間でどのような変更があったのでしょうか。 新機能の Azure Monitor アプリケーションの変更の分析は、この質問に対する答えを提供するために設計されています。 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) アプリケーションの変更の分析機能を基に構築することで、Azure App Service アプリケーションの変更に関する分析情報を提供して、可観測性を高め、MTTR (平均修理時間) を短縮します。

> [!IMPORTANT]
> Azure Monitor アプリケーションの変更の分析は現在、パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="how-do-i-use-application-change-analysis"></a>アプリケーションの変更の分析の使用方法

Azure Monitor アプリケーションの変更の分析は現在、セルフ サービスの**問題の診断と解決**エクスペリエンスに組み込まれており、ご利用の Azure App Service の **[概要]** セクションからアクセスできます。

![[概要] ボタンと [問題の診断と解決] ボタンが赤いボックスで囲まれた、Azure App Service の概要ページのスクリーンショット](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>変更の分析の有効化

1. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択します。

    ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/availability-and-performance.png)

2. **[アプリケーションのクラッシュ]** タイルをクリックします。

   ![[アプリケーションのクラッシュ] タイルが表示されたスクリーンショット](./media/change-analysis/application-crashes-tile.png)

3. **[Change Analysis]\(変更の分析\)** を有効にするには、**[今すぐ有効にする]** を選択します。

   ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/application-crashes.png)

4. 変更の分析の機能をフル活用するため、**[Change Analysis]\(変更の分析\)**、**[Scan for code changes]\(コード変更のスキャン\)**、および **[Always On]** を **[オン]** に設定して、**[保存]** を選択します。

    ![変更の分析を有効にする Azure App Service のユーザー インターフェイスのスクリーンショット](./media/change-analysis/change-analysis-on.png)

    **[Change Analysis]\(変更の分析\)** を有効にすると、リソース レベルの変更を検出できるようになります。 **[Scan for code changes]\(コード変更のスキャン\)** を有効にすると、デプロイ ファイルとサイト構成の変更も確認できます。 **[Always On]** を有効にすると、変更のスキャンのパフォーマンスを最適化できますが、課金の観点からは追加のコストが発生する可能性があります。

5.  すべてを有効にして、**[問題の診断と解決]** > **[Availability and Performance]\(可用性とパフォーマンス\)** > **[アプリケーションのクラッシュ]** の順に選択すると、変更の分析のエクスペリエンスにアクセスできるようになります。 発生した変更の種類とその詳細が時間の経過とともにグラフにまとめられます。

     ![変更の差分ビューのスクリーンショット](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="unable-to-fetch-change-analysis-information"></a>変更の分析情報をフェッチできません。

これは、現在プレビューのオンボード エクスペリエンスでの一時的な問題です。 回避策は、ご利用の Web アプリで非表示タグを設定してから、ページを更新することです。

   ![変更の非表示タグのスクリーンショット](./media/change-analysis/hidden-tag.png)

PowerShell を使用して非表示タグを設定するには:

1. Azure Cloud Shell を開きます。

    ![Azure Cloud Shell の変更のスクリーンショット](./media/change-analysis/cloud-shell.png)

2. シェルの種類を PowerShell に変更します。

   ![Azure Cloud Shell の変更のスクリーンショット](./media/change-analysis/choose-powershell.png)

3. 次のコマンドを実行します。

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
