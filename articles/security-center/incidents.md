---
title: Microsoft Defender for Cloud サービスでセキュリティ インシデントを管理|Microsoft Docs
description: このドキュメントは、Microsoft Defender for Cloud を使用してセキュリティ インシデントを管理する場合に役立ちます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3977d95358c20b98551170394bcd846d0b4e8358
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017466"
---
# <a name="manage-security-incidents-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でセキュリティ インシデントを管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

セキュリティ アラートのトリアージと調査は、熟練のセキュリティ アナリストでさえ長時間を要することのある作業です。 多くのユーザーにとっては、どこから始めればよいかを判断することも困難です。 

Defender for Cloud では、 [分析を使用して](./alerts-overview.md) 、個別のセキュリティ アラート 間で情報 [を接続します](managing-and-responding-alerts.md)。 これらの接続を使用すると、Defender for Cloud は攻撃キャンペーンとその関連アラートを 1 つのビューで表示して、攻撃者のアクションと影響を受けるリソースを把握するのに役立ちます。

このページでは、Defender for Cloud でのインシデントの概要について説明します。

## <a name="what-is-a-security-incident"></a>セキュリティ インシデントとは

Defender for Cloud では、セキュリティ インシデントは、キル チェーン パターンに合ったリソースのすべてのアラート [の](alerts-reference.md#intentions) 集計です。 インシデントは [[セキュリティ アラート]](managing-and-responding-alerts.md) ページの一覧に表示されます。 インシデントを選択すると、関連するアラートと詳細情報が表示されます。

## <a name="managing-security-incidents"></a>セキュリティ インシデントの管理

1. Defender for Cloud の **[アラート]** ページで、[フィルターの追加] ボタンを使用して、アラート名でフィルター処理し、複数のリソースで検出されたセキュリティ インシデントというアラート名 **に設定します**。 

    :::image type="content" source="media/incidents/locating-incidents.png" alt-text="Microsoft Defender for Cloud のアラート ページでインシデントを見つける。":::

    一覧がフィルター処理され、インシデントのみが表示されるようになりました。 セキュリティ インシデントには、セキュリティ アラートとは異なるアイコンが付いています。

    :::image type="content" source="media/incidents/incidents-list.png" alt-text="Microsoft Defender for Cloud のアラート ページのインシデントの一覧。":::

1. インシデントの詳細を表示するには、一覧から 1 つを選択します。 サイド ペインに、インシデントの詳細が表示されます。

    :::image type="content" source="media/incidents/incident-quick-peek.png" alt-text="インシデントの詳細を示すサイド ペイン。":::

1. 詳細を表示するには、 **[すべての詳細を表示]** を選択します。

    [![Microsoft Defender for Cloud のセキュリティ インシデントに対応します。](media/incidents/incident-details.png)](media/incidents/incident-details.png#lightbox)

    セキュリティ インシデント ページの左側のウィンドウには、セキュリティ インシデントに関する概要情報 (タイトル、重要度、状態、アクティビティ時間、説明、影響を受けるリソース) が表示されます。 影響を受けるリソースの横に、関連する Azure タグが表示されます。 これらのタグを使用して、アラートを調査するときにリソースの組織コンテキストを推測します。

    右側のウィンドウには、このインシデントの一部として関連付けられたセキュリティ アラートを含む **[アラート]** タブが表示されます。 

    >[!TIP]
    > 具体的なアラートの詳細については、そのアラートを選択してください。 

    [![インシデントの [アクションの実行] タブ。](media/incidents/incident-take-action-tab.png)](media/incidents/incident-take-action-tab.png#lightbox)

    **[アクションの実行]** タブに切り替えるには、右側のウィンドウの下部にあるタブまたはボタンを選択します。 このタブを使用すると、次のような操作を実行できます。
    - *[Mitigate the threat]\(脅威の軽減\)* - このセキュリティ インシデントに対する手動の修復手順を提供します
    - *[Prevent future attacks]\(将来の攻撃防止\)* - セキュリティに関する推奨事項を提供して、攻撃対象を減らし、セキュリティ体制を強化し、将来の攻撃を防ぐことができるようにします
    - *[Trigger automated response]\(自動応答のトリガー\)* - このセキュリティ インシデントへの応答としてロジック アプリをトリガーするオプションを提供します
    - *[Suppress similar alerts]\(類似のアラートの抑制\)* - 組織に関連しないアラートの場合、同様の特性を持つ今後のアラートを抑制するオプションを提供します 

   > [!NOTE]
   > 同じアラートが 1 つのインシデントに含まれることもあれば、スタンドアロン アラートとして表示されることもあります。

1. インシデントの脅威を修復するには、各アラートに対する修復手順に従います。


## <a name="next-steps"></a>次のステップ

このページでは、Defender for Cloud のセキュリティ インシデント機能について説明しました。 関連情報については、次のページを参照してください。

- [Defender for Cloud のセキュリティ アラート](alerts-overview.md)
- [セキュリティ アラートの管理と対応](managing-and-responding-alerts.md)
