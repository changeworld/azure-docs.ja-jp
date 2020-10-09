---
title: Azure Security Center の価格
description: Azure Security Center は、Azure Defender が有効および無効の 2 つのモードで提供されます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: a01d4137217aa594d4636f3338d3f33dc03cc836
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713742"
---
# <a name="pricing-of-azure-security-center"></a>Azure Security Center の価格
Azure Security Center は、Azure、オンプレミス、他のクラウドで実行されているワークロードの統合セキュリティ管理と高度な脅威保護を実現します。 ハイブリッド クラウド ワークロードの可視化と制御、脅威にさらされる機会を減らす積極的防御、急速に進化するサイバー攻撃への対応に役立つインテリジェント検出などの機能が提供されます。


## <a name="free-option-vs-azure-defender-enabled"></a>Free オプションと Azure Defender 有効

Security Center は、次の 2 つのモードで提供されます。

- **Azure Defender 無効** (Free) - Azure Defender を使用しない Security Center は、Azure portal 内の Azure Security Center ダッシュボードに初めてアクセスしたとき、または API を介してプログラムで有効にした場合に、すべての Azure サブスクリプションで無料で有効になります。 この無料モードを使用すると、セキュリティ ポリシー、継続的なセキュリティ評価、Azure リソースの保護に役立つ実践的なセキュリティの推奨事項が提供されます。

- **Azure Defender 有効** - 無料モードの機能を、プライベートおよび他のパブリック クラウドで実行されているワークロードまで拡張し、ハイブリッド クラウド ワークロード全体に統合されたセキュリティ管理と脅威防止機能を提供します。 Azure Defender の主な機能の一部を次に示します。

    - **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 セキュリティ ポリシーを適用し、ハイブリッド クラウドのワークロードのセキュリティを継続的に評価することで、セキュリティ標準に確実に準拠できます。 ファイアウォールやその他のパートナー ソリューションなどのさまざまなソースから、セキュリティ データを収集、検索、分析します。
    - **脅威防止アラート** - 高度な行動分析と Microsoft インテリジェント セキュリティ グラフを使用して、巧妙化するサイバー攻撃に対応します。 組み込みの行動分析と機械学習を活用して、各種攻撃やゼロデイ攻撃を特定します。 ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、調査を効率化します。
    - **仮想マシンとコンテナー レジストリの脆弱性スキャン** - 業界で最も高度な脆弱性管理ソリューションを提供するスキャナーを、すべての仮想マシンに簡単にデプロイできます。 結果の表示、調査、修復を Security Center 内で直接実行できます。 
    - **アクセスとアプリケーションの制御** - 特定のワークロードに適応する、機械学習を活用した推奨事項を適用して、許可リストと拒否リストを作成することで、マルウェアや他の望ましくないアプリケーションをブロックします。 Azure VM の管理ポートに対するジャスト イン タイムの制御されたアクセスで、ネットワーク攻撃対象領域を減らします。 ブルート フォースなどのネットワーク攻撃に対する露出が、これによって劇的に減少します。
    - **コンテナーのセキュリティ機能** - コンテナー化された環境で、脆弱性管理とリアルタイムの脅威の防止が利用できます。 **Azure Defender for container registries** を有効にした場合、すべての機能が有効になるまで最大 12 時間かかることがあります。 料金は、接続されたレジストリにプッシュされた一意のコンテナー イメージの数に基づいています。 イメージのスキャンが 1 回完了すると、変更されてもう 1 度プッシュされない限り、再度課金されることはありません。 

## <a name="try-azure-defender-free-for-30-days"></a>Azure Defender を 30 日間無料で試用する

Azure Defender は、最初の 30 日間は無料で利用できます。 30 日経過した時点で、サービスの利用を継続することを選択した場合、使用量に応じた課金が自動的に開始されます。

## <a name="enable-azure-defender"></a>Azure Defender を有効にする

Azure Defender を使用して Azure サブスクリプション全体を保護できます。保護は、サブスクリプション内のすべてのリソースに継承されます。

Azure Defender を有効にするには、次の手順に従います。

1. Security Center のメイン メニューから、 **[価格と設定]** を選択します。
1. アップグレードするサブスクリプションを選択します。
1. **[Azure Defender on]\(Azure Defender を有効化\)** を選択してアップグレードします。
1. **[保存]** を選択します。

サンプルのサブスクリプションの価格ページを次に示します。 Azure Defender の各プランは個別に価格設定されているので、個別に有効/無効に設定できます。

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="ポータルの Security Center の価格ページ":::

> [!NOTE]
> 脅威防止機能を含め、Security Center のすべての機能を有効にするには、適用可能なワークロードを含むサブスクリプションで Azure Defender を有効にする必要があります。 ワークスペース レベルで有効にしても、Just-In-Time VM アクセス、適応型アプリケーション制御、Azure リソースのネットワーク検出は有効になりません。 さらに、ワークスペース レベルで使用できる Azure Defender プランは、Azure Defender for servers と Azure Defender for SQL servers on machines のみです。
>
> **Azure Defender for Storage accounts** は、サブスクリプション レベルまたはリソース レベルで有効にすることができます。
> **Azure Defender for SQL** は、サブスクリプション レベルまたはリソース レベルで有効にすることができます。
> **Azure Database for MariaDB/MySQL/PostgreSQL** の脅威保護は、リソース レベルでのみ有効にできます。


## <a name="next-steps"></a>次のステップ
この記事では、Security Center の価格について紹介しました。 関連資料については、以下を参照してください。

- [Azure ワークロードのコストを最適化する方法](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [ご利用の通貨とリージョンにおける価格の詳細](https://azure.microsoft.com/pricing/details/security-center/)
- ソリューションを特定のエージェント セットに制限することで、コストを管理し、ソリューション用に収集されるデータの量を制限することもできます。 [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用すると、ソリューションにスコープを適用し、ワークスペース内のコンピューターのサブセットをターゲットにすることができます。 ソリューションのターゲット設定を使用している場合、Security Center はソリューションがないものとしてワークスペースを表示します。