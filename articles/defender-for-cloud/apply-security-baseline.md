---
title: Azure のセキュリティベースラインと Microsoft Defender for Cloud を使用して、Windows と Linux OS を強化する
description: Microsoft Defender for Cloud がゲスト構成を使用して、Azure セキュリティベンチマークのガイダンスと OS のセキュリティ強化を比較する方法について説明します
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 3ac0b2745ac7abe91bc9f1a122e98842ce0e4f0e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525606"
---
# <a name="apply-azure-security-baselines-to-machines"></a>マシンに Azure セキュリティ ベースラインを適用する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

マシンの攻撃面を減らし、既知のリスクを回避するには、オペレーティング システム (OS) を可能な限り安全に構成することが重要です。

Azure セキュリティ ベンチマークには、OS のセキュリティ強化に関するガイダンスが記載されており、これは [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) および [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md) 用のセキュリティ ベースライン ドキュメントにつながっています。

この記事で説明されているセキュリティに関する推奨事項を使用して、環境内のマシンを評価し、以下を実現します。

- セキュリティ構成のギャップを特定する
- これらのギャップを修復する方法を学習する

## <a name="availability"></a>可用性
|側面|詳細|
|----|:----|
|リリース状態:|プレビュー。<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|価格:|Free|
|前提条件:|マシンは、(1) ワークグループのメンバーであること、(2) ゲスト構成拡張機能が搭載されていること、(3) システム割り当てマネージド ID を持っていること、および (4) サポートされている OS を実行していることが必要です。<br>• Windows Server 2012、2012r2、2016 または 2019<br>• Ubuntu 14.04、16.04、17.04、18.04 または 20.04<br>• Debian 7、8、9、または 10<br>• CentOS 7 または 8<br>• Red Hat Enterprise Linux (RHEL) 7 または 8<br>• Oracle Linux 7 または 8<br>• SUSE Linux Enterprise Server 12|
|必要なロールとアクセス許可:|ゲスト構成拡張機能とその前提条件をインストールするには、関連するマシンに対する **書き込み** アクセス許可が必要です。<br>推奨事項を **表示** し、OS ベースライン データを探索するには、サブスクリプション レベルの **読み取り** アクセス許可が必要です。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="what-are-the-hardening-recommendations"></a>セキュリティ強化の推奨事項は何ですか?

Microsoft Defender for Cloud には、環境内の Windows と Linux マシンの構成が Azure のセキュリティベースライン構成を満たしているかどうかを確認する2つの推奨事項が含まれています。

- **Windows** マシンの場合、[Windows マシンのセキュリティ構成の脆弱性を修復する必要がある (Powered by ゲスト構成)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6) によって、構成と [Windows セキュリティ ベースライン](../governance/policy/samples/guest-configuration-baseline-windows.md)の比較が行われます。
- **Linux** マシンの場合、[Linux マシンのセキュリティ構成の脆弱性を修復する必要がある (Powered by ゲスト構成)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) によって、構成と [Linux セキュリティ ベースライン](../governance/policy/samples/guest-configuration-baseline-linux.md)の比較が行われます。

これらの推奨事項は、Azure Policy のゲスト構成機能を使用して、マシンの OS 構成と [Azure セキュリティ ベンチマーク](/security/benchmark/azure/overview)で定義されたベースラインとの比較を行っています。

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>サブスクリプション内のマシンと OS セキュリティ ベースラインを比較する

マシンを OS セキュリティ ベースラインと比較するには、以下の手順を実行します。
 
1. Security Center のポータル ページから、 **[推奨事項]** ページを開きます。 
1. 次のように、該当する推奨事項を選択します。
    - **Windows** マシンの場合は、[Windows マシンのセキュリティ構成の脆弱性を修復する必要がある (Powered by ゲスト構成)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
    - **Linux** マシンの場合は、[Linux マシンのセキュリティ構成の脆弱性を修復する必要がある (Powered by ゲスト構成)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="マシンの OS 構成と関連する Azure セキュリティ ベースラインの比較に使用する 2 つの推奨事項。" lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. 推薦事項の詳細ページには、以下が表示されています。
    1. 影響を受けるリソース。
    1. 失敗した具体的なセキュリティ チェック。

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="Windows マシンのベースライン構成の脆弱性に関する推奨事項が記載されている Windows の推奨事項の詳細ページ。" lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. 特定の結果の詳細を確認するには、その結果を選択します。

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text="定義されたセキュリティ ベースラインと OS 構成を比較した結果、ゲスト構成から得られた情報の詳細について確認しています。" lightbox="media/apply-security-baseline/finding-details.png":::

1. その他の実施可能な調査:

    - 評価済みのマシンのリストを表示するには、 **[影響を受けるリソース]** を開きます。
    - ある 1 つのマシンに対する調査結果のリストを表示するには、 **[正常でないリソース]** タブからマシンを選択します。すると、マシンに関する結果のみが表示されたページが開きます。


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>FAQ - セキュリティ ベースラインに従って OS のセキュリティを強化する

- [セキュリティ構成に関する推奨事項の前提条件をデプロイする方法を教えてください](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [マシンが該当なしと表示されるのはなぜですか?](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>セキュリティ構成に関する推奨事項の前提条件をデプロイする方法を教えてください

前提条件を満たすゲスト構成拡張機能をデプロイするには、以下の手順を実行します。

- 選択したマシンに対して、**セキュリティのベスト プラクティスを実装する** セキュリティ コントロールのセキュリティの推薦事項である **ゲスト構成拡張機能がマシンにインストールされている必要がある** に従います。

- 規模が大きい場合は、ポリシー イニシアティブの **仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする** を割り当てます。


### <a name="why-is-a-machine-shown-as-not-applicable"></a>マシンが該当なしと表示されるのはなぜですか?

**[該当なし]** タブに記載されているリソースのリストには、 **[理由]** 列も含まれています。 一般的な理由として、以下が挙げられます。

| 理由                                                            | 詳細                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **マシン上に利用可能なスキャン データがない**                         | Azure Resource Graph に、このマシンのコンプライアンス結果がありません。 すべてのコンプライアンス結果は、ゲスト構成拡張機能によって Azure Resource Graph に書き込まれます。 Azure Resource Graph のデータは、[Azure Policy ゲスト構成 - ARG クエリのサンプル](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration)に関するページのサンプル クエリを使用することで確認できます。|
| **ゲスト構成拡張機能がマシンにインストールされていない** | Azure セキュリティ ベースラインへのコンプライアンスを評価するための前提条件である、ゲスト構成拡張機能がマシンに搭載されていません。                               |
| **システム マネージド ID がマシンで構成されていない**      | システム割り当てマネージド ID をマシンにデプロイする必要があります。                                                                                                           |
| **ポリシーで推奨事項が無効になっている**                      | OS ベースラインを評価するポリシー定義が、関連するマシンが含まれているスコープで無効になっています。                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>次のステップ
このドキュメントでは、クラウドのゲスト構成に関する推奨事項に Defender を使用して、OS のセキュリティ強化と Azure のセキュリティベースラインを比較する方法について説明しました。

これらの構成設定に関する詳細については、以下を参照してください。

- [Windows セキュリティ ベースライン](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Linux セキュリティ ベースライン](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Azure セキュリティ ベンチマーク](/security/benchmark/azure/overview)
