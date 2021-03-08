---
title: Azure Security Center のメイン ダッシュボードまたは [概要] ページ
description: Security Center の概要ページの機能について学習します
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 899fddf08a817126ce775733da38ffd10ab3ab23
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952116"
---
# <a name="azure-security-centers-overview-page"></a>Azure Security Center の概要ページ

Azure Security Center を開いたときに、最初に表示されるページが概要ページです。 

:::image type="content" source="media/overview-page/overview.png" alt-text="Security Center の概要ページ":::

Security Center の概要ページで、ワークロードのセキュリティを検出して評価し、リスクを特定して軽減します。

概要では、ハイブリッド クラウド ワークロードのセキュリティ対策について統合されたビューが提供されます。 また、セキュリティ アラート、カバレッジ情報なども表示されます。


## <a name="features-of-the-overview-page"></a>概要ページの機能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Security Center の概要ページ":::

**上部のメニュー バー**では、次のものが提供されます。
- **サブスクリプション** - このボタンを選択して、サブスクリプションの一覧を表示およびフィルター処理できます。 選択されたサブスクリプションのセキュリティの状態を反映するように、Security Center の表示が調整されます。
- **新機能** - [リリース ノート](release-notes.md)を開いて、新機能、バグ修正、非推奨の機能を最新の状態に保つことができます。
- 接続されたクラウド アカウントにおいて、以下のメイン タイルに情報のコンテキストを表示するための**高レベルの数値**。 また、アクティブな推奨事項とアラートの数もあります。
    [AWS アカウント](quickstart-onboard-aws.md)と [GCP プロジェクト](quickstart-onboard-gcp.md)の接続の詳細について詳しく確認します。


ページの中央には **4 つの中央タイル**があります。詳細については、専用ダッシュボードへの各リンクを参照してください。
- **セキュア スコア** - Security Center によって、セキュリティの問題について、リソース、サブスクリプション、組織を継続的に評価されます。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。 [詳細については、こちらを参照してください](secure-score-security-controls.md)。
- **コンプライアンス** - お客様の Azure 環境の継続的評価に基づき、お客様のコンプライアンス体制についての分析情報が Security Center から提供されます。 Security Center を使うと、セキュリティのベスト プラクティスに従って、ハイブリッド クラウド環境のリスク要因を分析できます。 それらの評価は、サポートされている一連の標準のコンプライアンス制御に対応付けられます。 [詳細については、こちらを参照してください](security-center-compliance-dashboard.md)。
- **Azure Defender** - これは Azure とハイブリッド ワークロードを高度かつインテリジェントに保護するために Security Center 内に統合された、クラウド ワークロード保護プラットフォーム (CWPP) です。 タイルには、接続されているリソース (現在選択されているサブスクリプション用) と最近のアラート (重要度別にカラー コード) のカバレッジが表示されます。 [詳細については、こちらを参照してください](azure-defender.md)。
- **インベントリ** - このタイルには、監視されていない VM の数と、Security Center によって監視されるリソースのシンプルなバロメーターが表示されます。 [詳細については、こちらを参照してください](asset-inventory.md)。


**[分析情報]** ウィンドウでは、ご利用の環境用にカスタマイズされた次のような項目が提供されます。
- 最も攻撃を受けたリソース
- セキュア スコアを上げる可能性が最も高いセキュリティ制御
- 影響を受けたリソースが最も多いアクティブな推奨事項
- Azure Security Center の専門家による最近のブログ記事

## <a name="next-steps"></a>次の手順

このページでは、Security Center の概要ページを紹介しました。 関連情報については、以下をご覧ください。

- [資産インベントリ ツールと資産管理ツールを使用してリソースの調査と管理を行う](asset-inventory.md)
- [Azure Security Center 内のセキュリティ スコア](secure-score-security-controls.md)