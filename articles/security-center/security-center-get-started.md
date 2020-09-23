---
title: Azure Defender にアップグレードする - Azure Security Center
description: このクイックスタートでは、セキュリティを強化するために Security Center の Azure Defender にアップグレードする方法を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: e51d0bfb79eab4db9bb571cc0f4ee70ada352d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895772"
---
# <a name="quickstart-setting-up-azure-security-center"></a>クイック スタート:Azure Security Center を設定する

Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と脅威保護を実現します。 無料の機能で提供されるのは Azure リソースのみの制限付きセキュリティですが、Azure Defender ではこれらの機能がオンプレミスと他のクラウドに拡張されます。 Azure Defender は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用した脅威の検出、攻撃を受けたときのすばやい対応を支援します。 Azure Defender は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

この記事では、セキュリティを強化するために Azure Defender にアップグレードし、ご使用のマシンに Log Analytics エージェントをインストールしてセキュリティの脆弱性と脅威を監視します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

サブスクリプションで Azure Defender を有効にするには、サブスクリプションの所有者、サブスクリプションの共同作成者、またはセキュリティ管理者の役割が割り当てられている必要があります。


## <a name="open-security-center-for-the-first-time"></a>Security Center を初めて開く

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

1. ポータルのメニューから **[セキュリティ センター]** を選択します。 

    Security Center の概要ページが開きます。

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Security Center の概要ダッシュボード" lightbox="./media/security-center-get-started/overview.png":::

**[セキュリティ センター - 概要]** では、ハイブリッド クラウド ワークロードのセキュリティ対策の統合ビューが表示されるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 ご自身や他のサブスクリプション ユーザーによってまだオンボードされていない Azure サブスクリプションは、自動的に無料で有効になります。

**[サブスクリプション]** メニュー項目を選択して、サブスクリプションの一覧を表示およびフィルター処理できます。 選択されたサブスクリプションのセキュリティの状態を反映するように、Security Center の表示が調整されます。 

Security Center を初めて起動してから数分以内に、以下の項目が表示されます。

- 接続されているリソースのセキュリティを向上させる方法についての**推奨事項**。
- Security Center によって現在評価されているリソースとそれぞれのセキュリティの状態のインベントリ。

Security Center をフルに活用するには、次の手順に従って Azure Defender にアップグレードし、Log Analytics エージェントをインストールする必要があります。


## <a name="enable-azure-defender"></a>Azure Defender を有効にする

Security Center のクイックスタートおよびチュートリアルの目的上、Azure Defender を有効にする必要があります。 30 日間の無料試用版が提供されています。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 

1. Security Center のサイドバーで、 **[作業の開始]** を選択します。

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="[はじめに] ページの [アップグレード] タブ"::: 

    **[アップグレード]** タブに、オンボードの対象となるサブスクリプションとワークスペースが一覧表示されます。

1. **[Azure Defender を有効にするワークスペースを選択してください]** リストから、アップグレードするワークスペースを選択します。
   - 試用版の対象にならないサブスクリプションやワークスペースを選択した場合、次の手順でそれらがアップグレードされ、課金が開始されます。
   - 無料試用版の対象となるワークスペースを選択した場合、次の手順で試用版が開始されます。
1. **[アップグレード]** を選択すると、Azure Defender が有効になります。

## <a name="enable-automatic-data-collection"></a>自動データ収集を有効にする
Security Center は、セキュリティの脆弱性と脅威を監視するために、ご使用のマシンからデータを収集します。 データは、Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 既定では、Security Center によって新しいワークスペースが作成されます。

自動プロビジョニングを有効にすると、Security Center は、サポートされているすべてのマシンと新しく作成される VM に Log Analytics エージェントをインストールします。 自動プロビジョニングを強くお勧めします。

Log Analytics エージェントの自動プロビジョニングを有効にするには、次の手順に従います。

1. Security Center のメニューから、 **[価格と設定]** を選択します。
1. 関連するサブスクリプションを選択します。
1. **[データ収集]** ページで、 **[自動プロビジョニング]** を **[オン]** に設定します。
1. **[保存]** を選択します。

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Log Analytics エージェントの自動プロビジョニングの有効化":::

>[!TIP]
> ワークスペースをプロビジョニングする必要がある場合は、エージェントのインストールに最大で 25 分かかることがあります。

お使いのマシンにエージェントが展開されていることで、Security Center は、システムの更新状態、OS セキュリティ構成、エンドポイント保護に関連する追加の推奨事項を提供し、追加のセキュリティ アラートを生成できます。

>[!NOTE]
> 自動プロビジョニングを**オフ**に設定しても、Log Analytics エージェントが既にプロビジョニングされている Azure VM から、そのエージェントは削除されません。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。



## <a name="next-steps"></a>次のステップ
このクイックスタートでは、Azure Defender を有効にし、ハイブリッド クラウド ワークロード全体での統合セキュリティの管理と脅威保護のために Log Analytics エージェントをプロビジョニングしました。 Security Center の詳しい使用方法を学習するには、オンプレミスおよび他のクラウドの Windows コンピューターのオンボードに関するクイックスタートに進みます。

> [!div class="nextstepaction"]
> [クイック スタート: Azure 以外のマシンをオンボードする](quickstart-onboard-machines.md)

クラウドの支出を最適化して節約しますか?

> [!div class="nextstepaction"]
> [Cost Management を使用してコスト分析を開始する](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
