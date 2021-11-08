---
title: アラートへの対応のチュートリアル - Microsoft Defender for Cloud
description: このチュートリアルでは、セキュリティ アラートをトリアージし、アラートの根本原因と影響範囲の特定する方法について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 7053bcc1e28eeb262dda8833ddf98806aae770b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472107"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>チュートリアル:セキュリティ アラートのトリアージ、調査、対応

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud では、高度な分析および脅威インテリジェンスを使用してハイブリッド クラウド ワークロードを継続的に分析し、クラウド リソースでの潜在的な悪意のあるアクティビティに関するアラートを受け取ることができます。 さらに、他のセキュリティ製品およびサービスからのアラートを Defender for Cloud に統合することもできます。 アラートが発生したら、潜在的なセキュリティの問題の調査と修復を行うために、迅速なアクションが必要になります。 

このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * セキュリティ アラートのトリアージ
> * セキュリティ アラートの調査による根本原因の特定
> * セキュリティ アラートへの対応と根本原因の軽減

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルで説明する機能を手順に従って実行するには、Defender for Cloud の強化されたセキュリティ機能を有効にする必要があります。 これらは無料で試すことができます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 アップグレード方法については、[Defender for Cloud の概要](get-started.md)のクイックスタートを参照してください。


## <a name="triage-security-alerts"></a>セキュリティ アラートのトリアージ
Defender for Cloud では、すべてのセキュリティ アラートを統合された 1 つのビューで確認できます。 セキュリティ アラートは、検出されたアクティビティの重要度に基づいて優先度が付けられます。 

**[セキュリティ アラート]** ページからアラートをトリアージします。

:::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Microsoft Defender for Cloud のセキュリティ アラート一覧":::

このページを使用して、自分の環境内で発生しているセキュリティ アラートを確認し、最初に調査するアラートを決めます。

セキュリティ アラートをトリアージする際は、アラートにその重要度に基づく優先順位を付けます。重要度が高いアラートから先に対応します。 アラートの重要度の詳細については、「[アラートはどのように分類されますか](alerts-overview.md#how-are-alerts-classified)」を参照してください。

> [!TIP]
> Microsoft Defender for Cloud を Microsoft Sentinel を含む最も一般的な SIEM ソリューションに接続し、選択したツールからアラートを利用できます。 詳細については、「[SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)」を参照してください。


## <a name="investigate-a-security-alert"></a>セキュリティ アラートの調査

最初に調査するアラートが決まったら、次を実行します。

1. 目的のアラートを選択します。
1. アラートの概要ページから、最初に調査するリソースを選択します。
1. セキュリティ アラートの概要が表示される左ペインから調査を開始します。

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="概要情報が強調表示されている、アラートの詳細ページの左側のウィンドウ。":::

    このペインには、次の情報が表示されます。
    - アラートの重要度、状態、アクティビティの時刻
    - 検出された正確なアクティビティの説明
    - 影響を受けるリソース
    - アクティビティのキル チェーンの意図 (MITRE ATT&CK マトリックスで表示)

1. 不審なアクティビティの調査に役立つ可能性のある詳細情報については、 **[アラートの詳細]** タブを調べます。

1. このページの情報を確認したら、対応を続行する準備は完了です。 さらに詳しい情報が必要な場合は、次を実行してください。

    - リソース所有者に連絡して、検出されたアクティビティが擬陽性であるかどうかを確かめます。
    - 攻撃されたリソースによって生成された生のログを調査します。

## <a name="respond-to-a-security-alert"></a>セキュリティ アラートへの対応
セキュリティのアラートを調査し、そのスコープを理解したら、Microsoft Defender for Cloud 内からアラートに対応できます。

1.  **[アクションの実行]** タブを開いて推奨される対応を確認します。

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="セキュリティ アラートの [アクションの実行] タブ。" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  **[Mitigate the threat]\(脅威の軽減\)** セクションで、問題の軽減に必要な手作業による調査手順を確認します。
1.  リソースのセキュリティを強化し、今後、この種の攻撃を防止するには、 **[Prevent future attacks]\(将来の攻撃防止\)** セクションで、セキュリティに関する推奨事項を修正します。
1.  自動化された対応手順でロジック アプリをトリガーする場合は、 **[Trigger automated response]\(自動応答のトリガー\)** セクションを使用します。
1.  検出されたアクティビティが悪質 "*ではない*" 場合は、 **[類似のアラートの抑制]** セクションを使用して、今後はこの種のアラートが表示されないようにすることができます。

1.  アラートの調査が完了し、適切に対応したら、その状態を **[Dismissed]\(無視\)** に変更します。

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="アラートの状態の設定":::

    そのアラートがメインのアラート一覧から削除されます。 アラートの一覧ページからフィルターを使用して、 **[Dismissed]\(無視\)** 状態のアラートをすべて表示することができます。

1.  次のように、アラートについてのフィードバックを Microsoft に提供することをお勧めします。
    1. アラートが **役に立った** か **役に立たなかった** かを選択します。
    1. 理由を選択し、コメントを追加します。

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="アラートが役に立ったかどうかに関するフィードバックの Microsoft への提供。":::

    > [!TIP]
    > Microsoft は、お客様からのフィードバックを確認してアルゴリズムを改良し、セキュリティ アラートの向上に努めています。

## <a name="end-the-tutorial"></a>チュートリアルの終了

このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 引き続き後続のクイックスタートとチュートリアルに取り組む予定の場合、自動プロビジョニングと Defender for Cloud の強化されたセキュリティ機能を有効のままにしてください。 

続ける予定がない場合や、これらの機能をいずれも無効にしたい場合は、次を実行します。

1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。
1. 関連するサブスクリプションを選択します。
1. **[Defender プラン]** を選択し、 **[強化されたセキュリティ無効]** を選択します。

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Defender for Cloud の強化されたセキュリティ機能を有効または有効にします。":::

1. **[保存]** を選択します。

    > [!NOTE]
    > 強化されたセキュリティ機能を無効にすると、1 つのプランを無効にしたか、すべてのプランを一度に無効にしたかどうかに関係なく、データ収集が短時間継続される場合があります。 

1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。
1. 関連するサブスクリプションを選択します。
1. **[自動プロビジョニング]** を選択します。
1. 関連する拡張機能を無効にします。

    >[!NOTE]
    > 自動プロビジョニングを無効にしても、既に Azure VM に存在する Log Analytics エージェントは削除されません。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、セキュリティ アラートへの対応時に使用される Defender for Cloud の機能について説明しました。 関連資料については、以下を参照してください。

- [Microsoft Defender for Key Vault のアラートに対応する](defender-for-key-vault-usage.md)
- [セキュリティ アラート - リファレンス ガイド](alerts-reference.md)
- [Defender for Cloud の概要](defender-for-cloud-introduction.md)