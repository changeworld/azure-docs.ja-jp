---
title: 'チュートリアル: リソースの正常性を調査する - Azure Security Center'
description: 'チュートリアル: Azure Security Center を使用して、リソースの正常性を調査する方法について説明します。'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.openlocfilehash: cb6f81fc02245e7932222d8555774e398944f1f4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751828"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>チュートリアル: リソースの正常性を調査する

> [!NOTE]
> このチュートリアルで説明する [リソース正常性] ページはプレビュー リリースです。
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |価格:|**Azure Defender for DNS** の課金については、[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)に関するページをご覧ください。

[リソース正常性] ページでは、1 つのリソースの全体的な正常性のスナップショット ビューが提供されます。 リソースに関する詳細情報と、そのリソースに適用されるすべての推奨事項を確認できます。 また、[Azure Defender](azure-defender.md) を使用している場合は、その特定のリソースに関する未処理のセキュリティ アラートも表示されます。

Security Center のポータル ページにあるこの単一ページ (現在プレビュー段階) には、以下が表示されます。

1. **リソース情報** - リソースが属するリソース グループとサブスクリプション、地理的な場所など。
1. **適用されているセキュリティ機能** - そのリソースに対して Azure Defender が有効になっているかどうか。
1. **未処理の推奨事項とアラートの数** - 未処理のセキュリティに関する推奨事項と Azure Defender アラートの数。
1. **実行可能な推奨事項とアラート** - リソースに適用される推奨事項とアラートが 2 つのタブに一覧表示されます。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="仮想マシンの正常性情報を示す Azure Security Center の [リソース正常性] ページ":::

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

1. [リソース正常性] ページの左ペインでは、リソースのサブスクリプション、状態、監視に関する情報の概要を確認できます。 そのリソースに対して Azure Defender が有効になっているかどうかを確認することもできます。

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="Azure Security Center の [リソース正常性] ページの左ペインには、リソースのサブスクリプション、状態、監視に関する情報が表示されます。また、未処理のセキュリティに関する推奨事項と Azure Defender アラートの総数も含まれています。":::

1. 右ペインの 2 つのタブを使用して、このリソースに適用されるセキュリティに関する推奨事項と Azure Defender アラートの一覧を確認します。

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="Azure Security Center の [リソース正常性] ページの右ペインには、推奨事項とアラートの 2 つのタブがあります。" lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > Azure Security Center では、"正常" および "異常" という用語を使用して、リソースのセキュリティの状態を表します。 これらの用語は、リソースが特定の[セキュリティに関する推奨事項](security-policy-concept.md#what-is-a-security-recommendation)に準拠しているかどうかに関連しています。
    >
    > 上記のスクリーンショットでは、このリソースが "正常" である場合でも、推奨事項が示されていることがわかります。 [リソース正常性] ページの利点の 1 つは、リソースの正常性の全体像を把握できるように、すべての推奨事項が一覧表示されることです。 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>リソースに関する未解決のセキュリティの問題を評価する

[リソース正常性] ページには、リソースが "異常" である推奨事項とアクティブなアラートが一覧表示されます。 

- サブスクリプションに適用されているポリシーに従ってリソースのセキュリティを強化できるようにするには、推奨事項で説明されている問題を修正します。
    1. 右ペインで推奨事項を選択します。
    1. 画面の指示に従って続行します。

        > [!TIP]
        > セキュリティに関する推奨事項によって提起された問題を修正するための手順は、Security Center の推奨事項ごとに異なります。
        >
        > 最初に解決すべき推奨事項を決定するには、それぞれの重大度と、[セキュリティ スコアへの潜在的な影響](secure-score-security-controls.md#security-controls-and-their-recommendations)を確認します。

- Azure Defender アラートを調査するには:
    1. 右ペインでアラートを選択します。
    1. 「[セキュリティの警告への対応](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)」の手順に従います。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Security Center の [リソース正常性] ページの使用方法について説明しました。

詳細については、次の関連ページを参照してください。

- [セキュリティの警告への対応](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)
- [セキュリティの推奨事項を確認する](security-center-recommendations.md)