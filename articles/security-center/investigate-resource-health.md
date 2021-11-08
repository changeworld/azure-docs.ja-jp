---
title: 'チュートリアル: リソースの正常性を調査する - Microsoft Defender for Cloud'
description: 'チュートリアル: Microsoft Defender for Cloud を使用してリソースの正常性を調査する方法について説明します。'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.openlocfilehash: d368ca1c8268913bcb8240f542f7bc3af2c59cff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437887"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>チュートリアル: リソースの正常性を調査する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!NOTE]
> このチュートリアルで説明する [リソース正常性] ページはプレビュー リリースです。
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |価格:|**Microsoft Defender for DNS** は、[価格ページ](https://azure.microsoft.com/pricing/details/security-center/)に示されているように課金されます。

[リソース正常性] ページでは、1 つのリソースの全体的な正常性のスナップショット ビューが提供されます。 リソースに関する詳細情報と、そのリソースに適用されるすべての推奨事項を確認できます。 また、[Microsoft Defender の高度な保護プラン](defender-for-cloud-introduction.md)のいずれかを使用している場合は、その特定リソースに関する未処理のセキュリティ アラートも表示されます。

Defender for Cloud のポータル ページにあるこの単一ページ (現在プレビュー段階) には、以下が表示されます。

1. **リソース情報** - リソースが属するリソース グループとサブスクリプション、地理的な場所など。
1. **適用されているセキュリティ機能** - そのリソースに対して Microsoft Defender プランが有効になっているかどうか。
1. **未処理の推奨事項とアラートの数** - 未処理のセキュリティに関する推奨事項と Defender for Cloud アラートの数。
1. **実行可能な推奨事項とアラート** - リソースに適用される推奨事項とアラートが 2 つのタブに一覧表示されます。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="仮想マシンの正常性情報を示す Microsoft Defender for Cloud の [リソース正常性] ページ":::

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * すべてのリソースの種類の [リソース正常性] ページにアクセスする
> * リソースに関する未解決のセキュリティの問題を評価する
> * リソースのセキュリティ体制を強化する

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明されている機能を実行するには、以下が必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
- セキュリティに関する推奨事項を適用するには、関連するアクセス許可 (リソース グループの共同作成者、リソース グループの所有者、サブスクリプションの共同作成者、またはサブスクリプションの所有者) が付与されたアカウントでサインインする必要があります。
- アラートを無視するには、関連するアクセス許可 (セキュリティ管理者、サブスクリプションの共同作成者、またはサブスクリプションの所有者) が付与されたアカウントでサインインする必要があります。

##  <a name="access-the-health-information-for-a-resource"></a>リソースの正常性情報にアクセスする

> [!TIP]
> 以下のスクリーンショットでは、仮想マシンを開いていますが、[リソース正常性] ページには、すべてのリソースの種類の詳細を表示できます。 

リソースの [リソース正常性] ページを開くには:

1. [資産インベントリ ページ](asset-inventory.md)で任意のリソースを選択します。

    :::image type="content" source="media/investigate-resource-health/inventory-select-resource.png" alt-text="資産インベントリでリソースを選択して、[リソース正常性] ページを表示します。" lightbox="./media/investigate-resource-health/inventory-select-resource.png":::

1. [リソース正常性] ページの左ペインでは、リソースのサブスクリプション、状態、監視に関する情報の概要を確認できます。 強化されたセキュリティ機能がリソースに対して有効になっているかどうかも確認できます。

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="Microsoft Defender for Cloud の [リソース正常性] ページの左ペインには、リソースのサブスクリプション、状態、監視に関する情報が表示されます。また、未処理のセキュリティに関する推奨事項とセキュリティ アラートの総数も含まれています。":::

1. 右ペインの 2 つのタブを使用して、このリソースに適用されるセキュリティに関する推奨事項とアラートの一覧を確認します。

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="Microsoft Defender for Cloud の [リソース正常性] ページの右ペインには、推奨事項とアラートの 2 つのタブがあります。" lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > Microsoft Defender for Cloud では、リソースのセキュリティ状態を表すために、"正常" と "異常" という用語が使用されます。 これらの用語は、リソースが特定の[セキュリティに関する推奨事項](security-policy-concept.md#what-is-a-security-recommendation)に準拠しているかどうかに関連しています。
    >
    > 上記のスクリーンショットでは、このリソースが "正常" である場合でも、推奨事項が示されていることがわかります。 [リソース正常性] ページの利点の 1 つは、リソースの正常性の全体像を把握できるように、すべての推奨事項が一覧表示されることです。 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>リソースに関する未解決のセキュリティの問題を評価する

[リソース正常性] ページには、リソースが "異常" である推奨事項とアクティブなアラートが一覧表示されます。 

- サブスクリプションに適用されているポリシーに従ってリソースのセキュリティを強化できるようにするには、推奨事項で説明されている問題を修正します。
    1. 右ペインで推奨事項を選択します。
    1. 画面の指示に従って続行します。

        > [!TIP]
        > セキュリティに関する推奨事項によって提起された問題の修正手順は、Defender for Cloud の推奨事項ごとに異なります。
        >
        > 最初に解決すべき推奨事項を決定するには、それぞれの重大度と、[セキュリティ スコアへの潜在的な影響](secure-score-security-controls.md#security-controls-and-their-recommendations)を確認します。

- セキュリティ アラートを調査するには、次のようにします。
    1. 右ペインでアラートを選択します。
    1. 「[セキュリティの警告への対応](managing-and-responding-alerts.md#respond-to-security-alerts)」の手順に従います。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Defender for Cloud の [リソース正常性] ページの使用方法について説明しました。

詳細については、次の関連ページを参照してください。

- [セキュリティの警告への対応](managing-and-responding-alerts.md#respond-to-security-alerts)
- [セキュリティの推奨事項を確認する](review-security-recommendations.md)