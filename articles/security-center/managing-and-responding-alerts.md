---
title: Microsoft Defender for Cloud セキュリティ アラートを管理する | Microsoft Docs
description: このドキュメントは、Microsoft Defender for Cloud の機能を使用してセキュリティ アラートを管理および対応する際に役立ちます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 72b8be0c2f61eb954c4aa34c9477f786d152acd4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452891"
---
# <a name="manage-and-respond-to-security-alerts-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でセキュリティ アラートの管理と対応を行う

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このトピックでは、Defender for Cloud のアラートを表示および処理し、リソースを保護する方法について説明します。

セキュリティ アラートをトリガーする高度な検出は、Microsoft Defender for Cloud の拡張セキュリティ機能が有効になっている場合のみ使用できます。 無料試用版が提供されています。 アップグレードするには、「[強化された保護を有効にする](enable-enhanced-security.md)」をご覧ください。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
Defender for Cloud は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Defender for Cloud には、優先順位の付いたセキュリティ アラートの一覧が、問題を迅速に調査するために必要な情報、および攻撃を修復するために実行する手順と共に表示されます。

さまざまな種類のアラートについては、「[セキュリティ アラート - リファレンス ガイド](alerts-reference.md)」をご覧ください。

Defender for Cloud でアラートが生成されるしくみの概要については、[Microsoft Defender for Cloud での脅威の検出と対応方法](alerts-overview.md)に関する記事を参照してください。


## <a name="manage-your-security-alerts"></a>セキュリティ アラートの管理

1. Microsoft Defender for Cloud の概要ページの上部にある **[セキュリティ アラート]** タイルまたはサイドバーのリンクを選択します。

    :::image type="content" source="media/managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Microsoft Defender for Cloud の概要ページからセキュリティ アラートのページに移動する":::

    [セキュリティ アラート] ページが開きます。

    :::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Microsoft Defender for Cloud のセキュリティ アラート一覧":::

1. アラートの一覧をフィルター処理するには、関連するフィルターを選択します。 必要に応じて、 **[フィルターの追加]** オプションを使用して、さらにフィルターを追加することもできます。

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="アラート ビューへのフィルターの追加。" lightbox="./media/managing-and-responding-alerts/alerts-adding-filters-large.png":::

    この一覧は、選択したフィルター オプションに応じて更新されます。 フィルター処理はとても有益な機能です。 たとえば、システム内の潜在的な違反を調査するために、過去 24 時間以内に発生したセキュリティの警告を確認することができます。


## <a name="respond-to-security-alerts"></a>セキュリティの警告への対応

1. **[セキュリティ アラート]** リストから、アラートを選択します。 作業ウィンドウが開き、アラートとその影響を受けたすべてのリソースの説明が表示されます。 

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-details-pane.png" alt-text="セキュリティ アラートのミニ詳細ビュー。":::

    > [!TIP]
    > この作業ウィンドウを開いた状態で、キーボードの上矢印と下矢印を使用して、アラートの一覧をすばやく確認できます。

1. 詳細については、 **[すべての詳細を表示]** を選択します。

    セキュリティ アラート ページの左側のウィンドウには、セキュリティ アラートに関する概要情報 (タイトル、重要度、状態、アクティビティ時間、疑わしいアクティビティの説明、影響を受けるリソース) が表示されます。 影響を受けるリソースと共に、リソースに関連する Azure タグがあります。 これらを使用して、アラートを調査するときにリソースの組織コンテキストを推測します。

    右側のペインには、問題の調査に役立つアラートの詳細が含まれている **[アラートの詳細]** タブがあります。IP アドレス、ファイル、プロセスなど。
     
    ![セキュリティ アラートに対処する方法の推奨事項。](./media/managing-and-responding-alerts/security-center-alert-remediate.png)

    また、右側のペインには **[アクションの実行]** タブがあります。このタブを使用して、セキュリティ アラートに関するその他のアクションを実行します。 次のようなアクションがあります。
    - *[Mitigate the threat]\(脅威の軽減\)* - このセキュリティ アラートに対する手動の修復手順を提供します
    - *[Prevent future attacks]\(将来の攻撃防止\)* - セキュリティに関する推奨事項を提供して、攻撃対象を減らし、セキュリティ体制を強化し、将来の攻撃を防ぐことができるようにします
    - *[Trigger automated response]\(自動応答のトリガー\)* - このセキュリティ アラートへの応答としてロジック アプリをトリガーするオプションを提供します
    - *[Suppress similar alerts]\(類似のアラートの抑制\)* - 組織に関連しないアラートの場合、同様の特性を持つ今後のアラートを抑制するオプションを提供します

    ![[アクションの実行] タブ。](./media/managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>関連項目

このドキュメントでは、セキュリティ アラートを表示する方法について説明しました。 関連資料については、次のページを参照してください。

- [アラートの抑制ルールを構成する](alerts-suppression-rules.md)
- [Defender for Cloud のトリガーへの応答を自動化する](workflow-automation.md)
- [セキュリティ アラート - リファレンス ガイド](alerts-reference.md)
