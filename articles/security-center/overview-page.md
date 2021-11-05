---
title: Microsoft Defender for Cloud のメイン ダッシュボードまたは "概要" ページ
description: Defender for Cloud の概要ページの機能について説明します
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0ab115e1e963283a2b8f7e7abd963653b9a39dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084092"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Microsoft Defender for Cloud の概要ページ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud を開くと、最初に表示されるページが概要ページです。 

この対話式のダッシュボードでは、ハイブリッド クラウド ワークロードのセキュリティ対策について一元的なビューが提供されます。 また、セキュリティ アラート、カバレッジ情報なども表示されます。

ページ上の任意の要素を選択すると、より詳細な情報を取得できます。

:::image type="content" source="media/overview-page/overview.png" alt-text="Defender for Cloud の概要ページ":::

## <a name="features-of-the-overview-page"></a>概要ページの機能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Defender for Cloud の概要ページの上部バー":::

**上部のメニュー バー** では、次のものが提供されます。
- **サブスクリプション** - このボタンを選択して、サブスクリプションの一覧を表示およびフィルター処理できます。 Defender for Cloud では、選択されたサブスクリプションのセキュリティの状態を反映するように表示が調整されます。
- **新機能** - [リリース ノート](release-notes.md)を開いて、新機能、バグ修正、非推奨の機能を最新の状態に保つことができます。
- 接続されたクラウド アカウントにおいて、以下のメイン タイルに情報のコンテキストを表示するための **高レベルの数値**。 評価されたリソース、アクティブな推奨事項、セキュリティ アラートの数も示されます。 評価されたリソース数を選択すると、[資産インベントリ](asset-inventory.md)にアクセスできます。 [AWS アカウント](quickstart-onboard-aws.md)と [GCP プロジェクト](quickstart-onboard-gcp.md)の接続の詳細について詳しく確認します。


ページの中央には **4 つの中央タイル** があります。詳細については、専用ダッシュボードへの各リンクを参照してください。
- **セキュリティ スコア** - Defender for Cloud では、セキュリティの問題について、リソース、サブスクリプション、組織が継続的に評価されます。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。 [詳細については、こちらを参照してください](secure-score-security-controls.md)。
- **規制コンプライアンス** - Azure 環境の継続的評価に基づいて、コンプライアンス体制に関する分析情報が Defender for Cloud から提供されます。 Defender for Cloud では、セキュリティのベスト プラクティスに従って、ハイブリッド クラウド環境のリスク要因が分析されます。 それらの評価は、サポートされている一連の標準のコンプライアンス制御に対応付けられます。 [詳細については、こちらを参照してください](regulatory-compliance-dashboard.md)。
- **ワークロードの保護** - これは、Azure、オンプレミス マシン、または他のクラウド プロバイダーで実行されているワークロードの高度でインテリジェントな保護を実現するために、Defender for Cloud 内に統合されたクラウド ワークロード保護プラットフォーム (CWPP) です。 リソースの種類ごとに、対応する Microsoft Defender プランがあります。 タイルには、接続されているリソース (現在選択されているサブスクリプション用) と最近のアラート (重要度別にカラー コード) のカバレッジが表示されます。 [Microsoft Defender の高度な保護プラン](defender-for-cloud-introduction.md)の詳細を確認してください。
- **Firewall Manager** - このタイルには、[Azure Firewall Manager](../firewall-manager/overview.md) のハブとネットワークの状態が表示されます。 


**[分析情報]** ウィンドウでは、ご利用の環境用にカスタマイズされた次のような項目が提供されます。
- 最も攻撃を受けたリソース
- セキュア スコアを上げる可能性が最も高いセキュリティ制御
- 影響を受けたリソースが最も多いアクティブな推奨事項
- Microsoft Defender for Cloud 専門家による最近のブログ投稿

## <a name="next-steps"></a>次のステップ

このページでは、Defender for Cloud の概要ページを紹介しました。 関連情報については、以下をご覧ください。

- [資産インベントリ ツールと資産管理ツールを使用してリソースの調査と管理を行う](asset-inventory.md)
- [Microsoft Defender for Cloud のセキュリティ スコア](secure-score-security-controls.md)
