---
title: Azure Sentinel で組み込みの分析ルールを使用して脅威を検出する | Microsoft Docs
description: 組み込みのテンプレートに基づいて難しい設定なしで使用できる脅威検出ルールの使用方法について説明します。これを使用すると、何か疑わしいことが発生したときに通知が届きます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: yelevin
ms.openlocfilehash: ba757474ab24006b7b8b514bda085522bd353ea8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312673"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>チュートリアル:難しい設定なしで脅威を検出する

Azure Sentinel に[データ ソースを接続](quickstart-onboard.md)したら、不審な事態が起きたときに通知を受けるようにすることができます。 そのため Azure Sentinel には、脅威検出規則の作成に役立つ、すぐに使用できる組み込みのテンプレートが用意されています。 このテンプレートは、セキュリティ専門家とアナリストから構成される Microsoft のチームが既知の脅威、一般的な攻撃ベクトル、疑わしい行動の段階的拡大チェーンに基づいて設計したものです。 これらのテンプレートから作成される規則により、環境全体にわたって、疑わしい活動がないか自動的に検査されます。 テンプレートの多くは、ニーズに合わせて特定の活動を検索したり、除外したりするようにカスタマイズできます。 これらの規則で生成されるアラートによって、環境内で割り当てて調査することができるインシデントが作成されます。

このチュートリアルは、Azure Sentinel で脅威を検出するためのものです。

> [!div class="checklist"]
> * 難しい設定の要らない脅威検出機能を使用する
> * 脅威への対応を自動化する

## <a name="about-out-of-the-box-detections"></a>難しい設定の要らない検出機能について

難しい設定の要らない検出機能をすべて表示するには、 **[Analytics]** 、 **[Rule templates]\(ルール テンプレート\)** の順に選択します。 このタブには、すべての Azure Sentinel 組み込み規則が含まれています。

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Azure Sentinel の組み込みの検出機能を利用して脅威を検出する":::

次のセクションでは、使用可能な既定のテンプレートの種類について説明します。

### <a name="microsoft-security"></a>Microsoft セキュリティ

Microsoft セキュリティ テンプレートでは、他の Microsoft セキュリティ ソリューションで生成されたアラートから自動的に、Azure Sentinel インシデントがリアルタイムに作成されます。 同様のロジックで新しい規則を作成するためのテンプレートとして Microsoft セキュリティ規則を利用できます。

セキュリティ規則の詳細については、「[Microsoft セキュリティ アラートからインシデントを自動的に作成する](create-incidents-from-alerts.md)」を参照してください。

### <a name="fusion"></a>Fusion

Fusion テクノロジを基盤とする Azure Sentinel の高度なマルチステージ攻撃検出では、複数の製品にわたって多数の忠実度が低いアラートとイベントを関連付けて、忠実度が高くアクションにつながるインシデントを生成できるスケーラブルな機械学習アルゴリズムが使用されています。 Fusion は既定で有効になっています。 ロジックは非表示であるため、カスタマイズすることはできません。このテンプレートを使用して作成できる規則は 1 つだけです。

> [!IMPORTANT]
> Fusion 規則テンプレートでの一部の検出は、現在、**プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> どの検出がプレビュー中かを確認するには、「[Azure Sentinel の高度なマルチステージ攻撃の検出](fusion.md)」を参照してください。

### <a name="machine-learning-behavioral-analytics"></a>機械学習による行動分析

これらのテンプレートは、Microsoft 独自の機械学習アルゴリズムを基盤としているため、動作方法や実行のタイミングから構成される内部ロジックを参照することはできません。 ロジックは非表示であるため、カスタマイズすることはできません。この種類の各テンプレートを使用して作成できる規則は 1 つだけです。

> [!IMPORTANT]
> - 機械学習による行動分析規則テンプレートは、現在、**プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> - ML 行動分析テンプレートに基づいてルールを作成して有効にすると、機械学習エンジンおよびモデルによる処理を目的として、必要に応じて、**ご利用の Azure Sentinel ワークスペースの外部で取り込まれたデータをコピーするためのアクセス許可を Microsoft に付与することになります**。

### <a name="scheduled"></a>スケジュール済み

スケジュール付き分析ルールは、Microsoft のセキュリティ専門家が記述した組み込みのクエリに基づいています。 ユーザーはクエリ ロジックを閲覧し、変更できます。 スケジュール付き規則のテンプレートを使用してクエリ ロジックやスケジュール設定をカスタマイズし、新しい規則を作成できます。

> [!TIP]
> 規則のスケジュール設定オプションには、規則を有効にしたときに開始される時刻を使用して、指定した分、時間、または日ごとに実行する規則の構成が含まれます。
>
> 新しいまたは編集された分析規則を有効にして、新しいインシデントのスタックが時間内に規則で確実に取得されるようにするには、次のことを考慮することをお勧めします。 たとえば、SOC アナリストが就業時間の開始時に同期して規則を実行してから、規則を有効にすることができます。
>

## <a name="use-out-of-the-box-detections"></a>難しい設定の要らない検出機能を使用する

1. 組み込みのテンプレートを使用するには、テンプレート名をクリックしてから、詳細ウィンドウの **[規則の作成]** ボタンをクリックし、そのテンプレートに基づく新しいアクティブな規則を作成します。 各テンプレートには、必要なデータ ソースの一覧があります。 テンプレートを開くと、データ ソースの可用性が自動的にチェックされます。 可用性の問題がある場合は、 **[規則の作成]** ボタンが無効にされていたり、その影響に対する警告が表示されたりすることがあります。

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="検出規則のプレビュー パネル":::

1. **[規則の作成]** ボタンをクリックすると、選択したテンプレートに基づいて規則の作成ウィザードが開かれます。 すべての詳細が自動的に入力されます。**スケジュール付き** テンプレートや **Microsoft セキュリティ** テンプレートを使用する場合は、特定のニーズに合わせてロジックやその他の規則の設定をカスタマイズできます。 この手順を繰り返して、組み込みテンプレートに基づく追加の規則を作成できます。 最後まで規則の作成ウィザードの手順に従うと、テンプレートに基づく規則の作成が完了します。 **[アクティブな規則]** タブに新しい規則が表示されます。

    規則の作成ウィザードで規則をカスタマイズする方法の詳細については、「[チュートリアル:脅威を検出するためのカスタム分析規則を作成する](tutorial-detect-threats-custom.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Sentinel を使用して、脅威の検出を開始する方法について説明しました。

脅威への対応を自動化する方法については、「[Azure Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)」を参照してください。
